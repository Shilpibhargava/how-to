Load solver session Results Manually
========================================

.. meta::
   :description: How to load results of a solver session on demand in AIMMS PRO.
   :keywords: server, session, results

If the ``waitForCompletion`` argument of ``pro::DelegateToServer`` is 0, both the data session and the solver session run in parallel. This allows the end user to browse and modify data while a delegated procedure is executed on the server side in the background. However, at the end of execution, the results are loaded back in to the data session without any warning. This unannounced loading of data does not provide the best user experience. This article presents the approach to manually control this data transfer between the data and server sessions. 

What is Happening Here?
--------------------------

A typical ``pro::DelegateToServer`` call looks as below:

.. code-block:: aimms

   if pro::GetPROEndPoint() then
      if pro::DelegateToServer(  
         completionCallback :  'pro::session::LoadResultsCallBack' )  then 
         return 1;
      endif ;
   endif ;

Any procedure written after the above call will be executed on the solver session and the results are stored as a data case file with a certain ``RequestID``, a predefined AIMMS identifier. If the ``completionCallback`` argument is the predefined  procedure ``pro::session::LoadResultsCallBack``, the aforementioned case file is loaded into the data session automatically. ``pro::session::LoadResultsCallBack`` has ``RequestID`` as a local input argument.

If you want to manually trigger this loading of results, you need:

#. the ``RequestID`` of the case file you want to load, 
#. a procedure to load this case file, and 
#. a way to activate this procedure. Here we choose a secondary action.

Example Implementation
---------------------------

The approach outlined above is implemented in the running example - the Flow Shop project. Download the completed file from :download:`5. Flow Shop - Load Results <downloads/5. Flow Shop - Load Results.zip>`.

The workflow is as below:

#. Retrieve the ``RequestID`` in a string parameter using a simple assignment procedure.

#. Define the ``completionCallback`` argument of ``pro::DelegateToServer`` call as this procedure to trigger the retrieval. 

#. Create a procedure to load the case file corresponding to the retrieved ``RequestID`` and link it to an action in a secondary page. 

Retrieving the ``RequestID``
"""""""""""""""""""""""""""""""

Create a string parameter ``sp_SavedRequestID`` to store the ``requestId`` and a binary parameter ``p01_ResultsAvailable`` to control the visibility of the load button in the user interface. 
 
.. code-block:: aimms

   StringParameter sp_SavedRequestID {
      InitialData: "";
   }
   Parameter p01_ResultsAvailable {
      Range: binary;
      InitialData: 0;
   }

Now, create an assignment procedure ``pr_myLoadResultsCallback`` with a local input argument ``RequestID`` to update the values of these identifiers. 

.. code-block:: aimms

   Procedure pr_myLoadResultsCallback {
      Arguments: (RequestID);
      Body: {
         sp_SavedRequestID := RequestID;
         p01_ResultsAvailable := 1;
      }
      StringParameter RequestID {
         Property: Input;
      }
   }

When run, this procedure simply stores ``RequestID`` in ``spSavedRequestID`` and updates ``bpResultsAvailable`` to 1. This should not be mistaken with the actual loading procedure.

.. Note that is a very quick procedure; just some administration. This administration should not be confused by the load itself, that is why a ``NoSave`` property is set on the enclosing section. 

Provide ``completionCallback`` argument to ``pro::DelegateToServer``
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Now, we need to trigger the assignment procedure ``pr_myLoadResultsCallback`` when a solved case file is available on the solver session. This is done by providing ``pr_myLoadResultsCallback`` as the `completionCallback` argument.

.. code-block:: aimms

   if pro::GetPROEndPoint() then
      if pro::DelegateToServer(  
         completionCallback :  'pr_myLoadResultsCallback',
         waitForCompletion : 0 )  
      then return 1;
      endif ;
   endif ;

When the solve session is completed, the procedure ``pr_myLoadResultsCallback`` is executed which will store the ``RequestID`` in ``sp_SavedRequestID`` and make the secondary action available in the UI by updating the value of ``p01_ResultsAvailable`` to 1. 


Create a procedure to load the data 
""""""""""""""""""""""""""""""""""""""""""""""""""""

Create a procedure ``pr_LoadResults`` with the below body:

.. code-block:: aimms

   Procedure pr_LoadResults {
      Body: {
         pro::session::LoadResultsCallBack(spSavedRequestID);
         sp_SavedRequestID := "";
         p01_ResultsAvailable := 0 ;
      }
   }

We are executing the predefined procedure ``pro::session::LoadResultsCallBack`` to load the case file on the data session, but with our own argument ``sp_SavedRequestID`` instead of the default argument. After the results are loaded, we also empty the ``sp_SavedRequestID`` and ``p01_ResultsAvailable`` to hide the load results button. This last emptying step is not necessary but it is a good practice to not show buttons that are not available anyway.

We want to control the visibility of ``BtnLoadResults`` because it makes sense for it to show up only when results are available to load. This appearance acts as a notification for the end user that results are available. The user interface when the results are available, but not yet downloaded looks as follows:

.. image::  images/BB05_WebUI_screen.PNG 

The AIMMS project that does just this, can be downloaded from: :download:`5. Flow Shop - Load Results <downloads/5. Flow Shop - Load Results.zip>`.

Summary
-------

By following the above steps, the end user can control when the case file resulting from an execution on the solver session is loaded onto the data session (or available to view by the end user).

Further Opportunities
------------------------

By following the above steps, the end user can control when the case file resulting from an execution on the solver session is loaded onto the data session (or available to view by the end user). Some examples of opportunities for further improvement are:

#. End users may want to keep track of the progress of the solution process, especially the gap is interesting. In :doc:`../35/35-web-ui-progress-window` we show how to do this.

#. The contents of intermediate solutions can be interesting for the data session at hand. In :doc:`../36/36-intermediate-solution` we show how to copy selected intermediate results from the solver session to the data session.

#. Once the solution is "good enough for now", the end user may want to abort the solver session.  In :doc:`../34/34-interrupt-server-session` we will show how to interrupt the solver session.



 
