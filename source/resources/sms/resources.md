title:  代码片段
---

### 通知类短信发送及查询短信发送状态 Demo（Java） ：

```

import java.io.IOException;
import java.util.Comparator;
import java.util.HashMap;
import java.util.Map;
import java.util.TreeMap;

import okhttp3.Dispatcher;
import okhttp3.FormBody;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

import org.apache.commons.codec.digest.DigestUtils;


public class Example {
    
    private static final String APPID = "XXXXX";
    private static final String SECRET = "<YOUR_SECRET>";
    
    private static final String BASE_URL = "https://api.wilddog.com/sms/v1/" + APPID;
    private static final String SENDCODE_URL = BASE_URL + "/code/send";
    private static final String SEND_URL = BASE_URL + "/notify/send";
    private static final String CHECKCODE_URL = BASE_URL + "/code/check";
    private static final String QUERY_URL = BASE_URL + "/status";

    private static OkHttpClient client = null;
    static{
        Dispatcher dispatcher = new Dispatcher();
        dispatcher.setMaxRequests(100);
        dispatcher.setMaxRequestsPerHost(100);
        OkHttpClient.Builder builder = new OkHttpClient.Builder();
        client = builder.dispatcher(dispatcher).build();
    }

    // 发送短信验证码
    public static void sendCode(String templateId, String mobile) {
        long timestamp = System.currentTimeMillis();
        Map<String, String> params = new HashMap<String, String>();
        // 设置请求参数
        params.put("templateId", templateId);
        params.put("mobile", mobile);
        params.put("timestamp", String.valueOf(timestamp));
        Map<String, Object> sortedMap = new TreeMap<String, Object>(new Comparator<String>() {
            public int compare(String arg0, String arg1) {
                // 忽略大小写
                return arg0.compareToIgnoreCase(arg1);
            }
        });
        // 请求参数排序
        sortedMap.putAll(params);
        // 计算签名
        StringBuilder sb = new StringBuilder();
        FormBody.Builder formBody = new FormBody.Builder();
        for (String s : sortedMap.keySet()) {
            System.out.println(s + "  " + sortedMap.get(s));
            formBody.add(s, sortedMap.get(s) + "");
            sb.append(String.format("%s=%s&", s, sortedMap.get(s)));
        }
        sb.append(SECRET);
        String sig = DigestUtils.sha256Hex(sb.toString());
        // 追加签名参数
        RequestBody body = formBody.add("signature", sig).build();
        Request request = new Request.Builder().url(SENDCODE_URL).post(body).build();
        try {
            Response response = client.newCall(request).execute();
            System.out.println(response);
            System.out.println(response.body().string());
            System.out.println(response.body().toString());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    // 发送短信通知
    public static void send(String templateId, String mobiles, String parameters) {
        long timestamp = System.currentTimeMillis();
        Map<String, String> params = new HashMap<String, String>();
        // 设置请求参数
        params.put("templateId", templateId);
        params.put("mobiles", mobiles);
        params.put("timestamp", String.valueOf(timestamp));
        params.put("params", parameters);
        Map<String, Object> sortedMap = new TreeMap<String, Object>(new Comparator<String>() {
            public int compare(String arg0, String arg1) {
                // 忽略大小写
                return arg0.compareToIgnoreCase(arg1);
            }
        });
        // 请求参数排序
        sortedMap.putAll(params);
        // 计算签名
        StringBuilder sb = new StringBuilder();
        FormBody.Builder formBody = new FormBody.Builder();
        for (String s : sortedMap.keySet()) {
            System.out.println(s + "  " + sortedMap.get(s));
            formBody.add(s, sortedMap.get(s) + "");
            sb.append(String.format("%s=%s&", s, sortedMap.get(s)));
        }
        sb.append(SECRET);
        String sig = DigestUtils.sha256Hex(sb.toString());
        // 追加签名参数
        RequestBody body = formBody.add("signature", sig).build();
        Request request = new Request.Builder().url(SEND_URL).post(body).build();
        try {
            Response response = client.newCall(request).execute();
            System.out.println(response);
            System.out.println(response.body().string());
            System.out.println(response.body().toString());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    

    // 校验验证码
    public static void checkCode(String code, String mobile) {
        long timestamp = System.currentTimeMillis();
        Map<String, String> params = new HashMap<String, String>();
        // 设置请求参数
        params.put("code", code);
        params.put("mobile", mobile);
        params.put("timestamp", String.valueOf(timestamp));
        Map<String, Object> sortedMap = new TreeMap<String, Object>(new Comparator<String>() {
            public int compare(String arg0, String arg1) {
                // 忽略大小写
                return arg0.compareToIgnoreCase(arg1);
            }
        });
        // 请求参数排序
        sortedMap.putAll(params);
        // 计算签名
        StringBuilder sb = new StringBuilder();
        FormBody.Builder formBody = new FormBody.Builder();
        for (String s : sortedMap.keySet()) {
            System.out.println(s + "  " + sortedMap.get(s));
            formBody.add(s, sortedMap.get(s) + "");
            sb.append(String.format("%s=%s&", s, sortedMap.get(s)));
        }
        sb.append(SECRET);
        String sig = DigestUtils.sha256Hex(sb.toString());
        // 追加签名参数
        RequestBody body = formBody.add("signature", sig).build();
        Request request = new Request.Builder().url(CHECKCODE_URL).post(body).build();
        try {
            Response response = client.newCall(request).execute();
            System.out.println(response);
            System.out.println(response.body().string());
            System.out.println(response.body().toString());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    // 短信发送状态查询
    public static void query(String rrid) {
        // 短信发送后批次号
        Map<String, String> params = new HashMap<String, String>();
        params.put("rrid", rrid);
        Map<String, Object> sortedMap = new TreeMap<String, Object>(new Comparator<String>() {
            public int compare(String arg0, String arg1) {
                // 忽略大小写
                return arg0.compareToIgnoreCase(arg1);
            }
        });
        sortedMap.putAll(params);
        // 计算签名
        StringBuilder sb = new StringBuilder();
        for (String s : sortedMap.keySet()) {
            sb.append(String.format("%s=%s&", s, sortedMap.get(s)));
        }
        sb.append(SECRET);
        String sig = DigestUtils.sha256Hex(sb.toString());
        Request request = new Request.Builder().url(QUERY_URL + "?rrid=" + rrid + "&signature=" + sig).get().build();
        try {
            Response response = client.newCall(request).execute();
            System.out.println(response);
            System.out.println(response.body().string());
            System.out.println(response.body().toString());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
   
    public static void main(String[] args) {
        
        //发送短信验证码
        String templateId = "100000";
        String mobile = "XXXXXXX";
        Example.sendCode(templateId, mobile);
        
        //发送通知类短信
        String templateId2 = "100002";
        String mobiles = "[XXXXXXX]";
        String params = "[zidane]";
        Example.send(templateId2, mobiles, params);
        
        //校验验证码
        String code = "100100";
        String mobile2 = "XXXXXXX";
        Example.checkCode(code, mobile2);
        
        //查询发送状态
        String rrid = "XXXXXXXX";
        Example.query(rrid);
    }
}
      
```


### 验证码短信发送 Demo （Python）：

```
import urllib
import urllib2
import time
import hashlib
# appId
appId = 'xxxxx';
# init PATH
baseURL = "https://api.wilddog.com/sms/v1/" + appId;
sendURL = baseURL + "/code/send";

# 短信秘钥
SECRET = "xxxxxxxxxxxxxxxxx";

#准备数据 发送短信验证码类包括下面这些信息:templateId mobile timestamp
requestBody={"templateId":"100000", "mobile":"13031199447", "timestamp":"%d" %(time.time()*1000)}

# 排序
signParam = sorted(requestBody.items(), key=lambda d: d[0]);

# 拼接排序后信息
str = "";
for i in signParam:
        str = str + i[0] + "=" + i[1] + "&";

# 拼接短信秘钥
str = str + SECRET;
# 计算签名
sign = hashlib.sha256(str).hexdigest();

# 设置签名
requestBody['signature'] = sign;

# 发送请求
test_data_urlencode = urllib.urlencode(requestBody)
req = urllib2.Request(url = sendURL,data =test_data_urlencode)
print req
#
res_data = urllib2.urlopen(req)
res = res_data.read()
print res

```


