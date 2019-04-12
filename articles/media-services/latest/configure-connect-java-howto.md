---
title: Připojení k rozhraní API služby Azure Media Services v3 – Java
description: Zjistěte, jak se připojit k Media Services v3 API s využitím Javy.
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
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 9177a1ae1f2939979d1f824c98b6018a83c2779f
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502260"
---
# <a name="connect-to-media-services-v3-api---java"></a>Připojení k rozhraní API služby Media Services v3 – Java

Tento článek popisuje, jak se připojit k Azure Media Services v3 Java SDK pomocí přihlašovacího instančního objektu služby v metodě.

V tomto článku se používá Visual Studio Code pro vývoj aplikace.

## <a name="prerequisites"></a>Požadavky

- Postupujte podle [zápis Java pomocí Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) k instalaci:

   - JDK
   - Apache Maven
   - Balíčkem rozšíření Java
- Nezapomeňte nastavit `JAVA_HOME` a `PATH` proměnné prostředí.
- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Je potřeba pamatovat název skupiny prostředků a název účtu Media Services.
- Postupujte podle pokynů [přístup k rozhraní API](access-api-cli-how-to.md) tématu. Zaznamenejte ID předplatného, ID aplikace (ID klienta), ověřovací klíč (tajný klíč) a ID tenanta, který budete potřebovat v pozdějším kroku.

Také zkontrolujte:

- [Java v aplikaci Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Řízení projektů Java v nástroji VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

Otevřete nástroj příkazového řádku a `cd` do adresáře, ve kterém chcete vytvořit projekt.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Při spuštění příkazu, který `pom.xml`, `App.java`, a další soubory jsou vytvořeny. 

## <a name="add-dependencies"></a>Přidat závislosti

1. Ve Visual Studio Code otevřete složku, ve kterém je váš projekt. 
1. Vyhledání a otevření `pom.xml`. 
1. Přidá potřebné závislosti. Jeden z nich je [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar).

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Připojte se k klientskou sadou Java

1. Otevřít `App.java` soubor `src\main\java\com\azure\ams` a ujistěte se, že váš balíček je zahrnuté v horní části:

    ```java
    package com.azure.ams;
    ```
2. V rámci příkazu balíčku, přidejte tyto příkazy import:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
2. Vytvoření přihlašovacích údajů Active Directory, které potřebujete k podání žádostí o, přidejte následující kód k metodě hlavní třídy App a nastavit hodnoty, které jste získali z [přístup k rozhraní API](access-api-cli-how-to.md):
   
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

## <a name="see-also"></a>Další informace najdete v tématech

- [Koncepty služby Media Services](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Referenční informace k Java](https://aka.ms/ams-v3-java-ref)
- [https://search.maven.org/](https://search.maven.org/)

## <a name="next-steps"></a>Další postup

Nyní můžete zahrnout `import com.microsoft.azure.management.mediaservices.v2018_07_01.Asset;` a začít manipulaci s entitami.<br/>
Třeba získáte všechny prostředky ve vašem účtu: `Observable<Asset> asyncAssets = 
                    manager.assets().listAsync(groupId, accountId).last();`
