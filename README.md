## 專案介紹

這是一個 Spring Boot 3.x + MyBatis Generator + H2 資料庫的實戰專案，展示如何使用 MyBatis Generator 自動產生 Model、Mapper 介面和 XML 檔案。專案以線上咖啡館系統為背景，實作資料存取層的最佳實踐。

**主要學習目標：**
- 掌握 MyBatis Generator 的配置與使用
- 學會處理 H2 資料庫的主鍵自動產生問題
- 了解如何整合 Joda Money 處理金額資料型別
- 學習自訂 TypeHandler 處理複雜資料型別轉換
- 實踐相對路徑設定，提升專案可移植性

## 技術棧

本專案主要依賴以下軟體與工具：

### 核心框架
- **Spring Boot 3.4.5** - 現代化 Java 應用程式框架
- **MyBatis Spring Boot Starter 3.0.4** - MyBatis 與 Spring Boot 整合套件
- **MyBatis Generator 1.3.7** - 自動產生資料庫操作程式碼的工具

### 資料庫與工具
- **H2 Database 2.3.232** - 輕量級記憶體資料庫，適合開發與測試
- **Joda Money 1.0.1** - 處理貨幣與金額的專業函式庫
- **Lombok** - 減少 Java 樣板程式碼的工具

### 開發環境
- **Java JDK 21** - 使用最新的 Java 語言特性
- **Maven 3.8+** - 專案建置與依賴管理工具
- **IDE 推薦** - IntelliJ IDEA 或 Eclipse with Spring Tools

## 專案結構

```
mybatis-generator-demo/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── tw/fengqing/spring/data/mybatis/
│   │   │       ├── MybatisGeneratorDemoApplication.java  # 主程式入口點
│   │   │       ├── mapper/                               # 自動產生的 Mapper 介面
│   │   │       │   └── CoffeeMapper.java                 # 咖啡資料操作介面
│   │   │       ├── model/                                # 自動產生的 Model 類別
│   │   │       │   ├── Coffee.java                       # 咖啡實體類別
│   │   │       │   └── CoffeeExample.java                # 查詢條件類別
│   │   │       └── handler/                              # 自訂型別處理器
│   │   │           └── MoneyTypeHandler.java             # 金額型別處理器
│   │   └── resources/
│   │       ├── application.properties                    # Spring Boot 設定檔
│   │       ├── generatorConfig.xml                       # MyBatis Generator 設定檔
│   │       ├── schema.sql                                # 資料庫結構定義
│   │       └── mapper/                                   # 自動產生的 XML 檔案
│   │           └── tw/fengqing/spring/data/mybatis/mapper/
│   │               └── CoffeeMapper.xml                  # SQL 對應檔案
│   └── test/                                             # 測試程式碼
├── pom.xml                                               # Maven 專案設定檔
└── README.md                                             # 專案說明文件
```

### 重要檔案說明

- **MybatisGeneratorDemoApplication.java**: 主程式類別，包含 MyBatis Generator 執行邏輯和資料庫操作示範
- **generatorConfig.xml**: MyBatis Generator 核心設定檔，定義檔案產生規則和資料庫連線
- **application.properties**: Spring Boot 應用程式設定，包含 MyBatis 和資料庫相關配置
- **schema.sql**: H2 資料庫結構定義，包含 t_coffee 資料表建立語法

## 快速開始

1. 克隆此倉庫：
```bash
git clone {{GITHUB_REPO_URL}}
```

2. 進入專案目錄：
```bash
cd mybatis-generator-demo
```

3. 產生 MyBatis 檔案：
```bash
# 執行 MyBatis Generator 產生 Model、Mapper 和 XML 檔案
mvn clean compile exec:java -Dexec.mainClass="tw.fengqing.spring.data.mybatis.MybatisGeneratorDemoApplication"
```

4. 測試資料庫操作：
```bash
# 修改主程式中的 run 方法，切換到 playWithArtifacts() 模式
# 然後執行測試
mvn clean compile exec:java -Dexec.mainClass="tw.fengqing.spring.data.mybatis.MybatisGeneratorDemoApplication"
```

## 進階說明

### 環境變數
本專案使用 H2 記憶體資料庫，無需額外設定環境變數。若需連接外部資料庫，請在 `application.properties` 中設定：
```properties
# 外部資料庫連線設定範例
spring.datasource.url=jdbc:mysql://localhost:3306/coffee_shop
spring.datasource.username=your_username
spring.datasource.password=your_password
```

### 應用程式屬性設定
根據不同環境調整 `application.properties` 中的參數：

```properties
# MyBatis 相關設定
mybatis.mapper-locations=classpath*:/mapper/**/*.xml          # XML 檔案掃描路徑
mybatis.type-aliases-package=tw.fengqing.spring.data.mybatis.model  # Model 類別包路徑
mybatis.type-handlers-package=tw.fengqing.spring.data.mybatis.handler  # 型別處理器包路徑
mybatis.configuration.map-underscore-to-camel-case=true      # 下劃線轉駝峰命名

# H2 資料庫設定
spring.datasource.url=jdbc:h2:mem:testdb                     # 記憶體資料庫連線字串
spring.datasource.driver-class-name=org.h2.Driver            # 資料庫驅動程式
spring.datasource.username=sa                                # 資料庫使用者名稱
spring.datasource.password=                                  # 資料庫密碼
```

### 外部服務連接
本專案主要展示 MyBatis Generator 的使用，無需連接外部服務。若需整合其他服務，請在 `pom.xml` 中添加相關依賴：

```xml
<!-- 範例：整合 Redis 快取服務 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 核心程式碼解析

#### MyBatis Generator 設定檔 (generatorConfig.xml)
```xml
<!-- 資料表設定區塊 -->
<table tableName="t_coffee" domainObjectName="Coffee" >
    <!-- 主鍵自動產生設定：使用 JDBC 標準方式，適用於所有支援 JDBC 的資料庫 -->
    <generatedKey column="id" sqlStatement="JDBC" identity="true" />
    
    <!-- 自訂欄位型別處理器：將 BIGINT 型別轉換為 Joda Money 物件 -->
    <columnOverride column="price" javaType="org.joda.money.Money" jdbcType="BIGINT"
                    typeHandler="tw.fengqing.spring.data.mybatis.handler.MoneyTypeHandler"/>
</table>
```

#### 主程式核心邏輯 (MybatisGeneratorDemoApplication.java)
```java
/**
 * 執行 MyBatis Generator 產生 Model、Mapper 和 XML 檔案
 * 注意：使用相對路徑設定，不需要手動指定工作目錄
 */
private void generateArtifacts() throws Exception {
    log.info("開始執行 MyBatis Generator...");
    
    // 建立 MyBatis Generator 設定物件
    List<String> warnings = new ArrayList<>();
    ConfigurationParser cp = new ConfigurationParser(warnings);
    Configuration config = cp.parseConfiguration(
            this.getClass().getResourceAsStream("/generatorConfig.xml"));
    DefaultShellCallback callback = new DefaultShellCallback(true);
    MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
    
    // 執行檔案產生，使用 generatorConfig.xml 中的相對路徑設定
    myBatisGenerator.generate(null);
    
    // 輸出執行結果和警告訊息
    log.info("MyBatis Generator 執行完成，共有 {} 個警告", warnings.size());
    for (String warning : warnings) {
        log.warn("警告: {}", warning);
    }
}

/**
 * 測試資料庫操作：插入和查詢咖啡資料
 */
private void playWithArtifacts() {
    // 建立 espresso 咖啡資料並插入資料庫
    Coffee espresso = new Coffee()
            .withName("espresso")
            .withPrice(Money.of(CurrencyUnit.of("TWD"), 100.0))  // 使用台幣
            .withCreateTime(new Date())
            .withUpdateTime(new Date());
    coffeeMapper.insert(espresso);  // 執行插入操作

    // 建立 latte 咖啡資料並插入資料庫
    Coffee latte = new Coffee()
            .withName("latte")
            .withPrice(Money.of(CurrencyUnit.of("TWD"), 150.0))  // 使用台幣
            .withCreateTime(new Date())
            .withUpdateTime(new Date());
    coffeeMapper.insert(latte);  // 執行插入操作

    // 根據主鍵查詢資料
    Coffee s = coffeeMapper.selectByPrimaryKey(1L);
    log.info("查詢到的咖啡: {}", s);

    // 使用條件查詢：找出名稱為 "latte" 的咖啡
    CoffeeExample example = new CoffeeExample();
    example.createCriteria().andNameEqualTo("latte");
    List<Coffee> list = coffeeMapper.selectByExample(example);
    list.forEach(e -> log.info("條件查詢結果: {}", e));
}
```

## 參考資源

- [Spring Boot 官方網站](https://spring.io/projects/spring-boot) - Spring Boot 官方文件與教學
- [MyBatis Generator 官方文件](http://mybatis.org/generator/) - MyBatis Generator 詳細說明
- [H2 Database 官方文件](https://www.h2database.com/html/main.html) - H2 資料庫使用指南
- [start.spring.io](https://start.spring.io/) - Spring Boot 專案快速建立工具
- [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html) - 應用程式監控與管理
- [Joda Money 官方文件](https://www.joda.org/joda-money/) - 貨幣處理函式庫文件

## 注意事項

### 開發階段注意事項
1. **檔案產生順序**：必須先執行 `generateArtifacts()` 產生檔案，再執行 `playWithArtifacts()` 測試
2. **主鍵設定**：務必使用 `sqlStatement="JDBC"`，避免 H2 2.x 版本的語法相容性問題
3. **相對路徑**：使用相對路徑設定，確保專案在不同環境下都能正常運作

### 常見問題排解
- **編譯錯誤 "找不到 CoffeeMapper"**：先執行 `generateArtifacts()` 產生檔案
- **SQL 語法錯誤 "Function IDENTITY not found"**：確認使用 `sqlStatement="JDBC"`

### 效能考量
- 使用 H2 記憶體資料庫適合開發與測試，正式環境建議使用 MySQL 或 PostgreSQL
- MyBatis Generator 產生的檔案會覆蓋現有檔案，請注意備份重要修改

## 授權說明
本專案採用 MIT 授權條款，詳見 LICENSE 檔案。