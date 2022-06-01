### 저절로 움직이기

```C#
using UnityEngine;

public class Movement2D : MonoBehaviour
{
    private void Update()
    {
        // 새로운 위치 = 현재 위치 + (방향 * 속도)
        // transform.position = transform.position + new Vector3(1, 0, 0) * 1;

        transform.position += Vector3.right * 1 * Time.deltaTime;
        // Time.deltaTime : 이전 Update() 종료부터 다음 Update() 시작까지의 시간
        // 컴퓨터 사양이 높을수록 그 시간이 짧다. 60초에 120회면 0.5
        // 그래서 곱해주면 사양이 다른 컴퓨터끼리도 같은 속도로 움직이게 됨
    }
}
```

### 방향키로 움직이기

```C#
using UnityEngine;

public class Movement2D : MonoBehaviour
{
    private float moveSpeed = 5.0f; // 이동 속도
    private Vector3 moveDirection = Vector3.zero; // 이동 방향

    private void Update()
    {
        // GetAxisRaw : Edit -> Project Setting -> Input Manager 의 단축키 이용

        // float value = Input.GetAxisRaw("단축키명");
        // 긍정(Positive) : +1, 부정(Negative) : -1, 대기 : 0

        // float value = Input.GetAxis("단축키명");
        // GetAxisRaw()는 키를 누르면 바로 1 or -1이 되지만
        // GetAxis()는 키를 누르고 있으면 0에서 1 or -1로 서서히 증가.(부정도 마찬가지)

        float x = Input.GetAxisRaw("Horizontal"); // 좌우 이동
        float y = Input.GetAxisRaw("Vertical"); // 상하 이동

        // 이동방향 설정
        moveDirection = new Vector3(x, y, 0);

        // 새로운 위치 = 현재 위치 + (방향 * 속도)
        transform.position += moveDirection * moveSpeed * Time.deltaTime;
    }
}
```

### 충돌 구현하기

##### 권장하는 사용 방법
1. 컴포넌트와 동일한 타입의 변수 생성
   ```C#
   private Rigidbody2D rigid2D;
   ```
2. 컴포넌트 정보를 얻어와서 변수에 저장
   ```C#
   rigid2D = GetComponent<Rigidbody2D>();
   ```
3. 컴포넌트 정보가 저장된 변수를 사용
   ```C#
   rigid2D.velocity = new Vector3(x, y, 0) * moveSpeed;
   ```

##### Tip. 현재 방법과 같이 클래스 변수를 생성하고,
##### 컴포넌트 정보를 한번만 저장하면 현재 클래스 내부 어디에서든
##### rigid2D 변수를 이용해 Rigidbody2D 컴포넌트 정보를 바꾸거나 얻어올 수 있다

```C#
using UnityEngine;

public class Movement2D : MonoBehaviour
{
    private float moveSpeed = 5.0f; // 이동 속도
    private Rigidbody2D rigid2D;

    private void Awake()
    {
        rigid2D = GetComponent<Rigidbody2D>();
    }

    private void Update()
    {
        float x = Input.GetAxisRaw("Horizontal"); // 좌우 이동
        float y = Input.GetAxisRaw("Vertical"); // 상하 이동

        // 새로운 위치 = 현재 위치 + (방향 * 속도)
        //transform.position += moveDirection * moveSpeed * Time.deltaTime;

        // Rigidbody2D 컴포넌트에 있는 속력(velocity) 변수 설정
        rigid2D.velocity = new Vector3(x, y, 0) * moveSpeed;
    }
}
```

### 충돌시 이벤트

##### CollisionEvent
```C#
using UnityEngine;

public class CollisionEvent : MonoBehaviour
{
    [SerializeField]
    private Color color;
    private SpriteRenderer spriteRenderer;

    private void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
    }

    // 충돌이 일어나는 순간 1회 호출
    private void OnCollisionEnter2D(Collision2D collision)
    {
        spriteRenderer.color = color; // 설정한 색으로 변경 
    }

    // 충돌이 유지되는 동안 매 프레임 호출
    private void OnCollisionStay2D(Collision2D collision)
    {
        Debug.Log(gameObject.name + " : OnCollisionStay2D()  메소드 실행");
    }

    // 충돌이 종료되는 순간 1회 호출
    private void OnCollisionExit2D(Collision2D collision)
    {
        spriteRenderer.color = Color.white;
    }
}
```

##### TriggerEvent
```C#
using UnityEngine;

public class TriggerEvent : MonoBehaviour
{
    [SerializeField]
    private GameObject moveObject;
    [SerializeField]
    private Vector3 moveDirection;
    private float moveSpeed;

    private void Awake()
    {
        moveSpeed = 5.0f;
    }

    // 충돌이 일어나는 순간 1회 호출
    private void OnTriggerEnter2D(Collision2D collision)
    {
        // moveObject 오브젝트의 색상을 검은색(Color.black)으로 설정한다
        moveObject.GetComponent<SpriteRenderer>().color = Color.black;
    }

    // 충돌이 유지되는 동안 매 프레임 호출
    private void OnTriggerStay2D(Collision2D collision)
    {
        // moveObject 오브젝트를 moveDirection 방향으로 이동한다
        moveObject.transform.position += moveDirection * moveSpeed * Time.deltaTime;
    }

    // 충돌이 종료되는 순간 1회 호출
    private void OnTriggerExit2D(Collision2D collision)
    {
        // moveObject 오브젝트의 색상을 흰색(Color.white)으로 설정한다
        moveObject.GetComponent<SpriteRenderer>().color = Color.white;
        // moveObject 오브젝트의 위치를 (0, 4, 0)으로 설정한다
        moveObject.transform.position = new Vector3(0, 4, 0);
    }
}
```
