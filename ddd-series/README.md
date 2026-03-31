# DDD 實踐與概念解析系列

這是我關於 Domain-Driven Design (領域驅動設計) 的探索與實作梳理，希望透過有系統地整理核心概念與設計圖（如 UML、Sequence Diagram 等），逐步拼湊出 DDD 的全貌。

## 目錄與章節規劃

### 01 核心概念 (Core Concepts)
> *(預留區)* 包含 DDD 的基石知識，例如「通用語言 (Ubiquitous Language)」、「領域 (Domain)」、「子域 (Subdomain)」等概念討論。

### 02 架構與分層 (Architecture)
探討「領域邏輯應該放在系統的哪裡」。好的架構是落實 DDD 的載體，保護核心業務不受外部技術干擾。
- [01 從時序圖解析 Domain Model 的互動邊界與職責](./02-architecture/01-domain-interaction-boundaries.md)

### 03 戰術設計 (Tactical Design)
探討在程式碼層面落實 DDD 的具體元件與設計模式 (Building Blocks)。
> *(預留區)* 包含 Entity, Value Object, Aggregate, Repository, Factory, Domain Service, Domain Event 等實作細節。

### 04 戰略設計 (Strategic Design)
當系統變大，跨團隊合作與微服務劃分時，如何拆解與映射複雜系統。
> *(預留區)* 包含 Bounded Context (限界上下文)、Context Mapping、防腐層 (ACL) 等宏觀規劃。

---
> 此系列持續更新中...
