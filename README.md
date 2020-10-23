# Spring Cloud Hoxton版本的demo

[![build status](https://img.shields.io/badge/build-Hoxton-red)]()
[![jdk](https://img.shields.io/badge/jdk-1.8-green)]()
[![mybatis--plus](https://img.shields.io/badge/mybatis--plus-3.4.0-green)](https://mp.baomidou.com/guide/)
[![spring--boot](https://img.shields.io/badge/spring--boot-2.3.3-green)]()
[![spring--cloud](https://img.shields.io/badge/spring--cloud-Hoxton.SR8-green)]()
[![spring--cloud](https://img.shields.io/badge/注册中心-eureka-green)]()
[![spring--cloud](https://img.shields.io/badge/mysql-8.0-green)]()

### 根项目依赖锁定

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.3.3.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>fastjson</artifactId>
                <version>1.2.73</version>
            </dependency>
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>3.4.0</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>8.0.21</version>
            </dependency>
            <dependency>
                <groupId>org.apache.commons</groupId>
                <artifactId>commons-pool2</artifactId>
                <version>2.8.1</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    

## 一、eureka注册中心

#### 添加maven依赖

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.netflix.ribbon</groupId>
                    <artifactId>ribbon-eureka</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
        </dependency>
    </dependencies>
 
#### 创建启动类

    @EnableEurekaServer
    @SpringBootApplication
    public class EurekaApplication {
    
        public static void main(String[] args) {
            new SpringApplicationBuilder(EurekaApplication.class).web(WebApplicationType.SERVLET).run(args);
        }
    }
#### application.yml配置文件
> 单机版配置

    server:
      port: 8761
    eureka:
      instance:
        hostname: localhost
      client:
        registerWithEureka: false
        fetchRegistry: false
        serviceUrl:
          defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
          
### 注册服务到eureka中

#### 添加maven依赖
 
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--eureka注册中心客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.netflix.ribbon</groupId>
                    <artifactId>ribbon-eureka</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--Spring Cloud LoadBalancer is currently working with the default cache. You can switch to using Caffeine cache, by adding it to the classpath.-->
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
        </dependency>
    </dependencies>

#### 创建启动类

    @EnableDiscoveryClient
    @SpringBootApplication
    public class UserApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(UserApplication.class, args);
        }
    }

controller、service等代码此处省略    

访问：`http://127.0.0.1:8761/` 查看是否注册成功

由于 Ribbon load-balancer 现在处于维护模式，spring官方建议使用Spring Cloud LoadBalancer；在配置文件中添加如下配置：

    spring.cloud.loadbalancer.ribbon.enabled=false

eureka注册中心客户端的依赖中排除如下依赖

    <!--eureka注册中心客户端-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
            </exclusion>
            <exclusion>
                <groupId>com.netflix.ribbon</groupId>
                <artifactId>ribbon-eureka</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

关于`Spring Cloud LoadBalancer is currently working with the default cache. You can switch to using Caffeine cache, by adding it to the classpath.` 的警告添加如下依赖即可：

    <dependency>
        <groupId>com.github.ben-manes.caffeine</groupId>
        <artifactId>caffeine</artifactId>
    </dependency>

## 二、网关

### 添加maven依赖

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <!--eureka注册中心客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.netflix.ribbon</groupId>
                    <artifactId>ribbon-eureka</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
        </dependency>
    </dependencies>

### 添加application配置

    server:
      port: 9000
    spring:
      application:
        name: gateway-server
      cloud:
        gateway:
          discovery:
            locator:
              enabled: true
          routes:
            - id: user-server
              uri: lb://user-server
              predicates:
                - Path=/user/**
        loadbalancer:
          ribbon:
            enabled: false
    eureka:
      client:
        serviceUrl:
          defaultZone: http://localhost:8761/eureka/

### 创建启动类

    @EnableDiscoveryClient
    @SpringBootApplication
    public class GatewayApplication {
    
        public static void main(String[] args) {
            new SpringApplicationBuilder(GatewayApplication.class).web(WebApplicationType.REACTIVE).run(args);
        }
    }

启动所有服务验证是否成功：`http://127.0.0.1:9000/user/base?id=1`

### 网关鉴权

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-pool2</artifactId>
    </dependency>

> @Configuration 类似以前写在xml中配置bean，可以认为这就是一个xml配置文件

## 三、常用中间件

### elasticsearch搜索

spring文档：https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>

根据关键词查询站点信息：

    @Autowired
    private ElasticsearchRestTemplate elasticsearchRestTemplate;
    
    public Flux<TrainStationNameDTO> findTrainStationName(String keywords) {

        BoolQueryBuilder boolQuery = new BoolQueryBuilder();
        QueryBuilder cnNameQuery = new PrefixQueryBuilder("cnName.keyword", keywords);
        boolQuery.should(cnNameQuery);
        QueryBuilder pinyinQuery = new MatchPhrasePrefixQueryBuilder("pinyin", keywords);
        boolQuery.should(pinyinQuery);
        Query query = new NativeSearchQuery(boolQuery);
        return Flux.fromIterable(elasticsearchRestTemplate.search(query, TrainStationNameDTO.class)).map(SearchHit::getContent);
    }

或者：

    @Component
    @Document(indexName = "train_station_name")
    public interface TrainStationNameRepository extends ElasticsearchRepository<TrainStationNameDTO, Long> {
    
        /**
         * 查询以keywords开头的站点信息
         * @param cnName 中文
         * @param pinyin 拼音
         * @return 返回结果
         */
        List<TrainStationNameDTO> findByCnNameStartingWithOrPinyinStartingWith(String cnName, String pinyin);
    
    }    

    
       

          