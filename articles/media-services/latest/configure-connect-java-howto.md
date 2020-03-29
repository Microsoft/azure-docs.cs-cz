---
title: Připojení k rozhraní API Azure Media Services v3 – Java
description: Tento článek popisuje, jak se připojit k rozhraní Azure Media Services v3 API s Javou.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888491"
---
# <a name="connect-to-media-services-v3-api---java"></a>Připojení k rozhraní API Media Services v3 – Java

Tento článek ukazuje, jak se připojit k Azure Media Services v3 Java SDK pomocí metody přihlášení instančního objektu služby.

V tomto článku visual studio kód se používá k vývoji ukázkové aplikace.

## <a name="prerequisites"></a>Požadavky

- Chcete-li nainstalovat, postupujte [podle pokynů v jazyce Java pomocí kódu sady Visual Studio:](https://code.visualstudio.com/docs/java/java-tutorial)

   - JDK
   - Apache Maven
   - Rozšíření javy
- Ujistěte se, že nastavení `JAVA_HOME` a `PATH` proměnné prostředí.
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Služby Media Services.
- Postupujte podle pokynů v tématu [Přístupová api.](access-api-cli-how-to.md) Zaznamenejte ID předplatného, ID aplikace (ID klienta), ověřovací klíč (tajný klíč) a ID klienta, které potřebujete v pozdějším kroku.

Také recenzi:

- [Java v kódu Visual Studia](https://code.visualstudio.com/docs/languages/java)
- [Správa projektů Java v kódu VS](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Zkontrolujte [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

Otevřete nástroj příkazového `cd` řádku a do adresáře, do kterého chcete vytvořit projekt.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Při spuštění příkazu `pom.xml`jsou `App.java`vytvořeny , a další soubory. 

## <a name="add-dependencies"></a>Přidání závislostí

1. V kódu Visual Studia otevřete složku, ve které je projekt
1. Najděte a otevřete`pom.xml`
1. Přidání potřebných závislostí

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Připojení ke klientovi Java

1. Otevřete `App.java` soubor `src\main\java\com\azure\ams` pod a ujistěte se, že váš balíček je součástí v horní části:

    ```java
    package com.azure.ams;
    ```
1. Pod příkaz balíček přidejte tyto příkazy importu:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Chcete-li vytvořit pověření služby Active Directory, která potřebujete k vytváření požadavků, přidejte následující kód do hlavní metody třídy App a nastavte hodnoty, které jste [získali](access-api-cli-how-to.md)z rozhraní API aplikace Access :
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Spusťte aplikaci.

## <a name="see-also"></a>Viz také

- [Koncepty mediálních služeb](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Referenční příručka v jazyce Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Další kroky

Nyní můžete `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` zahrnout a začít manipulovat s entitami.

Další příklady kódu naleznete [v java sdk ukázky](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) repo.
