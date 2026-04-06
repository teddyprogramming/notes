# Entity, Value Object, Domain Service, and Module

在 DDD 的模型組成元素 (Building Blocks) 設計中，這四個層級（Entity, Value Object, Domain Service, Module）定義了領域模型的邏輯結構、行為與組織方式。

## 1. Entity

Entity 是指在系統中具有 **「唯一識別碼 (Identity)」** 的物件。Entity 的關注點在於它的生命週期，而不僅僅是它的屬性。

- **特性**：
    - **Identity**：即屬性（如姓名、地址）發生變化，只要 ID 不變，它就是同一個物件。
    - **生命週期**：從建立到銷毀都有明確的狀態變化。
    - **相等性 (Identity equality)**：若兩個物件 ID 相同，則視為相等。

- **Java 範例**：

```java
public class Customer {
    private final String customerId; // 唯一識別碼
    private String name;

    public Customer(String customerId) {
        this.customerId = customerId;
    }

    // 即便名字變了，客戶還是同一個
    public void changeName(String newName) {
        this.name = newName;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Customer)) return false;
        Customer customer = (Customer) o;
        return Objects.equals(customerId, customer.customerId); // 唯 ID 是從
    }

    @Override
    public int hashCode() {
        return Objects.hash(customerId);
    }
}
```

## 2. Value Object (VO)

Value Object 是指不具備唯一識別碼，僅透過其 **「屬性」** 來定義的物件。

- **特性**：
    - **不可變性 (Immutable)**：一旦建立就不能修改其內部狀態。若要更換，必須重新建立一個新的。
    - **相等性 (Attribute equality)**：即便記憶體位置不同，只要內部的數值完全相同，它們就在邏輯上相等。
    - **側重描述**：VO 常被用來描述 Entity 的某個特徵。

- **Java 範例**：

```java
// 使用不可變物件表示貨幣與金額
public final class Money {
    private final BigDecimal amount;
    private final String currency;

    public Money(BigDecimal amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Money)) return false;
        Money money = (Money) o;
        return Objects.equals(amount, money.amount) && 
               Objects.equals(currency, money.currency); // 全部欄位都要相同
    }

    @Override
    public int hashCode() {
        return Objects.hash(amount, currency);
    }

    // 加法操作會傳回一個全新的物件
    public Money add(Money other) {
        return new Money(this.amount.add(other.amount), this.currency);
    }
}
```

## 3. Domain Service

當某個業務邏輯無法自然地歸屬於某個 Entity 或 VO 時，就會將其定義為 **Domain Service**。

- **特性**：
    - **無狀態 (Stateless)**：Service 僅負責操作而不儲存狀態。
    - **協調性**：負責協調多個實體或聚合根之間的互動。

- **範例場景**：
    - 涉及兩個帳戶的「轉帳邏輯」。
    - 呼叫外部 API 進行匯率轉換。

## 4. Module (Package)

在 Java 中透過 Package (包) 來實作。Module 的設計是用來組織領域模型，確保具有高度相關性的模型元素被聚集在一起。

- **設計原則**：
    - **高內聚 (Cohesion)**：放在同一個 Module 內的物件在領域概念上有強大的關聯。
    - **低耦合 (Low Coupling)**：Module 之間的依賴應盡可能簡單。
    - **語意命名**：Package 的命名應來自 **領域概念 (Ubiquitous Language)**，而非技術分類或技術層次。

- **常見誤區與建議**：
    - ❌ `com.app.domain.entities` 或 `com.app.domain.value_objects` (以技術類型分類，會造成概念混亂)。
    - ✅ `com.app.domain.order` 或 `com.app.domain.shipping` (以領域概念分類，將相關的 Entity, VO, Service 包在一起)。

## 5. 主要的開發範式 (The Predominant Paradigm)

在實作領域模型時，我們必須選擇一個與模型（Mental Model）匹配的 **開發範式 (Paradigm)**。若範式與模型不匹配，會導致「洩漏的抽象 (Leaky Abstractions)」，讓開發者被迫在程式碼中透過「技術手段」來補償模型與實作之間的鴻溝。

### 為什麼是物件導向 (OO)？

雖然 DDD 不限於特定的程式語言，但絕大多數的實務案例都採用 **物件導向 (Object-Oriented)** 範式。其核心優勢在於：

- **行為與狀態的合一**：OO 允許我們將資料與對應的業務規則封裝在一起，這與領域專家看待現實世界的方式一致。
- **建立邊界**：透過封裝，我們可以確保 Entity 的狀態變更是受控的，而非散落在系統各處。

### 範式誤區：貧血模型 (Anemic Domain Model)

最常見的錯誤是 **「名義上的 OO，實質上的程序化」**。開發者雖然使用了 Java/C# 等語言，卻將邏輯與資料拆分：

1. **Entity 淪為資料容器**：只有屬性與 Getter/Setter，不具備任何行為。
2. **邏輯外溢到 Service**：所有的驗證與計算都放在所謂的 `XxxService` 中，導致 Entity 變成了「貧血的 (Anemic)」。


!!! warning "警告：貧血模型"
    **貧血模型並非真正的 Domain Model**。它會導致系統邏輯散亂、復用性低，且難以維護領域的不變量（Invariants）。一個健康的 DDD 實作應致力於將邏輯放回 Entity 與 Value Object 中（即 Rich Domain Model）。

---

## 核心概念對比表

| 特性 | Entity | Value Object | Domain Service | Module (Package) |
| :--- | :--- | :--- | :--- | :--- |
| **識別碼** | 有唯一 ID | 無 ID，看屬性 | 無狀態，無 ID | N/A |
| **可變性** | 可變 (Mutable) | 不可變 (Immutable) | 無狀態 | N/A |
| **關注點** | 生命週期與連續性 | 屬性描述 | 流程與業務運算 | 組織、內聚與解耦 |
| **相等判斷** | ID 相同即相等 | 屬性全部相同即相等 | N/A | N/A |

---

## 該如何選擇？
1. **只要能做成 Value Object，就不要做成 Entity**。VO 能大幅減少並行開發時的副作用，且易於測試。
2. 只有當業務上需要追蹤某個東西的「歷史軌跡」或「變更狀態」時，才建立為 Entity。
3. 如果操作涉及多個物件且不屬於其中任何一個聚合，才考慮使用 Domain Service。
4. 當系統變得複雜時，使用 Module 依照領域邊界切分 Package，有助於降低認知負荷。
