---
title: 'Rychlý Start: váš první dotaz v jazyce Java'
description: V tomto rychlém startu provedete postup povolení balíčků prostředků Maven pro jazyk Java a spuštění prvního dotazu.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223959"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí jazyka Java

Prvním krokem při použití Azure Resource graphu je ověření, že jsou nainstalované požadované balíčky Maven pro Java. Tento rychlý Start vás provede procesem přidání balíčků Maven do instalace Java.

Na konci tohoto procesu jste přidali balíčky Maven do instalace Java a spustili svůj první dotaz na graf prostředku.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.21.0**). Pokud ještě není nainstalovaná, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

  > [!NOTE]
  > Rozhraní příkazového řádku Azure vyžaduje, aby sada Azure SDK pro jazyk Java mohla používat ověřování pomocí rozhraní **příkazového řádku** v následujících příkladech. Informace o dalších možnostech najdete v tématu [Klientská knihovna identit Azure pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze
  8.

- [Apache Maven](https://maven.apache.org/)verze 3,6 nebo vyšší.

## <a name="create-the-resource-graph-project"></a>Vytvoření projektu prostředku grafu

Pokud chcete povolit, aby se v jazyce Java dotazoval na Azure Resource Graph, vytvořte a nakonfigurujte novou aplikaci pomocí Maven a nainstalujte požadované balíčky Maven.

1. Inicializujte novou aplikaci Java s názvem "argQuery" s [Maven Archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Změňte adresáře na novou složku projektu `argQuery` a otevřete ji `pom.xml` ve svém oblíbeném editoru. Do `<dependency>` existujícího uzlu přidejte následující uzly `<dependencies>` :

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. V `pom.xml` souboru přidejte do `<properties>` základního uzlu následující uzel, aby se `<project>` aktualizovaly zdrojové a cílové verze:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. V `pom.xml` souboru přidejte následující `<build>` uzel pod základním `<project>` uzlem pro konfiguraci cíle a hlavní třídy pro spuštění projektu.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Nahraďte výchozí `App.java` hodnotu `\argQuery\src\main\java\com\Fabrikam` pomocí následujícího kódu a uložte aktualizovaný soubor:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Sestavte `argQuery` konzolovou aplikaci:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Pomocí vytvořené konzolové aplikace Java je čas vyzkoušet jednoduchý dotaz na graf. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

V každém volání metody `argQuery` jsou použity proměnné, které je třeba nahradit vlastními hodnotami:

- Proměnnou `{subscriptionId}` nahraďte ID předplatného.
- `{query}` – Nahraďte dotazem vašeho grafu Azure Resource.

1. K ověření použijte Azure CLI `az login` .

1. Změňte adresář na `argQuery` složku projektu, kterou jste vytvořili pomocí předchozího `mvn -B archetype:generate` příkazu.

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí Maven a zkompilujte konzolovou aplikaci a předejte argumenty. `exec.args`Vlastnost identifikuje argumenty podle mezer. Chcete-li identifikovat dotaz jako jeden argument, zabalte ho pomocí jednoduchých uvozovek ( `'` ).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Jelikož tento příklad dotazu neposkytuje modifikátor řazení, jako je `order by` spuštění tohoto dotazu vícekrát, je pravděpodobně výsledkem jiné sady prostředků na žádost.

1. Změňte argument na `argQuery.exe` a změňte dotaz na `order by` vlastnost **Name** :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Změňte konečný parametr na `argQuery.exe` a změňte dotaz na `order by` **název vlastnosti název** a pak `limit` na horních pět výsledků:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat konzolovou aplikaci Java a nainstalované balíčky, můžete to udělat tak, že odstraníte `argQuery` složku projektu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili konzolovou aplikaci Java s požadovanými balíčky grafu prostředků a spustíte svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)