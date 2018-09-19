---
title: 'Ověřování služba služba: Java s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování služba služba s Azure Data Lake Storage Gen1 používání Javy v Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 86cc5148c862c18c01cec2951fc58e2932c17ca8
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298145"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Ověřování služba služba s Azure Data Lake Storage Gen1 s využitím Javy
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte o tom, jak pomocí sady Java SDK provádět ověřování služba služba s Azure Data Lake Storage Gen1. Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí sady Java SDK se nepodporuje.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Web"**. Je nutné dokončit kroky v [ověřování služba služba Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavení vytvářet bez použití systému pro sestavení, jako je Maven a Gradle, tyto systémy podstatně usnadňují správu závislostí.

* (Volitelné) Integrované vývojové prostředí, jako jsou [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) nebo [Eclipse](https://www.eclipse.org/downloads/) nebo podobného.

## <a name="service-to-service-authentication"></a>Ověřování služba-služba
1. Vytvořte projekt Maven pomocí příkazu [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) z příkazového řádku nebo pomocí integrovaného vývojového rozhraní (IDE). Pokyny k vytvoření projektu jazyka Java s použitím IntelliJ najdete [zde](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Pokyny k vytvoření projektu s použitím Eclipse najdete [zde](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Přidejte k souboru Maven **pom.xml** následující závislosti. Před značku **\</project>** přidejte následující fragment kódu:
   
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
   
    První závislostí je použití sady SDK pro Data Lake Storage Gen1 (`azure-data-lake-store-sdk`) z úložiště maven. Druhou závislostí je zadání protokolovacího rozhraní (`slf4j-nop`), které se pro tuto aplikaci použije. Používá sadu SDK pro Data Lake Storage Gen1 [slf4j](http://www.slf4j.org/) průčelí protokolování, které umožňuje vybrat z řady oblíbených protokolovacích rozhraní, jako je log4j, Java, logback atd., protokolování nebo žádné protokolování. Pro tento příklad zakážeme protokolování a použijeme tedy vazbu **slf4j-nop**. Pokud chcete ve své aplikaci použít jiné možnosti protokolování, přečtěte si informace [zde](http://www.slf4j.org/manual.html#projectDep).

3. Přidejte do své aplikace následující příkazy pro import.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Použít následující fragment kódu v aplikaci Java k získání tokenu pro aplikaci webové služby Active Directory, které jste předtím vytvořili pomocí jednu z podtříd třídy `AccessTokenProvider` (následující příklad používá `ClientCredsTokenProvider`). Poskytovatel tokenu má uložené přihlašovací údaje v mezipaměti a automaticky token obnovuje, pokud má vypršet jeho platnost. Je možné vytvořit vlastní podtřídy třídy `AccessTokenProvider` takže tokeny se získávají pomocí zákaznického kódu. Teď použijeme je k dispozici v sadě SDK.

    Místo **FILL-IN-HERE** zadejte skutečné hodnoty pro webovou aplikaci Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Sady SDK pro Data Lake Storage Gen1 jsou vhodné metody, které umožňují spravovat tokeny zabezpečení potřebné ke komunikaci s účtem Data Lake Storage Gen1. Není ale povinné použít tuto sadu SDK a tyto metody. Můžete použít také jakýkoliv jiný způsob získání tokenu, například sadu [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) nebo vlastní kód.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování koncového uživatele k ověření pomocí Data Lake Storage Gen1 pomocí sady Java SDK. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat sadu Java SDK pro práci s Data Lake Storage Gen1.

* [Operace s daty v Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)


