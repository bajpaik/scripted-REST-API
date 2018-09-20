# scripted-REST-API



1) Navigate to SYSTEM WEB SERVICES > Scripted REST api
2) Create a scripted rest api named "Populate CMDB"
3) Now create two request headers named "Accept" and "Content-Type" with value as "application/json" for both.
4) Now create a Resource named post with Relative path named as "/INSERT/{name_of_ci}" . Resource path will autopopulate. Select the HTTP Method as POST
5) copy the script as shown below:

(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {

	//GET ALL THE REQUEST PARAMETERS
	var queryParameters = request.queryParams;
	
	 if (!((queryParameters.hasOwnProperty("u_category_desired"))&&(queryParameters.hasOwnProperty("category"))&& (queryParameters.hasOwnProperty("install_status")) && (queryParameters.hasOwnProperty("u_license_type"))&&(queryParameters.hasOwnProperty("u_ci_id"))&&(queryParameters.hasOwnProperty("asset_tag"))&&(queryParameters.hasOwnProperty("subcategory")))){
        //If request parameters not set or was malformed, return an error.
        response.setStatus(417);//set the status to indicate a bad argument
        return 'Unable to process request. Request fields \'query_fields\' not defined. ' ;
            
    }
	
	//Glide the table on which you want to post the record.
	var gr = new GlideRecord('cmdb_ci_appl');
	
	
	var req = request.pathParams.name_of_ci;
	
		
	gr.name = req;
	gr.asset_tag = request.queryParams.asset_tag.toString();
	gr.u_ci_id = request.queryParams.u_ci_id.toString();
	gr.install_status = request.queryParams.install_status.toString();
	gr.category = request.queryParams.category.toString();
	gr.subcategory = request.queryParams.subcategory.toString();
	gr.u_license_type = request.queryParams.u_license_type.toString();
	gr.u_category_desired = request.queryParams.u_category_desired.toString();
	
	gr.insert();

	//Set Response
	
	var resDetails = {
		
		"nameofCMDB": req,
		"CIclass" : gr.sys_class_name,
		"sys_id" : gr.getUniqueValue(),
		"CI ID" : gr.u_ci_id,
		"Install Status" : gr.install_status,
		"Category" : gr.category,
		"SubCategory": gr.subcategory,
		"License Type" : gr.u_license_type,
		"Category (Desired)" : gr.u_category_desired,
		"query parms": request.queryParams
		
	};
	
	response.setStatus(201);
	response.setContentType('application/json');
	response.setLocation(request.url);
	response.setBody(resDetails);
	
  6) Create Request Header association. 
  u_category_desired - 1,2,3
  category - <leave it blank since it is a string field>
  install_status - 100,3,6,1,2,4,5,7,8  (these are the backend values of the dropdown field)
  u_license_type - 	Fixed,Permanent  (dropdown field options)
  u_ci_id - <leave it blank since it is a string field>
  asset_tag - <leave it blank since it is a string field>
  subcategory - <leave it blank since it is a string field>
  
  
