## 三角函数实现Mesh波浪效果 ，贴图滚动，动态碰撞器

<!-- more -->

### 代码：

```c
using UnityEngine;
using System.Collections;


[RequireComponent(typeof(MeshFilter))]
public class MeshWaterWave : MonoBehaviour
{
    public enum WaveType
    {
        none,
        simple,
        multiply,
    }

    [SerializeField] float scollSpeed = 0.1f;

   
    //高度(振幅)
    [SerializeField] float height = 0.5f;
    //速度(初相)
    [SerializeField] float speed = 1f;
    //X/Z方向系数
    [SerializeField, Range(-1, 1)] float xDirection = 1;
    [SerializeField, Range(-1, 1)] float zDirection = 1;
    //幅度(角速度)
    [SerializeField, Range(0, 1)] float range = 1;
    //方式
    [SerializeField] WaveType waveType;
    //Mesh及顶点
    Mesh waveMesh;
    Vector3[] baseVertex;
    Vector3[] nowVertex;
    float xOffset;
    MeshRenderer meshRenderer;
    new MeshCollider collider;

    void Start()
    {
        waveMesh = GetComponent<MeshFilter>().mesh;
        meshRenderer = GetComponent<MeshRenderer>();
        collider = GetComponent<MeshCollider>();

        baseVertex = waveMesh.vertices;
        nowVertex = new Vector3[baseVertex.Length];
    }
    void Update()
    {
        switch (waveType)
        {
            case WaveType.simple:
                WaveSimple();
                break;
            case WaveType.multiply:
                WaveMultiply();
                break;
            default:
                break;
        }
    }
    void StartWave(WaveType type)
    {
        waveType = type;
    }
    void ResetWave()
    {
        waveType = WaveType.none;
        waveMesh.vertices = baseVertex;
    }
    //-----
    void WaveSimple()
    {
        for (int i = 0; i < baseVertex.Length; i++)
        {
            nowVertex[i] = baseVertex[i];
            nowVertex[i].y += Mathf.Sin((Time.time * speed + nowVertex[i].x * xDirection + nowVertex[i].z * zDirection) * range) * height;
        }
        waveMesh.vertices = nowVertex;
        TextureScoll();
        ColliderMeshUpdate();
    }
    void WaveMultiply()
    {
        for (int i = 0; i < baseVertex.Length; i++)
        {
            nowVertex[i] = baseVertex[i];
            nowVertex[i].y += Mathf.Sin((Time.time * speed + baseVertex[i].x * xDirection) * range) * height + Mathf.Sin((Time.time * speed + baseVertex[i].z * zDirection) * range) * height;
        }
        waveMesh.vertices = nowVertex;
        TextureScoll();
        ColliderMeshUpdate();
    }

    //贴图滚动
    void TextureScoll()
    {
        xOffset = Time.time * scollSpeed;
        meshRenderer.material.SetTextureOffset("_MainTex", new Vector2(xOffset, 0));
       
    }

    //动态碰撞
    void ColliderMeshUpdate()
    {
        collider.sharedMesh = waveMesh;
    }
}

```

### 效果图：

![](https://ftp.bmp.ovh/imgs/2020/04/6b334eaca2292f17.gif)

### 原理：

#### 将mesh的顶点x轴坐标与z轴坐标映射到 三角函数Sin的自变量x

#### mesh的顶点y坐标映射到 三角函数sin的自变量Sinx

#### 在乘以高度A => ASinx 映射回mesh顶点坐标yz值

### Update 4.19：

#### MeshCollider动态碰撞

![](https://ftp.bmp.ovh/imgs/2020/04/6fb95e2578226cd1.gif)

