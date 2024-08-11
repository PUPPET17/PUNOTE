# SpringBoot

## Maven 插件

- maven 打 jar 包找不到主类
  ```terminal
      <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.6</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <!-- 修改为自己主类的全限定名 -->
                            <mainClass>org.example.Main</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
  ```
- maven 打包跳过测试
  ```terminal
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.6</version>
    <configuration>
        <skipTests>true</skipTests>
        <testFailureIgnore>false</testFailureIgnore>
            <includes>
                <include>**/*Test.java</include>
            </includes>
        </configuration>
  </plugin>
  ```

## 依赖重复导入导致冲突
- 使用 `mvn dependency:tree` 命令来查看依赖树
- 找到冲突后添加 `<exclusions>` 标签将不想要的依赖从包中排除单独导入，比如：
  ```terminal
        <dependency>
            <groupId>com.tencentcloudapi</groupId>
            <artifactId>tencentcloud-sdk-java</artifactId>
            <version>3.1.916</version>
            <exclusions>
                <exclusion>
                    <groupId>com.squareup.okhttp3</groupId>
                    <artifactId>okhttp</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>okhttp</artifactId>
            <version>3.14.7</version>
        </dependency>
  ```
- 还可以使用 `<dependencyManagement>` 标签来统一指定版本号
  ```terminal
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.squareup.okhttp3</groupId>
                <artifactId>okhttp</artifactId>
                <version>4.12.0</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
  ```
## 分布式ID
 ### 雪花算法

    雪花算法（Snowflake Algorithm）是由 Twitter 开发的分布式唯一 ID 生成算法。它的设计目标是生成全局唯一且有序的 ID，这对于分布式系统中的数据一致性和数据库分片等场景非常重要。以下是雪花算法的详细介绍：

  #### 1. 组成

    雪花算法生成的 ID 通常是一个 64 位的整数，具体组成如下：

    - **符号位（1 位）**: 用于表示数字的正负，通常为 0，表示正数。
    - **时间戳（41 位）**: 表示时间戳，精确到毫秒。41 位可以表示约 69 年的时间跨度（2^41 / 1000 / 60 / 60 / 24 / 365 ≈ 69 年）。
    - **数据中心 ID（5 位）**: 表示数据中心的 ID，用于区分不同的数据中心。可以支持最多 32 个数据中心。
    - **机器 ID（5 位）**: 表示机器的 ID，用于区分同一数据中心中的不同机器。可以支持最多 32 台机器。
    - **序列号（12 位）**: 表示同一毫秒内生成的 ID 的序列号，解决同一时间戳下生成多个 ID 的冲突问题。同一毫秒内生成最多 4096 个不同 ID。

  #### 2. 算法原理

    雪花算法的生成过程如下：

    1. **获取当前时间戳**: 使用系统时钟获取当前的时间戳，精确到毫秒。
    2. **生成唯一的 ID**: 将时间戳、数据中心 ID、机器 ID 和序列号组合成一个 64 位的整数。
       - 时间戳部分：将当前时间戳减去一个固定的起始时间（epoch），并移位到合适的位置。
       - 数据中心 ID 和机器 ID 部分：将数据中心 ID 和机器 ID 移位并与时间戳部分组合。
       - 序列号部分：在同一时间戳下，序列号递增以确保唯一性。
    3. **处理溢出**: 如果在同一毫秒内生成了超过 4096 个 ID，则需要等待下一毫秒。

  #### 3. 优缺点

    - 优点：

      - **高性能**: 可以在分布式系统中快速生成唯一 ID，无需网络通信。
      - **有序性**: 生成的 ID 是有序的，对于数据库索引和排序很有用。
      - **高可扩展性**: 支持分布式部署，数据中心和机器的 ID 可以灵活配置。

    - 缺点：

      - **时间依赖**: 如果系统时钟回退，可能会导致 ID 冲突或生成的 ID 不唯一。通常需要配置时间回退处理机制。
      - **时间戳限制**: 时间戳部分的长度限制了 ID 的有效时间跨度（约 69 年）。
      - **配置复杂性**: 需要合理配置数据中心 ID 和机器 ID，确保在分布式系统中唯一性。
  
  #### 4. 时间回溯
    时间回退: 如果服务器的系统时钟发生了回退（比如因为网络时间协议（NTP）校准或者手动调整时间），可能导致在同一毫秒内生成多个相同时间戳的 ID。在这种情况下，雪花算法会生成重复的 ID，因为它依赖于时间戳来确保唯一性。

    - 解决策略:

      - 时间回退检测: 在雪花算法的实现中，通常会检测系统时间是否回退。如果检测到时间回退，系统可以采取一些措施，比如等待时间恢复到正常状态后再继续生成 ID，或者丢弃当前时间段内的 ID。
      - 回退保护: 设定一个合理的时间回退保护窗口。比如，设置一个时间戳回退的最大容忍范围，当检测到时间回退时，系统会暂停生成新的 ID，直到时间戳回到正常范围。
      - 唯一性保证机制: 采用其他的唯一性保证机制，比如引入节点 ID、机器 ID 或者使用数据库的自增 ID 作为补充，以减少重复的可能性。

    - 常见处理方式:

      - 等待和重试: 如果检测到时间回退，系统可以选择等待一段时间，并重试 ID 生成，直到时间回到正常范围。
      - 增加序列号: 在同一毫秒内，使用更多的序列号位数来进一步确保唯一性。
      - 日志记录: 记录生成的 ID 和时间戳，以便在出现重复时进行日志分析和问题追踪。