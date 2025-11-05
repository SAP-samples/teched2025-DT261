[Home - Cloud ERP Extensibility](../../../../#exercises)  

# Exercise 2 - Extend the CDS data model of the extensible RAP base business object  

- [Exercise 2 - Extend the CDS data model of the extensible RAP base business object](#exercise-2---extend-the-cds-data-model-of-the-extensible-rap-base-business-object)
  - [🔴 Important Information](#-important-information)
  - [Introduction](#introduction)
  - [Exercise 2.1 - Extend the CDS data model using the field extension wizard](#exercise-21---extend-the-cds-data-model-using-the-field-extension-wizard)
  - [Exercise 2.2 - Extend the data structure with the extension field wizard](#exercise-22---extend-the-data-structure-with-the-extension-field-wizard)
  - [Exercise 2.3 - Check the data structure extension](#exercise-23---check-the-data-structure-extension)
  - [Exercise 2.4 - Test the field extensibility](#exercise-24---test-the-field-extensibility)
  - [Summary](#summary)

## 🔴 Important Information

> **📌 Note**   
> We’ve got sessions running in several locations → please pick the one that’s right for you!

> **📌 Replace the last two digits to get your group number:**
> * ZDT261_**0##** → **001** → **040** → **SAP TechEd Berlin**  (e.g ``ZDT261_019``)
> * ZDT261_**0##**_EXT → **001** → **040** → **SAP TechEd Berlin**  (e.g ``ZDT261_019_EXT``)
> * ZDT261_**1##** → **100** → **199** → **ASUG Tech-Connect**  (e.g ``ZDT261_123``)
> * ZDT261_**1##**_EXT → **100** → **199** → **ASUG Tech-Connect**  (e.g ``ZDT261_123_EXT``)

<br>Don't forget to replace all occurrences of the placeholder **`###`** with your assigned location and group number in the exercise steps below.
<br>

## Introduction  

In the previous exercise we have extended the _behavior_ of the base RAP BO by adding a determination, a validation and a side effect. 

We will now adapt the _CDS data model_, that means we will add new fields to the _Shop_ entity. 

Compared to the activities for adding additional behavior this is a larger amount of work since the complete stack of CDS views has to be extended rather than only extending the behavior definition (BDEF).

<img src="images//image00.png" width="100%">


> In order to extend the data model of a RAP BO the base RAP BO must be enabled to support extensibility as described in the Online Help.  
> [RAP Extensibility-Enablement](https://help.sap.com/docs/abap-cloud/abap-rap/rap-extensibility-enablement)
> For your convenience the generated RAP BO `ZDT261R_ShopTP_###` that has been prepared for this workshop, supports the extensibility of the data model.  

As shown in the schematic view above we have to extend the following objects of our extensible base BO.
  
Order of extension | Repository object name | Name           | Comment  
-------- | ---------------------- | ------------------------ | -----------------------------------------------------   
1        | `ZDT261SSHOP_###`     | Extension include        | extends active and draft table with additional fields    
2        | `ZDT261E_Shop_###`    | Extension include view   | selects from (extended) active table   
3        | `ZDT261R_ShopTP_###`  | Restricted base view     | selects from (2)   
4        | `ZDT261C_ShopTP_###`  | CDS projection view      | selects from (3)   
5        | `ZDT261I_ShopTP_###`  | Interface view           | selects from (3)   
6        | `ZDT261R_Shop_D_###`  | Draft query view         | selects from (extended) draft table   


## Exercise 2.1 - Extend the CDS data model using the field extension wizard 

The **extension field wizard** can be started from the consumption (c-view) or interface projection view (i-view) or from the underlying restricted base view (r-view). Depending on which view is used as the starting point certain options are pre-set in the wizard. 

<img src="images//image01.png" width="70%">

When for example using the C-view as the entry point the wizard will suggest to extend the C-view by default.

## Exercise 2.2 - Extend the data structure with the extension field wizard

1. Right click on the consumption projection view `ZDT261C_ShopTP_###` (the one with the statement `provider contract TRANSACTIONAL_QUERY` ) and choose **Generate ABAP Repository Objects ..**.

   <img src="images//image02.png" width="70%">


3. In the **Select Generator** screen choose **ABAP Cloud: Extensibility** and then **Extension Fields**. Press **Next**.

   <img src="images//image03.png" width="70%">
  
4. In the **Enter Package** screen enter the name of the package `ZDT261_###_EXT` and press **Next**.

   <img src="images//image04.png" width="70%">

5. In the **Configure Generator** screen press **Add** (1). Then, in the **Add Field** dialogue enter the following values (2) to add a field to enter feedback:   

   - Name `ZZFEEDBACKZAA`  
   - Label: `Feedback`  
   - Data Elemeent: `TEXT100`  
   - Activate the check box **Generate UI annotations**   (3)
  
   and press **Add** (4)

   <img src="images//image05.png" width="70%">
 
6. Expand the section **Business Service Layer Extensions** and verify that the option **Extend Object ?** is checked and press **Next**.   

   <img src="images//image06.png" width="70%">

7. The wizard now shows the list of ABAP artifacts that are going to be created / modified.

   <img src="images//image07.png" width="70%"/>


   You can check out the code of the artifacts that are going to be generated
   
     ### Extension include structure 
 
     <img src="images//image08.png" width="70%">

     ### Extension include view
 
     <img src="images//image09.png" width="70%">

     ### Extension for draft query view
    
     <img src="images//image10.png" width="70%">

     ### Extension for base restricted view 
    
     <img src="images//image11.png" width="70%">

     ### Extension for tranactional interface view
    
     <img src="images//image12.png" width="70%">

     ### Extension for tranactional consumption view 
    
     <img src="images//image13.png" width="70%">


8. Press  **Next** to continue

9. Select a transport request



## Exercise 2.3 - Check the data structure extension

The wizard has generated the following repository objects:   

  
No.      | Extension repository object | Extended repository object       | Comment  
-------- | ------------------------- | ------------------------ | -----------------------------------------------------   
1        | `ZAPD_ZDT261SSHOP_###`   | `ZDT261SSHOP_###`       | extends extension include structure 
2        | `ZX_ZDT261E_SHOP_###`    | `ZDT261E_Shop_###`      | extends extension include view   
3        | `ZX_ZDT261R_SHOPTP_###`  | `ZDT261R_ShopTP_###`    | extends restricted transactional base view   
4        | `ZX_ZDT261C_SHOPTP_###`  | `ZDT261C_ShopTP_###`    | extends transactional projection view
5        | `ZX_ZDT261I_SHOPTP_###`  | `ZDT261I_ShopTP_###`    | extends transactional interface view 
6        | `ZX_ZDT261R_SHOP_D_###`  | `ZDT261R_Shop_D_###`    | extends draft query view

The extension project should now look like as follows:   

   <img src="images//image14.png" width="70%">

## Exercise 2.4 - Test the field extensibility

After having extended all the objects in the hierarchy of your base RAP business object you can now leverage the additional field in the UI.


1. Open the Service Binding of the extensible RAP business object and double-click on the entity **Shop**.   
  
2. Press the **Go** button.  
  
   You should see a new column called "Feedback".   
  
   <img src="images//image15.png" width="70%">

3. Select one entry and use the **Edit** button
  
   <img src="images//image16.png" width="70%">  

  > Hint:   
  > ⚠️ When no field is visible you should check the extension of your projection view `ZX_ZDT261C_SHOPTP_###` and check the UI annotations.   


## Summary

You have now added a single editable field to your extensible RAP business object.   
In the next exercise we will show how to add an action. 

Continue to - [Exercise 3 - Add an action ](../ex03/README.md)
