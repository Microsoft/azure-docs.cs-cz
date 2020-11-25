---
title: Ověřování služba-Služba – Data Lake Storage Gen1 – Java SDK
description: Naučte se, jak dosáhnout ověřování služby-služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000381"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Ověřování služba-služba s Azure Data Lake Storage Gen1 pomocí jazyka Java

> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

V tomto článku se dozvíte, jak pomocí sady Java SDK provádět ověřování pomocí služby s Azure Data Lake Storage Gen1. Ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí Java SDK se nepodporuje.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory aplikaci "Web"**. Musíte dokončit kroky v [ověřování služba-služba s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavit bez použití systému sestavení, jako je Maven nebo Gradle, tyto systémy výrazně usnadňují správu závislostí.

* Volitelné Rozhraní IDE, jako je [INTELLIJ nápad](https://www.jetbrains.com/idea/download/) nebo [zatmění](https://www.eclipse.org/downloads/) nebo podobné.

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

1. Vytvořte projekt Maven pomocí příkazu [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) z příkazového řádku nebo pomocí integrovaného vývojového rozhraní (IDE). Pokyny k vytvoření projektu jazyka Java s použitím IntelliJ najdete [zde](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Pokyny k vytvoření projektu s použitím Eclipse najdete [zde](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Přidejte k souboru Maven **pom.xml** následující závislosti. Přidejte následující fragment kódu před **\</project>** značku:

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    První závislost je použít sadu Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) z úložiště Maven. Druhou závislostí je zadání protokolovacího rozhraní (`slf4j-nop`), které se pro tuto aplikaci použije. Sada Data Lake Storage Gen1 SDK používá [slf4j](https://www.slf4j.org/) protokolování, které vám umožní vybírat z mnoha oblíbených protokolovacích rozhraní, jako je log4j, protokolování Java, logback atd., nebo bez protokolování. Pro tento příklad zakážeme protokolování a použijeme tedy vazbu **slf4j-nop**. Pokud chcete ve své aplikaci použít jiné možnosti protokolování, přečtěte si informace [zde](https://www.slf4j.org/manual.html#projectDep).

3. Přidejte do své aplikace následující příkazy pro import.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Použijte následující fragment kódu v aplikaci Java k získání tokenu pro webovou aplikaci služby Active Directory, kterou jste vytvořili dříve, pomocí jedné z podtříd `AccessTokenProvider` (Následující příklad používá `ClientCredsTokenProvider` ). Poskytovatel tokenu má uložené přihlašovací údaje v mezipaměti a automaticky token obnovuje, pokud má vypršet jeho platnost. Je možné vytvořit vlastní podtřídy `AccessTokenProvider` , takže tokeny jsou získány kódem zákazníka. Teď teď používáme ten, který jste zadali v sadě SDK.

    Místo **FILL-IN-HERE** zadejte skutečné hodnoty pro webovou aplikaci Azure Active Directory.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

Sada Data Lake Storage Gen1 SDK poskytuje vhodné metody, které umožňují spravovat tokeny zabezpečení potřebné ke komunikaci s účtem Data Lake Storage Gen1. Není ale povinné použít tuto sadu SDK a tyto metody. Můžete použít také jakýkoliv jiný způsob získání tokenu, například sadu [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) nebo vlastní kód.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat ověřování koncovými uživateli k ověřování pomocí Data Lake Storage Gen1 pomocí sady Java SDK. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití sady Java SDK pro práci s Data Lake Storage Gen1.

* [Operace s daty při Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)
