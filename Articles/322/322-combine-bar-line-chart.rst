Combine Bar Chart and Line Chart
=============================================================

.. meta::
   :description: What happens when you combine a linechart and barchart? "Bline Chart"
   :keywords: linechart, barchart, widget, WebUI, Application Specific Resource

Sometimes you may want to combine a bar and linechart in one widget, a `blinechart <https://manual.aimms.com/webui/bar-line-chart-widget.html>`_. 

There are three ways to combine a line chart with a bar chart:

#.  The `Combination Chart <https://documentation.aimms.com/webui/combination-chart-widget.html>`_ available from AIMMS 4.85 and onwards.  Using this widget is the best practice of combining a bar chart and a line chart.

#.  The `Bline chart <https://www.aimms.com/support/new-features/#Barline>`_ available since AIMMS 4.76. 

#.  The Application specific resource (ASR) blinechart.  
    If you are using that ASR, it is good practice to switch to the Combination Chart:

    #.  Functionality is consistent with other widgets

    #.  Supports more than two identifiers

    #.  The widget is maintained over AIMMS versions

    To remove the deprecated blinechart from your project, please remove the folder ``blinechart`` from the folder ``MainProject\WebUI\resources\javascript``.
    
.. This is introduced as a `new feature of AIMMS 4.76 <https://www.aimms.com/support/new-features/#Barline>`_

.. In an older version of this article, a blinechart was offered as an application specific resource (ASR).
.. If you are using that ASR, please switch to the blinechart provided in AIMMS 4.76 or newer, because:
.. 
.. #.  Functionality is consistent with other widgets
.. 
.. #.  Supports more than two identifiers
.. 
.. #.  The widget is maintained over AIMMS versions
.. 


.. The example shown below has a single y-axis.
.. 
.. 
.. .. image:: images/blinechart.png
..     :align: center
.. 
.. 
.. (The blinechart is not a supported feature, so this handy tip is provided "as is".)
.. 
.. It is an example of how to use Application Specific Resources (ASR).
.. 
.. This widget has a number of assumptions regarding the data, and a couple of configurations as outlined below.
.. 
.. Using the blinechart
.. ---------------------
.. 
.. The blinechart assumes you have two identifiers in your contents, in a specific order:
.. 
.. 1. linechart identifier
.. 2. barchart identifier
.. 
.. The first item in your contents will be rendered to a linechart, and the second to a barchart. 
.. Any additional contents will not be displayed in the chart.
.. 
.. The linechart will always overlay the barchart.
.. 
.. .. note:: Changes to the data are reflected inmediately on the blinechart, but you cannot change values in the blinechart itself.
.. 
.. Pivoting
.. ^^^^^^^^
.. 
.. For this widget, it is required to have "<IDENTIFIER-SET>" in the layers; make sure the pivoting respects this.
.. 
.. Any other indexes can be ordered freely in the "headers".
.. 
.. Configuration
.. -------------
.. 
.. The blinechart has two configurations you can make in the code.
.. 
.. Changing the colors
.. ^^^^^^^^^^^^^^^^^^^^
.. 
.. The colors in the blinechart are not based on the ordinals and palette that AIMMS WebUI provides. Instead, the bars are always green and the line is always blue.
.. 
.. On line 5 and 6 of jquery.blinechart.js you can set the bar and line colors in hexadecimal notation. 
.. This is the default format for colors on the web.
.. 
.. Updating margins
.. ^^^^^^^^^^^^^^^^^^^^
.. 
.. The axis labels do not automatically size to their contents. 
.. If your indexes have long names, they might not fit inside the widget as a result. 
.. To change, this, update the "b" (for bottom) value on line 15 of jquery.blinechart.js to a higher number.
.. 
.. Example download
.. ---------------------
.. 
.. This example AIMMS project is available for download below:
.. 
.. * :download:`blineTest.zip <model/blineTest.zip>`  