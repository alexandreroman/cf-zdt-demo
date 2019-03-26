# Deploy apps to Cloud Foundry with zero downtime

This project shows
[how to use Cloud Foundry to roll out apps with no downtime](https://docs.cloudfoundry.org/devguide/deploy-apps/rolling-deploy.html).
Using CF CLI, the platform takes care of publishing new app versions, with
a single command, with no app downtime.

## How to use it?

Compile this app using a JDK 8:
```bash
$ ./mvnw clean package
```

This app is made of a single REST controller:
```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

@RestController
class ZDT {
    @GetMapping("/")
    String zeroDowntime() {
        return "V1";
    }
}
```

Publish this app to Cloud Foundry:
```bash
$ cf push
```

Check that the app is up and running:
```bash
$ curl cf-zdt-demo-XXX.cfapps.io
V1%
```

## Deploy a new version with no app downtime

Update the app source code:
```java
@RestController
class ZDT {
    @GetMapping("/")
    String zeroDowntime() {
        return "V2";
    }
}
```

Compile this new version:
```bash
$ ./mvnw clean package
```

Use CF CLI to publish this update:
```bash
$ cf v3-zdt-push cf-zdt-demo -p target/cf-zdt-demo.jar --wait-for-deploy-complete
```

This command waits for deployment to finish.

While the app is being deployed to the platform,
monitor app availability by hitting the REST endpoint:
```bash
$ watch curl cf-zdt-demo-XXX.cfapps.io
Every 2.0s: curl -s cf-zdt-demo-XXX.cfapps.io         Caprica.local: Tue Mar 26 15:49:04 2019
V2
```

## Contribute

Contributions are always welcome!

Feel free to open issues & send PR.

## License

Copyright &copy; 2019 [Pivotal Software, Inc](https://pivotal.io).

This project is licensed under the [Apache Software License version 2.0](https://www.apache.org/licenses/LICENSE-2.0).
