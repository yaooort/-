```javascript
function aj(opt) {
    var url = 'http://127.0.0.1:5000/';
    var def = {
        type: "post",
        dataType: "json",
        data: {},
        async: false,
        success: function (data) {

        },
        beforeSend: function () {
            $("#exampleModal").modal("show");
        },
        complete: function () {
            // $("#exampleModal").modal("hide");
        }
    };
    $.extend(def, opt);
    $.ajax({
        url: url + def.url,
        type: def.type,
        dataType: def.dataType,
        async: def.async,
        data: def.data,
        success: function (json) {
            var response = json;
            if (response['code']==1) {
                def.success(response);
            } else {
                alert("错误");
            }
        },
        beforeSend: function () {
            // showLoadingDialog();
            def.beforeSend();
        },
        error: function () {
            alert("出错了!");
        },
        complete: function () {
            // closeLoadingDialog();
            def.complete();
        }
    });
}


<script>
    function login() {
        var username = document.getElementById("username").value;
        var password = document.getElementById("password").value;
        //console.log(username + password);
        aj({
            data: {'username': username, 'password': password},
            url: 'login',
            success: function (data) {
                if (data['code'] == 1) {
                    self.location = "index.html";
                }
            }
        });
    }
</script>
```
