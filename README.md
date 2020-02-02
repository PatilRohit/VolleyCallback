# VolleyCallback
Create a separate class to define all about volley and in another activity pass URL,CONTEXT to instance and Get Response

First create callback interface to get result in Activity

    public interface IResult {
        public void notifySuccess(String requestType,JSONObject response);
        public void notifyError(String requestType,VolleyError error);
    }

Create a separate class with volley function to response the result through interface to activity

    public class VolleyService {
    
        IResult mResultCallback = null;
        Context mContext;
    
        VolleyService(IResult resultCallback, Context context){
            mResultCallback = resultCallback;
            mContext = context;
        }
    
    
        public void postDataVolley(final String requestType, String url,JSONObject sendObj){
            try {
                RequestQueue queue = Volley.newRequestQueue(mContext);
    
                JsonObjectRequest jsonObj = new JsonObjectRequest(url,sendObj, new Response.Listener<JSONObject>() {
                    @Override
                    public void onResponse(JSONObject response) {
                        if(mResultCallback != null)
                            mResultCallback.notifySuccess(requestType,response);
                    }
                }, new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        if(mResultCallback != null)
                            mResultCallback.notifyError(requestType,error);
                    }
                });
    
                queue.add(jsonObj);
    
            }catch(Exception e){
    
            }
        }
    
        public void getDataVolley(final String requestType, String url){
            try {
                RequestQueue queue = Volley.newRequestQueue(mContext);
    
                JsonObjectRequest jsonObj = new JsonObjectRequest(Request.Method.GET, url, new Response.Listener<JSONObject>() {
                    @Override
                    public void onResponse(JSONObject response) {
                        if(mResultCallback != null)
                            mResultCallback.notifySuccess(requestType, response);
                    }
                }, new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        if(mResultCallback != null)
                            mResultCallback.notifyError(requestType, error);
                    }
                });
    
                queue.add(jsonObj);
    
            }catch(Exception e){
    
            }
        }
    } 


Then initialize callback interface into main activity

        mResultCallback = new IResult() {
            @Override
            public void notifySuccess(String requestType,JSONObject response) {
                Log.d(TAG, "Volley requester " + requestType);
                Log.d(TAG, "Volley JSON post" + response);
            }

            @Override
            public void notifyError(String requestType,VolleyError error) {
                Log.d(TAG, "Volley requester " + requestType);
                Log.d(TAG, "Volley JSON post" + "That didn't work!");
            }
        };

Now create object of VolleyService class and pass it context and callback interface

    mVolleyService = new VolleyService(mResultCallback,this);

Now call the Volley method for post or get data also pass requestType which is to identify the service requester when getting result back into main activity

        mVolleyService.getDataVolley("GETCALL","http://192.168.1.150/datatest/get/data");
        JSONObject sendObj = null;

        try {
            sendObj = new JSONObject("{'Test':'Test'}");
        } catch (JSONException e) {
            e.printStackTrace();
        }
        mVolleyService.postDataVolley("POSTCALL", "http://192.168.1.150/datatest/post/data", sendObj);

Final MainActivity

    public class MainActivity extends AppCompatActivity {
        private String TAG = "MainActivity";
        IResult mResultCallback = null;
        VolleyService mVolleyService;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            initVolleyCallback();
            mVolleyService = new VolleyService(mResultCallback,this);
            mVolleyService.getDataVolley("GETCALL","http://192.168.1.150/datatest/get/data");
            JSONObject sendObj = null;
    
            try {
                sendObj = new JSONObject("{'Test':'Test'}");
            } catch (JSONException e) {
                e.printStackTrace();
            }
            mVolleyService.postDataVolley("POSTCALL", "http://192.168.1.150/datatest/post/data", sendObj);
        }
    
        void initVolleyCallback(){
            mResultCallback = new IResult() {
                @Override
                public void notifySuccess(String requestType,JSONObject response) {
                    Log.d(TAG, "Volley requester " + requestType);
                    Log.d(TAG, "Volley JSON post" + response);
                }
    
                @Override
                public void notifyError(String requestType,VolleyError error) {
                    Log.d(TAG, "Volley requester " + requestType);
                    Log.d(TAG, "Volley JSON post" + "That didn't work!");
                }
            };
        }
    
    }
