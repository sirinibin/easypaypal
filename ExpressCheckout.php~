<?php
/**
 * ExpressCheckout.php
 *
 * @author: sirin k <sirinibin2006@gmail.com>
 * Date: 12/dec/2013
 * Time: 8.21pm
 */
  
 class ExpressCheckout extends CApplicationComponent
 {
   
   public $API_USERNAME="sirini_1313473286_biz_api1.gmail.com";
   
   public $API_PASSWORD="1313473344";
   
   public $API_SIGNATURE="An5ns1Kso7MWUdW4ErQKJJJ4qi4-AU8IA9mUzvO17e5sDQiA1iHUfL2j";
   
  
   public $returnURL;
   
   public $cancelURL;
 
   public $https_port="6443";



 /**
# Endpoint: this is the server URL which you have to connect for submitting your API request.
*/
   public $API_ENDPOINT="https://api-3t.sandbox.paypal.com/nvp";

/* Define the PayPal URL. This is the URL that the buyer is
   first sent to to authorize payment with their paypal account
   change the URL depending if you are testing on the sandbox
   or going to the live PayPal site
   For the sandbox, the URL is
   https://www.sandbox.paypal.com/webscr&cmd=_express-checkout&token=
   For the live site, the URL is
   https://www.paypal.com/webscr&cmd=_express-checkout&token=
   */

   public $PAYPAL_URL="https://www.sandbox.paypal.com/webscr&cmd=_express-checkout&token=";
  /**
# Version: this is the API version in the request.
# It is a mandatory parameter for each API request.
# The only supported value at this time is 2.3
*/
   public $VERSION="65.1";

// Ack related constants
   public $ACK_SUCCESS="SUCCESS";
   public $ACK_SUCCESS_WITH_WARNING="SUCCESSWITHWARNING";

/*
 # Third party Email address that you granted permission to make api call.
 */
   public $SUBJECT="";
/*for permission APIs ->token, signature, timestamp  are needed */
   public $AUTH_TOKEN='';
   public $AUTH_SIGNATURE='';
   public $AUTH_TIMESTAMP='';
 
    //$AUTHMODE = "3TOKEN"; //Merchant's API 3-TOKEN Credential is required to make API Call.
    //$AUTHMODE = "FIRSTPARTY"; //Only merchant Email is required to make EC Calls.
    //$AUTHMODE = "THIRDPARTY";Partner's API Credential and Merchant Email as Subject are required.
		 
   public $AUTHMODE='';

      /**
    USE_PROXY: Set this variable to TRUE to route all the API requests through proxy.
    like define('USE_PROXY',TRUE);
    */
   public $USE_PROXY=FALSE;
    /**
    PROXY_HOST: Set the host name or the IP address of proxy server.
    PROXY_PORT: Set proxy port.

    PROXY_HOST and PROXY_PORT will be read only if USE_PROXY is set to TRUE
    */
   public $PROXY_HOST="127.0.0.1";
   public $PROXY_PORT="808";
   
   
   
   	public $paymentType;
   	
   	public $FIRST_NAME;
        public $LAST_NAME;
        public $EMAIL;
        public $MOB;
        public $ADDRESS; 

        public $SHIPTOSTREET;
        public $SHIPTOCITY;
        public $SHIPTOSTATE;
        public $SHIPTOCOUNTRYCODE;
        public $SHIPTOZIP;
        
        public $shipping_info=array();
        
        public $products=array();



	public $currencyCode;/*Currency CODE*/
	
	public $product_string;
	
	public $shipping_cost=0.0;
	
	public $total_amount=0.0;
	
	
	 function __construct() {
       
               
               if(isset(Yii::app()->params['PAYPAL_API_USERNAME']))
               $this->API_USERNAME=Yii::app()->params['PAYPAL_API_USERNAME'];
   
               if(isset(Yii::app()->params['PAYPAL_API_PASSWORD']))
	       $this->API_PASSWORD=Yii::app()->params['PAYPAL_API_PASSWORD'];
		
	        if(isset(Yii::app()->params['PAYPAL_API_SIGNATURE']))	
	       $this->API_SIGNATURE=Yii::app()->params['PAYPAL_API_SIGNATURE'];
	       
	        if(Yii::app()->params['PAYPAL_MODE']=="sandbox")
	         {
	          $this->PAYPAL_URL="https://www.sandbox.paypal.com/webscr&cmd=_express-checkout&token=";
	          $this->API_ENDPOINT="https://api-3t.sandbox.paypal.com/nvp";
	         }
	        else 
	         {
	          $this->PAYPAL_URL="https://www.paypal.com/webscr&cmd=_express-checkout&token=";
	          $this->API_ENDPOINT="https://api-3t.paypal.com/nvp";
	          
	         }
   
        }
	
	/*Products*/
	public function setProducts($products)
        {
         $this->products=$products;
         
          $i=0;
          $p_string="";
          $total=0;
          foreach($products as $p)
          {
            $item_amount=$p['AMOUNT']*$p['QTY'];
            $total+=$item_amount;
             if($i>0)
               {
               // echo "ok";
                //exit;
                $p_string.="&";
               }
            $p_string.="L_NAME".$i."=".$p['NAME']."&L_AMT".$i."=".$p['AMOUNT']."&L_QTY".$i."=".$p['QTY'];
            $i++;
          }
           $this->total_amount=$total;
          
           $this->product_string=$p_string;
          return($p_string);
        }
        public function getProducts()
        {
         return($this->products);
        } 
        
        /*Shipping cost*/
	public function setShippingCost($shipping_cost)
	{
	 $this->shipping_cost=$shipping_cost;
	}
	public function getShippingCost()
	{
	 return($this->shipping_cost);
	}
	/*Currency*/
	public function setCurrencyCode($currency_code)
	{
	 $this->currencyCode=$currency_code;
	}
	public function getCurrencyCode()
	{
	 return($this->currencyCode);
	}
	public function requestPayment()
	{ 
	      $nvp=$this->getNVPString();
	      $resArray=$this->hash_call("SetExpressCheckout",$nvp);  
	      return($resArray);
	}
	public function getPaymentDetails($token)
	{
	  $nvpstr="&TOKEN=".$token;
	  $nvpHeader=$this->nvpHeader();    
	  $nvpstr = $nvpHeader.$nvpstr;
	    /* Make the API call and store the results in an array.  If the
		call was a success, show the authorization details, and provide
		an action to complete the payment.  If failed, show the error
		*/
				
	    $resArray=$this->hash_call("GetExpressCheckoutDetails",$nvpstr);
	    
	    return($resArray);
	    
	}
	public function doPayment($paymentDetails)
	{
	    $nvpstr='&TOKEN='.urlencode($paymentDetails['TOKEN']).'&PAYERID='.urlencode($paymentDetails['PAYERID']).'&PAYMENTACTION=Sale&AMT='.urlencode($paymentDetails['AMT']).'&CURRENCYCODE='. urlencode($paymentDetails['CURRENCYCODE']).'&IPADDRESS='.urlencode($_SERVER['SERVER_NAME']);
		
	    $resArray=$this->hash_call("DoExpressCheckoutPayment",$nvpstr);
	    
	    return($resArray);
	}
	
	public function setShippingInfo($attributes)
        {
         $this->shipping_info=$attributes;
         
         $this->FIRST_NAME=isset($attributes['FIRST_NAME'])?$attributes['FIRST_NAME']:"";
         
         $this->LAST_NAME=isset($attributes['LAST_NAME'])?$attributes['LAST_NAME']:"";
         
         $this->EMAIL=isset($attributes['EMAIL'])?$attributes['EMAIL']:"";
         
         $this->MOB=isset($attributes['MOB'])?$attributes['MOB']:"";
                
         $this->ADDRESS=isset($attributes['ADDRESS'])?$attributes['ADDRESS']:"";
         
         $this->SHIPTOSTREET=isset($attributes['SHIPTOSTREET'])?$attributes['SHIPTOSTREET']:"";
                   
         $this->SHIPTOCITY=isset($attributes['SHIPTOCITY'])?$attributes['SHIPTOCITY']:"";
                     
         $this->SHIPTOCOUNTRYCODE=isset($attributes['SHIPTOCOUNTRYCODE'])?$attributes['SHIPTOCOUNTRYCODE']:"";
         
         $this->SHIPTOZIP=isset($attributes['SHIPTOZIP'])?$attributes['SHIPTOZIP']:"";
        
        }
        public function getShippingInfo()
        {
         return($this->shipping_info);
	}
        
       public function getNVPString()
       {
      $nvpHeader=$this->nvpHeader(); 
    
    $nvpstr="";
     $shiptoAddress = "&SHIPTONAME=".$this->FIRST_NAME." ".$this->LAST_NAME."&SHIPTOSTREET=".$this->SHIPTOSTREET."&SHIPTOCITY=".$this->SHIPTOCITY."&SHIPTOSTATE=".$this->SHIPTOSTATE."&SHIPTOCOUNTRYCODE=".$this->SHIPTOCOUNTRYCODE."&SHIPTOZIP=".$this->SHIPTOZIP;  
     
     $nvpstr = "&ADDRESSOVERRIDE=1".$shiptoAddress."&".$this->getProductString()."&ReturnUrl=".$this->returnURL."&CANCELURL=".$this->cancelURL."&CURRENCYCODE=".$this->currencyCode; 

       if($this->shipping_cost>0)
        {
         $nvpstr.="&SHIPPINGAMT=".$this->shipping_cost;
        }
        $nvpstr.="&AMT=".($this->total_amount+$this->shipping_cost)."&ITEMAMT=".$this->total_amount;
  $nvpstr = $nvpHeader.$nvpstr; 
    
     return($nvpstr);
        }
        public function getProductString()
        {
         return($this->product_string);
        
        }
        

        
 
       
       
        
   //this function is used to get the appropriate NVP HEADER (i.e.API CREDENTIALS)
	  
	  public function nvpHeader()
	    {
	    /*
	    global $API_Endpoint,$version,$API_UserName,$API_Password,$API_Signature,$nvp_Header, $subject, $AUTH_token,$AUTH_signature,$AUTH_timestamp;
	    */
	    //global  $AUTH_token,$AUTH_signature,$AUTH_timestamp;
    
	      $nvpHeaderStr = "";

	      if(!empty($this->AUTHMODE))
	      {
	      //$AuthMode = "3TOKEN"; //Merchant's API 3-TOKEN Credential is required to make API Call.
	      //$AuthMode = "FIRSTPARTY"; //Only merchant Email is required to make EC Calls.
	      //$AuthMode = "THIRDPARTY";Partner's API Credential and Merchant Email as Subject are required.
	      $AuthMode =$this->AUTHMODE; 
	      } 
	    else 
	      {
	    
		  if((!empty($this->API_USERNAME)) && (!empty($this->API_PASSWORD)) && (!empty($this->API_SIGNATURE)) && (!empty($this->SUBJECT)))
		  {
		    $AuthMode = "THIRDPARTY";
		  }
	    
		  else if((!empty($this->API_USERNAME)) && (!empty($this->API_PASSWORD)) && (!empty($this->API_SIGNATURE)))
		  {
		    $AuthMode = "3TOKEN";
		  }
	    
		  else if (!empty($this->AUTH_TOKEN) && !empty($this->AUTH_SIGNATURE) && !empty($this->AUTH_TIMESTAMP)) 
		  {
		    $AuthMode = "PERMISSION";
		  }
		else if(!empty($this->SUBJECT))
		{
		    $AuthMode = "FIRSTPARTY";
		}
	      }
	switch($AuthMode)
	      {
	    
	    case "3TOKEN" : 
			    $nvpHeaderStr = "&PWD=".urlencode($this->API_PASSWORD)."&USER=".urlencode($this->API_USERNAME)."&SIGNATURE=".urlencode($this->API_SIGNATURE);
			    break;
	    case "FIRSTPARTY" :
			    $nvpHeaderStr = "&SUBJECT=".urlencode($this->SUBJECT);
			    break;
	    case "THIRDPARTY" :
			    $nvpHeaderStr = "&PWD=".urlencode($this->API_PASSWORD)."&USER=".urlencode($this->API_USERNAME)."&SIGNATURE=".urlencode($this->API_SIGNATURE)."&SUBJECT=".urlencode($this->SUBJECT);
			    break;		
	    case "PERMISSION" :
			$nvpHeaderStr = formAutorization($this->AUTH_TOKEN,$this->AUTH_SIGNATURE,$this->AUTH_TIMESTAMP);
			break;
	  }
	    return $nvpHeaderStr;
    }     

    /**
      * hash_call: Function to perform the API call to PayPal using API signature
      * @methodName is name of API  method.
      * @nvpStr is nvp string.
      * returns an associtive array containing the response from the server.
    */


    public function hash_call($methodName,$nvpStr)
    {
      $session=new CHttpSession;
	
	    //declaring of global variables
	    //global $API_Endpoint,$version,$API_UserName,$API_Password,$API_Signature,$nvp_Header, $subject, $AUTH_token,$AUTH_signature,$AUTH_timestamp;
	    // form header string
	    $nvpheader=$this->nvpHeader();
	    //setting the curl parameters.
	    $ch = curl_init();
	    curl_setopt($ch, CURLOPT_URL,$this->API_ENDPOINT);
	    curl_setopt($ch, CURLOPT_VERBOSE, 1);

	    //turning off the server and peer verification(TrustManager Concept).
	    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
	    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);

	    curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);
	    curl_setopt($ch, CURLOPT_POST, 1);
	    
	    //in case of permission APIs send headers as HTTPheders
	    if(!empty($this->AUTH_TOKEN) && !empty($this->AUTH_SIGNATURE) && !empty($this->AUTH_TIMESTAMP))
	    {
		    $headers_array[] = "X-PP-AUTHORIZATION: ".$nvpheader;
      
	curl_setopt($ch, CURLOPT_HTTPHEADER, $headers_array);
	curl_setopt($ch, CURLOPT_HEADER, false);
	    }
	    else 
	    {
		    $nvpStr=$nvpheader.$nvpStr;
	    }
	//if USE_PROXY constant set to TRUE in Constants.php, then only proxy will be enabled.
      //Set proxy name to PROXY_HOST and port number to PROXY_PORT in constants.php 
	    if($this->USE_PROXY)
	    curl_setopt ($ch, CURLOPT_PROXY, PROXY_HOST.":".PROXY_PORT); 

	    //check if version is included in $nvpStr else include the version.
	    if(strlen(str_replace('VERSION=', '', strtoupper($nvpStr))) == strlen($nvpStr)) 
	    {
		    $nvpStr = "&VERSION=" . urlencode($this->VERSION) . $nvpStr;	
	    }
	    
	    $nvpreq="METHOD=".urlencode($methodName).$nvpStr;
	    
	    //setting the nvpreq as POST FIELD to curl
	    curl_setopt($ch,CURLOPT_POSTFIELDS,$nvpreq);

	    //getting response from server
	    $response = curl_exec($ch);

	    //convrting NVPResponse to an Associative Array
	    $nvpResArray=$this->deformatNVP($response);
	    $nvpReqArray=$this->deformatNVP($nvpreq);

	    $session->open();
	    $session['nvpReqArray']=$nvpReqArray;
	    $session->close(); 

	    if (curl_errno($ch)) {
		    // moving to display page to display curl errors
		      $session->open();
		    
		      $session['curl_error_no']=curl_errno($ch) ;
		      $session['curl_error_msg']=curl_error($ch);
		      //$location = "APIError.php";
		      $session->close(); 
		    // $this->redirect(array('/paypal/APIError','msg'=>'Error in curl'));   
		    // header("Location: $location");
	    } else {
		    //closing the curl
			    curl_close($ch);
	      }

    return $nvpResArray;
    }
    /** This function will take NVPString and convert it to an Associative Array and it will decode the response.
      * It is usefull to search for a particular key and displaying arrays.
      * @nvpstr is NVPString.
      * @nvpArray is Associative Array.
      */

    public function deformatNVP($nvpstr)
    {

	    $intial=0;
	    $nvpArray = array();


	    while(strlen($nvpstr)){
		    //postion of Key
		    $keypos= strpos($nvpstr,'=');
		    //position of value
		    $valuepos = strpos($nvpstr,'&') ? strpos($nvpstr,'&'): strlen($nvpstr);

		    /*getting the Key and Value values and storing in a Associative Array*/
		    $keyval=substr($nvpstr,$intial,$keypos);
		    $valval=substr($nvpstr,$keypos+1,$valuepos-$keypos-1);
		    //decoding the respose
		    $nvpArray[urldecode($keyval)] =urldecode( $valval);
		    $nvpstr=substr($nvpstr,$valuepos+1,strlen($nvpstr));
	}
	    return $nvpArray;
    }
    public function formAutorization($auth_token,$auth_signature,$auth_timestamp)
    {
	    $authString="token=".$auth_token.",signature=".$auth_signature.",timestamp=".$auth_timestamp ;
	    return $authString;
    }
 
 }

/*
NVP documentation:
 https://developer.paypal.com/webapps/developer/docs/classic/api/merchant/SetExpressCheckout_API_Operation_NVP/
*/
?>