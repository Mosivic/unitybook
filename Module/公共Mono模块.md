## 公共Mono模块 - 实现对Mono内方法的统一管理

### 1.单例基类

```c
public class BaseManager<T> where T: new()
{
    private static T instance;
    public static T Instance
    {
        get
        {
            if (instance == null)
            {
                instance = new T();
                return instance;
            }
            else
            {
                return instance;
            }
        }
    }  
}
```


###  2.Mono方法实际执行者(挂载在物体上)

```c
public class MonoController : MonoBehaviour
{
    private event UnityAction updateEvent;

    void Start()
    {
        DontDestroyOnLoad(this.gameObject);
    }

    private void Update()
    {
        if (updateEvent != null)
        {
            updateEvent();
        }
    }

    public void AddUpdateEvent(UnityAction fun)
    {
        updateEvent += fun;
    }


    public void RemoveUpdateEvent(UnityAction fun)
    {
        updateEvent -= fun;
    }
}
```

### 3.MonoManager(单例模式，管理所有Mono方法)

```c
public class MonoMgr : BaseManager<MonoMgr>
{
    public MonoController controller;

    public MonoMgr()
    {
        GameObject go = new GameObject();
        controller = go.AddComponent<MonoController>();
    }


    public void AddUpdateEvent(UnityAction fun)
    {
        controller.AddUpdateEvent(fun);
    }


    public void RemoveUpdateEvent(UnityAction fun)
    {
        controller.RemoveUpdateEvent(fun);
    }
}
```

