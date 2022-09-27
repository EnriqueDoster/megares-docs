---
search:
  exclude: true
---

# Run AMR++ Workflow

!!! warning
    This documentation is for AMR++ version 1.1. Click [here](./../latest/introduction.md) for the latest docs.

Now that we've got our data uploaded to our Galaxy server, it's time to run the workflow.


??? example "Step 1: Navigate to the Workflow Tab"
    ### Navigate to the Workflow Tab
    
    See the workflow tab circled in red below on the Galaxy homepage:

    ![](../images/workflow_tab.png)

    Then click on the workflow you imported earlier and select **Run**
    
    ![](../images/workflow_run.png){ width="350" }

    You will be redirected to a page (see below) displaying each component of the workflow.
    
??? example "Step 2: Select the Datasets"
    ### Select the Datasets
    
    First, select the forward read pair. Then, select the second read pair. Next, select the host genome and resistance database. If you wish to customize any of the workflow components, please see the [Workflow Parameters](../documentation/usermanual.md) section before proceeding.

    Once the datasets have been selected, simply click the **Run workflow** button (circled in red).

    ![](../images/workflow_inputs.png)


??? example "Step 3: View the Outputs"
    ### View the Outputs
    
    When the workflow completes, you can find all intermediate and final outputs in the Galaxy history pane. The outputs you will be most interested in come from the SNPFinder and CoverageSampler modules. To view the outputs, simply click on the view data icon (circled in red).

    Once the datasets have been selected, simply click the Run workflow button (circled in red).

    ![](../images/workflow_outputs.png){ width="300" }
    
    These outputs can then be downloaded to your local machine by clicking on the download icon (circled in red). For more information on interpreting the output from each of these tools, please see the [CoverageSampler](../documentation/pipeline.md) and [SNPFinder](../documentation/pipeline.md) output descriptions.

    ![](../images/download_workflow_outputs.png){ width="300" }



