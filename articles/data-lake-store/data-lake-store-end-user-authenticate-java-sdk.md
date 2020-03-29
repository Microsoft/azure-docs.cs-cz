---
title: 'Ověřování koncových uživatelů: Java s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s Javou
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8b558fca964f33d47d331e007329d1bae2626877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878097"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Javy
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

V tomto článku se dozvíte, jak pomocí sady Java SDK provést ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Ověřování mezi službami pomocí funkce Data Lake Storage Gen1 pomocí sady Java SDK najdete v [tématu Ověřování mezi službami pomocí funkce Data Lake Storage Gen1 pomocí jazyka Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte nativní aplikaci služby Azure Active Directory**. Kroky ověřování koncových uživatelů musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavení vytvářet bez použití systému pro sestavení, jako je Maven a Gradle, tyto systémy podstatně usnadňují správu závislostí.

* (Volitelné) Rozhraní IDE, jako je například [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) nebo [Eclipse](https://www.eclipse.org/downloads/) nebo podobné.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Pomocí následujícího fragmentu výstřižek v aplikaci Java získáte token `DeviceCodeTokenProvider`pro nativní aplikaci služby Active Directory, kterou jste vytvořili dříve pomocí rozhraní . Nahraďte **FILL-IN-HERE** skutečnými hodnotami pro nativní aplikaci služby Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Sada SDK úložiště datového jezera poskytuje pohodlné metody, které umožňují spravovat tokeny zabezpečení potřebné k rozhovoru s účtem Data Lake Storage Gen1. Není ale povinné použít tuto sadu SDK a tyto metody. Můžete použít také jakýkoliv jiný způsob získání tokenu, například sadu [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) nebo vlastní kód.

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak pomocí ověřování koncových uživatelů k ověření pomocí Azure Data Lake Storage Gen1 pomocí sady Java SDK. Teď se můžete podívat na následující články, které hovoří o tom, jak používat Java SDK pro práci s Azure Data Lake Storage Gen1.

* [Datové operace na Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)


