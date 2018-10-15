# Retrofit
For retrofit api calling

 // retrofit, gson
    compile 'com.google.code.gson:gson:2.6.2'
    compile 'com.squareup.retrofit2:retrofit:2.0.2'
    compile 'com.squareup.retrofit2:converter-gson:2.0.2'


//ApiInterface

public interface ApiInterface {
  
   
    @FormUrlEncoded
    @POST("index.php/Home/login")
    Call<ModelLogin> ResponseLogin(@Field("mobile") String mobileNo, @Field("password") String password, @Field("email") String email);


    @Multipart
    @POST("index.php/Home/sendVendorBikeAdded")
    Call<CommonEntity> ResponseSendVendorBikeAdded(@Part("userIdDb") int userIdDb, @Part("selectedBikeTypeId") int selectedBikeTypeId, @Part MultipartBody.Part  encodedInsurancePhoto, @Part MultipartBody.Part encodedRCPhotos, @Part MultipartBody.Part  encodedPUCPhotos, @Part("registrationNo") RequestBody registrationNo, @Part("brandId")  int brandId, @Part("modelId")  int modelId, @Part("vehNameId")  int vehNameId, @Part("cityId")  int cityId, @Part("subCityId")  int subCityId);



}
//ApiClient
public class ApiClient {

   // public static final String BASE_URL = "http://api.themoviedb.org/3/";
    public static final String BASE_URL = "http://dfdf";
    private static Retrofit retrofit = null;


    public static Retrofit getClient() {
        if (retrofit==null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}

//Model Login


public class ModelLogin {

    @SerializedName("code")
    public int code;
    @SerializedName("msg")
    public String Msg;
    @SerializedName("userIdDb")
    public String userIdDb;
    @SerializedName("name")
    public String name;
    @SerializedName("email")
    public String email;
    @SerializedName("mobile")
    public String mobile;

    @SerializedName("address")
    public String address;
    @SerializedName("refferalCode")
    public String refferalCode;
    @SerializedName("promotionalCode")
    public String promotionalCode;
    @SerializedName("dob")
    public String dob;

    @SerializedName("userVendorType")
    public int userVendorType;
}

//Comoon Entity


public class CommonEntity {


    @SerializedName("code")
    public int code;
    @SerializedName("msg")
    public String Msg;
    @SerializedName("OTP")
    public String OTP;


}


//Main Logic

 showProgressDialog.showDialog("Loading", "Please Wait...", false);

        ApiInterface apiService = ApiClient.getClient().create(ApiInterface.class);

        apiService.ResponseLogin(mobile, password, email).enqueue(new Callback<ModelLogin>() {
            @Override
            public void onResponse(Call<ModelLogin> call, Response<ModelLogin> response) {
                int statusCode = response.code();


                if (response.isSuccessful())
                    if (response.body() != null && response.body().code == 1) {

                        sheredPrfnc.setObjectAsString(Constants.userIdDb, String.valueOf(response.body().userIdDb));
                        sheredPrfnc.setObjectAsString(Constants.name, String.valueOf(response.body().name));
                        sheredPrfnc.setObjectAsString(Constants.email, String.valueOf(response.body().email));
                        sheredPrfnc.setObjectAsString(Constants.mobile, String.valueOf(response.body().mobile));
                        sheredPrfnc.setObjectAsString(Constants.address, String.valueOf(response.body().address));
                        sheredPrfnc.setObjectAsString(Constants.refferalCode, String.valueOf(response.body().refferalCode));
                        sheredPrfnc.setObjectAsString(Constants.dob, String.valueOf(response.body().dob));
                        sheredPrfnc.setObjectAsInt(Constants.userVenderType, response.body().userVendorType);
                        sheredPrfnc.setObjectAsBoolean(Constants.loginStatus, true);


                        showProgressDialog.hideDialog();
                        ShowToastMsg.showToast(getActivity(), response.body().Msg);
                        startActivity(new Intent(getActivity(), MainActivity.class));
                        getActivity().finish();

                    } else if (response.body() != null && response.body().code == 2) {
                        showProgressDialog.hideDialog();
                        popupForMobileNo();
                    } else {
                        showProgressDialog.hideDialog();
                        ShowToastMsg.showToast(getActivity(), response.body().Msg);
                    }


            }

            @Override
            public void onFailure(Call<ModelLogin> call, Throwable t) {
                // Log error here since request failed
                showProgressDialog.hideDialog();
                Log.e(TAG, t.toString());
                ShowToastMsg.showToast(getActivity(), Constants.failedMsg);
            }
        });
