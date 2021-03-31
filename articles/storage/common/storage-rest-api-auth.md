---
title: Volání REST API operací s autorizací sdíleného klíče
titleSuffix: Discover how to call Azure Storage REST API operations with Shared Key authorization. Get detailed information about each step of the sample operation.
description: Pomocí Azure Storage REST API vytvořte požadavek na úložiště objektů BLOB pomocí autorizace pomocí sdíleného klíče.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: ozge
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f569fdac19c4f765828d24f4d6615fdd7bafef8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010898"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Volání REST API operací s autorizací sdíleného klíče

V tomto článku se dozvíte, jak volat Azure Storage rozhraní REST API, včetně toho, jak vytvořit autorizační hlavičku. Je napsaný z pohledu vývojáře, který neví nic o REST a neobsahuje žádné informace o tom, jak provést volání REST. Až se dozvíte, jak zavolat operaci REST, můžete tyto znalosti využít k používání dalších Azure Storagech operací REST.

## <a name="prerequisites"></a>Požadavky

Ukázková aplikace obsahuje seznam kontejnerů objektů BLOB pro účet úložiště. K tomu, abyste si vyzkoušeli kód v tomto článku, potřebujete následující položky:

- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) s úlohou **vývoj pro Azure** .

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Účet úložiště pro obecné účely. Pokud ještě nemáte účet úložiště, přečtěte si téma [Vytvoření účtu úložiště](storage-account-create.md).

- V příkladu v tomto článku se dozvíte, jak vytvořit seznam kontejnerů v účtu úložiště. Pokud chcete zobrazit výstup, přidejte do úložiště objektů BLOB v účtu úložiště nějaké kontejnery, než začnete.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace je Konzolová aplikace napsaná v jazyce C#.

Pomocí [Gitu](https://git-scm.com/) si stáhněte kopii aplikace do vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Chcete-li otevřít řešení sady Visual Studio, vyhledejte složku Storage-dotnet-REST-API-with-auth, otevřete ji a dvakrát klikněte na StorageRestApiAuth. sln.

## <a name="about-rest"></a>O REST

V případě, že je *přenos stavu znovu prezentační*, zůstane. Konkrétní definici najdete v [Wikipedii](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST je architektura, která umožňuje interakci se službou prostřednictvím internetového protokolu, například HTTP/HTTPS. REST je nezávislé na softwaru spuštěném na serveru nebo klientovi. REST API lze volat z jakékoli platformy, která podporuje protokol HTTP/HTTPS. Můžete napsat aplikaci, která běží na Macu, Windows, Linux, telefonu s Androidem nebo tabletu, iPhonu, iPod nebo na webu a používat stejný REST API pro všechny tyto platformy.

Volání REST API se skládá z požadavku, který je vytvořen klientem, a odpovědí, která je vrácena službou. V žádosti odešlete adresu URL s informacemi o tom, jakou operaci chcete zavolat, prostředku, který se má jednat, všech parametrech dotazů a hlavičkách a v závislosti na operaci, která byla volána, datovou část dat. Odpověď ze služby zahrnuje stavový kód, sadu hlaviček odpovědí a v závislosti na operaci, která byla volána, datovou část dat.

## <a name="about-the-sample-application"></a>O ukázkové aplikaci

Ukázková aplikace vypíše kontejnery v účtu úložiště. Jakmile pochopíte, jak jsou informace v dokumentaci REST API v korelaci s vaším skutečným kódem, je snazší zjistit další volání REST.

Pokud se podíváte na [REST API služby BLOB Service](/rest/api/storageservices/Blob-Service-REST-API), zobrazí se všechny operace, které můžete provádět v úložišti objektů BLOB. Klientské knihovny pro úložiště jsou obálkami rozhraní REST API – usnadňují přístup k úložišti bez použití rozhraní REST API přímo. Ale jak je uvedeno výše, někdy chcete místo klientské knihovny pro úložiště použít REST API.

## <a name="list-containers-operation"></a>Operace výpisu kontejnerů

Projděte si odkaz na operaci [ListContainers](/rest/api/storageservices/List-Containers2) . Tyto informace vám pomůžou pochopit, kde některá pole pocházejí z v žádosti a odpovědi.

**Metoda požadavku**: získat. Tato operace je metoda HTTP, kterou zadáte jako vlastnost objektu Request. Mezi další hodnoty tohoto příkazu patří HEADers, PUT a DELETE v závislosti na rozhraní API, které voláte.

**Identifikátor URI žádosti**: `https://myaccount.blob.core.windows.net/?comp=list` .  Identifikátor URI žádosti se vytvoří z koncového bodu účtu úložiště objektů BLOB `https://myaccount.blob.core.windows.net` a řetězce prostředků `/?comp=list` .

[Parametry identifikátoru URI](/rest/api/storageservices/List-Containers2#uri-parameters): existují další parametry dotazu, které lze použít při volání ListContainers. Několik z těchto parametrů je *časovým limitem* pro volání (v sekundách) a *předponou*, která se používá pro filtrování.

Dalším užitečným parametrem je *MaxResults:* Pokud je k dispozici více kontejnerů než tato hodnota, text odpovědi bude obsahovat element *NextMarker* , který indikuje další kontejner, který se má vrátit na další požadavek. Chcete-li použít tuto funkci, zadejte hodnotu *NextMarker* jako parametr *značky* v identifikátoru URI při provedení dalšího požadavku. Při použití této funkce je podobná stránkování prostřednictvím výsledků.

Chcete-li použít další parametry, přidejte je k řetězci prostředku s hodnotou, například v tomto příkladu:

```
/?comp=list&timeout=60&maxresults=100
```

[Hlavičky žádosti](/rest/api/storageservices/List-Containers2#request-headers)**:** v této části jsou uvedeny povinné a volitelné hlavičky požadavků. Jsou požadovány tři hlavičky: *autorizační* hlavička, *x-MS-Date* (obsahuje čas UTC pro požadavek) a *x-MS-Version* (určuje verzi REST API, která se má použít). Zahrnutí *x-MS-Client-Request-ID* do hlaviček je volitelné – můžete nastavit hodnotu pro toto pole na cokoli. Když je povolené protokolování, zapisuje se do protokolů analýzy úložiště.

[Text žádosti](/rest/api/storageservices/List-Containers2#request-body)**:** pro ListContainers není k dispozici žádný text žádosti. Text žádosti se používá na všech operacích PUT při nahrávání objektů BLOB a také na SetContainerAccessPolicy, což umožňuje, aby se odesílaly v seznamu XML uložených zásad přístupu, které se mají použít. Zásady uloženého přístupu jsou popsány v článku [Používání podpisů sdíleného přístupu (SAS)](storage-sas-overview.md).

[Stavový kód odpovědi](/rest/api/storageservices/List-Containers2#status-code)**:** oznamuje všechny stavové kódy, které potřebujete znát. V tomto příkladu je stavový kód HTTP 200 OK. Úplný seznam stavových kódů HTTP najdete v části [definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Kódy chyb specifické pro rozhraní REST API pro úložiště najdete v tématu [běžné kódy chyb REST API](/rest/api/storageservices/common-rest-api-error-codes)

[Hlavičky odpovědi](/rest/api/storageservices/List-Containers2#response-headers)**:** patří sem tento *typ obsahu*; *x-MS-Request-ID*, což je ID žádosti, kterou jste předali. *x-MS-Version*, která označuje verzi používaného BLOB Service; a *Datum*, které je ve standardu UTC, a informuje o tom, jaká doba byla vytvořena.

[Tělo odpovědi](/rest/api/storageservices/List-Containers2#response-body): Toto pole je struktura XML poskytující požadovaná data. V tomto příkladu je odpověď seznamem kontejnerů a jejich vlastností.

## <a name="creating-the-rest-request"></a>Vytváření žádosti REST

V případě zabezpečení při provozu v produkčním prostředí vždy použijte protokol HTTPS místo HTTP. Pro účely tohoto cvičení byste měli použít protokol HTTP, abyste mohli zobrazit data žádosti a odpovědi. Pokud chcete zobrazit informace o žádosti a odpovědi ve skutečných voláních REST, můžete si stáhnout [Fiddler](https://www.telerik.com/fiddler) nebo podobnou aplikaci. V řešení sady Visual Studio se název a klíč účtu úložiště pevně zakódované ve třídě. Metoda ListContainersAsyncREST předá do metod, které slouží k vytvoření různých součástí žádosti REST, název účtu úložiště a klíč účtu úložiště. V reálné aplikaci se název a klíč účtu úložiště nacházejí v konfiguračním souboru, proměnných prostředí nebo je možné je načíst z Azure Key Vault.

V našem ukázkovém projektu je kód pro vytvoření autorizační hlavičky v samostatné třídě. Nápad je, že můžete použít celou třídu a přidat ji do vlastního řešení a použít ji "tak, jak je". Kód autorizační hlavičky funguje pro většinu REST API volání Azure Storage.

Chcete-li vytvořit požadavek, který je objektem zprávy HttpRequestMessage, v programu program. cs použijte ListContainersAsyncREST. Postup pro sestavování žádosti:

- Vytvořte identifikátor URI, který se má použít pro volání služby.
- Vytvořte objekt zprávy HttpRequestMessage a nastavte datovou část. Datová část má pro ListContainersAsyncREST hodnotu null, protože neprovádíme žádnou možnost.
- Přidejte hlavičky žádosti pro x-MS-Date a x-MS-Version.
- Získejte autorizační hlavičku a přidejte ji.

Některé základní informace, které potřebujete:

- Pro ListContainers je **Metoda** `GET` . Tato hodnota je nastavena při vytváření instance žádosti.
- **Prostředek** je DOTAZOVACÍ část identifikátoru URI, která určuje, které rozhraní API se zavolá, takže hodnota je `/?comp=list` . Jak bylo uvedeno výše, prostředek se nachází na stránce Referenční dokumentace, která zobrazuje informace o [rozhraní ListContainers API](/rest/api/storageservices/List-Containers2).
- Identifikátor URI je vytvořený vytvořením koncového bodu Blob service pro tento účet úložiště a zřetězením prostředku. Hodnota **identifikátoru URI žádosti** končí `http://contosorest.blob.core.windows.net/?comp=list` .
- V případě ListContainers má **částmi** hodnotu null a nejsou k dispozici žádné další **hlavičky**.

Různá rozhraní API mohou mít další parametry, které budou předávat například *ifMatch*. Příklad, kde můžete použít ifMatch, je při volání PutBlob. V takovém případě nastavíte ifMatch na eTag a aktualizuje jenom objekt blob, pokud se značka eTag, kterou zadáte, shoduje s aktuálním eTag objektu BLOB. Pokud někdo jiný aktualizoval objekt BLOB od načtení značky eTag, jejich změna se nepřepisuje.

Nejprve nastavte `uri` a `payload` .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Dále vytvořte instanci požadavku a nastavte metodu na `GET` a poskytněte identifikátor URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Přidejte hlavičky žádosti pro `x-ms-date` a `x-ms-version` . Toto místo v kódu je také tam, kde přidáte další hlavičky požadavků vyžadované pro volání. V tomto příkladu nejsou k dispozici žádné další hlavičky. Příkladem rozhraní API, které projde do dodatečných hlaviček, je operace nastavení seznamu ACL kontejneru. Toto volání rozhraní API přidá hlavičku s názvem "x-MS-BLOB-Public-Access" a hodnotou úrovně přístupu.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Zavolejte metodu, která vytvoří autorizační hlavičku a přidá ji do hlaviček požadavku. Později v článku se dozvíte, jak vytvořit autorizační hlavičku. Název metody je GetAuthorizationHeader, který vidíte v tomto fragmentu kódu:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

V tomto okamžiku `httpRequestMessage` obsahuje žádost REST úplnou hlavičkou autorizace.

## <a name="send-the-request"></a>Odeslat žádost

Teď, když jste sestavili žádost, můžete zavolat metodu SendAsync a odeslat ji Azure Storage. Ověřte, zda je hodnota stavového kódu odpovědi 200, což znamená, že operace byla úspěšná. Dále Analyzujte odpověď. V takovém případě získáte seznam XML kontejnerů. Pojďme se podívat na kód pro volání metody GetRESTRequest k vytvoření žádosti, provedení žádosti a následné kontrole odpovědi na seznam kontejnerů.

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

Pokud při volání SendAsync spustíte síťový Sniffer, jako je třeba [Fiddler](https://www.telerik.com/fiddler) , můžete zobrazit informace o žádosti a odpovědi. Pojďme se na to podívat. Název účtu úložiště je *contosorest*.

**Request**

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

**Stavový kód a hlavičky odpovědi vrácené po provedení:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Tělo odpovědi (XML):** V případě operace seznam kontejnerů se zobrazí seznam kontejnerů a jejich vlastností.

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

Teď, když rozumíte tomu, jak vytvořit žádost, zavolat službu a analyzovat výsledky, Pojďme se podívat, jak vytvořit autorizační hlavičku. Vytvoření tohoto záhlaví je složité, ale dobrá zpráva je, že jakmile kód funguje, funguje pro všechna rozhraní REST API služby Storage.

## <a name="creating-the-authorization-header"></a>Vytváření autorizační hlavičky

> [!TIP]
> Azure Storage teď podporuje integraci Azure Active Directory (Azure AD) pro objekty BLOB a fronty. Azure AD nabízí mnohem jednodušší prostředí pro autorizaci žádosti o Azure Storage. Další informace o používání služby Azure AD k autorizaci operací REST najdete v tématu [autorizace pomocí Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory). Přehled integrace služby Azure AD s Azure Storage najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).

K dispozici je článek, který vysvětluje koncepční princip (žádný kód), jak [autorizovat požadavky na Azure Storage](/rest/api/storageservices/authorize-requests-to-azure-storage).

Pojďme na to, aby tento článek byl co nejpřesněji, a zobrazil kód.

Nejdřív použijte autorizaci pomocí sdíleného klíče. Formát autorizační hlavičky vypadá takto:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole signatura je ověřovací kód zprávy založený na hodnotě hash (HMAC) vytvořený z požadavku a vypočítaný pomocí algoritmu SHA256 a pak je kódovaný pomocí kódování Base64. Dostali jste to? (V takovém případě jste si ještě neslyšeli slovo v *kanonickém* formátu.)

Tento fragment kódu ukazuje formát řetězce podpisu sdíleného klíče:

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

Většina těchto polí se používá zřídka. Pro úložiště objektů BLOB zadáte příkazy, MD5, délku obsahu, kanonické hlavičky a kanonický prostředek. Ostatní můžete nechat prázdné (ale umístit je do, `\n` aby znali, že jsou prázdné).

Co jsou CanonicalizedHeaders a CanonicalizedResource? Dobrá otázka. Co je ve skutečnosti kanonický význam? Microsoft Word ho ani nerozpozná jako slovo. V tomto článku najdete [Wikipedii o kanonikalizaci](https://en.wikipedia.org/wiki/Canonicalization): *v počítačové vědy, kanonikalizaci (někdy normalizace nebo normalizace) je proces převodu dat, která mají více než jednu možnou reprezentaci do "Standard", "normálního" nebo kanonického tvaru.* V normálním diktování to znamená převzít seznam položek (například hlavičky v případě kanonických hlaviček) a sjednotit je v požadovaném formátu. V podstatě se společnost Microsoft rozhodla o formátu a je nutné ji přiřadit.

Pojďme začít s těmito dvěma kanonickými poli, protože jsou nutné k vytvoření autorizační hlavičky.

### <a name="canonicalized-headers"></a>Kanonické hlavičky

Chcete-li vytvořit tuto hodnotu, načtěte hlavičky začínající řetězcem "x-MS-" a seřaďte je a naformátujte je do řetězce `[key:value\n]` instancí, zřetězené do jednoho řetězce. V tomto příkladu budou kanonické hlavičky vypadat takto:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Zde je kód, který slouží k vytvoření tohoto výstupu:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Kanonický prostředek

Tato část řetězce podpisu představuje účet úložiště, který cílí na požadavek. Mějte na paměti, že identifikátor URI žádosti je `<http://contosorest.blob.core.windows.net/?comp=list>` s skutečným názvem účtu ( `contosorest` v tomto případě). V tomto příkladu se vrátí:

```
/contosorest/\ncomp:list
```

Pokud máte parametry dotazu, tento příklad obsahuje také tyto parametry. Zde je kód, který také zpracovává další parametry dotazu a parametry dotazu s více hodnotami. Nezapomeňte, že vytváříte tento kód, který bude fungovat pro všechna rozhraní REST API. Chcete zahrnout všechny možnosti, a to i v případě, že metoda ListContainers je nepotřebuje.

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
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Teď, když jsou kanonické řetězce nastavené, Podívejme se na to, jak vytvořit vlastní hlavičku autorizace. Začnete vytvořením řetězce podpisu zprávy ve formátu StringToSign dříve zobrazeném v tomto článku. Tento koncept je snazší vysvětlit pomocí komentářů v kódu, takže tady je poslední metoda, která vrátí hlavičku autorizace:

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
        storageAccountName + ":" + signature);
    return authHV;
}
```

Po spuštění tohoto kódu vypadá výsledný MessageSignature jako v tomto příkladu:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Tady je poslední hodnota pro AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader je poslední hlavička umístěná v hlavičce požadavku před odesláním odpovědi.

To pokrývá všechno, co potřebujete znát, abyste mohli spojit třídu, pomocí které můžete vytvořit žádost o volání rozhraní REST API služby Storage.

## <a name="example-list-blobs"></a>Příklad: seznam objektů BLOB

Pojďme se podívat, jak kód změnit, aby se volala operace objektů BLOB seznamu pro *kontejner kontejneru-1*. Tento kód je téměř totožný s kódem pro výpisy kontejnerů, jedinými rozdíly jsou identifikátory URI a způsob analýzy odpovědi.

Pokud se podíváte na referenční dokumentaci pro [ListBlobs](/rest/api/storageservices/List-Blobs), zjistíte, že je metoda *Get* , a RequestURI je:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

V ListContainersAsyncREST změňte kód, který nastaví identifikátor URI na rozhraní API pro ListBlobs. Název kontejneru je **kontejner-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Poté, kde odpověď zpracováváte, změňte kód tak, aby místo kontejnerů hledal objekty blob.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Při spuštění této ukázky získáte výsledky, jako jsou tyto:

**Kanonické hlavičky:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonický prostředek:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Podpis zprávy:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Autorizační hlavička:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Následující hodnoty jsou z [Fiddler](https://www.telerik.com/fiddler):

**Request**

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

**Stavový kód a hlavičky odpovědi vrácené po provedení:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Tělo odpovědi (XML):** Tato odpověď XML zobrazuje seznam objektů BLOB a jejich vlastností.

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

V tomto článku jste zjistili, jak vytvořit požadavek na úložiště objektů BLOB REST API. S požadavkem můžete načíst seznam kontejnerů nebo seznam objektů BLOB v kontejneru. Zjistili jste, jak vytvořit autorizační podpis pro volání REST API a jak ho použít v žádosti REST. Nakonec jste zjistili, jak můžete odpověď prostudovat.

## <a name="next-steps"></a>Další kroky

- [Rozhraní REST API služby Blob Service](/rest/api/storageservices/blob-service-rest-api)
- [Rozhraní REST API služby File Service](/rest/api/storageservices/file-service-rest-api)
- [Rozhraní REST API služby Queue Service](/rest/api/storageservices/queue-service-rest-api)
- [Rozhraní REST API služby Table Service](/rest/api/storageservices/table-service-rest-api)
