

```
#include <jni.h>
#include <stdio.h>

extern "C"
JNIEXPORT jobject JNICALL
Java_net_moonjoy_yumeiren_show_util_LinkC_keysFromJNI(
        JNIEnv* env,
        jobject /* this */) {

    jclass class_hashmap = env->FindClass("java/util/HashMap");
    jmethodID hashmap_init = env->GetMethodID(class_hashmap, "<init>",
            "()V");
    jobject HashMap = env->NewObject(class_hashmap, hashmap_init, "");
    jmethodID HashMap_put = env->GetMethodID(class_hashmap, "put",
            "(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;");
    //域名加密KEY
    env->CallObjectMethod(HashMap, HashMap_put, env->NewStringUTF("AES_KEY"), env->NewStringUTF("1XXXXXX"));
    return HashMap;
}
```
