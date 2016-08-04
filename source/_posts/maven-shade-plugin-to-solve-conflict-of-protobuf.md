title: maven shade plugin to solve conflict of denpendency
date: 2016-04-24 22:43:48
categories: Practice
tags:
    - java
    - maven
---
## 问题
针对我们的服务，我们提供出了java sdk供用户使用。但是经常收到用户反馈有关google protobuf的Exception。这种Exception一般都是是由于protobuf不同版本的兼容性造成，主要表现是找不到相应的Method或者Class。为了解决这个问题，我们想要源码去依赖protobuf的包，但是直接去把源码放在项目里又有点麻烦，经过调研，选用了shade plugin。

<!-- more -->

## 关于shade plugin
看官方wiki吧，我就不赘述了。用来解决冲突的大致原理，我理解感觉就是用shadePattern去把冲突的dependency package重命名，形成唯一的路径，并在打包时把相应源码打进去，然后在maven解依赖的时候就不会出现同名包冲突的问题。原理等我日后java知识精进了再来诌。
* <https://maven.apache.org/plugins/maven-shade-plugin/>

## 例子
* 重点关注下`pattern`和`shadePattern`这两个字段。
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>2.4.3</version>
    <executions>
        <execution>
        <phase>package</phase>
        <goals>
                <goal>shade</goal>
        </goals>
        </execution>
    </executions>
    <configuration>
    <!--合包去掉原来包的依赖关系， shade过的jar作为项目默认的包 -->
        <shadedArtifactAttached>false</shadedArtifactAttached>
        <!-- <promoteTransitiveDependencies>true</promoteTransitiveDependencies> -->
        <!-- <createDependencyReducedPom>true</createDependencyReducedPom> -->
        <!-- <keepDependenciesWithProvidedScope>false</keepDependenciesWithProvidedScope> -->
        <!-- 包含源代码发布jar -->
        <createSourcesJar>true</createSourcesJar>
        <relocations>
            <relocation>
                <pattern>com.google.protobuf</pattern>
                <shadedPattern>com.alibaba.etao.igraph.com.google.protobuf</shadedPattern>
            </relocation>
        </relocations>
        <artifactSet>
            <includes>
            <!-- 这里必须是groupID对应的空间名，包含-->
                 <include>com.google.protobuf</include>
            </includes>
        <!-- 这里是不包含 -->
        <excludes />
        </artifactSet>
    </configuration>
</plugin>
```