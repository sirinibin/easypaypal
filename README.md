EASY PAYPAL
===========
Easy paypal Simplify integrating paypal express checkout with your Yii app.

DEMO LINK:http://yiidemos.gopagoda.com/index.php?r=site/paypalDemo


USAGE:


1.Download the package and extract into your extensions folder.

2.update import section in config.php
          'import'=>array(
                          'application.extensions.easyPaypal.*'
                         )
3.update params section in config.php

           'params'=>array(
                            'PAYPAL_API_USERNAME'=>'username here',
   
                            'PAYPAL_API_PASSWORD'=>'API password here',
   
                            'PAYPAL_API_SIGNATURE'=>'API signature here',
                            
                            'PAYPAL_MODE'=>'sandbox'   // sandbox/live  default=sandbox
                       
                          )
                          
4.
   
    a)Request paypal to process an express checkout payment
    
    <CODE>
    <?php
    class SiteController extends Controller
    {
     public function actionRequestPayment()
      {
	      $e=new ExpressCheckout;
	    
	      $products=array(
		  
			    '0'=>array(
				      'NAME'=>'p1',
				      'AMOUNT'=>'250.00',
				      'QTY'=>'2'
				      ),
			    '1'=>array(
				      'NAME'=>'p2',
				      'AMOUNT'=>'300.00',
				      'QTY'=>'2'
				      ),
			    '2'=>array(
				      'NAME'=>'p3',
				      'AMOUNT'=>'350.00',
				      'QTY'=>'2'
				      ),
	
			    );
                         /*Optional */
                   $shipping_address=array(
    
			'FIRST_NAME'=>'Sirin',
			'LAST_NAME'=>'K',
			'EMAIL'=>'sirinibin2006@gmail.com',
			'MOB'=>'0918606770278',
			'ADDRESS'=>'mannarkkad', 
			'SHIPTOSTREET'=>'mannarkkad',
			'SHIPTOCITY'=>'palakkad',
			'SHIPTOSTATE'=>'kerala',
			'SHIPTOCOUNTRYCODE'=>'IN',
			'SHIPTOZIP'=>'678761'
                                          ); 
    
		    $e->setShippingInfo($shipping_address); // set Shipping info Optional
		    
		    $e->setCurrencyCode("EUR");//set Currency (USD,HKD,GBP,EUR,JPY,CAD,AUD)
		    
		    $e->setProducts($products); /* Set array of products*/
		    
		    $e->setShippingCost(5.5);/* Set Shipping cost(Optional) */
		    
		    
		    $e->returnURL=Yii::app()->createAbsoluteUrl("site/PaypalReturn");
    
                    $e->cancelURL=Yii::app()->createAbsoluteUrl("site/PaypalCancel");
		    
		    $result=$e->requestPayment(); 
		    
		    /*
		      The response format from paypal for a payment request
	        Array
		(
		    [TOKEN] => EC-9G810112EL503081W
		    [TIMESTAMP] => 2013-12-12T10:29:35Z
		    [CORRELATIONID] => 67da94aea08c3
		    [ACK] => Success
		    [VERSION] => 65.1
		    [BUILD] => 8725992
		)
	            */
      
    
		if(strtoupper($result["ACK"])=="SUCCESS")
		  {
			/*redirect to the paypal gateway with the given token */
			header("location:".$e->PAYPAL_URL.$result["TOKEN"]);
		  }	
		      
		     
       
         }		    
		    
        public function actionPaypalReturn()
        {
         /*Look at next step b to see the definition*/
		 
        }
        public function actionPaypalCancel()
        {  
           /*The user flow  wil come here when a user cancels the payment */
           /*Do what you want*/   
        }
      }		    
      ?>
      </CODE>
       
       Note:After redirecting to paypal gateway there is 2 possible user flows ie ..
          1.he/she may complete the payment successfully.so after this the user will redirected to the actionPaypalReturn()
          2.he/she may cancel the payment & return to the store again.so after this the user press the cancel button he/she will  get redirected
              to the actionPaypalCancel()
       b)Define PaypalReturn Action
           
           <CODE>
           
          public function actionPaypalReturn()
          {
              /*
                here paypal will send you the following 2 parameters
              $_REQUEST[token] => EC-59C81234SW941750C
	      $_REQUEST[PayerID] => ZW3KSL2H557XC
	      
               */	
               /* You need to do 2 more final steps to complete the user payment. ie 
                  1.get the payment details from payment &
                  2.make doPayment api call to paypal to complete the payment 
               */
                 $e=new ExpressCheckout;
		 $paymentDetails=$e->getPaymentDetails($_REQUEST['token']); //1.get payment details by using the given token
		 
		 /*
		   Below you can see a sample format of a successfull payment details response from paypal
		      Array
			    (
				[TOKEN] => EC-73B51491U8895353R
				[CHECKOUTSTATUS] => PaymentActionNotInitiated
				[TIMESTAMP] => 2013-12-12T11:03:09Z
				[CORRELATIONID] => b812d7a367878
				[ACK] => Success
				[VERSION] => 65.1
				[BUILD] => 8725992
				[EMAIL] => sirini_1313434856_per@gmail.com
				[PAYERID] => ZW3KSL2H557XC
				[PAYERSTATUS] => verified
				[FIRSTNAME] => Test
				[LASTNAME] => User
				[COUNTRYCODE] => US
				[SHIPTONAME] => Test User
				[SHIPTOSTREET] => 1 Main St
				[SHIPTOCITY] => San Jose
				[SHIPTOSTATE] => CA
				[SHIPTOZIP] => 95131
				[SHIPTOCOUNTRYCODE] => US
				[SHIPTOCOUNTRYNAME] => United States
				[ADDRESSSTATUS] => Confirmed
				[CURRENCYCODE] => USD
				[AMT] => 1800.00
				[ITEMAMT] => 1800.00
				[SHIPPINGAMT] => 0.00
				[HANDLINGAMT] => 0.00
				[TAXAMT] => 0.00
				[INSURANCEAMT] => 0.00
				[SHIPDISCAMT] => 0.00
				[L_NAME0] => p1
				[L_NAME1] => p2
				[L_NAME2] => p3
				[L_QTY0] => 2
				[L_QTY1] => 2
				[L_QTY2] => 2
				[L_TAXAMT0] => 0.00
				[L_TAXAMT1] => 0.00
				[L_TAXAMT2] => 0.00
				[L_AMT0] => 250.00
				[L_AMT1] => 300.00
				[L_AMT2] => 350.00
				[L_ITEMWEIGHTVALUE0] =>    0.00000
				[L_ITEMWEIGHTVALUE1] =>    0.00000
				[L_ITEMWEIGHTVALUE2] =>    0.00000
				[L_ITEMLENGTHVALUE0] =>    0.00000
				[L_ITEMLENGTHVALUE1] =>    0.00000
				[L_ITEMLENGTHVALUE2] =>    0.00000
				[L_ITEMWIDTHVALUE0] =>    0.00000
				[L_ITEMWIDTHVALUE1] =>    0.00000
				[L_ITEMWIDTHVALUE2] =>    0.00000
				[L_ITEMHEIGHTVALUE0] =>    0.00000
				[L_ITEMHEIGHTVALUE1] =>    0.00000
				[L_ITEMHEIGHTVALUE2] =>    0.00000
				[PAYMENTREQUEST_0_CURRENCYCODE] => USD
				[PAYMENTREQUEST_0_AMT] => 1800.00
				[PAYMENTREQUEST_0_ITEMAMT] => 1800.00
				[PAYMENTREQUEST_0_SHIPPINGAMT] => 0.00
				[PAYMENTREQUEST_0_HANDLINGAMT] => 0.00
				[PAYMENTREQUEST_0_TAXAMT] => 0.00
				[PAYMENTREQUEST_0_INSURANCEAMT] => 0.00
				[PAYMENTREQUEST_0_SHIPDISCAMT] => 0.00
				[PAYMENTREQUEST_0_INSURANCEOPTIONOFFERED] => false
				[PAYMENTREQUEST_0_SHIPTONAME] => Test User
				[PAYMENTREQUEST_0_SHIPTOSTREET] => 1 Main St
				[PAYMENTREQUEST_0_SHIPTOCITY] => San Jose
				[PAYMENTREQUEST_0_SHIPTOSTATE] => CA
				[PAYMENTREQUEST_0_SHIPTOZIP] => 95131
				[PAYMENTREQUEST_0_SHIPTOCOUNTRYCODE] => US
				[PAYMENTREQUEST_0_SHIPTOCOUNTRYNAME] => United States
				[PAYMENTREQUEST_0_ADDRESSSTATUS] => Confirmed
				[L_PAYMENTREQUEST_0_NAME0] => p1
				[L_PAYMENTREQUEST_0_NAME1] => p2
				[L_PAYMENTREQUEST_0_NAME2] => p3
				[L_PAYMENTREQUEST_0_QTY0] => 2
				[L_PAYMENTREQUEST_0_QTY1] => 2
				[L_PAYMENTREQUEST_0_QTY2] => 2
				[L_PAYMENTREQUEST_0_TAXAMT0] => 0.00
				[L_PAYMENTREQUEST_0_TAXAMT1] => 0.00
				[L_PAYMENTREQUEST_0_TAXAMT2] => 0.00
				[L_PAYMENTREQUEST_0_AMT0] => 250.00
				[L_PAYMENTREQUEST_0_AMT1] => 300.00
				[L_PAYMENTREQUEST_0_AMT2] => 350.00
				[L_PAYMENTREQUEST_0_ITEMWEIGHTVALUE0] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMWEIGHTVALUE1] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMWEIGHTVALUE2] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMLENGTHVALUE0] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMLENGTHVALUE1] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMLENGTHVALUE2] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMWIDTHVALUE0] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMWIDTHVALUE1] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMWIDTHVALUE2] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMHEIGHTVALUE0] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMHEIGHTVALUE1] =>    0.00000
				[L_PAYMENTREQUEST_0_ITEMHEIGHTVALUE2] =>    0.00000
				[PAYMENTREQUESTINFO_0_ERRORCODE] => 0
			    )
		    */
		    if($paymentDetails['ACK']=="Success")
		    {
			$ack=$e->doPayment($paymentDetails);  //2.Do payment
			
			echo "<pre>";
			print_r($ack);
			echo "</pre>";
		    }
		    
		    /*
		      Below you can see a sample successfull response of a payment process from paypal
		     Array
			  (
			      [TOKEN] => EC-1AG000796M3683304
			      [SUCCESSPAGEREDIRECTREQUESTED] => false
			      [TIMESTAMP] => 2013-12-12T11:57:17Z
			      [CORRELATIONID] => 89a33a155e512
			      [ACK] => Success
			      [VERSION] => 65.1
			      [BUILD] => 8725992
			      [TRANSACTIONID] => 7S255873FM437633X
			      [TRANSACTIONTYPE] => expresscheckout
			      [PAYMENTTYPE] => instant
			      [ORDERTIME] => 2013-12-12T11:57:17Z
			      [AMT] => 1800.00
			      [FEEAMT] => 52.50
			      [TAXAMT] => 0.00
			      [CURRENCYCODE] => USD
			      [PAYMENTSTATUS] => Completed
			      [PENDINGREASON] => None
			      [REASONCODE] => None
			      [PROTECTIONELIGIBILITY] => Eligible
			      [INSURANCEOPTIONSELECTED] => false
			      [SHIPPINGOPTIONISDEFAULT] => false
			      [PAYMENTINFO_0_TRANSACTIONID] => 7S255873FM437633X
			      [PAYMENTINFO_0_TRANSACTIONTYPE] => expresscheckout
			      [PAYMENTINFO_0_PAYMENTTYPE] => instant
			      [PAYMENTINFO_0_ORDERTIME] => 2013-12-12T11:57:17Z
			      [PAYMENTINFO_0_AMT] => 1800.00
			      [PAYMENTINFO_0_FEEAMT] => 52.50
			      [PAYMENTINFO_0_TAXAMT] => 0.00
			      [PAYMENTINFO_0_CURRENCYCODE] => USD
			      [PAYMENTINFO_0_PAYMENTSTATUS] => Completed
			      [PAYMENTINFO_0_PENDINGREASON] => None
			      [PAYMENTINFO_0_REASONCODE] => None
			      [PAYMENTINFO_0_PROTECTIONELIGIBILITY] => Eligible
			      [PAYMENTINFO_0_PROTECTIONELIGIBILITYTYPE] => ItemNotReceivedEligible,UnauthorizedPaymentEligible
			      [PAYMENTINFO_0_ERRORCODE] => 0
			      [PAYMENTINFO_0_ACK] => Success
			  )
		    
		    */
		    
          }
           </CODE>
          
		     
