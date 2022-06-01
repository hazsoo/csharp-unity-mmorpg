### 게임오브젝트 생성
##### Instantiate(원본 오브젝트);
```C#
using UnityEngine;

public class ObjectSpawner : MonoBehaviour
{
    [SerializeField]
    private GameObject boxPrefab;

    private void Awake()
    {
        // 1. Instantiate(원본 오브젝트);
        // original 게임오브젝트(프리팹)를 복제해서 생성
        // (복제되는 오브젝트의 모든 컴포넌트 정보가 원본과 완전히 동일)        
        Instantiate(boxPrefab);
    }
}
```
##### Instantiate(원본 오브젝트, 위치, 회전);
```C#
        // 2. Instantiate(원본 오브젝트, 위치, 회전);
        // Instantiate(GameObject original, Vector3 position, Quaternion rotation);
        // original 게임오브젝트(프리팹)를 복제해서 생성하고,
        // 생성된 복제본의 위치를 position으로, 회전을 rotation으로 설정
        // 세 개 매개변수 함께 사용. 개별 사용 불가.
        Instantiate(boxPrefab, new Vector3(3, 3, 0), Quaternion.identity);
```
##### 회전값 설정
```C#
        // 3. 회전값 설정 : Euler로 Quaternion 변환
        Quaternion rotation = Quaternion.Euler(0, 0, 45);
        Instantiate(boxPrefab, new Vector3(2, 1, 0), rotation);
```
##### 복제 정보 받아서 설정
```C#
        // 4. 방금 생성된 복제 정보 받아서 설정하기.
        Quaternion rotation = Quaternion.Euler(0, 0, 45);
        GameObject clone = Instantiate(boxPrefab, Vector3.zero, rotation);

        // 이름 변경
        clone.name = "Box001";
        // 색상 변경
        clone.GetComponent<SpriteRenderer>().color = Color.black;
        // 위치 변경
        clone.transform.position = new Vector3(2, 1, 0);
        // 크기 변경
        clone.transform.localScale = new Vector3(3, 2, 1);
```
