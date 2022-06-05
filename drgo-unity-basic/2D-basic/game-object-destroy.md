##### 컴포넌트 삭제
```C#
using UnityEngine;

public class DestroySample : MonoBehaviour
{
    [SerializeField]
    private GameObject playerObject;

    private void Awake()
    {
        // playerObject 게임오브젝트의 "PlayerController" 컴포넌트 삭제
        Destroy(playerObject.GetComponent<PlayerController>());
    }
}
```
###### 컴포넌트도 삭제가 가능하다는 것을 보여주기 위해
###### 예제에서 Destroy(Component):를 사용하고 있지만
###### playerObject.GetComponent<PlayerController>().enabled = false;
###### 와 같이 컴포넌트를 삭제하지 않고 꺼두는 것을 더 권장한다

##### 오브젝트 삭제
```C#
using UnityEngine;

public class DestroySample : MonoBehaviour
{
    [SerializeField]
    private GameObject playerObject;

    private void Awake()
    {
        // playerObject 게임오브젝트 삭제
        Destroy(playerObject);

        // 2초 뒤에 삭제
        Destroy(playerObject, 2.0f);
    }
}
```
##### 지정 영역 밖 오브젝트 삭제
```C#
using UnityEngine;

public class PositionAutoDestroyer : MonoBehaviour
{
    private Vector2 limitMin = new Vector2(-7.5f, -4.5f);
    private Vector2 limitMax = new Vector2(7.5f, 4.5f);

    private void Update()
    {
        // 이 스크립트를 가지고 있는 게임오브젝트의 x, y 좌표가 범위 밖으로 벗어나면 오브젝트 삭제
        if (transform.position.x < limitMin.x || transform.position.x > limitMax.x ||
            transform.position.y < limitMin.y || transform.position.y > limitMax.y)
        {
            // 소문자 gameObject는 본인이 소속된 게임 오브젝트
            Destroy(gameObject);
        }
    }
}
```
##### 충돌 기반 오브젝트 삭제
```C#
using System.Collections;
using UnityEngine;

public class Wall : MonoBehaviour
{
    private SpriteRenderer spriteRenderer;

    private void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        // 벽에 부딪힌 오브젝트 삭제
        Destroy(collision.gameObject);
        // 충돌이 일어나면 벽의 색상을 잠깐 변경
        StartCoroutine("HitAnimation");
    }

    private IEnumerator HitAnimation()
    {
        spriteRenderer.color = Color.red;

        yield return new WaitForSeconds(0.1f);

        spriteRenderer.color = Color.white;
    }
}
```
![destroy](https://user-images.githubusercontent.com/81146582/172039767-47228aed-ae16-45a5-85a8-44f7f1688038.gif)
