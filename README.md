using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    public float moveSpeed = 5f; // 移動速度
    private MapBoundary currentBoundary; // 當前地圖的邊界
    private Vector3 moveDirection; // 移動方向

    public Vector3 MoveDirection => moveDirection; // 提供給其他腳本訪問的屬性

    void Start()
    {
        // 自動尋找當前場景中的 MapBoundary
        currentBoundary = FindObjectOfType<MapBoundary>();

        if (currentBoundary == null)
        {
            Debug.LogError("找不到 MapBoundary，請確保場景中有一個 MapBoundary 物件。");
        }
    }

    void Update()
    {
        // 確保當前邊界已設置
        if (currentBoundary == null) return;

        // 處理玩家輸入
        HandleInput();

        // 移動角色
        MovePlayer();
    }

    /// <summary>
    /// 處理玩家輸入並計算移動方向
    /// </summary>
    private void HandleInput()
    {
        float moveX = Input.GetAxisRaw("Horizontal");
        float moveY = Input.GetAxisRaw("Vertical");

        // 限制只能在一個方向上移動
        if (Mathf.Abs(moveX) > Mathf.Abs(moveY))
        {
            moveY = 0f;
        }
        else
        {
            moveX = 0f;
        }

        // 計算移動方向
        moveDirection = new Vector3(moveX, moveY, 0f).normalized;
    }

    /// <summary>
    /// 處理角色移動
    /// </summary>
    private void MovePlayer()
    {
        Vector3 newPosition = transform.position + moveDirection * moveSpeed * Time.deltaTime;

        // 限制角色位置在邊界內
        Vector2 clampedPosition = currentBoundary.ClampPosition(new Vector2(newPosition.x, newPosition.y));
        transform.position = new Vector3(clampedPosition.x, clampedPosition.y, transform.position.z);
    }

    /// <summary>
    /// 切換到新的地圖邊界
    /// </summary>
    /// <param name="newBoundary">新的邊界</param>
    public void UpdateBoundary(MapBoundary newBoundary)
    {
        currentBoundary = newBoundary;
    }
}
