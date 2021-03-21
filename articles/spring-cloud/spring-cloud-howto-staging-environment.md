---
title: Nastavení přípravného prostředí v Azure jaře cloudu | Microsoft Docs
description: Naučte se používat nasazování s modrou zelenou službou Azure Pramenitého cloudu
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016572"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Nastavení přípravného prostředí v Azure jaře cloudu

**Tento článek se týká:** ✔️ Java

Tento článek vysvětluje, jak nastavit pracovní nasazení pomocí modelu nasazení Blue-zelený v Azure jaře cloudu. Nasazení s modrou zeleným prostředím je model průběžného doručování Azure DevOps, který se spoléhá na udržení existující (modré) verze v době, kdy je nasazená nová (zelená) jedna. V tomto článku se dozvíte, jak umístit toto pracovní nasazení do produkčního prostředí beze změny produkčního nasazení.

## <a name="prerequisites"></a>Předpoklady

* Instance jarního cloudu Azure na cenové úrovni Standard
* [Rozšíření Azure jaře Cloud](/cli/azure/azure-cli-extensions-overview) pro rozhraní příkazového řádku Azure

V tomto článku se používá aplikace vytvořená ze Initializr pružiny. Pokud chcete pro tento příklad použít jinou aplikaci, budete muset provést jednoduchou změnu v rámci veřejné části aplikace, aby se vaše pracovní nasazení lišilo od výroby.

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů v tomto článku.  Má běžné, předem instalované nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte instanci Cloud Shell. Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md).

Při nastavování Blue-zelená nasazení v Azure jaře cloudu postupujte podle pokynů v dalších částech.

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Nainstalujte rozšíření Azure jaře Cloud pro Azure CLI pomocí následujícího příkazu:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Příprava aplikace a nasazení
K sestavení aplikace použijte následující postup:

1. Vygenerujte kód pro ukázkovou aplikaci pomocí jarní Initializr s [touto konfigurací](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Stáhněte si kód.
3. Do složky přidejte následující zdrojový soubor HelloController. Java `\src\main\java\com\example\hellospring\` :

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
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
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

Pomocí následujícího postupu zobrazte nasazené aplikace:

1. V Azure Portal přejít na svou instanci cloudu Azure na jaře.

1. V levém podokně otevřete podokno **aplikace** a zobrazte aplikace pro vaši instanci služby.

   ![Snímek obrazovky s podoknem otevřené aplikace](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Můžete vybrat aplikaci a zobrazit podrobnosti.

   ![Snímek obrazovky s podrobnostmi o aplikaci](media/spring-cloud-blue-green-staging/app-overview.png)

1. Otevřete **nasazení** , aby se zobrazila všechna nasazení aplikace. Mřížka ukazuje nasazení v produkčním i přípravném prostředí.

   ![Snímek obrazovky zobrazující uvedená nasazení aplikací](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Vyberte adresu URL a otevřete aktuálně nasazenou aplikaci.
    
   ![Snímek obrazovky, který zobrazuje U nasazené aplikace U R L.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Pokud chcete zobrazit výchozí aplikaci, vyberte ve sloupci **stav** možnost **produkce** .
    
   ![Snímek obrazovky, který zobrazuje U výchozí aplikace U R L.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Chcete-li zobrazit pracovní aplikaci, vyberte ve sloupci **stav** možnost **fázování** .
    
   ![Snímek obrazovky zobrazující U R L pro pracovní aplikaci](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Potvrďte, že koncový bod testu končí lomítkem (/), aby se zajistilo, že se soubor CSS správně načte.  
> * Pokud Váš prohlížeč vyžaduje, abyste zadali přihlašovací údaje pro zobrazení stránky, použijte k dekódování koncového bodu testu [adresu URL](https://www.urldecoder.org/) . Dekódování adresy URL vrátí adresu URL ve formátu *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green*. Pro přístup ke koncovému bodu použijte tento formát.

>[!NOTE]    
> Nastavení konfiguračního serveru platí pro vaše přípravné prostředí i pro produkční prostředí. Například pokud nastavíte cestu kontextu (*Server. servlet. Context-Path*) pro bránu aplikace na konfiguračním serveru jako *somepath*, cesta k zelenému nasazení se změní na *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...*.
 
Pokud v tuto chvíli navštívíte svou veřejnou bránu aplikace, měli byste vidět starou stránku bez nové změny.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Nastavení zeleného nasazení jako produkčního prostředí

1. Jakmile ověříte změnu v přípravném prostředí, můžete ji vložit do produkčního prostředí. Na stránce   >  **nasazení** aplikací vyberte aplikaci, která je aktuálně v **produkčním** prostředí.

1. Vyberte tři tečky po **stavu registrace** zeleného nasazení a pak vyberte **nastavit jako produkční**. 

   ![Snímek obrazovky zobrazující výběry pro nastavení přípravného sestavení do produkčního prostředí](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Potvrďte, že adresa URL aplikace zobrazuje vaše změny.

   ![Snímek obrazovky zobrazující U R L aplikace nyní v produkčním prostředí](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Po nastavení zeleného nasazení v produkčním prostředí se předchozí nasazení pokusí o pracovní nasazení.

## <a name="modify-the-staging-deployment"></a>Úprava pracovního nasazení

Pokud nejste spokojeni s vaší změnou, můžete upravit kód aplikace, vytvořit nový balíček. jar a nahrát ho do zeleného nasazení pomocí Azure CLI:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Odstraní pracovní nasazení.

Pokud chcete odstranit pracovní nasazení z Azure Portal, pokračujte na stránku pro pracovní nasazení a vyberte tlačítko **Odstranit** .

Případně odstraňte pracovní nasazení z Azure CLI spuštěním následujícího příkazu:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Další kroky

* [CI/CD pro jarní cloud Azure](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)