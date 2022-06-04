##### 반복문을 이용한 오브젝트 생성
```C#
        for (int i = 0; i < 10; ++i)
        {
            Vector3 position = new Vector3(-4.5f + i, 0, 0);
            Quaternion rotation = Quaternion.Euler(0, 0, i * 10);

            Instantiate(boxPrefab, position, rotation);
        }
```
```C#
        // 외부 반복문 (격자의 y축 계산용으로 활용)
        for (int y = 0; y < 10; ++y)
        {
            // 내부 반복문 (격자의 x축 계산용으로 활용)
            for (int x = 0; x < 10; ++x)
            {
                // 건너뛰게 할 수도 있음
                if(x + y == 4 || x - y == 5 || y - x == 5 || x + y == 14)
                {
                    continue;
                }
                Vector3 position = new Vector3(-4.5f + x, 4.5f - y, 0);

                Instantiate(boxPrefab, position, Quaternion.identity);
            }          
        }
```

###### 랜덤 오브젝트 생성
```C#
using UnityEngine;

public class ObjectSpawner : MonoBehaviour
{
    [SerializeField]
    private GameObject[] prefabArray;

    private void Awake()
    {
        // 오브젝트 배열 중 임의의 프리팹을 뽑음
        for (int i = 0; i < 10; ++i)
        {
            int index = Random.Range(0, prefabArray.Length);
            Vector3 position = new Vector3(-4.5f + i, 0, 0);

            Instantiate(prefabArray[index], position, Quaternion.identity);
        }
    }
}
```

###### 랜덤한 위치에 랜덤 오브젝트 생성
```C#
using UnityEngine;

public class ObjectSpawner : MonoBehaviour
{
    [SerializeField]
    private int objectSpawnCount = 30;
    [SerializeField]
    private GameObject[] prefabArray;

    private void Awake()
    {
        for (int i = 0; i < objectSpawnCount; ++i)
        {
            int index = Random.Range(0, prefabArray.Length);
            float x = Random.Range(-7.5f, 7.5f);    // x 위치
            float y = Random.Range(-4.5f, 4.5f);    // y 위치
            Vector3 position = new Vector3(x, y, 0);

            Instantiate(prefabArray[index], position, Quaternion.identity);
        }
    }
}
```

###### 미리 지정한 스폰 위치 중 랜덤으로 랜덤 오브젝트 생성
```C#
using UnityEngine;

public class ObjectSpawner : MonoBehaviour
{
    [SerializeField]
    private int objectSpawnCount = 30;
    [SerializeField]
    private GameObject[] prefabArray;
    [SerializeField]
    private Transform[] spawnPointArray;

    private void Awake()
    {
        for (int i = 0; i < objectSpawnCount; ++i)
        {
            int prefabIndex = Random.Range(0, prefabArray.Length);
            int spawnIndex = Random.Range(0, spawnPointArray.Length);

            Vector3 position = spawnPointArray[spawnIndex].position;

            GameObject clone = Instantiate(prefabArray[prefabIndex], position, Quaternion.identity);
        }
    }
}
```

###### 생성된 후 움직이게 하기
1. Movement2D.cs
```C#
using UnityEngine;

public class Movement2D : MonoBehaviour
{
    private float moveSpeed = 5.0f; // 이동 속도
    private Vector3 moveDirection;

    public void Setup(Vector3 direction)
    {
        moveDirection = direction;
    }

    private void Update()
    {
        // 새로운 위치 = 현재 위치 + (방향 * 속도)
        transform.position += moveDirection * moveSpeed * Time.deltaTime;
    }
}
```
2. ObjectSpawner.cs
```C#
using UnityEngine;

public class ObjectSpawner : MonoBehaviour
{
    [SerializeField]
    private int objectSpawnCount = 30;
    [SerializeField]
    private GameObject[] prefabArray;
    [SerializeField]
    private Transform[] spawnPointArray;

    private void Awake()
    {
        for (int i = 0; i < objectSpawnCount; ++i)
        {
            int prefabIndex = Random.Range(0, prefabArray.Length);
            int spawnIndex = Random.Range(0, spawnPointArray.Length);

            Vector3 position = spawnPointArray[spawnIndex].position;

            GameObject clone = Instantiate(prefabArray[prefabIndex], position, Quaternion.identity);

            // spawnIndex가 0인 오브젝트가 왼쪽에 있기 때문에 오른쪽으로 이동
            // spawnIndex가 1인 오브젝트가 오른쪽에 있기 때문에 왼쪽으로 이동
            Vector3 moveDirection = (spawnIndex == 0 ? Vector3.right : Vector3.left);
            clone.GetComponent<Movement2D>().Setup(moveDirection);
        }
    }
}
```

###### 근데 각각의 오브젝트 생성되는 속도 다르게
```C#
using UnityEngine;

public class ObjectSpawner : MonoBehaviour
{
    [SerializeField]
    private int objectSpawnCount = 30;
    [SerializeField]
    private GameObject[] prefabArray;
    [SerializeField]
    private Transform[] spawnPointArray;
    private int currentObjectCount = 0; // 현재까지 생성한 오브젝트 개수
    private float objectSpawnTime = 0.0f;

    // Awake()는 한번만 호출되므로 Update()로 변경해서
    // 원하는 시간에 한번 호출되도록 함
    private void Update()
    {
        // objectSpawnCount 개수만큼만 생성하고 더이상 생성하지 않도록 하기위함
        if(currentObjectCount + 1 > objectSpawnCount)
        {
            return;
        }

        // 원하는 시간마다 오브젝트를 생성하기 위한 시간 변수 연산
        // 델타타임만을 변수로 활용하면 실제 시간과 동일하게 흐른다
        objectSpawnTime += Time.deltaTime;

        // 0.5초에 한번씩 실행
        if(objectSpawnTime >= 0.5f)
        {
            int prefabIndex = Random.Range(0, prefabArray.Length);
            int spawnIndex = Random.Range(0, spawnPointArray.Length);

            Vector3 position = spawnPointArray[spawnIndex].position;
            GameObject clone = Instantiate(prefabArray[prefabIndex], position, Quaternion.identity);

            // spawnIndex가 0인 오브젝트가 왼쪽에 있기 때문에 오른쪽으로 이동
            // spawnIndex가 1인 오브젝트가 오른쪽에 있기 때문에 왼쪽으로 이동
            Vector3 moveDirection = (spawnIndex == 0 ? Vector3.right : Vector3.left);
            clone.GetComponent<Movement2D>().Setup(moveDirection);

            currentObjectCount++; // 현재 생성된 오브젝트의 개수를 1 증가
            objectSpawnTime = 0.0f; // 시간을 0으로 초기화
        } 
    }
}
```
![instantiate_example](https://user-images.githubusercontent.com/81146582/171992913-3f41e64c-3406-4af0-beee-012cfa5ec960.gif)
