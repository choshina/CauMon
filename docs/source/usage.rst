CauMon: A Tool for Online Monitoring against Signal Temporal Logic
==================================================================

Run in your local machine
=========================

Documentation
-------------

`Readthedocs Website <https://caumon.readthedocs.io/en/latest/>`_

System requirement
------------------

- Operating system: Linux or MacOS;

- MATLAB (Simulink/Stateflow) version: >= 2020a. (MATLAB license needed)

Installation of our tool
------------------------

- Clone the repository.

  1. ``git clone https://github.com/choshina/CauMon.git``
  2. ``git submodule init``
  3. ``git submodule update``

- Run ``make``

- Start MATLAB GUI, set up a C/C++ compiler using the command ``mex -setup``.
  (Refer to `here <https://www.mathworks.com/help/matlab/matlab_external/changing-default-compiler.html>`_ for more details.)

- Run ``configure.m`` in MATLAB GUI.

  .. note::

     Users of Macbook with Apple silicon (an M-series chip) may encounter the following issue:

     ::

        Undefined symbols for architecture arm64

     This compiling issue can be solved by adding a new flag ``-ld_classic`` in the compiling command.

     Namely, please change Line 62 of ``breach/Online/m_src/compile_stl_mex.m`` from:

     ::

        cxxflags = '-silent -DYYDEBUG=1 CXXFLAGS=''$CXXFLAGS -Wno-write-strings -std=gnu++11 -std=gnu++0x -Wno-deprecated-register''';

     to

     ::

        cxxflags = '-ld_classic -silent -DYYDEBUG=1 CXXFLAGS=''$CXXFLAGS -Wno-write-strings -std=gnu++11 -std=gnu++0x -Wno-deprecated-register''';


Usage
-----

- Users are required to prepare a script to start the monitor. One example is given in ``experiment/test.m``.

  Specify the names of the signals. They must be in the same order with their occurrences in traces.

  .. code-block:: matlab

     signal_str = 'speed,RPM';

  Specify an STL formula, following the syntax specified in Breach.

  .. code-block:: matlab

     phi_str = 'alw_[0,27](not(speed[t]>50) or ev_[1,3](RPM[t] < 3000))';

  Specify the moment at which the monitor targets.

  .. code-block:: matlab

     tau = 0;

  Specify the name of the file to load traces.
  If needed, trace loading can be periodic; then a termination condition can be added.

  .. code-block:: matlab

     trace_file = 'data/AT_phi3_trace9.mat';
     load(trace_file, 'trace');

  Start the robust monitor.

  .. code-block:: matlab

     [up_robM, low_robM] = stl_eval_mex_pw(signal_str, phi_str, trace, tau);

  Start the causation monitor.

  .. code-block:: matlab

     [up_optCau, low_optCau] = stl_causation_opt(signal_str, phi_str, trace, tau);

  Visualize the monitoring results.

  .. code-block:: matlab

     visualize(...
         trace, ...              % time and signals
         phi_str, ...            % formula string for title
         up_robM, low_robM, ...  % robustness metrics
         up_optCau, low_optCau, ... % causation metrics
         'result.png' ...        % output file
     );

- Run the script. A figure will be displayed and stored.

  .. image:: experiment/result.png
     :alt: result
     :width: 500px


Reproduction of the experimental result
---------------------------------------

- Figure 2 in the paper can be reproduced by running the script ``Figure2a.m``.
