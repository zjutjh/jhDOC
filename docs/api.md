
## 精弘用户中心

### 登录

!!! info ""

    **请求**

    ```python
    route: 'http://user.jh.zjut.edu.cn/api.php'
    get:
        app: 'passport'
        action: 'login'
        passport: '$username'  #学号
        password: '$password'  #密码
    ```

    ---

    **返回值**

    | state | info | 备注 |
    | :---: | :---: | :--- | 
    | error | 通行证（学号）不存在或者未注册，如未注册，请注册后重新登陆 | 无法登录 |
    | success | 登录成功 | 登录成功 |

    ---

    **样例**

    ```bat
    #get
    http://user.jh.zjut.edu.cn/api.php?app=passport&action=login&passport=201806061201&password=123456
    #return
    {
        "state":"success",
        "info":"登录成功",
        "data":{
            "pid":"201806061201",
            "email":"t1542462994@outlook.com",
            "currentUidCount":"0",
            "availableUidCount":"3",
            "lastLoginTime":"1544883861",
            "lastLoginIP":"10.128.69.69",
            "activeTime":"1534913073",
            "activeIP":"172.16.9.101",
            "type":"1",
            "zjutmail":null
        }
    }
    ```

## 正方教务系统

### 查询课表

!!! info ""

    **请求**

    ```python
    route: 'http://api.jh.zjut.edu.cn/student/classZf.php'
    get:
        username: '$username'  # 学号
        password: '$password'  # 正方教务系统的密码
        year: '$year'  # 学年(2017)
        term: '$term'  # 学期(3为上学期，12为下学期，16为短学期)
    ```

    ---

    **返回值**

    | status | msg | 备注 |
    | :---: | :---: | :--- |
    | error | 参数错误 | | 
    | error | 用户名或密码错误 | |
    | success | $data | |

    ---

    **样例**

    ```bat
    #get
    http://api.jh.zjut.edu.cn/student/classZf.php?username=201806061201&password=123456&year=2017&term=3
    #return
    {
        "status": "success",
        "msg": [{
            "cd_id": "19832", //课程id
            "cdmc": "教404", //上课教室(场地名称？)
            "jc": "1-2节",  
            "jcor": "1-2", //第几节上课
            "jcs": "1-2",
            "jgh_id": "1166",
            "jgpxzd": "1",
            "jxb_id": "6CDD0B1E70ED68EBE053A11310AC0090", //教学班id
            "jxbmc": "线性代数-0010", //教学班名称
            "kch_id": "101", 
            "kcmc": "线性代数", //课程名称
            "khfsmc": "考查", //考核方式名称
            "listnav": "false",
            "localeKey": "zh_CN",
            "oldjc": "3",
            "oldzc": "65535",
            "pageable": true,
            "pkbj": "1",
            "rangeable": true,
            "rsdzjs": 0,
            "sxbj": "1",
            "totalResult": "0",
            "xm": "金建国", //教师姓名
            "xnm": "2018", //学年名
            "xqdm": "0",
            "xqh_id": "01",
            "xqj": "1",
            "xqjmc": "星期一",//星期几
            "xqm": "3",
            "xqmc": "朝晖校区",//校区名称
            "xsdm": "01",
            "xslxbj": "◆",
            "zcd": "1-16周",
            "zcmc": "副教授",
            "zyfxmc": "无方向",
            //这些字段是后来开发者为了方便理解添加的字段
            "name": "线性代数:金建国", //课程名称
            "collage": "", //开课学院 *谁把college拼错了?
            "classinfo": "1-16周:星期1(1-2) 教404;", //课程信息
            "classtype": "考查", //课程类型"未安排"/"考查"/"考试"
            "classscore": 2,//学分
            "classhuor": 32//学时 *谁把hour拼错了?
        }]
    }
    ```

### 查询成绩

!!! info ""

    **请求**

    ```python
    route: 'http://api.jh.zjut.edu.cn/student.scoresZf.php'
    get:
        username=201312341234  #[学号]
        password=xxxxx  #[密码]
        year=2017  #[学年]
        term=3  #[学期, 3为上学期，12为下学期, 16为短学期]
    ```

    ---

    **返回值**

    ---

    **样例**

    ```bat
    #get
    http://api.jh.zjut.edu.cn/student.scoresZf.php?username=201312341234&password=xxxx&year=2017&term=3
    #result
    {
        "status": "success",
        "msg": [
            {
                "bh_id": "081201452007", 
                "bj": "中文1403", //班级
                "cj": "92", //成绩
                "cjsfzf": "否",
                "jg_id": "08",
                "jgmc": "人文学院",
                "jgpxzd": "1",
                "jsxm": "方爱武", //教师姓名
                "jxb_id": "158685",
                "jxbmc": "158685",
                "kcbj": "主修",
                "kch": "G108136",
                "kch_id": "22342",
                "kcmc": "台港澳暨海外华文文学", //课程名称
                "kkbmmc": "人文学院",
                "ksxz": "正常考试",
                "listnav": "false",
                "localeKey": "zh_CN",
                "njdm_id": "2014",
                "njmc": "2014",
                "pageable": true,
                "rangeable": true,
                "row_id": "1",
                "sfxwkc": "否",
                "totalResult": "6",
                "xb": "女",
                "xbm": "2",
                "xf": "2", //学分
                "xh": "201400000123",
                "xh_id": "927126",
                "xm": "姓名",
                "xnm": "2016",
                "xnmmc": "2016-2017",
                "xqm": "12",
                "xqmmc": "2",
                "zyh_id": "520",
                "zymc": "中国语言文学类",
                "term": "2016/2017(2)",
                "name": "台港澳暨海外华文文学",
                "classprop": "主修",
                "classscore": "92",
                "classhuor": "92",
                "classcredit": 32
            },
            {
                "bh_id": "081201452007",
                "bj": "中文1403",
                "cj": "92",
                "cjsfzf": "否",
                "jg_id": "08",
                "jgmc": "人文学院",
                "jgpxzd": "1",
                "jsxm": "褚蓓娟",
                "jxb_id": "158624",
                "jxbmc": "158624",
                "kcbj": "主修",
                "kch": "G108142",
                "kch_id": "16027",
                "kcmc": "外国文学Ⅱ",
                "kkbmmc": "人文学院",
                "ksxz": "正常考试",
                "listnav": "false",
                "localeKey": "zh_CN",
                "njdm_id": "2014",
                "njmc": "2014",
                "pageable": true,
                "rangeable": true,
                "row_id": "2",
                "sfxwkc": "否",
                "totalResult": "6",
                "xb": "女",
                "xbm": "2",
                "xf": "2.5",
                "xh": "201400000123",
                "xh_id": "927126",
                "xm": "姓名",
                "xnm": "2016",
                "xnmmc": "2016-2017",
                "xqm": "12",
                "xqmmc": "2",
                "zyh_id": "520",
                "zymc": "中国语言文学类",
                "term": "2016/2017(2)",
                "name": "外国文学Ⅱ",
                "classprop": "主修",
                "classscore": "92",
                "classhuor": "92",
                "classcredit": 40
            },
            // ...
        ]
    }
    ```