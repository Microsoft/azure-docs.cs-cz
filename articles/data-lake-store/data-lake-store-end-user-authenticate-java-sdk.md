---
title: 'Ověřování koncového uživatele: Java s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 používání Javy v Azure Active Directory
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
ms.openlocfilehash: 47b975b3ea0cfa9d2fb2536236b0a8dfaef14503
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126934"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Ověřování koncových uživatelů s Azure Data Lake Storage Gen1 s využitím Javy
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

V tomto článku se dozvíte o tom, jak pomocí sady Java SDK provádět ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1. Ověřování služba služba s Data Lake Storage Gen1 pomocí sady Java SDK, přečtěte si téma [ověřování služba služba s Data Lake Storage Gen1 s využitím Javy](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Je nutné dokončit kroky v [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavení vytvářet bez použití systému pro sestavení, jako je Maven a Gradle, tyto systémy podstatně usnadňují správu závislostí.

* (Volitelné) Rozhraní IDE, jako je například [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) nebo [Eclipse](https://www.eclipse.org/downloads/) nebo podobné.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Použít následující fragment kódu v aplikaci Java k získání tokenu pro nativní aplikace služby Active Directory, které jste předtím vytvořili pomocí `DeviceCodeTokenProvider`. Nahraďte **Fill-in-HERE** skutečnými hodnotami pro nativní aplikaci Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Sady SDK pro Data Lake Storage Gen1 jsou vhodné metody, které umožňují spravovat tokeny zabezpečení potřebné ke komunikaci s účtem Data Lake Storage Gen1. Není ale povinné použít tuto sadu SDK a tyto metody. Můžete použít také jakýkoliv jiný způsob získání tokenu, například sadu [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) nebo vlastní kód.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování koncového uživatele k ověření pomocí Azure Data Lake Storage Gen1 pomocí sady Java SDK. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat sadu Java SDK pro práci s Azure Data Lake Storage Gen1.

* [Operace s daty v Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)


