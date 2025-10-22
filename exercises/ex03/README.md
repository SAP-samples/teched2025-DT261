[Home - Cloud ERP Extensibility](../../../../#exercises)

# Exercise 3 - Extend the business object with an action 

- [Exercise 3 - Extend the business object with an action](#exercise-3---extend-the-business-object-with-an-action)
  - [🔴 Important Information](#-important-information)
    - [Exercise 3.1 Create an abstract entity](#exercise-31-create-an-abstract-entity)
    - [Exercise 3.2 Extend the behavior definition](#exercise-32-extend-the-behavior-definition)
    - [Exercise 3.3 - Implement the method for the action in the behvior implementation class](#exercise-33---implement-the-method-for-the-action-in-the-behvior-implementation-class)
    - [Exercise 3.4 - Extend the projection BDEF](#exercise-34---extend-the-projection-bdef)
    - [Exercise 3.5 - Add UI annotation](#exercise-35---add-ui-annotation)
  - [Summary](#summary)


## 🔴 Important Information

> **📌 Note**   
> We’ve got sessions running in several locations → please pick the one that’s right for you!

> **📌 Replace the last two digits to get your group number:**
> * ZDT261_**0##** → **01** → **40** → **SAP TechEd Berlin**  
> * ZDT261_**1##** → **00** → **99** → **ASUG Tech-Connect**

<br>

After having added a field we will add an action to the base RAP BO.

> ⚠ Please note:  
> Actions can only be added using extensibility to fields that have been added via extensibility themselves.
> This is why we have added this exercise after a field for adding feedback has been added to our Shop RAP business object.   

This action will be used to create/ update comments. 
We will thus make the new field that has been added to enter a comment read-only 
and add the required extensions to the 

- BDEF (add the action)
- Projection BDEF (add a use statement)
- Projection view (add UI annotations to make the button of the action visible)

### Exercise 3.1 Create an abstract entity

We want to provide the feedback for an order as a parameter of an action that will be added as an extenstion.
Parameters are passed to the action using abstract entites.

1. Right click on the folder **Data Definition** in your package `ZDT261_###_EXT` 
2. Choose **New data defintion** from the context menu  
   - Package: `ZDT261_###_EXT`  
   - Name: `ZX_ZDT261A_Feedback_###`   
   - Description: `Pass feedback as a parameter`  
   and press **Next**
3. Expand the folder **Abstract Entity(creation)**   
4. Select the template **Define abstract entity with parameters** and press **Finish**  

   <img src="images//image01.png" width="70%">

5. Enter the following coding

   <pre lang="ABAP">
   @EndUserText.label: 'Pass feedback as a parameter'
   define abstract entity ZX_ZDT261A_Feedback_###  
   {
       feedback : abap.char(100);  
   }
   </pre>    

6. Activate your changes   

### Exercise 3.2 Extend the behavior definition 

Now we can define the action with the parameter that has just been created.  

1. Open the behavior extension `ZDT261R_EXT_SHOPTP_###` 
2. Add the following code snippet in your behavior extension `ZDT261R_EXT_SHOPTP_###`  

   <pre lang="ABAP">
   field(readonly) zzfeedbackzaa;
   action(authorization : global, features : instance ) ZZ_ProvideFeedback  parameter ZX_ZDT261A_Feedback_### result[1] $self;
   </pre> 
  
   so that your code now reads
  
   <pre lang="ABAP">  
   extension using interface ZDT261I_shoptp_###
   implementation in class ZBP_DT261R_ext_shoptp_### unique;
   
   extend behavior for Shop
   {
   
     validation zz_validateDeliverydate on save { create; field DeliveryDate; }
   
     extend draft determine action Prepare
     {
       validation zz_validateDeliveryDate;
     }
     determination ZZ_setOverallStatus on modify { field OrderedItem; }
   
     side effects { field OrderedItem affects field OrderItemPrice , field CurrencyCode ; }
   
     field(readonly) zzfeedbackzaa;
     action(authorization : global, features : instance ) ZZ_ProvideFeedback  parameter ZX_ZDT261A_Feedback_### result[1] $self;
    }
   </pre>

3. Activate your changes.     

3. Use the code assist (Press Ctrl+1) on the **Shop** entity and let the framework generate the missing artefacts.

   <img src="images//image02.png" width="70%">

### Exercise 3.3 - Implement the method for the action in the behvior implementation class

1. Open the local class of the behavior implementation class.

   <img src="images//image03.png" width="70%">

2. Implement the method zz_providefeedback and activate your changes

   <pre lang="ABAP">
   METHOD ZZ_ProvideFeedback.
       MODIFY ENTITIES OF ZDT261I_ShopTP_### IN LOCAL MODE
     ENTITY Shop
   
     UPDATE FIELDS ( zzfeedbackzaa )
     WITH VALUE #( FOR key IN keys ( %tky              = key-%tky
                                      zzfeedbackzaa  = key-%param-feedback  ) ).
   
       "Read the changed data for action result
       READ ENTITIES OF ZDT261I_ShopTP_### IN LOCAL MODE
         ENTITY Shop
           ALL FIELDS WITH
           CORRESPONDING #( keys )
         RESULT DATA(result_read).
       "return result entities
       result = VALUE #( FOR order_2 IN result_read ( %tky   = order_2-%tky
                                                      %param = order_2 ) ).
   
     ENDMETHOD.
   </pre>

### Exercise 3.4 - Extend the projection BDEF

In order to make the action visible an appropriate `use action` statement has to added to the projection level of the BDEF of the extensible root BO.

1. Right click on the projection behavior definition `ZDT261C_ShopTP_###` within the package `ZDT261_###` and select **New Behavior Extension**  
   
   <img src="images//image04.png" width="70%">

2. Fill in the data for the projection behavior extension

   - Package: `ZDT261_###_EXT` 
   - Name: `ZDT261C_Ext_SHOPTP_###`
   - Description: Behavior extension for projection bdef   

   <img src="images//image05.png" width="70%">

3. Select a transport and press **Finish**  

4. Enter a use statement for your action so that the code looks like 

   <pre lang="ABAP">
   extension for projection;
   
   extend behavior for Shop
   {
   use action ZZ_ProvideFeedback;
   }
   </pre>

### Exercise 3.5 - Add UI annotation

UI annotations have to be added to make the action button visible. This is done in the C-View Extension `ZX_ZDT261C_EXT_SHOPTP_###`.   

For this you have to add the following code snippet to the `UI.identification` and `UI.lineitem` annotations.

<pre>{ type: #FOR_ACTION, dataAction: 'ZZ_ProvideFeedback', label: 'Update feedback' } </pre>

<pre lang="ABAP">
  extend view entity ZDT261C_ShopTP_### with
  {
  @EndUserText.label: 'Feedback'
  @UI.dataFieldDefault: [{hidden: false}]
  @UI.identification: [{hidden: false},
                       {type: #FOR_ACTION, dataAction: 'ZZ_ProvideFeedback', label: 'Update feedback' } ]
  @UI.lineItem: [{hidden: false},
                 {type: #FOR_ACTION, dataAction: 'ZZ_ProvideFeedback', label: 'Update feedback' } ]
  Shop.ZZFEEDBACKZAA as ZZFEEDBACKZAA

  }
</pre>

You should now see an action button that allows you to enter feedback.

<img src="images//image06.png" width="70%">

## Summary

You've now finished.

[^Top of page](#)   
[Home - DT261](../../../../#exercises)   

