---
title: Použití zprostředkovatele Open Service Broker pro službu Azure database s aplikací na Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/18/2019
ms.topic: tutorial
description: Vysvětluje, jak nakonfigurovat aplikaci Pivotal Cloud Foundry pro použití zprostředkovatele Open Service Broker pro databázi Azure
keywords: Pivotal Cloud Foundry, Cloud Foundry, otevřete Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: cbaf1bc65950abb88630e90a62c4c1f5a8d3e7ec
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244082"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Kurz: Použití zprostředkovatele Open Service Broker pro službu Azure database s aplikací na Pivotal Cloud Foundry

Použití zprostředkovatele Open Service Broker for Azure s Pivotal Cloud Foundry instance vám umožní k poskytování služeb, jako je databáze v Azure přímo z rozhraní příkazového řádku Cloud Foundry a vaše instance Pivotal Cloud Foundry. Tyto služby můžete také navázat k aplikaci spuštěné v instanci Pivotal Cloud Foundry. Když se navážete aplikace ke službě tímto způsobem, není nutné aktualizovat kód ani konfigurace v rámci vaší aplikace. Tento článek vysvětluje, jak používat Open Service Broker pro služby Azure pro databázi s aplikací v Pivotal Cloud Foundry.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte váš prostor aplikace v Pivotal Cloud Foundry.
> * Klonovat zdroj aplikace ukázky z Githubu.
> * Příprava aplikací pro nasazení.
> * Nasazení aplikace.
> * Vytvoření databáze pomocí zprostředkovatele Open Service Broker for Azure.
> * Vytvoření vazby databáze do vaší aplikace.

## <a name="prerequisites"></a>Požadavky

Abyste mohli pokračovat, musíte mít:

* [Máte nainstalovanou a nakonfigurovanou Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md)
* [Mají zprostředkovatele Open Service Broker pro Azure nainstalovaný a nakonfigurovaný](https://network.pivotal.io/products/azure-open-service-broker-pcf) s vaší instancí Cloud Foundry

Tady je příklad Pivotal Cloud Foundry Ops Manager obrazovky s Open Service Broker for Azure dlaždice, instalaci a konfiguraci:

![Pivotal Cloud Foundry s Open Service Broker for Azure nainstalovaný](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Připravte váš prostor aplikace v Pivotal Cloud Foundry

Pokud chcete nasadit aplikaci do vaší instance Pivotal Cloud Foundry, je potřeba s přihlášením `cf` nástroj příkazového řádku. Také musíte mít požadovaný organizace a místa na cílové.

K ověření přihlášeni a zobrazit místa, na které cílíte, použijte `cf target`. Následující příklad zobrazuje již přihlášeni jako uživatel *správce*, použije *myorg* organizace a cílení *dev* místa:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Chcete-li přihlásit, použijte `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Chcete-li vytvořit novou organizaci a místa, použijte `cf create-org` a `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Chcete-li cílit na místo, použijte `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Získání kódu aplikace

Pro aplikace pro použití zprostředkovatele Open Service Broker for Azure, aplikace musí používat jako zdroj dat, například do databáze. V tomto článku budeme používat [Hudba ukázkovou aplikaci spring z Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) předvést, který používá zdroj dat aplikace.

Klonování aplikace z Githubu a přejděte do jeho adresář:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Chcete-li zobrazit zdroje dat pro tuto aplikaci, otevřete [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) souboru.


## <a name="prepare-the-application-for-deployment"></a>Příprava aplikací pro nasazení

Před nasazením aplikace k vaší instanci Pivotal Cloud Foundry, budete muset sestavit. Pro účely Demonstrativní, také umožňujeme některé *ladění* protokolování, které se budou protokolovat informace o připojení zdroje dat.

Povolit *ladění* protokolování pro podrobnosti připojení k databázi, přidejte níže yaml vlastnost na konec *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

Ukázková aplikace používá gradle k sestavení aplikace Spring Boot spustitelný soubor jar. K vaší instanci Pivotal Cloud Foundry nasadí se spustitelný soubor jar. K sestavení aplikace:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Nasazení aplikace

Použití `cf push` příkaz pro nasazení aplikace do instance služby Pivotal Cloud Foundry:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Zkopírujte hodnotu z *trasy* zobrazí ve z `cf push`. Trasy je adresa URL bude používat pro přístup k běžící aplikaci. Příklad:

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Po nasazení aplikace se zobrazí protokoly aplikace zobrazíte adresy URL pro připojení v aplikaci použít. Následující příkaz zobrazí protokoly aplikace a používá `grep` pro hledání *jdbcUrl* konfigurace.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Všimněte si, že aplikace používá *h2:mem:testdb*, což je databáze v paměti. Aplikace Spring se automaticky nakonfiguruje používat databázi v paměti je databázi v paměti závislost na cesty pro třídy a [automatickou konfiguraci](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) je povolená. Ukázková aplikace má [závislost databáze v paměti h2 nakonfigurované](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) a povolená v konfiguraci automatického [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Přejděte v prohlížeči pomocí postupu vaší aplikace. Ve výstupu se zobrazí trasu nebo adresu URL, `cf push` příkazu.

> [!TIP]
> Adresa URL vaší aplikace můžete také zobrazit spuštěním `cf apps`.

Až přejdete k aplikaci pomocí prohlížeče, pracovat s ním odstraněním několik existujících alba a vytvořením několika nových. Ukázková aplikace používá databázi v paměti uložte provedené změny. Také si povšimněte aplikace byly zadány s některými [výchozí data](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Aplikace Music Spring s výchozími daty](media/music-app.png)

Vzhledem k tomu, že vaše aplikace používá databázi v paměti, nebude provedené změny trvalé, pokud vaše aplikace je restartován nebo znovu nasadit. Chcete-li zobrazit, že se změny trvalé, po provedení některé změny, restage aplikace pomocí `cf restage`:

```cmd
cf restage spring-music
```

Jakmile aplikace má byla racované, přejděte v prohlížeči pomocí stejné adresy URL na ni. Všimněte si, že provedené změny jsou pryč a zobrazí se výchozí data.

![Aplikace Music Spring s výchozími daty](media/music-app.png)

## <a name="create-a-database"></a>Vytvoření databáze

Chcete-li vytvořit trvalé databáze v Azure pomocí zprostředkovatele Open Service Broker, použijte `cf create-service` příkazu. Následující příkaz zřizuje databázi PostgreSQL v Azure ve skupině prostředků *MyResourceGroup* v *eastus* oblasti. Další informace o *resourceGroup*, *umístění*, a je k dispozici v dalších parametrů JSON týkající se Azure [PostgreSQL modulu referenční dokumentaci](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

Databáze je ve vaší instanci Pivotal Cloud Foundry vystavený jako služba s názvem *mypgsql*. Po databázi dokončení zřizování, který by měl trvat několik minut, lze vazbu vytvořit aplikaci. K ověření vaší databáze byla dokončena, zřizování, použijte `cf services` příkaz:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

*Poslední operace* hodnota bude vaše služba *vytvořila* po jeho dokončení zřizování.

## <a name="bind-the-database-to-your-application"></a>Vytvoření vazby databáze do vaší aplikace

Použití `bind-service` příkaz k vytvoření vazby službu do vaší aplikace.

```cmd
cf bind-service spring-music mypgsql
```

Po vytvoření vazby službu do aplikace, budete muset restage aplikace, aby se změny projevily.

```cmd
cf restage spring-music
```

Aplikace je [nakonfigurován na použití aplikace Spring Cloud konektory](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), umožňuje používat instanci Pivotal Cloud Foundry [automaticky Rekonfigurace](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) na zdroj dat vaší aplikace. V takovém případě vaší instance Pivotal Cloud Foundry automaticky překonfiguruje aplikace pro používání služby jeho mez pro zdroj dat při racované je vaše aplikace.

Jakmile se vaše aplikace má byla racované, se bude používat *mypgsql* pro ukládání dat, ne na databázi v paměti.

Provedené změny budou nyní zachován po restartování a znovu nasadí. Uvidíte také, že adresy URL pro připojení aplikace používá zobrazením protokolů aplikace znovu.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Všimněte si, že se dvě položky:

* Původní hodnota *h2:mem:testdb*.
* Aktualizovaná hodnota pro vaši databázi PostgreSQL, když vaše aplikace byla racované.

Pokud chcete ověřit, že je se data ukládají do databáze PostgreSQL, přejděte k aplikaci v prohlížeči, udělat nějaké změny a pak restage aplikace:

```cmd
cf restage spring-music
```

Jakmile aplikace má byla racované, přejděte v prohlížeči pomocí stejné adresy URL na ni. Všimněte si, že provedené změny jsou stále existuje.

## <a name="cleanup"></a>Vyčištění

Pokud chcete se odpojit aplikaci z databáze, můžete ji pomocí odpojit `cf unbind-service` příkazu.

```cmd
cf unbind-service spring-music mypgsql
```

Podobně jako u vazby aplikace ke službě se vám musí restage aplikace pro tyto změny projevily. Tato akce zachová i *mypgsql* a vaše aplikace beze změny, ale vaše aplikace bude začít používat databázi v paměti namísto *mypgsql*.

Pokud chcete odstranit vaši databázi, můžete použít `cf delete-service` příkazu. *Nelze vrátit zpět, tato akce proto buďte Opravdu chcete odstranit vaši databázi, než budete pokračovat.*

```cmd
cf delete-service mypgsql
```

Postup odebrání aplikace z vaší instance Pivotal Cloud Foundry:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Další postup

V tomto kurzu zahrnutých v nasazení aplikace do Pivotal Cloud Foundry, stejně jako vytvoříte databázi pomocí zprostředkovatele Open Service Broker for Azure. Zahrnuté také vazby databáze do vaší aplikace v rámci vaší instance Pivotal Cloud Foundry. Další informace o nasazení aplikací na Cloud Foundry v Azure najdete v tématu:

* [Cloud Foundry v Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Nasazení první aplikace pro Cloud Foundry v Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)