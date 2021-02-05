---
title: Nastavení přípravného prostředí v Azure jaře cloudu | Microsoft Docs
description: Naučte se používat nasazování s modrou zelenou službou Azure Pramenitého cloudu
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 82a8da9d2663b03d89ad0819ec6d918bebaf5f5e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574722"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Nastavení přípravného prostředí v Azure jaře cloudu

**Tento článek se týká:** ✔️ Java

Tento článek vysvětluje, jak nastavit pracovní nasazení pomocí modelu nasazení Blue-zelený ve jarním cloudu Azure. Nasazení s modrou zeleným prostředím je model průběžného doručování Azure DevOps, který se spoléhá na udržení stávající (modré) verze živě, zatímco je nasazená nová (zelená) jedna. V tomto článku se dozvíte, jak umístit toto pracovní nasazení do produkčního prostředí beze změny produkčního nasazení.

## <a name="prerequisites"></a>Požadavky

* Instance Azure jaře cloudu na **cenové úrovni** *Standard* .
* [Cloudové rozšíření](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) Azure CLI Azure pro jaře

V tomto článku se používá aplikace vytvořená z inicializátoru pružiny. Pokud chcete pro tento příklad použít jinou aplikaci, budete muset provést jednoduchou změnu v rámci veřejné části aplikace, aby se vaše pracovní nasazení lišilo od výroby.

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů v tomto článku.  Má běžné, předem instalované nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com).  Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md).

Pokud chcete nastavit Blue-zelená nasazení v Azure jaře cloudu, postupujte podle pokynů v dalších částech.

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Nainstalujte rozšíření Azure jaře Cloud pro Azure CLI pomocí následujícího příkazu:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Příprava aplikací a nasazení
K sestavení aplikace použijte následující postup:
1. Vygenerujte kód pro ukázkovou aplikaci pomocí inicializátoru pružiny s [touto konfigurací](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Stáhněte si kód.
3. Do složky přidejte následující zdrojový soubor HelloController. Java `\src\main\java\com\example\hellospring\` .
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud!"; 
  } 

} 
```
4. Sestavte soubor. jar:
```azurecli
mvn clean packge -DskipTests
```
5. Vytvořte aplikaci ve vaší instanci cloudu Azure na jaře:
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --is-public
```
6. Nasaďte aplikaci do jarního cloudu Azure:
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. Upravte kód pro pracovní nasazení:
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. Znovu sestavte soubor. jar:
```azurecli
mvn clean packge -DskipTests
```
9. Vytvořte zelené nasazení: 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>Zobrazit aplikace a nasazení

Pomocí následujících postupů zobrazte nasazené aplikace.

1. V Azure Portal přejít na svou instanci cloudu Azure na jaře.

1. V levém navigačním podokně otevřete okno aplikace, kde zobrazíte aplikace pro vaši instanci služby.

    [![Aplikace – Řídicí panel](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Můžete kliknout na aplikaci a zobrazit podrobnosti.

    [![Aplikace – přehled](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Otevřete **nasazení** , aby se zobrazila všechna nasazení aplikace. Mřížka ukazuje nasazení v produkčním i přípravném prostředí.

    [![Řídicí panel aplikace/nasazení](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Kliknutím na adresu URL otevřete aktuálně nasazenou aplikaci.
    ![Nasazená adresa URL](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Kliknutím na možnost **Výroba** ve sloupci **stav** zobrazíte výchozí aplikaci.
    ![Výchozí spuštění](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Pracovní aplikaci zobrazíte kliknutím na **fázování** ve sloupci **stav** .
    ![Spuštěná Příprava](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Potvrďte, že koncový bod testu končí lomítkem (/), aby se zajistilo, že se soubor CSS správně načte.  
> * Pokud Váš prohlížeč vyžaduje, abyste zadali přihlašovací údaje pro zobrazení stránky, použijte k dekódování koncového bodu testu [adresu URL](https://www.urldecoder.org/) . Dekódování adresy URL vrátí adresu URL ve formátu "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green".  Pomocí tohoto formuláře můžete získat přístup ke koncovému bodu.

>[!NOTE]    
> Nastavení konfiguračního serveru se týká jak vašeho přípravného prostředí, tak i produkčního prostředí. Například pokud nastavíte cestu kontextu ( `server.servlet.context-path` ) pro bránu aplikace na serveru konfigurace jako *somepath*, cesta k zelenému nasazení se změní na "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...".
 
 Pokud v tuto chvíli navštívíte svou veřejnou bránu aplikace, měli byste vidět starou stránku bez nové změny.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Nastavení zeleného nasazení jako produkčního prostředí

1. Jakmile ověříte změnu v přípravném prostředí, můžete ji vložit do produkčního prostředí. Na stránce  / **nasazení** aplikací vyberte aplikaci, která je aktuálně v systému `Production` .

1. Klikněte na tři tečky po **stavu registrace** zeleného nasazení a nastavte pracovní sestavení na produkční. 

   [![Nastavit produkci na fázování](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Teď má adresa URL aplikace zobrazit vaše změny.

   ![Příprava teď v nasazení](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Po nastavení zeleného nasazení v produkčním prostředí se předchozí nasazení pokusí o pracovní nasazení.

## <a name="modify-the-staging-deployment"></a>Úprava pracovního nasazení

Pokud nejste spokojeni s vaší změnou, můžete upravit kód aplikace, sestavit nový balíček jar a nahrát ho do zeleného nasazení pomocí Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Odstraní pracovní nasazení.

Pokud chcete odstranit pracovní nasazení z portu Azure, klikněte na stránku pracovní nasazení a pak vyberte tlačítko **Odstranit** .

Případně odstraňte pracovní nasazení z Azure CLI spuštěním následujícího příkazu:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Další kroky

* [CI/CD pro jarní cloud Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
