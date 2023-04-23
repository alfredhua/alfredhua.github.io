## docker 下环境搭建

```java
.env文件:

CONSUL_IDR=./
  
docker-compose.yml文件：
  
version: '3'
services:
  consul:
    image: consul:latest
    container_name: consul
    restart: always
    privileged: true
    volumes:
      - ${CONSUL_IDR}/data:/consul/data
      - ${CONSUL_IDR}/config:/consul/config
    ports:
      - 8300:8300
      - 8301:8301
      - 8301:8301/udp
      - 8302:8302
      - 8302:8302/udp
      - 8400:8400
      - 8500:8500
      - 53:53/udp
    command: agent -server -bind=0.0.0.0 -client=0.0.0.0 -node=consul_Server1 -bootstrap-expect=1 -ui
```

## 注册服务中心

[github地址](https://github.com/alfredhua/spring-cloud/tree/master/consul):  https://github.com/alfredhua/spring-cloud/tree/master/consul

引入依赖

```java

   dependencies{
        compile group: 'org.springframework.cloud', name: 'spring-cloud-starter-consul-discovery', version: '2.2.3.RELEASE'
        compile group: 'org.springframework.boot', name: 'spring-boot-starter-actuator', version: '2.2.3.RELEASE'
        compile group: 'org.springframework.boot', name: 'spring-boot-starter-web', version: '2.2.3.RELEASE'
    }
```

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
@RestController
public class WebSiteServerApplication {
    public static void main(String[] args){
        SpringApplication.run(WebSiteServerApplication.class);
    }
  
    @RequestMapping("/{name}")
    public String home(@PathVariable("name") String name) {
        return "Hello "+name;
    }
}

```

消费者：

```java
package com.consume.controller;

import com.consume.config.UrlConfig;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class TestController {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    UrlConfig urlConfig;
  
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

    @GetMapping("/getByUsername")
    public String getByUsername() {
        return restTemplate.getForObject(urlConfig.getWebsiteUrl() + "/{1}",String.class,"张三");
    }

}


package com.consume;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;


@SpringBootApplication
@EnableDiscoveryClient
public class ConsumeApplication {

    public static void main(String[] args){
        SpringApplication.run(ConsumeApplication.class);

    }
}


package com.consume.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableConfigurationProperties
public class UrlConfig {

    @Value("${service-url.website}")
    private String websiteUrl;

    public String getWebsiteUrl() {
        return websiteUrl;
    }

    public void setWebsiteUrl(String websiteUrl) {
        this.websiteUrl = websiteUrl;
    }
}



```

