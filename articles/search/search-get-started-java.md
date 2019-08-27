---
title: 'Rychlý Start Java: Vytváření, načítání a dotazování indexů v jazyce Java'
description: Vysvětluje, jak vytvořit index, načíst data a spustit dotazy pomocí Java a rozhraní REST API pro Azure Search.
author: lisaleib
manager: cgronlun
ms.author: v-lilei
tags: azure-portal
services: search
ms.service: search
ms.devlang: java
ms.topic: quickstart
ms.date: 07/11/2019
ms.openlocfilehash: b7e5986ad156fe09dc3caa07d952abf85896c5ba
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019225"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>Rychlý start: Vytvoření indexu Azure Search v jazyce Java
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Vytvořte konzolovou aplikaci Java, která vytvoří, načte a zadá dotaz na index Azure Search pomocí [IntelliJ](https://www.jetbrains.com/idea/), [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)a [REST API služby Azure Search](/rest/api/searchservice/). Tento článek poskytuje podrobné pokyny k vytvoření aplikace. Případně můžete [Stáhnout a spustit kompletní aplikaci](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro sestavení a otestování této ukázky jsme použili následující software a služby:

+ [IntelliJ nápad](https://www.jetbrains.com/idea/)

+ [Sada SDK pro Java 11](/java/azure/jdk/?view=azure-java-stable)

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Vytvořte také klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

![Získání názvu služby a klíčů pro správu a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Každý požadavek odeslaný do vaší služby vyžaduje klíč rozhraní API. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením NÁPADu IntelliJ a nastavením nového projektu.

### <a name="create-the-project"></a>Vytvoření projektu

1. Otevřete nápad IntelliJ a vyberte **vytvořit nový projekt**.
1. Vyberte **Maven**.
1. V seznamu **SDK projektu** vyberte sadu SDK Java 11.

    ![Vytvoření projektu Maven](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. Pro ID **skupiny** a **ArtifactId**zadejte `AzureSearchQuickstart`.
1. Přijměte zbývající výchozí hodnoty pro otevření projektu.

### <a name="specify-maven-dependencies"></a>Zadat závislosti Maven

1. Vyberte**Nastavení** **souboru** > .
1. V okně **Nastavení** vyberte **sestavení, spouštění,**  > **nástroje** > sestavení nasazení**Maven** > **Import**.
1. Zaškrtněte políčko **importovat projekty Maven automaticky** a kliknutím na tlačítko **OK** okno zavřete. Moduly plug-in Maven a další závislosti se teď automaticky synchronizují při aktualizaci souboru pom. XML v dalším kroku.

    ![Možnosti importu Maven v nastavení IntelliJ](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Otevřete soubor pom. XML a nahraďte jeho obsah následujícími podrobnostmi konfigurace Maven. Patří sem odkazy na [modul plug-in exec Maven](https://www.mojohaus.org/exec-maven-plugin/) a [rozhraní API rozhraní JSON](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2) .

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>Nastavení struktury projektu

1. Vyberte **Souborová** > **struktura projektu**.
1. Vyberte **moduly**a rozbalte zdrojový strom pro přístup k obsahu `src`  >   `main` složky.
1. `src` Vesložcepřidejte`app` složky a .`service`  >   `main`  >  `java` Provedete to tak, `java` že vyberete složku, stisknete ALT + INSERT a pak zadáte název složky.
1. `src` Vesložcepřidejte`app` složky a .`service`  >   `main`  > `resources`

    Až skončíte, strom projektu by měl vypadat podobně jako na následujícím obrázku.

    ![Adresářová struktura projektu](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Kliknutím na tlačítko **OK** zavřete okno.

### <a name="add-azure-search-service-information"></a>Přidat informace o Azure Search službě

1. V okně **projektu** rozbalte zdrojový `src` strom pro přístup  > `main`  >  kesložcea přidejte soubor`config.properties` . `resources`  >  `app` Provedete to tak, `app` že vyberete složku, stisknete ALT + INSERT, vyberete **soubor**a pak zadáte název souboru.

1. Zkopírujte následující nastavení do nového souboru a nahraďte `<YOUR-SEARCH-SERVICE-NAME>`je, `<YOUR-ADMIN-KEY>`a `<YOUR-QUERY-KEY>` pomocí názvu služby a klíčů. Pokud je `https://mydemo.search.windows.net`koncový bod služby, název služby by byl "mydemo".

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>Přidat metodu Main

1. `src` Vesložce`App`přidejte třídu. `app`  >   `main`  >  `java`  >  Provedete to tak, `app` že vyberete složku, stisknete ALT + INSERT, vyberete **třídu Java**a pak zadáte název třídy.
1. `App` Otevřete třídu a nahraďte obsah následujícím kódem. Tento kód obsahuje `main` metodu. 

    Nekomentovaný kód přečte parametry vyhledávací služby a použije je k vytvoření instance klienta služby Search Service. Kód klienta služby Search se přidá do další části.

    Komentář kódu v této třídě bude v pozdější části tohoto rychlého startu odkomentovat.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>Přidat operace HTTP

1. `src` Vesložce`SearchServiceClient`přidejte třídu. `service`  >   `main`  >  `java`  >  Provedete to tak, `service` že vyberete složku, stisknete ALT + INSERT, vyberete **třídu Java**a pak zadáte název třídy.
1. `SearchServiceClient` Otevřete třídu a nahraďte obsah následujícím kódem. Tento kód poskytuje operace HTTP vyžadované k použití REST API Azure Search. Další metody pro vytvoření indexu, nahrání dokumentů a dotazování indexu budou přidány v pozdější části.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Sestavení projektu

1. Ověřte, zda má projekt následující strukturu.

    ![Adresářová struktura projektu](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Otevřete okno nástroje **Maven** a proveďte tento cíl Maven: `verify exec:java`
![Spustit Maven cíl: ověření exec: Java](media/search-get-started-java/java-quickstart-execute-maven-goal.png)

Po dokončení zpracování vyhledejte zprávu o úspěchu sestavení následovaný nulou (0) ukončovacím kódem.

## <a name="1---create-index"></a>1\. vytvoření indexu

Definice indexu hotelů obsahuje jednoduchá pole a jedno komplexní pole. Příkladem jednoduchého pole jsou "hotely" nebo "Description". Pole adresa je komplexní pole, protože obsahuje podpole, jako je například ulice a město. V tomto rychlém startu je definice indexu určena pomocí formátu JSON.

1. V okně **projektu** rozbalte zdrojový `src` strom pro přístup  > `main`  >  kesložcea přidejte soubor`index.json` . `resources`  >  `service` Provedete to tak, `app` že vyberete složku, stisknete ALT + INSERT, vyberete **soubor**a pak zadáte název souboru.

1. `index.json` Otevřete soubor a vložte následující definici indexu.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Název indexu bude "hotely-rychlý Start". Atributy polí indexu určují, jak lze v aplikaci vyhledat indexovaná data. Například `IsSearchable` atribut musí být přiřazen každému poli, které by mělo být zahrnuto do fulltextového vyhledávání. Další informace o atributech naleznete v tématu [kolekce polí a atributy polí](search-what-is-an-index.md#fields-collection).
    
    Pole v tomto indexu používá volitelnou `analyzer` vlastnost k přepsání výchozího analyzátoru jazyka Lucene. `Description` Toto `Description_fr` pole používá analyzátor `fr.lucene` Lucene pro francouzštinu, protože ukládá francouzský text. `Description` Používá volitelnou jazykovou analyzátoru Microsoft en. Lucene. Další informace o analyzátorech najdete v tématu [analyzátory pro zpracování textu v Azure Search](search-analyzers.md).

1. Do `SearchServiceClient` třídy přidejte následující kód. Tyto metody sestavují Azure Search adresy URL služby REST, které vytvářejí a odstraňují index a které určují, jestli index existuje. Metody také vytvářejí požadavek protokolu HTTP.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Odkomentujte následující kód ve `App` třídě. Tento kód odstraní index "hotely-rychlé spuštění", pokud existuje, a vytvoří nový index na základě definice indexu v souboru index. JSON. 

    Po požadavku na vytvoření indexu je vloženo pozastavení s jednou sekundou. Tím se zajistí, že se index vytvoří před odesláním dokumentů.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Otevřete okno nástroje **Maven** a proveďte tento cíl Maven:`verify exec:java`

    Při spuštění kódu vyhledejte zprávu "vytvoření indexu" následovaný kódem odpovědi 201. Tento kód odpovědi potvrzuje, že byl index vytvořen. Běh by měl končit zprávou o ÚSPĚŠNÉm sestavení a nulovým (0) ukončovacím kódem.
    
## <a name="2---load-documents"></a>2\. načtení dokumentů

1. V okně **projektu** rozbalte zdrojový `src` strom pro přístup  > `main`  >  kesložcea přidejte soubor`hotels.json` . `resources`  >  `service` Provedete to tak, `app` že vyberete složku, stisknete ALT + INSERT, vyberete **soubor**a pak zadáte název souboru.
1. Do souboru vložte následující hotelové dokumenty.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Do `SearchServiceClient` třídy vložte následující kód. Tento kód vytvoří adresu URL služby REST pro nahrání hotelových dokumentů do indexu a pak provede požadavek HTTP POST.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Odkomentujte následující kód ve `App` třídě. Tento kód nahraje dokumenty do indexu v "hotely. JSON".

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Po žádosti o nahrání se vloží pauza o dvou sekundách, abyste zajistili, že se proces načítání dokumentu dokončil před dotazem na index.

1. Otevřete okno nástroje **Maven** a proveďte tento cíl Maven:`verify exec:java`

    Vzhledem k tomu, že jste v předchozím kroku vytvořili index "hotely-rychlé spuštění", kód ho teď odstraní a znovu ho znovu vytvoří před načtením dokumentů hotelového typu.

    Při spuštění kódu vyhledejte zprávu "nahrávání dokumentů" následovaný kódem odpovědi 200. Tento kód odpovědi potvrzuje, že se dokumenty nahrály do indexu. Běh by měl končit zprávou o ÚSPĚŠNÉm sestavení a nulovým (0) ukončovacím kódem.

## <a name="3---search-an-index"></a>3\. Prohledání indexu

Teď, když jste načetli dokumenty hotelů, můžete vytvořit vyhledávací dotazy pro přístup k datům hotelů.

1. Do `SearchServiceClient` třídy přidejte následující kód. Tento kód sestaví Azure Search adresy URL služby REST pro hledání indexovaných dat a vytiskne výsledky hledání.

    `SearchOptions` Třída a`createSearchOptions` Metoda umožňují určit podmnožinu dostupných možností dotazu REST API Azure Search. Další informace o možnostech dotazů REST API naleznete v tématu [Search Documents (Azure Search Service REST API)](/rest/api/searchservice/search-documents).

    `SearchPlus` Metoda vytvoří adresu URL vyhledávacího dotazu, provede požadavek hledání a pak výsledky vytiskne do konzoly. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. `App` Ve třídě odkomentujte následující kód. Tento kód nastaví pět různých dotazů, včetně vyhledávacího textu, parametrů dotazu a datových polí, která se mají vrátit. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Existují dva [způsoby, kterými se v dotazu shodují výrazy](search-query-overview.md#types-of-queries): fulltextové vyhledávání a filtry. Fulltextový vyhledávací dotaz vyhledává jeden nebo více podmínek v `IsSearchable` polích indexu. Filtr je logický výraz, který se vyhodnocuje přes `IsFilterable` pole v indexu. Můžete použít fulltextové vyhledávání a filtry společně nebo samostatně.

1. Otevřete okno nástroje **Maven** a proveďte tento cíl Maven:`verify exec:java`

    Vyhledejte souhrn jednotlivých dotazů a jejich výsledků. Spuštění by mělo být dokončeno se zprávou o ÚSPĚŠNÉm sestavení a nulovým (0) ukončovacím kódem.

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu v jazyce Java jste pracovali pomocí řady úkolů, abyste mohli vytvořit index, načíst ho s dokumenty a spouštět dotazy. Pokud jste obeznámeni se základními pojmy, doporučujeme pro hlubší učení následující články.

+ [Operace indexu](/rest/api/searchservice/index-operations)

+ [Operace dokumentů](/rest/api/searchservice/document-operations)

+ [Operace indexeru](/rest/api/searchservice/indexer-operations)
