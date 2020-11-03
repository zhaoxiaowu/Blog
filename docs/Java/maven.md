### 1.spring-boot-maven-plugin

```
//mvn package 打包成直接运行的jar包
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>${spring.boot.version}</version>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal><!--如果你的POM不是继承spring-boot-starter-parent-->
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```