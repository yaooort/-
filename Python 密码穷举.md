```python
strKey = "AaBbCcDdEeFfGgHhIiJjKkLlMmNnOoPpQqRrSsTtUuVvWwXxYyZz1234567890~!@#$%^&*()_+=-;'[]{}:\"\\|,./<>?"


def get_pwd(sarky, num):
    if num == 1:
        for x in sarky:
            yield x
    else:
        for x in sarky:
            for y in get_pwd(sarky, num - 1):
                yield x + y

# 5到15位长度的密码
for y in range(5, 15):
    for x in get_pwd(strKey, y):
        print(x)
```
