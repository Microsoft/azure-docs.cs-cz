---
title: 'Java SDK: operace systému souborů v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu'
description: Použití Azure Data Lake Storage Gen1 Java SDK k provádění operací systému souborů v Data Lake Storage Gen1, jako je vytváření složek atd.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: a91593c1bae3002be8b7e423b627f1baf19e86e2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129182"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Operace systému souborů v Azure Data Lake Storage Gen1 pomocí sady Java SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Další informace o použití Azure Data Lake Storage Gen1 Java SDK k provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů atd. Další informace o Data Lake Storage Gen1 najdete v tématu [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Přistupujete k dokumentaci rozhraní API sady Java SDK pro Data Lake Storage Gen1 na [Java API služby Azure Data Lake Storage Gen1 dokumentace](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Požadavky
* Java Development Kit (JDK 7 nebo vyšší s využitím Java verze 1.7 nebo vyšší)
* Účet data Lake Storage Gen1. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). V tomto kurzu se používá Maven pro závislosti sestavení a projektu. I když je možné sestavení vytvářet bez použití systému pro sestavení, jako je Maven a Gradle, tyto systémy podstatně usnadňují správu závislostí.
* (Volitelné) Rozhraní IDE, jako je například [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) nebo [Eclipse](https://www.eclipse.org/downloads/) nebo podobné.

## <a name="create-a-java-application"></a>Vytvoření aplikace Java
Ukázka kódu, která je k dispozici [na GitHubu](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/), vás provede procesem vytvoření souborů v úložišti, zřetězení souborů, stažení souboru a nakonec odstranění některých souborů v úložišti. Tato část článku vás provede hlavními částmi kódu.

1. Vytvořte projekt Maven pomocí příkazu [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) z příkazového řádku nebo pomocí integrovaného vývojového rozhraní (IDE). Pokyny k vytvoření projektu jazyka Java s použitím IntelliJ najdete [zde](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Pokyny k vytvoření projektu s použitím Eclipse najdete [zde](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Přidejte k souboru Maven **pom.xml** následující závislosti. Před značku **\</project>** přidejte následující fragment kódu:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
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

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* Své aplikaci ověřování koncového uživatele, najdete v části [koncového uživatele ověřování-s Data Lake Storage Gen1 pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md).
* Své aplikaci ověřování služba služba, naleznete v tématu [ověřování služba služba s Data Lake Storage Gen1 s využitím Javy](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Vytvoření klienta Data Lake Storage Gen1
Vytváření [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objekt je potřeba zadat název účtu Data Lake Storage Gen1 a poskytovatele tokenu, kterého jste vygenerovali při ověření pomocí služby Data Lake Storage Gen1 (viz [ověřování](#authentication) části). Název účtu Data Lake Storage Gen1 musí být použitím plně kvalifikovaného názvu domény. Nahraďte třeba **Fill-in-HERE** nahraďte něčím jako **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Fragmenty kódu v následujících částech obsahují příklady některých běžných operací systému souborů. Můžete se podívat na kompletní [dokumentace rozhraní Data Lake Storage Gen1 Java SDK API](https://azure.github.io/azure-data-lake-store-java/javadoc/) z **ADLStoreClient** objekt můžete zobrazit další operace.

## <a name="create-a-directory"></a>Vytvoření adresáře

Následující fragment kódu vytvoří adresářovou strukturu v kořenovém adresáři účtu Data Lake Storage Gen1, který jste zadali.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Vytvoření souboru

Následující fragment kódu v adresářové struktuře vytvoří soubor (c.txt) a zapíše do něj nějaká data.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Soubor (d.txt) můžete vytvořit také pomocí polí bajtů.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Definice funkce `getSampleContent` použité v předchozím fragmentu kódu je k dispozici jako součást ukázky [na GitHubu](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Připojení k souboru

Následující fragment kódu připojí obsah k existujícímu souboru.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Definice funkce `getSampleContent` použité v předchozím fragmentu kódu je k dispozici jako součást ukázky [na GitHubu](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Čtení souboru

Následující fragment kódu přečte obsah souboru v účtu Data Lake Storage Gen1.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Řetězení souborů

Následující fragment kódu zřetězí dva soubory v účtu Data Lake Storage Gen1. V případě úspěchu se dva existující soubory nahradí zřetězeným souborem.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Přejmenování souboru

Následující fragment kódu přejmenuje soubor v účtu Data Lake Storage Gen1.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Získání metadat souboru

Následující fragment kódu načte metadata souboru v účtu Data Lake Storage Gen1.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Nastavení oprávnění k souboru

Následující fragment kódu nastaví oprávnění k souboru, který jste vytvořili v předchozí části.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Následující fragment kódu rekurzivně vypíše obsah adresáře.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Definice funkce `printDirectoryInfo` použité v předchozím fragmentu kódu je k dispozici jako součást ukázky [na GitHubu](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Odstranění souborů a složek

Následující fragment kódu odstraní zadané soubory a složky v účtu Data Lake Storage Gen1 rekurzivně.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace
1. Pokud chcete aplikaci spustit z rozhraní IDE, stiskněte tlačítko **Spustit**. Pokud ji chcete spustit z Mavenu, použijte příkaz [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Jestliže chcete vytvořit samostatný soubor jar, který budete moct spustit z příkazového řádku, vytvořte ho se všemi závislostmi s použitím [modulu plug-in sestavení Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Soubor pom.xml v [ukázkovém zdrojovém kódu na GitHubu](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) obsahuje příklad.

## <a name="next-steps"></a>Další postup
* [Prozkoumání JavaDoc k sadě Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)


