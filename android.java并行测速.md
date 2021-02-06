# 直接上代码

```java
import com.lzy.okgo.https.HttpsUtils;

import net.moonjoy.firstroom.utils.common.sickle.L;
import net.moonjoy.firstroom.utils.common.utils.HandlerUtil;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.IOException;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Vector;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import okhttp3.OkHttpClient;
import okhttp3.Request;


public class ListDomainSort {
    //域名测速
    public static final int DOMAIN_TYPE = 0x1009;
    //ip ping
    public static final int PING_TYPE = 0x1010;
    //rtmp测速
    public static final int RTMP_TYPE = 0x1011;

    private int workNum = 0;//任务数量
    private ExecutorService service; //线程池
    //    private ArrayBlockingQueue<String> blockingQueue; //结果列表
    private List<String> blockingQueue;
    private CountDownLatch latch; //任务锁
    private boolean ignoreError = true; //忽略失败
    private int TYPE = DOMAIN_TYPE; //测速类型
    private int num_return;//需要返回前几个排名


    private List<String> oldList = new ArrayList<>();//原数据

    private CallHostsDomain callHosts; //接口回调

    private ListDomainSort() {
    }

    public static ListDomainSort getSortUtil() {
        return new ListDomainSort();
    }

    /**
     * 执行测速
     *
     * @param oldList     需要测速的列表
     * @param num_return  需要返回前几个排名
     * @param ignoreError 是否忽略失败的 默认是ture
     * @param callHosts   回调接口
     */
    public void startSort(List<String> oldList, int num_return, boolean ignoreError, int type, final CallHostsDomain callHosts) {
        this.callHosts = callHosts;
        this.TYPE = type;
        this.num_return = num_return;
        this.oldList = oldList;
        this.ignoreError = ignoreError;
        //1.創建和列表数量相同的线程
        setUp();
        //2.抽取做工单元
        for (int i = 0; i < workNum; i++) {
            service.execute(new WorkRunnable(oldList.get(i)));
        }
        //3.等待所有工作线程执行完成
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    latch.await();//等待子线程的所有任务完成
                    //停止所有线程
                    service.shutdownNow();
                } catch (Exception e) {
                } finally {
                    HandlerUtil.runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            callHosts.callHostList(copyIterator(blockingQueue.iterator()));
                        }
                    });
                }
            }
        }).start();
    }

    private void setUp() {
        this.workNum = oldList.size();
        //执行任务的子线程数量
        int threadNum = workNum;
        //创建可变大小线程池
        service = Executors.newCachedThreadPool();
        //计数器的值为任务的数量
        latch = new CountDownLatch(threadNum);
//        blockingQueue = new ArrayBlockingQueue<>(acceptCount);
        blockingQueue = new Vector<>();
    }

    //单个线程逻辑
    class WorkRunnable implements Runnable {
        final String host;

        WorkRunnable(String ip) {
            host = ip;
        }

        @Override
        public void run() {
            //4.对每个线程执行回来的结果进行依次加入列表
            L.http(Thread.currentThread().getName() + "正在执行。。。");
            if (ignoreError) {
                countExeuct(host);
                blockingQueue.add(host);
            } else {
                if (countExeuct(host)) {
                    blockingQueue.add(host);
                }
            }
            if (num_return < workNum && blockingQueue.size() >= num_return) {
                //当任务成功，结束所有线程
                for (int i = 0; i < workNum; i++)
                    latch.countDown();
            }
            //完成一个任务就调用一次
            latch.countDown();
        }
    }

    /**
     * 执行测速单元
     *
     * @param host 需要测速的域名或IP
     * @return
     */
    private boolean countExeuct(String host) {

        if (TYPE == DOMAIN_TYPE) {
            return testDomain(host);
        } else if (TYPE == PING_TYPE) {
            return testPing(host);
        } else if (TYPE == RTMP_TYPE) {
            return testRtmp(host);
        } else {
            return false;
        }
    }

    private boolean testRtmp(String host) {
        return true;//RtmpClient.open(host, false) > 0;
    }

    private boolean testPing(String host) {
        Runtime runtime = Runtime.getRuntime();
        try {
            if (host.equals("0")) {
                return false;
            }
            //ping 1次 超时3秒 数据包512 TTL 255
            String prog = "/system/bin/ping -w 1 -c 1 -i 1 -s 128 -t 255 " + host;
            Process mIpAddrProcess = runtime.exec(prog);
            int mExitValue = mIpAddrProcess.waitFor();
            return mExitValue == 1;
        } catch (Exception ignore) {
        }
        return false;
    }

    private boolean testDomain(String host) {
        String result = httpGet(host);
        if (result != null) {
            try {
                JSONObject object = new JSONObject(result);
                int code = object.optInt("status");
                L.d("OTHER", "测速" + host + "==>>" + (code == 1));
                return code == 1;
            } catch (JSONException e) {
                L.d("OTHER", "测速" + host + "==>>" + false);
            }
        }
        return false;
    }


    public interface CallHostsDomain {
        void callHostList(List<String> newList);
    }

    private <T> List<T> copyIterator(Iterator<T> iter) {
        List<T> copy = new ArrayList<T>();
        while (iter.hasNext())
            copy.add(iter.next());
        return copy;
    }


    /**
     * get的方式请求
     */
    private String httpGet(String host) {
        //测试异步执行

//        try {
//            Thread.sleep(5000);
//        } catch (InterruptedException e) {
//            e.printStackTrace();
//        }
//        L.http(Thread.currentThread().getName() + "执行结束");
//        return "";


//        使用okGo进行访问

//        String response = OK.get(host + "/api/m/ping?_t=" + System.currentTimeMillis()).tag(Thread.currentThread().getName()).start();
//        L.http(Thread.currentThread().getName() + "执行结束");
//        return response;

        //使用OKHTTP3进行访问

        long timeOut = 1000;
        OkHttpClient.Builder builder = new OkHttpClient.Builder();
        builder.connectTimeout(timeOut, TimeUnit.MILLISECONDS);
        builder.readTimeout(timeOut, TimeUnit.MILLISECONDS);
        builder.writeTimeout(timeOut, TimeUnit.MILLISECONDS);
        HttpsUtils.SSLParams sslParams = HttpsUtils.getSslSocketFactory();
        builder.sslSocketFactory(sslParams.sSLSocketFactory, sslParams.trustManager);
        OkHttpClient client = builder.build();//创建OkHttpClient对象
        Request request = new Request.Builder()
                .url(host + "/api/m/ping?_t=" + System.currentTimeMillis())//请求接口。如果需要传参拼接到接口后面。
                .build();//创建Request 对象
        String responseStr = "";
        try {
            okhttp3.Response response = client.newCall(request).execute();//得到Response 对象
            if (response.body() != null)
                responseStr = response.body().string();
        } catch (IOException e) {
            e.printStackTrace();
        }
        L.http(Thread.currentThread().getName() + "执行结束");
        return responseStr;


        //设置原生请求，支持https

//        try {
//            URL url = new URL(host + "/api/m/ping?_t=" + System.currentTimeMillis());
//            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
//            connection.setConnectTimeout(2000);
//            connection.setReadTimeout(1000);
//            connection.setRequestMethod("GET");
//            L.http("请求" + host);
//            //获得结果码
//            int responseCode = connection.getResponseCode();
//            L.http(host + "返回");
//            if (responseCode == 200) {
//                //请求成功 获得返回的流
//                InputStream is = connection.getInputStream();
//                BufferedReader reader = new BufferedReader(new InputStreamReader(is));
//                StringBuffer sb = new StringBuffer();
//                String line = null;
//                while ((line = reader.readLine()) != null) {
//                    sb.append(line);
//                }
//                return sb.toString();
//            } else {
//                //请求失败
//                return null;
//            }
//        } catch (Exception ignored) {
//        }
//        return null;
    }
}
```
# 使用方式
```java
        ArrayList<String> list = new ArrayList<>();
        list.add("http://www.baidu.com");
        list.add("http://www.google.com");
        list.add("http://www.yulled.com");
        list.add("http://www.yuhhhs.com");
        list.add("http://www.cdhtgeg.com");
            ListDomainSort.getSortUtil().startSort(list, 5, false, ListDomainSort.DOMAIN_TYPE, new ListDomainSort.CallHostsDomain() {
                @Override
                public void callHostList(List<String> newList) {
                    L.d("OTHER", "测速结果==>>" + JSON.toJSONString(newList).toString());
                }
            });
```
