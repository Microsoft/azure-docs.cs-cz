---
title: Volání operace služby Azure Storage Services REST API, včetně ověřování | Dokumentace Microsoftu
description: Volání operace služby Azure Storage Services REST API, včetně ověřování
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 08a86e1b2808a0778734edecc9385f4d61779b25
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476192"
---
# <a name="using-the-azure-storage-rest-api"></a>Použití rozhraní REST API pro službu Azure Storage

V tomto článku se dozvíte, jak používat rozhraní REST API služby Storage Blob a jak ověřovat volání služby. Zápisem z pohledu uživatele, který neví nic o REST a představu o volání REST, ale je vývojář. Doporučujeme podívat se na referenční dokumentaci pro volání REST a zjistit, jak přeloží ji do skutečný volání REST – pole, která přejít where? Po získání nastavení volání REST, můžete využít tyto znalosti k používání některé z jiných rozhraní API REST služby úložiště.

## <a name="prerequisites"></a>Požadavky 

Aplikace vypíše kontejnery v úložišti objektů blob pro účet úložiště. Vyzkoušet kód v tomto článku, budete potřebovat následující položky: 

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) s následující sadou funkcí:
    - Vývoj pro Azure

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Účet úložiště pro obecné účely. Pokud ještě nemáte účet úložiště, přečtěte si téma [vytvořit účet úložiště](storage-quickstart-create-account.md).

* V příkladu v tomto článku ukazuje, jak vypsat kontejnery v účtu úložiště. Chcete-li zobrazit výstup, přidejte některé kontejnery úložiště objektů blob v účtu úložiště než začnete.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace je konzolové aplikace napsané v jazyce C#.

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Otevřít řešení sady Visual Studio, vyhledejte storage-dotnet-rest-api-with-auth složku, otevřete ji a dvakrát klikněte na StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Co je REST?

ZBÝVAJÍCÍ prostředky *přenosu representational stavu*. Pro konkrétní definici, projděte si [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

V podstatě REST architektura je taková, lze použít při volání rozhraní API nebo zajištěním, k dispozici k volání rozhraní API. Je nezávislý na co se děje na obou stranách a jaký software se používá při odesílání nebo přijímání ZBÝVAJÍCÍ volá. Můžete napsat aplikace, která běží na Macu, Windows, Linux, telefon s Androidem nebo tabletu, iPhone, iPod nebo webové stránky a použijte stejné rozhraní REST API pro všechny tyto platformy. Data mohou být předána v a/nebo si při volání rozhraní REST API. Rozhraní REST API není pro vás z jaké platformy je volána – co je důležité je informací předaných v požadavku a data zadaná v odpovědi.

Vědět, jak použít REST je užitečné dovedností. Produktovému týmu Azure často vydává nové funkce. V mnoha případech, nových funkcí jsou přístupné prostřednictvím rozhraní REST, ale ještě se ještě prezentované prostřednictvím **všechny** klientských knihoven pro úložiště nebo uživatelského rozhraní (třeba na webu Azure portal). Pokud chcete vždy použít nejnovější a nejlepší, učení REST je povinné. Navíc pokud chcete zadat vlastní knihovnu pro interakci s Azure Storage, nebo chcete pro přístup k úložišti Azure pomocí programovacího jazyka, který nemá klientské knihovny SDK nebo úložiště, můžete použít rozhraní REST API.

## <a name="about-the-sample-application"></a>O ukázkové aplikace

Ukázková aplikace vypíše kontejnery v účtu úložiště. Jakmile pochopíte, jak souvisí informace v dokumentaci k rozhraní REST API pro skutečný kód, se snadněji zjistit, ostatní volání REST. 

Když se podíváte na [rozhraní REST API služby Blob](/rest/api/storageservices/Blob-Service-REST-API), uvidíte všechny operace můžete provádět na úložiště objektů blob. Klientské knihovny úložiště jsou obálky kolem rozhraní REST API – jejich usnadňují za vás do úložiště bez přímo pomocí rozhraní REST API. Jak bylo uvedeno výše, někdy ale chcete používat rozhraní REST API namísto Klientská knihovna pro úložiště.

## <a name="rest-api-reference-list-containers-api"></a>Reference k rozhraní REST API: Vypsat kontejnery rozhraní API

Podívejme se na stránce v referenci rozhraní REST API pro [ListContainers](/rest/api/storageservices/List-Containers2) takže víte, kde některá pole pocházejí z požadavku a odpovědi v další části s kódem.

**Metoda požadavku**: GET. Tento příkaz je metoda HTTP, který zadáte jako vlastnost objektu požadavku. Ostatní hodnoty pro tuto operaci zahrnují HEAD, PUT a DELETE, v závislosti na rozhraní API, se označuje jako volání.

**Identifikátor URI žádosti**: https://myaccount.blob.core.windows.net/?comp=list  Tím se vytvoří z koncového bodu účtu úložiště objektů blob `http://myaccount.blob.core.windows.net` a řetězec prostředku `/?comp=list`.

[Parametry identifikátoru URI](/rest/api/storageservices/List-Containers2#uri-parameters): Existují další parametry dotazu, které můžete použít při volání metody ListContainers. Několik z těchto parametrů se *vypršení časového limitu* volání (v sekundách) a *předponu*, který se používá k filtrování.

Další užitečné parametr *maxresults:* Pokud než tato hodnota jsou k dispozici více kontejnerů, budou obsahovat tělo odpovědi *NextMarker* element, který označuje dalšího kontejneru se vraťte na další požadavek. Pokud chcete tuto funkci používat, je zadat *NextMarker* hodnoty jako *značky* parametr v identifikátoru URI při příští žádosti o. Při použití této funkce je obdobou stránkování přes výsledky. 

Chcete-li použít další parametry, přidejte je na řetězec prostředku s hodnotou, jako v tomto příkladu:

```
/?comp=list&timeout=60&maxresults=100
```

[Hlavičky požadavku](/rest/api/storageservices/List-Containers2#request-headers)**:** Tato část obsahuje seznam hlaviček žádostí požadované a volitelné. Tři hlavičky jsou povinné: *autorizace* záhlaví, *x-ms-date* (čas UTC požadavku obsahuje), a *x-ms-version* (určuje verzi modulu REST Rozhraní API pro použití). Včetně *x-ms klienta request-id* v hlavičkách je nepovinný – můžete nastavit hodnotu pro toto pole k ničemu; jsou zapsána do úložiště analýzy protokolů, pokud je povoleno protokolování.

[Text žádosti](/rest/api/storageservices/List-Containers2#request-body)**:** Není k dispozici není datová část požadavku pro ListContainers. Text požadavku se používá ve všech operacích PUT při odesílání objektů BLOB, stejně jako SetContainerAccessPolicy, který umožňuje odeslat XML seznam uložené zásady přístupu k použití. Uložené zásady přístupu jsou popsány v následujícím článku [použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Stavový kód odpovědi](/rest/api/storageservices/List-Containers2#status-code)**:** Říká jakékoli stavové kódy, které potřebujete znát. V tomto příkladu je stavový kód HTTP 200 ok. Úplný seznam stavových kódů HTTP, projděte si [definice stavových kódů](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Kódy chyb, které jsou specifické pro rozhraní REST API služby Storage najdete v tématu [kódy chyb společné rozhraní REST API](/rest/api/storageservices/common-rest-api-error-codes)

[Hlavičky odpovědi](/rest/api/storageservices/List-Containers2#response-headers)**:** Patří mezi ně *typ obsahu*; *x-ms-request-id* (id žádosti je předán, pokud je k dispozici); *x-ms-version* (označuje verzi služby Blob service použít) a *datum* (UTC, určuje, kdy byl požadavek).

[Text odpovědi](/rest/api/storageservices/List-Containers2#response-body): Toto pole je struktury XML poskytuje požadovaná data. V tomto příkladu je odpověď na seznam kontejnery a jejich vlastnosti.

## <a name="creating-the-rest-request"></a>Vytvoření požadavku REST

Několik poznámek k před zahájením – zabezpečení při spuštění v produkčním prostředí vždy používejte protokol HTTPS, nikoli protokol HTTP. Pro účely tohoto cvičení měli byste použít HTTP, můžete zobrazit data požadavku a odpovědi. Chcete-li zobrazit informace o požadavku a odpovědi ve skutečné volání REST, si můžete stáhnout [Fiddler](http://www.telerik.com/fiddler) nebo podobné aplikace. V řešení sady Visual Studio název účtu úložiště a klíč jsou pevně zakódované ve třídě a metodě ListContainersAsyncREST předá název účtu úložiště a klíč účtu úložiště metody, které se používají k vytváření různých součástí požadavku REST . V reálné aplikaci název účtu úložiště a klíč by se nacházejí v konfiguračním souboru, proměnné prostředí, nebo ze služby Azure Key Vault načíst.

V našem projektu ukázkový kód pro vytvoření autorizační hlavičky je v samostatné třídě s myšlenkou, že může trvat celé jedné třídy a přidáte vlastní řešení a použije "tak jak jsou." Autorizační hlavička kód funguje pro většinu volání rozhraní REST API do služby Azure Storage.

Chcete-li vytvářet žádosti, které je objekt HttpRequestMessage, přejděte na ListContainersAsyncREST v souboru Program.cs. Tady jsou kroky pro vytvoření žádosti: 

* Vytvořte identifikátor URI, který má být použit pro volání služby. 
* Vytvoření objektu HttpRequestMessage a nastavení datové části. Datová část má hodnotu null pro ListContainersAsyncREST, protože jsme nepředáváme cokoli.
* Přidáte záhlaví požadavku pro x-ms-date a x-ms-version.
* Získat autorizační hlavičku a přidejte ji.

Některé základní informace, které budete potřebovat: 

*  Pro ListContainers **metoda** je `GET`. Tato hodnota nastavena, při vytváření instance požadavku. 
*  **Prostředků** je část dotazu identifikátoru URI, která určuje, které rozhraní API je volána, takže hodnota je `/?comp=list`. Jak bylo uvedeno dříve, prostředek je na stránce dokumentace ke službě odkaz s informacemi o [ListContainers API](/rest/api/storageservices/List-Containers2).
*  Identifikátor URI je vytvořený tak, že vytváří se koncový bod služby Blob service pro daný účet úložiště a zřetězení prostředku. Hodnota pro **identifikátor URI žádosti** končí nebuďte `http://contosorest.blob.core.windows.net/?comp=list`.
*  Pro ListContainers **includesearchresults: true** má hodnotu null a neexistují žádné doplňující **záhlaví**.

Různých rozhraní API může mít jiné parametry a zajistěte tak předání jako *ifMatch*. Příklad, ve kterém může pomocí ifMatch je při volání metody PutBlob. V takovém případě nastavte ifMatch na značku eTag a zároveň pouze aktualizuje objekt blob, pokud aktuální značku eTag na objektu blob odpovídá značce eTag zadané. Pokud někdo jiný se aktualizovala objekt blob od načítání eTag, jejich změny nebudou přepsány. 

Nejprve nastavte `uri` a `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

V dalším kroku vytvoření instance žádosti, nastavení metodu na `GET` a poskytnutí identifikátoru URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Přidáte záhlaví požadavku pro x-ms-date a x-ms-version. Toto místo v kódu je také kde můžete přidávat žádné další hlavičky žádosti volání. V tomto příkladu nejsou žádná další záhlaví. Příklad rozhraní API, které se předává v dodatečné hlavičky je SetContainerACL. Pro úložiště objektů Blob přidá hlavičku nazývá "x-ms-blob veřejný přístup" a hodnotou pro úroveň přístupu.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Volání metody, která vytvoří autorizační hlavičky a přidejte ho do hlavičky žádosti. Uvidíte jak vytvoření autorizační hlavičky později v tomto článku. Název metody rozlišuje GetAuthorizationHeader, který se zobrazí v tomto fragmentu kódu:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

V tomto okamžiku `httpRequestMessage` obsahuje kompletní autorizační hlavičky požadavku REST. 

## <a name="call-the-rest-api-with-the-request"></a>Volání rozhraní REST API s požadavkem

Teď, když máte požadavek, můžete volat SendAsync odeslání požadavku REST. SendAsync volá rozhraní API a získá zpět odpověď. Zkontrolujte odpovědi StatusCode (200 je v pořádku), pak analyzovat odpověď. V tomto případě získáte seznam XML kontejnerů. Podívejme se na kód pro volání metody GetRESTRequest vytvořit požadavek, proveďte požadavek a pak zkontrolujte odpovědi pro seznam kontejnerů.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Při spuštění jiného programu pro sítě, jako [Fiddler](https://www.telerik.com/fiddler) při provádění volání SendAsync, zobrazí se informace o požadavku a odpovědi. Podívejme se na to. Název účtu úložiště je *contosorest*.

**Žádost:**

```
GET /?comp=list HTTP/1.1
```

**Hlavičky žádosti:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Stavový kód a hlavičky odpovědi se vrátí po spuštění:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Text odpovědi (XML):** Pro ListContainers to zobrazuje seznam kontejnery a jejich vlastnosti.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Teď, když je pochopit, jak vytvořit žádost, zavolá službu a analyzovat výsledky, Podívejme se na tom, jak vytvořit autorizační hlavičky. Vytvoření této hlavičky je složité, ale dobrou zprávou je, že až budete mít kód funguje, funguje u všech rozhraní REST API služby Storage.

## <a name="creating-the-authorization-header"></a>Vytváří se autorizační hlavičky.

> [!TIP]
> Azure Storage teď podporuje integraci služby Azure Active Directory (Azure AD) pro služby objektů Blob a frontu (Preview). Azure AD nabízí mnohem jednodušší prostředí pro autorizaci žádost do služby Azure Storage. Další informace o používání služby Azure AD k autorizaci operace REST, naleznete v tématu [ověřování pomocí Azure Active Directory (Preview)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Přehled integrace služby Azure AD pomocí služby Azure Storage najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory (Preview)](storage-auth-aad.md).

Existuje článek, který vysvětluje, koncepčně (žádný kód) provádění [ověřování pro služby Azure Storage](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Pojďme přesně zjištění využitelných dolů k tomuto článku je potřeba a zobrazit kód.

Nejprve použijte ověření sdíleným klíčem. Formát hlavičky autorizace vypadá takto:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Podpis pole je Hash-based Message ověřování kódu metoda HMAC () vytvořené z požadavku a počítané algoritmem SHA256 a kódované pomocí kódování Base64. Který je teď? (Tady přestane reagovat, dokonce ještě slyšeli slovo *ho kanonizovat* ještě.)

Tento fragment kódu ukazuje formát řetězce podpis sdíleného klíče:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

Většina z těchto polí jsou zřídka se používá. Pro úložiště objektů Blob zadejte příkaz, md5, délka obsahu, ho Kanonizovat záhlaví a ho Kanonizovat prostředků. Ostatní můžete nechat prázdné (ale put v `\n` aby věděl, že jsou prázdné).

Co jsou CanonicalizedHeaders a CanonicalizedResource? Funkční dotaz. Ve skutečnosti co dělá ho kanonizovat průměr? Aplikace Microsoft Word i nerozpoznal ho jako slovo. Tady je co [Wikipedia říká o převodu do kanonického tvaru](http://en.wikipedia.org/wiki/Canonicalization): *Z počítačových věd převodu do kanonického tvaru (někdy normalizaci nebo normalizace) je proces převodu dat, která má více než jednu možnou reprezentaci do "standard", "Normální (normal) nebo kanonickém tvaru.* V normální mluvit, to znamená, že seznam položek (například záhlaví, v případě ho Kanonizovat záhlaví) a standardizovat do požadovanému formátu. V podstatě Microsoft jste se rozhodli formátu a budete muset spárujte ji.

Začněme s tyto dvě kanonizovaného pole, protože jsou vyžadovány pro vytvoření autorizační hlavičky.

**Kanonizovaného záhlaví**

Pro vytvoření této hodnoty, načtení hlaviček, které začínají řetězcem "x - ms-" a je můžete seřadit, pak je naformátovat do řetězce `[key:value\n]` instancí, které jsou spojeny do jednoho řetězce. V tomto příkladu kanonizovaného záhlaví vypadat nějak takto: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Tady je kód používaný k vytvoření výstupu:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Kanonizovaného prostředků**

Tato část řetězce podpis představuje cílový požadavek na účet úložiště. Mějte na paměti, že je identifikátor URI požadavku `<http://contosorest.blob.core.windows.net/?comp=list>`, s skutečný název účtu (`contosorest` v tomto případě). V tomto příkladu se vrátí:

```
/contosorest/\ncomp:list
```

Pokud máte parametry dotazu, jedná se o těch také. Tady je kód také zpracovává další parametry dotazu a dotaz parametrů s více hodnotami. Mějte na paměti, že vytváříte tento kód pro práci pro všechna rozhraní REST API, které chcete zahrnout všechny možnosti, i v případě, že metoda ListContainers nemusí všechny z nich.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Teď, když kanonizovaného řetězců jsou nastaveny, Podívejme se na tom, jak vytvořit vlastní autorizační hlavičky. Začněte vytvořením řetězec podpis zprávy ve formátu StringToSign dříve zobrazí v tomto článku. Tento koncept je snazší k vysvětlení, používání komentáře v kódu, takže tady je, poslední metodu, která vrátí autorizační hlavičky:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Při spuštění tohoto kódu je výsledný MessageSignature vypadá takto:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Tady je konečná hodnota pro AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader je poslední záhlaví umístěn v záhlaví požadavku před publikováním odpovědi.

Který zahrnuje všechno, co potřebujete vědět, spolu s kódem, sestavit třídy, které lze použít k vytvoření žádosti o který se má použít pro volání rozhraní REST API služby Storage.

## <a name="how-about-another-example"></a>A co další příklad? 

Podívejme se na tom, jak změnit kód pro kontejner volání ListBlobs *kontejner 1*. Toto je téměř stejný jako kód pro zobrazení seznamu kontejnerů, pouze rozdíly se identifikátor URI a jak analyzovat odpověď. 

Když se podíváte na referenční dokumentaci pro [ListBlobs](/rest/api/storageservices/List-Blobs), zjistíte, že metoda je *získat* a je RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

V ListContainersAsyncREST změňte kód, který nastaví identifikátor URI pro rozhraní API pro ListBlobs. Název kontejneru je **kontejner 1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Kde zpracovat odpověď, změňte kód Hledat objekty BLOB místo kontejnery.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Pokud tuto ukázku spustit, získáte výsledky, jako jsou následující:

**Kanonizovaného hlavičky:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonizovaného prostředků:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Následující hodnoty jsou od [Fiddler](http://www.telerik.com/fiddler):

**Žádost:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Hlavičky žádosti:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Stavový kód a hlavičky odpovědi se vrátí po spuštění:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Text odpovědi (XML):** Tato odpověď XML obsahuje seznam objektů BLOB a jejich vlastnosti. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, jak vytvořit požadavek na rozhraní REST API k načtení seznamu kontejnerů nebo seznam objektů BLOB v kontejneru úložiště objektů blob. Také jste se naučili vytvoření ověření podpisu pro volání rozhraní REST API, jak ji používat v požadavku REST a jak prozkoumat odpovědi.

## <a name="next-steps"></a>Další postup

* [BLOB Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Souborové rozhraní REST API služby](/rest/api/storageservices/file-service-rest-api)
* [Rozhraní REST API služby front](/rest/api/storageservices/queue-service-rest-api)
