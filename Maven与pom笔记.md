# Maven 与 pom.xml 结构笔记

## 一、Maven 是什么

Java 的**依赖管理 + 项目构建工具**。你在 `pom.xml` 里登记要用的第三方库（采购清单），Maven 自动去仓库下载并放进项目。编译、打包、测试也都用它。

- Maven 世界里每个项目/库都有一套**全球唯一身份证**（坐标）
- 你引别人的库 = 用坐标点名要谁
- 默认从 Maven 中央仓库下载，国内配了**阿里云镜像**就快

---

## 二、Maven 项目标准结构

```
项目名/
├── src/
│   ├── main/
│   │   ├── java/        ← Java 源码放这里 ⭐
│   │   └── resources/   ← 配置文件放这里（application.yml 等）
│   └── test/
│       └── java/        ← 测试代码放这里
├── pom.xml              ← Maven 配置文件，最重要！⭐⭐⭐
└── target/              ← 编译产物（.class）自动生成在这里
    └── classes/         ← 编译后的字节码
```

- 写代码 → `src/main/java`
- 引依赖、配项目 → 改 `pom.xml`
- 编译产物 → `target/classes`（别手动改，自动生成）

---

## 三、pom.xml 三大核心块

### ① 根元素 + 模型版本（照抄，不动）
```xml
<project xmlns="..." xsi:schemaLocation="...">
  <modelVersion>4.0.0</modelVersion>   <!-- POM模型版本，固定 4.0.0 -->
```

### ② 项目坐标（全球身份证）⭐
```xml
<groupId>org.example</groupId>      <!-- 组织/团队（反写域名）-->
<artifactId>mvnDemo</artifactId>     <!-- 项目名 -->
<version>1.0-SNAPSHOT</version>      <!-- 版本（SNAPSHOT=开发中）-->
<packaging>jar</packaging>          <!-- 打包方式：jar=普通Java程序，war=Web应用 -->
```
**groupId + artifactId + version = 全球唯一身份证**。引别人库就是用这套坐标点名。

### ③ 依赖列表（采购清单）⭐⭐⭐ 写 SpringBoot 改最多的地方
```xml
<dependencies>
  <dependency>
    <groupId>junit</groupId>        <!-- 库的组织 -->
    <artifactId>junit</artifactId>   <!-- 库的名字 -->
    <version>3.8.1</version>        <!-- 库的版本 -->
    <scope>test</scope>             <!-- 使用范围 -->
  </dependency>
</dependencies>
```
- 每个依赖自己也有一套坐标
- `<scope>` 常见值：
  - `test`：只在测试时用，不打包进最终程序（如 junit）
  - `compile`（默认）：编译和运行都要用
  - `provided`：编译要，运行时环境已提供（不打包）
  - `runtime`：运行时需要，编译不需要（如 JDBC 驱动）

---

## 四、常用 Maven 命令（在项目根目录敲）

| 命令 | 作用 |
|---|---|
| `mvn clean` | 清理 target 目录 |
| `mvn compile` | 编译 src/main/java → target/classes |
| `mvn test` | 编译并跑测试 |
| `mvn package` | 编译+测试+打包成 jar/war |
| `mvn install` | 打包并装进本地仓库（~/.m2/repository） |
| `mvn clean package` | 清理后重新打包（最常用组合） |

> IDEA 右侧 Maven 面板里双击 Lifecycle 的对应项 = 执行这些命令，不用手敲。

---

## 五、关键概念速记

- **坐标**：groupId + artifactId + version，Maven 世界的身份证
- **pom.xml**：项目户口本 + 采购清单
- **依赖(dependency)**：在 `<dependencies>` 里登记的第三方库
- **镜像(mirror)**：把中央仓库的下载转到国内源（阿里云），加速
- **本地仓库**：`~/.m2/repository`，下载过的依赖缓存这里，下次直接用
- **archetype**：项目模板，选不同 archetype 生成不同结构（如 quickstart 生成简单 Java 项目）

---

## 六、以后写 SpringBoot 时会怎么用

1. 在 `<dependencies>` 加 SpringBoot 的 starter 依赖（如 `spring-boot-starter-web`）
2. 加父 POM（`<parent>`，统一管理版本号）
3. 加插件（`<build><plugins>`，如 spring-boot-maven-plugin 用于打包可执行 jar）
4. 写代码在 `src/main/java`，配置在 `src/main/resources/application.yml`
5. `mvn clean package` 打包，`java -jar` 运行

这些到阶段 2 学 SpringBoot 时会具体讲。现在只要记住：**pom.xml 的 `<dependencies>` 是你以后最常改的地方**。
