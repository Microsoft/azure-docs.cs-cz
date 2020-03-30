---
title: Ověřování mezi službami – Úložiště datových jezer Gen1 – Java SDK
description: Zjistěte, jak dosáhnout ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s Javou
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904537"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Javy

> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

V tomto článku se dozvíte, jak pomocí sady Java SDK provést ověřování mezi službami pomocí Azure Data Lake Storage Gen1. Ověřování koncových uživatelů pomocí sady Data Lake Storage Gen1 pomocí sady Java SDK není podporováno.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte "webovou" aplikaci služby Azure Active Directory**. Kroky ověřování mezi službami musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavit bez použití systému sestavení, jako je Maven nebo Gradle, tyto systémy usnadňují správu závislostí.

* (Nepovinné) IDE jako [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) nebo [Eclipse](https://www.eclipse.org/downloads/) nebo podobné.

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

1. Vytvořte projekt Maven pomocí příkazu [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) z příkazového řádku nebo pomocí integrovaného vývojového rozhraní (IDE). Pokyny k vytvoření projektu jazyka Java s použitím IntelliJ najdete [zde](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Pokyny k vytvoření projektu s použitím Eclipse najdete [zde](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Přidejte k souboru Maven **pom.xml** následující závislosti. Před značku ** \</project>** přidejte následující úryvek:

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

    První závislost je použití data lake storage Gen1`azure-data-lake-store-sdk`SDK ( ) z úložiště maven. Druhou závislostí je zadání protokolovacího rozhraní (`slf4j-nop`), které se pro tuto aplikaci použije. Data Lake Storage Gen1 SDK používá [slf4j](https://www.slf4j.org/) protokolování fasády, která vám umožní vybrat si z řady populárních protokolování rámců, jako je log4j, Java protokolování, přihlášení, atd., nebo žádné protokolování. Pro tento příklad zakážeme protokolování a použijeme tedy vazbu **slf4j-nop**. Pokud chcete ve své aplikaci použít jiné možnosti protokolování, přečtěte si informace [zde](https://www.slf4j.org/manual.html#projectDep).

3. Přidejte do své aplikace následující příkazy pro import.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Pomocí následujícího fragmentu v aplikaci Java získáte token pro webovou aplikaci Active Directory, kterou jste vytvořili dříve pomocí jedné z `AccessTokenProvider` podtříd aplikace (používá `ClientCredsTokenProvider`následující příklad). Poskytovatel tokenu má uložené přihlašovací údaje v mezipaměti a automaticky token obnovuje, pokud má vypršet jeho platnost. Je možné vytvořit vlastní podtřídy tak tokeny `AccessTokenProvider` jsou získány pomocí kódu zákazníka. Pro tuto chvíli, pojďme použít ten, který je k dispozici v SDK.

    Místo **FILL-IN-HERE** zadejte skutečné hodnoty pro webovou aplikaci Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Sada SDK úložiště datového jezera poskytuje pohodlné metody, které umožňují spravovat tokeny zabezpečení potřebné k rozhovoru s účtem Data Lake Storage Gen1. Není ale povinné použít tuto sadu SDK a tyto metody. Můžete použít také jakýkoliv jiný způsob získání tokenu, například sadu [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) nebo vlastní kód.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak pomocí ověřování koncových uživatelů k ověření pomocí data lake storage gen1 pomocí sady Java SDK. Nyní se můžete podívat na následující články, které hovoří o tom, jak používat java sdk pro práci s Data Lake Storage Gen1.

* [Datové operace na Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)
