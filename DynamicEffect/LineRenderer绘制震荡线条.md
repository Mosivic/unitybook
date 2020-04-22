## 用LineRenderer绘制震荡线条，并用EdgeCollider 2D加以动态碰撞器

### 代码：

```c
public class LineSwing : MonoBehaviour
{
    public LineRenderer lineRenderer;
    private EdgeCollider2D edgeCollider;
    //总长
    [SerializeField]
    private float maxLength =10;
    //步长
    [SerializeField]
    private float step = 0.1f;
    //震荡速度
    [SerializeField]
    private float swingSpeed;
    //震荡高度
    [SerializeField]
    private float height;
    //缩放率
    [SerializeField]
    private float rate;
    //绘制点数目
    private int pointCount;

    //绘制点位置
    private Vector3[] positions;
    private Vector2[] points;



    void Start()
    {
        lineRenderer = GetComponent<LineRenderer>();
        edgeCollider = GetComponent<EdgeCollider2D>();

        pointCount =(int)(maxLength/step);
        positions = new Vector3[pointCount];
        points = new Vector2[pointCount];

        //初始化Line始末端宽度，位置点数量
        lineRenderer.startWidth = 0.02f;
        lineRenderer.endWidth = 0.02f;
        lineRenderer.positionCount = pointCount;
    }


    private void Update()
    {
        Swing();
    }

    void Swing()
    {
        float indexLength=0; 
        float temp;

        for (int i = 0; i < pointCount; i++)
        {
            //正弦函数震荡
            temp = Mathf.Sin(Time.time * swingSpeed + (indexLength * rate)) * height;

            //固定双端，周围震荡幅度衰减
            temp -= temp *(float) (0.04 * indexLength * indexLength - 0.4f * indexLength + 1);

            positions[i] = new Vector3(indexLength, temp, 0);
            points[i] = new Vector2(indexLength, temp);
            indexLength += step;      
        }  

        lineRenderer.SetPositions(positions);
        edgeCollider.points = points;
    }
    
}
```

### 缩放率为0，Line正弦摆动：

![](https://ftp.bmp.ovh/imgs/2020/04/16edc169f4d7cc95.gif)

### 缩放率为1，Line正弦摆动：

![](https://ftp.bmp.ovh/imgs/2020/04/41f48a56d59730bb.gif)