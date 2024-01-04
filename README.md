# 불편(不便)용사

# 🦸🦹 A704 프로젝트 컨셉

<img src="https://github.com/heejinssss/project-knight/blob/master/1Intro.png?raw=true" width="500" height="300">

> **“세상 사람들이 불편해 하는 행동은 다 해서 주변의 빈축(嚬蹙)만 샀던 내가
> 이 세계에서는 프로 불편 용사라고?”**
>
> 완벽 마왕들이 완벽하지 않은 사람들을 모두 잡아내고 쓰러뜨려 세상을 지배했다.
>
> 잡혀가지 않고 남은 사람들은 행동 하나하나를 조심해야 살아남을 수 있게 됐다.
>
> 그런 세상에 떨어진 **`다리 떨고 쩝쩝 대고 맞춤법 틀리고 면 치기 하는 주인공`**.

<img src="https://github.com/heejinssss/project-knight/blob/master/2Map.png?raw=true" width="500" height="300">

> 마왕과 그의 부하들을 쓰러뜨려 세상을 자유롭게 만들어야 하는 운명의 소용돌이 속으로..!!

# 시나리오

## 🔌 닉네임 입력으로 게임 접속

<img src="https://github.com/heejinssss/project-knight/blob/master/3InputName.png?raw=true" width="500" height="300">

> 개성 있는 닉네임으로 게임을 시작해요

---

## 🏹 첫 번째 게임 `이브르다다라`

<img src="https://github.com/heejinssss/project-knight/blob/master/4Scene1.png?raw=true" width="500" height="300">

> 조용히 음식을 먹어야 하는 `랭KING` 이 점령한 이브다다라 성!

### 설명서

```
뛰어난 미식가 겸 요리사인 랭KING의 공격을 피하며 보스를 처치해야하는 보스전 게임입니다.
아이템 상자에서 나오는 아이템(스피커, 음식, 총알, 하트 등)을 이용하여 용사의 공격력을 올리고 아이템을 장착하여 보다 쉽게 랭KING을 처치할 수 있을거에요

점프(SPACE)와 방향키(위 방향키, 오른쪽 방향키)을 통해 보스의 공격을 피해보세요
쩝쩝 공격(LeftCtrl)을 통해 쩝쩝 소리를 내며 공격을 해보세요
공격은 보스와 아이템 상자 중 가까운 곳으로 발사가 되니 랭KING과의 거리를 잘 생각하시면서 게임을 플레이하셔야합니다.
방어(아래쪽 방향키)를 통해 무자비하게 공격해오는 보스의 공격을 막아보세요
```

## 🕊️ 두 번째 게임 `지거국`

<img src="https://github.com/heejinssss/project-knight/blob/master/5Scene2.png?raw=true" width="500" height="300">

> 탕수육은 무조건 부어 먹는 `부머기라스` 가 점령한 지거국!

### 설명서

<img src="https://github.com/heejinssss/project-knight/blob/master/6Scene2.png?raw=true" width="500" height="300">

### Player

### 불 & 기름 공격

- Z(불) or X(물)를 누르면 플레이어 위치에서 바라보는 방향으로 X축에 평행하게 Projectile 발사

  ```csharp
  void CreateProjectile(GameObject projectilePrefab)
  {
      anim.SetTrigger("Skill");
      // 프로젝타일의 시작 위치를 플레이어 앞으로 조금 옮김
      Vector3 startPosition = transform.position - transform.right * 0.7f + transform.up * 0.5f;

      // 프리페브로부터 새로운 프로젝타일 오브젝트 생성
      GameObject projectile = Instantiate(projectilePrefab, startPosition, transform.rotation);

      // 프로젝타일로부터 리지드바디 2D 컴포넌트 가져옴
      Rigidbody2D rb = projectile.GetComponent<Rigidbody2D>();

      // 프로젝타일 발사
      rb.AddForce(-transform.right * launchSpeed, ForceMode2D.Impulse);
  }
  ```

### 탕수육 공격

- Z(불)을 두번 연속 맞추면 Enemy가 죽고 해당 위치에 탕수육 Prefab 생성
- 해당 탕수육 Object와 플레이어가 닿으면 바라보는 방향으로 X축에 평행하게 발사
- 해당 탕수육에 맞은 Enemy는 사망

### 회복 아이템

- Z(불)과 X(기름)을 한번씩 맞추면 Enemy가 죽으면 해당 위치에 치킨 Prefab 생성
- 해당 치킨 Object와 플레이어가 닿으면 체력 1 회복

### Enemy

```csharp
void Think()
{
    nextMove = Random.Range(-1, 2);
    // 애니메이션
    if (nextMove == 0)
    {
        anim.SetBool("isFlying", false);
            // 불 공격
        StartCoroutine(ShootFireBall());
    } else
    {
        anim.SetBool("isFlying", true);
    }

    // 방향에 따라 뒤집기
    if (nextMove != 0)
    {
        spriteRenderer.flipX = nextMove < 0;
    }

    // 재귀
    float nextThinkTime = Random.Range(2f, 5f);
    Invoke("Think", nextThinkTime);
}
```

- nextThinkTime 마다 Enemy의 이동 방향 랜덤으로 변경
- Enemy의 속도가 0일 때 공격 Prefab 발사

### Boss

- 용의 남은 체력에 따라 이동속도, 공격속도, 색상 변경

  ```csharp
  // 공격, 이동, 대기 속도 설정
  if (gameManager.bossHealth > 9)
  {
      range = 5;
      waitTime = 1.5f;
      launchSpeed = 2.0f;
      forceLevel = 2000;
  } else if (gameManager.bossHealth > 6)
  {
      range = 7;
      sr.color = new Color(255, 100, 0, 255);
      waitTime = 1;
      launchSpeed = 5.0f;
      forceLevel = 4000;
  } else
  {
      range = 9;
      sr.color = new Color(255, 0, 0, 255);
      waitTime = 0.5f;
      launchSpeed = 7.0f;
      forceLevel = 6000;
  }
  ```

- 이동 패턴들을 Queue에 넣고 차례로 사용

  ```csharp
  if (!anim.GetBool("isFlying") && anim.GetBool("isLifted"))
  {
      // 큐에서 다음 패턴 꺼내 사용
      string pattern = queue.Dequeue();
      if (pattern.Equals("Fly"))
      {
          Fly();

      } else if (pattern.Equals("Teleport"))
      {
          anim.SetTrigger("Teleport");
          Invoke("Teleport", 0.5f);
      } else if (pattern.Equals("Stay"))
      {
          StartCoroutine(Stay());
      } else if (pattern.Equals("Spit"))
      {
          Spit();
      } else if (pattern.Equals("Summons"))
      {
          Summons();
      }
      if (!pattern.Equals("Stay")) queue.Enqueue(pattern); // 다시 큐에 넣음
      queue.Enqueue("Stay");
  }
  ```

## 👟 세 번째 게임 `후르프후릎`

<img src="https://github.com/heejinssss/project-knight/blob/master/7Scene3.gif?raw=true" width="500" height="300">

> **면치기**를 가장 더러워하는 `후르브킹` 이 점령한 후르프후릎 성!
>
> `후르브킹`을 이기기 위해서는 면치기를 하며 온 방안을 돌아다녀야 한다!
>
> 그렇게 튄 라면 국물로 모든 방이 더러워지면 `후르브킹`과 대면할 수 있게 되는데…

### 설명서

```
1. 라면을 먹으면서 면발을 끊지 않고 달려갑니다.
2. 길 중간에 등장하는 가위는 점프를 통해 피합니다.
3. 면을 달고, 온 방안을 휘저어 다니면 끝납니다!

- 점프키는 [SPACE] 입니다.
- 중간에 나오는 어묵은 생명이 됩니다.
  [끝까지 남은 어묵 X 10]가 점수가 됩니다.
- 보스전 후, 남아있는 어묵은 점수가 됩니다.
- 점프키를 길게 누르면 조금 더 높게 뜁니다.
```

### 스토리

<table>
    <tr align="center">
        <td>인트로</td>
        <td>인트로 설명서</td>
    </tr>
    <tr align="center">
        <td>
            <img src="https://github.com/heejinssss/project-knight/blob/master/8Scene3.png?raw=true" width="500" height="300">
        </td>
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/9Scene3.png?raw=true" width="500" height="300">
        </td>
    </tr>
    <tr align="center">
        <td>보스 엔딩</td>
        <td>엔딩</td>
    </tr>
    <tr align="center">
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/10Scene3.png?raw=true" width="500" height="300">
        </td>
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/11Scene3.png?raw=true" width="500" height="300">
        </td>
    </tr>
</table>

1. `ScriptPlayer`

   ```csharp
   public class ScriptPlayer : MonoBehaviour
   {
       public GameManager3 gameManager;

       void Update()
       {
           if (Input.GetButtonDown("Jump"))
               gameManager.Action();
       }
   }
   ```

   - 유저의 `Input` 값 관리
   - 스토리 씬과 플레이 씬의 조작키를 구별하기 위한 스크립트

2. `TalkManager`, `GameManager`

   ```csharp
   // TalkManager.talkData

   [ {0, ["ruleStart:desc", "용사님!:9", "덤벼라!:default", ...] }, ... ]
   ```

   ```csharp
   // GameManager.Talk()

   if (talkData.Split(":")[1] == "default")
   {
   		// 초상화 X 대화창
       defaultPanel.SetActive(true);
       defaultText.text = talkData.Split(":")[0];
   }
   else if (talkData.Split(":")[1] == "desc")
   {
   		// UI 변경
       switch (talkData.Split(":")[0])
       {
           case "ruleStart":
               rulePanel.SetActive(true);
               break;
   				...
   		}
   }
   else
       {
   				// 초상화 O 대화창
           talkPanel.SetActive(true);
           talkText.text = talkData.Split(":")[0];
           portrait.sprite = talkManager.GetPortrait(int.Parse(talkData.Split(":")[1]));
       }

       isAction = true;
       talkIdx++;
   }
   ```

   - 딕셔너리 자료구조인 `TalkManager.talkData` 에 데이터 저장 후 `GameManager.Talk()` 에서 split 하여 사용
     - 초상화가 없는 UI를 사용할 때는 `대사:default` 저장
     - 초상화가 있는 UI를 사용할 때는 `대사:초상화Index` 저장
     - 장면 전환이 필요할 때는 `장면 정환 방식:desc` 저장

### 스테이지 1, 2

<table>
    <tr align="center">
        <td>스테이지 1 인트로</td>
        <td>스테이지 1 게임 장면</td>
    </tr>
    <tr align="center">
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/12Scene3.png?raw=true" width="500" height="300">
        </td>
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/13Scene3.png?raw=true" width="500" height="300">
        </td>
    </tr>
    <tr align="center">
        <td>스테이지 2 인트로</td>
        <td>스테이지 2 게임 장면</td>
    </tr>
    <tr align="center">
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/14Scene3.png?raw=true" width="500" height="300">
        </td>
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/15Scene3.png?raw=true" width="500" height="300">
        </td>
    </tr>
</table>

1. 무한 스크롤

   ```csharp
   public class Scroller : MonoBehaviour
   {
       public float speedRate;

       void Update()
       {
           ...
   				// 왼쪽으로 이동
           float totalSpeed = GameManager3.globalSpeed * speedRate * Time.deltaTime * -1f;
           transform.Translate(totalSpeed, 0, 0);
       }
   }
   ```

   ```csharp
   public class Reposition : MonoBehaviour
   {
       void LateUpdate()
       {
           if (transform.position.x > -25.5) // 맨 왼쪽 끝에 닿지 X
               return;

           // 되돌아가기
           transform.Translate(49.5f, 0, 0, Space.Self);
       }
   }
   ```

   - 벽, 땅, 적, 아이템에 `scroller` 를 적용해 계속 왼쪽으로 이동함
   - 벽, 땅의 경우 맨 왼쪽에 도달했을 때 맨 오른쪽으로 이동하도록 `Reposition` 적용

2. 랜덤 사물 전환

   ```csharp
   public class ChangeObject : MonoBehaviour
   {
       public GameObject[] objects;

       public void Change()
       {
           int ran = Random.Range(0, objects.Length);

           for (int index = 0; index < objects.Length; index++)
           {
               transform.GetChild(index).gameObject.SetActive(ran == index);
           }
       }
   }
   ```

   - 랜덤 값을 받아 Object Group에서 해당 Index의 Object를 활성화

3. 재시작

   ```csharp
   public class ReStart : MonoBehaviour
   {
       public Vector3 initialPosition;

       void Awake()
       {
           initialPosition = transform.position;
   				// 해당 스크립트가 적용된 모든 Object 갯수 세기
           GameManager3.restartObj++;
           GameManager3.tempRestartObj++;
       }
       void FixedUpdate()
       {
           if (GameManager3.isRestart)
           {
               if (GameManager3.tempRestartObj != 0)
               {
   								// 재시작 시 위치 변경 후 갯수 감소
                   transform.position = initialPosition;
                   GameManager3.tempRestartObj--;
                   if (transform.name == "Health Group") // 아이템일 경우
                   {
                       for (int i = 0; i < transform.childCount; i++)
                       {
   												// SetActive 전부 true로 전환
                           transform.GetChild(i).gameObject.SetActive(true);
                       }
                   }
               }
               else
               {
   								// 전부 위치를 바꾸었을 때 재시작 로직 끝
                   GameManager3.isRestart = false;
                   GameManager3.tempRestartObj = GameManager3.restartObj;
               }
           }
       }
   }
   ```

   - 무한 스크롤이 아닌 `Enemy` 같은 경우, 재시작 했을 때 초기 위치로 이동해야 함
   - 따라서 초기 위치를 입력받고, 재시작 시 다시 그 위치로 이동

### 보스전

<table>
    <tr align="center">
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/16Scene3.png?raw=true" width="500" height="300">
        </td>
        <td>
	<img src="https://github.com/heejinssss/project-knight/blob/master/17Scene3.png?raw=true" width="500" height="300">
        </td>
    </tr>
</table>

1. 공격 패턴

   ```csharp
   public void Think()
   {
       ...

       nextAnim = Random.Range(1, 4);
       ChangeAnim(nextAnim);

       switch (nextAnim)
       {
           case 1:
               // 1. Cleave
               break;
           case 2:
               // 2. Breath
               break;
           case 3:
               // 3. Smash
               break;
           default:
               break;
       }

       int turn = Random.Range(2, 4);
       Invoke("Think", turn);
   }
   ```

   - `Cleave`, `Breath`, `Smash` 총 3개의 공격을 랜덤한 타이밍에 랜덤한 순서로 실행
   - 재시작 시 `GameManager` 에서 해당 메소드를 참조해 다시 실행

## 🥁 네 번째 게임 `다리떨리아`

<img src="https://github.com/heejinssss/project-knight/blob/master/18Scene4.png?raw=true" width="500" height="300">

> 다리 떠는 것을 혐오하는 `고만터러킹` 이 점령한 다리떨리아 성!

### 설명서

<img src="https://github.com/heejinssss/project-knight/blob/master/19Scene4.png?raw=true" width="500" height="300">

### 오브젝트 풀링 기법 활용

- 반복적인 객체의 생성 및 제거는 부하를 유발하기에 미리 필요한 만큼 생성해두고, 이를 생성하거나 제거하지 않고 가져다 쓰는 방법 활용

```csharp
// ObjectPool4.cs

Queue<GameObject> InsertQueue(ObjectInfo4 p_objectInfo)
{
    Queue<GameObject> t_queue = new Queue<GameObject>();
    for (int i = 0; i < p_objectInfo.count; i++)
    {
        // 노트를(프리펩을) 생성시켜줘야함
        // 적당한 위치에 넣어주면 될듯, 어차피 비활성화 시킬거라 게임에선 보이지 않으니
        GameObject t_clone = Instantiate(p_objectInfo.goPrefab, transform.position, Quaternion.identity);
        t_clone.SetActive(false); // 생성했으니 바로 비활성화

        // 부모를 설정해줄거
        // 기존에 설정했던 노트 같은 경우에는 노트 매니저 스크립트가 붙어있는 객체가 부모였다
        // 그래서 부모 객체가 존재한다면, 그 객체를 부모로 삼아주자
        if (p_objectInfo.tfPoolParent != null)
            t_clone.transform.SetParent(p_objectInfo.tfPoolParent);
        else
            t_clone.transform.SetParent(this.transform); // 부모 객체가 null 값이라면, 이 스크립트가 붙어있는 객체를 부모로

        // 반복문이 돌고나면 큐에는 카운트 개수 만큼의 객체가 들어있을거
        t_queue.Enqueue(t_clone);
    }
    return t_queue;
}
```

### 노트 출현 로직

- 일정 시간마다 40% 확률로 노트 출현(위에서 미리 생성해둔 노트를 출발시키는 방식)
  - 큐에서 Dequeue
- 노트가 목적지에 도달하면, 재활용을 위해 큐에 Enqueue
- 노트 타이밍에 맞게 space바를 누르면 우리 눈에 보이지 않도록, 단 노트 객체가 이때 풀에 Enqueue된 것이 아니라, 최종 목적지까지는 이동하도록

```csharp
// NoteManager4.cs

void Update()
{
    if (noteActive)
    {
        currentTime += Time.deltaTime;

        // 60(double) / bpm => 1비트 당 시간
        if (currentTime >= 30d / bpm)
        {
            if (Random.value < 0.4)
            {
                GameObject t_note = ObjectPool4.instance.noteQueue.Dequeue();
                t_note.transform.position = tfNoteAppear.position;
                t_note.SetActive(true);
                t_note.transform.localScale = new Vector3(1, 1, 1);
                theTimingManager.boxNoteList.Add(t_note);
            }

            // 프레임 단위로 업데이트 하기에 0.51005xx 뭐 이런 식으로 오차가 생김
            // 근데 이를 그냥 0으로 초기화 해버리면 그 오차가 누적되서 나중에는 음이 안맞고 그럴 수 있다
            // 그래서 그것까지 고려해서 다음 노트는 오차 만큼 더 빨리 나오는 식으로 조정
            currentTime -= 30d / bpm;
        }
    }

}
```

### 점수 로직

- Perfect : 10점
- Cool : 7점
- Good : 4점
- Bad : -3점
- Miss : -6점
- Bad/Miss 시 콤보 초기화
- 10콤보당 3점씩 추가로 획득
- 최종 획득 점수 = (미니게임 점수) / 3000

---

## 🍽️ 마지막 게임 `보스전`

<img src="https://github.com/heejinssss/project-knight/blob/master/20Scene5.png?raw=true" width="500" height="300">

> 가장 강력하고 결벽적인 `파르펙토(perfect)` 대마왕!
> 그런 대마왕에게 단 한 가지 결점이 있었다?
> 비대칭 `괴식 콘텐츠`로 마왕의 홧병을 유도하여 이세계를 구해줘!

### 설명서

```
마왕의 심기를 건드려서 파르펙토 마왕이 열받도록 도와주세요!

1. 마왕이 제시하는 사진의 빈칸에 들어갈 콘텐츠를 3 Match Puzzle에서 부숴주세요.
2. 단, 마왕을 불편하게 만들기 위해서는, 괴식 콘텐츠를 골라야 합니다.
3. 괴식이 아닌 콘텐츠는 점수 합산이 되지 않습니다.
4. 100점이 넘을 때마다 스페이스바를 연타하여 마왕에게 홧병 에네르기파를 쏘세요.
```

### 3-Match Puzzle 로직

1. 유사 애니팡 구현을 위한 로직

- 타일 선택 로직 조건
  - 반드시 두 개의 타일 선택
  - 첫번째 타일과 두번째 타일의 인접 여부 확인 (상하좌우)

```csharp
    public async void Select(Tile5 tile)
    {

        if (isSwapping) return;

        //if (!_selection.Contains(tile)) _selection.Add(tile);

        /* 선택된 타일 상호작용 [S] */
        if (_selection.Count > 0 && _selection[0] != tile)
        {
            _selection[0].icon.transform.localScale = Vector3.one;
        }

        if (!_selection.Contains(tile))
        {
            _selection.Add(tile);
            // 선택된 타일의 크기를 줄임
            tile.icon.transform.localScale = Vector3.one * 0.8f;
        }
        /* 선택된 타일 상호작용 [E] */

        // 두 개의 타일이 선택되었는가?
        if (_selection.Count < 2) return;

        // 첫 번째 타일과 두 번째 타일이 인접해 있는지 확인
        if (!IsAdjacent(_selection[0], _selection[1]))
        {
            Debug.Log("Tiles are not adjacent.");
            _selection.Clear();
            return;
        }

        Debug.Log($"Selected tiles at ({_selection[0].x}, {_selection[0].y}) ({_selection[1].x}, {_selection[1].y})");

        isSwapping = true;

        await Swap(_selection[0], _selection[1]);

        if (CanPop())
        {
            Pop();
        }
        else
        {
            await Swap(_selection[0], _selection[1]);
        }

        isSwapping = false;

        _selection.Clear();
    }

    // 인접한 타일인지 확인하는 메서드
    private bool IsAdjacent(Tile5 tile1, Tile5 tile2)
    {
        return (tile1.x == tile2.x && Mathf.Abs(tile1.y - tile2.y) == 1) ||
               (tile1.y == tile2.y && Mathf.Abs(tile1.x - tile2.x) == 1);
    }
```

2. 타일 교환 로직

```csharp
    public async Task Swap(Tile5 tile1, Tile5 tile2)
    {
        var icon1 = tile1.icon;
        var icon2 = tile2.icon;

        var icon1Transform = icon1.transform;
        var icon2Transform = icon2.transform;

        var sequence = DOTween.Sequence();

        sequence.Join(icon1Transform.DOMove(icon2Transform.position, TweenDuration))
                .Join(icon2Transform.DOMove(icon1Transform.position, TweenDuration));

        await sequence.Play().AsyncWaitForCompletion();

        icon1Transform.SetParent(tile2.transform);
        icon2Transform.SetParent(tile1.transform);

        tile1.icon = icon2;
        tile2.icon = icon1;

        var tile1Item = tile1.Item;

        tile1.Item = tile2.Item;
        tile2.Item = tile1Item;
    }
```

---

## 🥇 랭킹 확인

<img src="https://github.com/heejinssss/project-knight/blob/master/21Ranking.png?raw=true" width="500" height="300">

# 기술 스택

<img src="https://github.com/heejinssss/project-knight/blob/master/22Skill.png?raw=true" width="500">

# 팀원 소개

### 👤 Game

류정모 <이브르다다라>

엄한결 <지거국>

김유진 <후르프후릎>

조현기 <다리떨리아>

배희진 <보스전>

### ⚙️ Database

임혜지 : 닉네임 입력, 플레이 타임, 랭킹
