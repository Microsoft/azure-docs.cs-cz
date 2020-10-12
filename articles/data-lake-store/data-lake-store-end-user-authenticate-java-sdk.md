---
title: Ověřování koncových uživatelů – Java s Data Lake Storage Gen1 – Azure
description: Naučte se, jak dosáhnout ověřování koncovými uživateli pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: ece996f7edb8d37cea47756c5b7e635e02409b35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318857"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Ověřování koncových uživatelů s Azure Data Lake Storage Gen1 pomocí jazyka Java
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Použití REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

V tomto článku se dozvíte, jak pomocí sady Java SDK provádět ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Pro ověřování služby-služba pomocí Data Lake Storage Gen1 pomocí sady Java SDK, Projděte si téma [ověřování služby-služba s Data Lake Storage Gen1 pomocí Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory "nativní" aplikaci**. Musíte dokončit kroky v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavení vytvářet bez použití systému pro sestavení, jako je Maven a Gradle, tyto systémy podstatně usnadňují správu závislostí.

* (Volitelné) Rozhraní IDE, jako je například [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) nebo [Eclipse](https://www.eclipse.org/downloads/) nebo podobné.

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
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
   
    První závislost je použít sadu Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) z úložiště Maven. Druhou závislostí je zadání protokolovacího rozhraní (`slf4j-nop`), které se pro tuto aplikaci použije. Sada Data Lake Storage Gen1 SDK používá [SLF4J](https://www.slf4j.org/) protokolování, které vám umožní vybírat z mnoha oblíbených protokolovacích rozhraní, jako je log4j, protokolování Java, Logback atd., nebo bez protokolování. Pro tento příklad zakážeme protokolování a použijeme tedy vazbu **slf4j-nop**. Pokud chcete ve své aplikaci použít jiné možnosti protokolování, přečtěte si informace [zde](https://www.slf4j.org/manual.html#projectDep).

3. Přidejte do své aplikace následující příkazy pro import.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;
    ```

4. Použijte následující fragment kódu v aplikaci Java k získání tokenu pro nativní aplikaci služby Active Directory, kterou jste vytvořili dříve pomocí `DeviceCodeTokenProvider` . Nahraďte **zde vyplnit** skutečnými hodnotami pro Azure Active Directory nativní aplikaci.

    ```java
    private static String nativeAppId = "FILL-IN-HERE";
            
    AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   
    ```

Sada Data Lake Storage Gen1 SDK poskytuje vhodné metody, které umožňují spravovat tokeny zabezpečení potřebné ke komunikaci s účtem Data Lake Storage Gen1. Není ale povinné použít tuto sadu SDK a tyto metody. Můžete použít také jakýkoliv jiný způsob získání tokenu, například sadu [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) nebo vlastní kód.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat ověřování koncovými uživateli k ověřování pomocí Azure Data Lake Storage Gen1 pomocí sady Java SDK. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití sady Java SDK pro práci s Azure Data Lake Storage Gen1.

* [Operace s daty při Data Lake Storage Gen1 pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)


