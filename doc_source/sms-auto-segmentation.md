# Auto\-Segmentation Tool<a name="sms-auto-segmentation"></a>

Image segmentation is the process of dividing an image into multiple segments, or sets of labeled pixels\. In Amazon SageMaker Ground Truth, the process of identifying all pixels that fall under a given label involves applying a colored filler, or "mask", over those pixels\. Some labeling job tasks contain images with a large numbers of objects that need to be segmented\. To help workers label these objects in less time and with greater accuracy, Ground Truth provides an auto\-segmentation tool for segmentation tasks assigned to private and vendor workforces\. This tool uses a machine learning model to automatically segment individual objects in the image with minimal worker input\. Workers can refine the mask generated by the auto\-segmentation tool using other tools found in the worker console\. This helps workers complete image segmentation tasks faster and more accurately, resulting in lower cost and higher label quality\. 

**Note**  
The auto\-segmentation tool is available for segmentation tasks that are sent to a private workforce or vendor workforce\. It isn't available for tasks sent to the public workforce \(Amazon Mechanical Turk\)\. 

## Tool Preview<a name="sms-auto-segment-tool-preview"></a>

When workers are assigned a labeling job that provides the auto\-segmentation tool, they are provided with detailed instructions on how to use the tool\. For example, a worker might see the following in the worker console: 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/sagemaker/latest/dg/images/semantic_segmentation_sample.gif)

Workers can use **View full instructions** to learn how to use the tool\. Workers will need to place a point on four extreme\-points \( top\-most, bottom\-most, left\-most, and right\-most points \) of the object of interest, and the tool will automatically generate a mask for the object\. Workers can further\-refine the mask using the other tools provided, or by using the auto\-segment tool on smaller portions of the object that were missed\. 

## Tool Availability<a name="sms-auto-segment-tool-availability"></a>

The auto\-segmentation tool automatically appears in your workers' consoles if you create a semantic segmentation labeling job using the Amazon SageMaker console\. While creating a semantic segmentation job in the Amazon SageMaker console, you will be able to preview the tool while creating worker instructions\. To learn how to create a semantic segmentation labeling job in the Amazon SageMaker console, see [Getting started](sms-getting-started.md)\. 

If you are creating a custom instance segmentation labeling job in the Amazon SageMaker console or creating an instance\- or semantic\-segmentation labeling job using the Ground Truth API, you need to create a custom task template to design your worker console and instructions\. To include the auto\-segmentation tool in your worker console, ensure that the following conditions are met in your custom task template:
+ For semantic segmentation labeling jobs created using the API, the `<crowd-semantic-segmentation>` is present in the task template\. For custom instance segmentation labeling jobs, the `<crowd-instance-segmentation>` tag is present in the task template\.
+ The task is assigned to a private workforce or vendor workforce\. 
+ The images to be labeled are Amazon Simple Storage Service Amazon S3\) objects that have been pre\-signed for the Worker so that they can access it\. This is true if the task template includes the `grant_read_access` filter\. For information about the `grant_read_access` filter, see [Adding automation with Liquid](sms-custom-templates-step2.md#sms-custom-templates-step2-automate)\.

The following is an example of a custom task template for a custom instance segmentation labeling job, which includes the `<crowd-instance-segmentation/>` tag and the `grant_read_access` Liquid filter\.

```
<script src="https://assets.crowd.aws/crowd-html-elements.js"></script>
<crowd-form>
  <crowd-instance-segmentation
    name="crowd-instance-segmentation"
    src="{{ task.input.taskObject | grant_read_access }}"
    labels="['Car','Road']"
   <full-instructions header="Segmentation instructions">
      Segment each instance of each class of objects in the image. 
    </full-instructions>

    <short-instructions>
      <p>Segment each instance of each class of objects in the image.</p>

      <h3 style="color: green">GOOD EXAMPLES</h3>
      <img src="path/to/image.jpg" style="width: 100%">
      <p>Good because A, B, C.</p>

      <h3 style="color: red">BAD EXAMPLES</h3>
      <img src="path/to/image.jpg" style="width: 100%">
      <p>Bad because X, Y, Z.</p>
    </short-instructions>
  </crowd-instance-segmentation>
</crowd-form>
```