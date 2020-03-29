---
title: Volání operací rozhraní REST API s autorizací sdíleného klíče
titleSuffix: Azure Storage
description: Pomocí rozhraní AZURE Storage REST API můžete vytvořit požadavek na úložiště objektů Blob pomocí autorizace sdíleného klíče.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5c6125b850062450516e7fc0b19c2e0d5d6f577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916060"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Volání operací rozhraní REST API s autorizací sdíleného klíče

Tento článek ukazuje, jak volat Azure Storage REST API, včetně jak vytvořit hlavičku autorizace. Je napsán z pohledu vývojáře, který neví nic o REST a netuší, jak volat REST. Až se naučíte volat operaci REST, můžete tyto znalosti využít k použití jiných operací Azure Storage REST.

## <a name="prerequisites"></a>Požadavky

Ukázková aplikace obsahuje seznam kontejnerů objektů blob pro účet úložiště. Chcete-li vyzkoušet kód v tomto článku, potřebujete následující položky:

- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) s **úlohou vývoje Azure.**

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

- Účet úložiště pro obecné účely. Pokud ještě nemáte účet úložiště, přečtěte [si tématu Vytvoření účtu úložiště](storage-account-create.md).

- Příklad v tomto článku ukazuje, jak seznam kontejnerů v účtu úložiště. Pokud chcete zobrazit výstup, přidejte některé kontejnery do úložiště objektů blob v účtu úložiště před zahájením.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace je konzolová aplikace napsaná v c#.

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Chcete-li otevřít řešení Sady Visual Studio, vyhledejte složku storage-dotnet-rest-api-with-auth, otevřete ji a poklepejte na StorageRestApiAuth.sln.

## <a name="about-rest"></a>O společnosti REST

REST je zkratka pro *reprezentační přenos stavu*. Pro konkrétní definici, podívejte se na [Wikipedii](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST je architektura, která umožňuje interakci se službou prostřednictvím internetového protokolu, jako je například HTTP/HTTPS. REST je nezávislý na softwaru spuštěném na serveru nebo na klientovi. Rozhraní REST API lze volat z libovolné platformy, která podporuje HTTP/HTTPS. Můžete napsat aplikaci, která běží na Mac, Windows, Linux, Android telefon nebo tablet, iPhone, iPod, nebo webové stránky, a používat stejné ROZHRANÍ REST API pro všechny tyto platformy.

Volání rozhraní REST API se skládá z požadavku, který je proveden klientem a odpověď, která je vrácena službou. V žádosti odešlete adresu URL s informacemi o operaci, kterou chcete volat, prostředek, na kterém chcete pracovat, všechny parametry dotazu a záhlaví a v závislosti na operaci, která byla volána, datová část dat. Odpověď ze služby obsahuje stavový kód, sadu hlaviček odpovědí a v závislosti na operaci, která byla volána, datovou část dat.

## <a name="about-the-sample-application"></a>O ukázkové aplikaci

Ukázková aplikace uvádí kontejnery v účtu úložiště. Jakmile pochopíte, jak informace v dokumentaci rozhraní REST API koreluje s vaším skutečným kódem, ostatní volání REST jsou snadněji zjistit.

Pokud se podíváte na [rozhraní REST API služby blob](/rest/api/storageservices/Blob-Service-REST-API), uvidíte všechny operace, které můžete provádět na úložišti objektů blob. Knihovny klientů úložiště jsou obálky kolem REST API – usnadňují přístup k úložišti bez přímého použití rest API. Ale jak je uvedeno výše, někdy chcete použít rozhraní REST API namísto knihovny klienta úložiště.

## <a name="list-containers-operation"></a>Seznam kontejnerů operace

Zkontrolujte odkaz na operaci [ListContainers.](/rest/api/storageservices/List-Containers2) Tyto informace vám pomohou pochopit, odkud pocházejí některá pole v požadavku a odpovědi.

**Metoda požadavku**: GET. Toto sloveso je metoda HTTP, kterou zadáte jako vlastnost objektu požadavku. Další hodnoty pro toto sloveso patří HEAD, PUT a DELETE, v závislosti na rozhraní API, které voláte.

**Identifikátor**URI `https://myaccount.blob.core.windows.net/?comp=list`požadavku : .Identifikátor URI požadavku je vytvořen z koncového bodu `http://myaccount.blob.core.windows.net` `/?comp=list`účtu úložiště objektů blob a řetězce prostředků .

[Parametry IDENTIFIKÁTORU URI](/rest/api/storageservices/List-Containers2#uri-parameters): Existují další parametry dotazu, které můžete použít při volání ListContainers. Několik z těchto parametrů jsou *časový rozsah* pro volání (v sekundách) a *předpona*, která se používá pro filtrování.

Dalším užitečným parametrem je *maxresults:* pokud více kontejnerů jsou k dispozici než tato hodnota, tělo odpovědi bude obsahovat *NextMarker* prvek, který označuje další kontejner vrátit na další požadavek. Chcete-li použít tuto funkci, zadejte *nextmarker* hodnotu jako parametr *značky* v URI při dalším požadavku. Při použití této funkce je analogické stránkování výsledky.

Chcete-li použít další parametry, připojte je k řetězci prostředku s hodnotou, například v tomto příkladu:

```
/?comp=list&timeout=60&maxresults=100
```

[Hlavičky požadavků](/rest/api/storageservices/List-Containers2#request-headers)**:** V této části jsou uvedeny povinné a volitelné hlavičky požadavků. Jsou vyžadovány tři hlavičky: hlavička *autorizace,* *x-ms-date* (obsahuje čas UTC pro požadavek) a *verze x-ms* (určuje verzi rozhraní REST API, která má být používána). Zahrnutí *x-ms-client-request-id* do záhlaví je volitelné – hodnotu tohoto pole můžete nastavit na cokoli; je zapsán a protokoly analýzy úložiště při protokolování je povolena.

[Tělo požadavku](/rest/api/storageservices/List-Containers2#request-body)**:** Neexistuje žádný text požadavku pro ListContainers. Tělo požadavku se používá na všechny operace PUT při nahrávání objektů BLOB, stejně jako SetContainerAccessPolicy, který umožňuje odeslat seznam XML uložených zásad přístupu použít. Uložené zásady přístupu jsou popsány v článku [Pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

[Kód stavu odpovědi](/rest/api/storageservices/List-Containers2#status-code)**:** Informuje o všech stavových kódech, které potřebujete znát. V tomto příkladu je stavový kód HTTP 200 v pořádku. Úplný seznam stavových kódů HTTP naleznete v části [Definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Kódy chyb specifické pro rozhraní API STORAGE REST naleznete v [tématu Common REST API error codes](/rest/api/storageservices/common-rest-api-error-codes)

[Záhlaví odpovědí](/rest/api/storageservices/List-Containers2#response-headers)**:** Patří mezi ně *typ obsahu*; *x-ms-request-id*, což je ID žádosti, které jste předali; *x-ms-version*, která označuje použitou verzi služby Blob; a *Datum*, které je v UTC a říká, kdy byl požadavek podán.

[Tělo odpovědi](/rest/api/storageservices/List-Containers2#response-body): Toto pole je struktura XML poskytující požadovaná data. V tomto příkladu je odpověď seznam kontejnerů a jejich vlastnosti.

## <a name="creating-the-rest-request"></a>Vytvoření požadavku REST

Pro zabezpečení při spuštění v produkčním prostředí vždy používejte protokol HTTPS spíše než PROTOKOL HTTP. Pro účely tohoto cvičení byste měli použít protokol HTTP, abyste mohli zobrazit data požadavku a odpovědi. Chcete-li zobrazit informace o požadavku a odpovědi ve skutečných volánírest, můžete si stáhnout [Šumař](https://www.telerik.com/fiddler) nebo podobné aplikace. V řešení Visual Studio název účtu úložiště a klíč jsou pevně zakódovány ve třídě. ListContainersAsyncREST Metoda předá název účtu úložiště a klíč účtu úložiště metody, které se používají k vytvoření různých součástí požadavku REST. V aplikaci reálného světa by se název a klíč účtu úložiště nastořil v konfiguračním souboru, proměnných prostředí nebo by se načítaný z trezoru klíčů Azure.

V našem ukázkovém projektu je kód pro vytvoření hlavičky Autorizace v samostatné třídě. Myšlenka je, že byste mohli vzít celou třídu a přidat ji do svého vlastního řešení a použít ji "tak, jak je". Kód hlavičky autorizace funguje pro většinu volání rozhraní REST API do Azure Storage.

Chcete-li vytvořit požadavek, který je objekthttpRequestMessage, přejděte na ListContainersAsyncREST v Program.cs. Kroky pro sestavení požadavku jsou:

- Vytvořte identifikátor URI, který se použije pro volání služby.
- Vytvořte objekt HttpRequestMessage a nastavte datovou část. Datová část je null pro ListContainersAsyncREST, protože nejsme předávání nic.
- Přidejte hlavičky požadavku pro x-ms-date a x-ms-version.
- Získejte hlavičku autorizace a přidejte ji.

Některé základní informace, které potřebujete:

- Pro ListContainers **metoda** `GET`je . Tato hodnota je nastavena při vytváření instancí požadavku.
- **Prostředek** je dotaz část identifikátoru URI, který označuje, které `/?comp=list`rozhraní API je volána, takže hodnota je . Jak již bylo uvedeno dříve, prostředek je na stránce referenční dokumentace, která zobrazuje informace o [ListContainers ROZHRANÍ API](/rest/api/storageservices/List-Containers2).
- Identifikátor URI je vytvořen vytvořením koncového bodu služby Objektů blob pro tento účet úložiště a zřetězením prostředku. Hodnota **identifikátoru URI** požadavku `http://contosorest.blob.core.windows.net/?comp=list`skončí jako .
- Pro ListContainers **requestBody** je null a neexistují žádné další **hlavičky**.

Různá rozhraní API může mít další parametry předat například *ifMatch*. Příklad, kde můžete použít ifMatch je při volání PutBlob. V takovém případě nastavíte ifMatch na eTag a aktualizuje pouze objekt blob, pokud eTag, který zadáte, odpovídá aktuálnímu eTag u objektu blob. Pokud někdo jiný aktualizoval objekt blob od načtení eTag, jejich změna nebude přepsána.

Nejprve nastavte `uri` a `payload`.

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Dále vytvořte instanci požadavku, nastavení `GET` metody a poskytnutí identifikátoru URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Přidejte záhlaví požadavku `x-ms-date` `x-ms-version`pro a . Toto místo v kódu je také místo, kde přidáte všechny další hlavičky požadavku potřebné pro volání. V tomto příkladu neexistují žádné další záhlaví. Příkladem rozhraní API, které prochází v hlavičkách navíc, je operace ACL sady kontejnerů. Toto volání rozhraní API přidá záhlaví s názvem "x-ms-blob-public-access" a hodnotu pro úroveň přístupu.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Volání metody, která vytvoří hlavičku autorizace a přidejte ji do hlavičky požadavku. Jak vytvořit hlavičku autorizace dále v článku. Název metody je GetAuthorizationHeader, který můžete vidět v tomto fragmentu kódu:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

V tomto `httpRequestMessage` okamžiku obsahuje požadavek REST kompletní s hlavičkami autorizace.

## <a name="send-the-request"></a>Odeslat žádost

Teď, když jste vytvořili požadavek, můžete volat SendAsync metodu k odeslání do služby Azure Storage. Zkontrolujte, zda je hodnota kódu stavu odpovědi 200, což znamená, že operace byla úspěšná. Dále analyzovat odpověď. V takovém případě získáte seznam XML kontejnerů. Podívejme se na kód pro volání GetRESTRequest metoda k vytvoření požadavku, spusťte požadavek a potom zkontrolujte odpověď pro seznam kontejnerů.

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

Pokud spustíte síť sniffer, jako je [Fiddler](https://www.telerik.com/fiddler) při volání SendAsync, můžete zobrazit informace o požadavku a odpovědi. Pojďme se na to podívat. Název účtu úložiště je *nejpřímý .*

**Požadavek:**

```
GET /?comp=list HTTP/1.1
```

**Hlavičky požadavků:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Stavový kód a hlavičky odpovědí vrácené po spuštění:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Tělo odpovědi (XML):** Pro operaci Seznam kontejnerů to ukazuje seznam kontejnerů a jejich vlastnosti.

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

Teď, když jste pochopili, jak vytvořit požadavek, volat službu a analyzovat výsledky, podívejme se, jak vytvořit hlavičku autorizace. Vytvoření tohoto záhlaví je složité, ale dobrou zprávou je, že jakmile máte kód funguje, funguje to pro všechny storage service REST API.

## <a name="creating-the-authorization-header"></a>Vytvoření hlavičky autorizace

> [!TIP]
> Azure Storage teď podporuje integraci Azure Active Directory (Azure AD) pro objekty BLOB a fronty. Azure AD nabízí mnohem jednodušší prostředí pro autorizaci požadavku na Azure Storage. Další informace o použití Azure AD k autorizaci operací REST najdete [v tématu Autorizace pomocí služby Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory). Přehled integrace Azure AD s Azure Storage najdete [v tématu Ověřování přístupu k Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).

Je článek, který vysvětluje koncepčně (žádný kód), jak [autorizovat požadavky na Azure Storage](/rest/api/storageservices/authorize-requests-to-azure-storage).

Pojďme destilovat, že článek se přesně je potřeba a ukázat kód.

Nejprve použijte autorizaci sdíleného klíče. Formát hlavičky autorizace vypadá takto:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole podpisu je kód hmac (Hash-based Message Authentication Code) vytvořený z požadavku a vypočtený pomocí algoritmu SHA256, který je poté kódován pomocí kódování Base64. Máš to? (Vydržte tam, jste ještě ani neslyšeli slovo *kanonizovaný* ještě.)

Tento fragment kódu zobrazuje formát řetězce podpisu sdíleného klíče:

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

Většina těchto polí se používá jen zřídka. Pro úložiště objektů blob zadáte sloveso, md5, délku obsahu, kanonizovaná záhlaví a kanonický prostředek. Ostatní můžete nechat prázdné (ale `\n` dát do tak, že ví, že jsou prázdné).

Co jsou CanonicalizedHeaders a CanonicalizedResource? Dobrá otázka. Ve skutečnosti, co znamená kanonizovaný? Aplikace Microsoft Word jej ani nerozpozná jako slovo. Zde je to, co [Wikipedie říká o kanonizaci](https://en.wikipedia.org/wiki/Canonicalization): *V informatice je kanonizace (někdy normalizace nebo normalizace) procesem převodu dat, který má více než jednu možnou reprezentaci do "standardu", "normální" nebo kanonické formy.* V normálním mluvení to znamená vzít seznam položek (například záhlaví v případě kanonických záhlaví) a standardizovat je do požadovaného formátu. V podstatě se Společnost Microsoft rozhodla pro formát a musíte jej porovnat.

Začněme s těmito dvěma kanonickými poli, protože jsou nutné k vytvoření hlavičky Autorizace.

### <a name="canonicalized-headers"></a>Záhlaví kanonické

Chcete-li vytvořit tuto hodnotu, načtěte záhlaví, které začínají "x-ms-", a seřaďte je a pak je naformátujte do řetězce `[key:value\n]` instancí, zřetězených do jednoho řetězce. V tomto příkladu vypadají kanonické hlavičky takto:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Zde je kód použitý k vytvoření tohoto výstupu:

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

### <a name="canonicalized-resource"></a>Kanonický zdroj

Tato část řetězce podpisu představuje účet úložiště, na který je požadavek zaměřen. Nezapomeňte, že identifikátor `<http://contosorest.blob.core.windows.net/?comp=list>`URI požadavku je`contosorest` s názvem skutečného účtu ( v tomto případě). V tomto příkladu je vrácena:

```
/contosorest/\ncomp:list
```

Pokud máte parametry dotazu, tento příklad zahrnuje také tyto parametry. Zde je kód, který také zpracovává další parametry dotazu a parametry dotazu s více hodnotami. Nezapomeňte, že vytváříte tento kód pro všechny rest API. Chcete zahrnout všechny možnosti, i v případě, že ListContainers metoda nepotřebuje všechny z nich.

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

Nyní, když jsou nastaveny kanonické řetězce, podívejme se na to, jak vytvořit samotnou hlavičku autorizace. Můžete začít vytvořením řetězce podpisu zprávy ve formátu StringToSign dříve zobrazené v tomto článku. Tento koncept je snadněji vysvětlit pomocí komentáře v kódu, tak tady to je, konečná metoda, která vrací hlavičku autorizace:

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

Při spuštění tohoto kódu výsledný MessageSignature vypadá v tomto příkladu:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Zde je konečná hodnota authorizationheader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader je poslední hlavička umístěná v hlavičkách požadavku před odesláním odpovědi.

To zahrnuje vše, co potřebujete vědět, abyste dali dohromady třídu, pomocí které můžete vytvořit požadavek na volání rest API služby Storage Services.

## <a name="example-list-blobs"></a>Příklad: Objekty BLOB seznamu

Podívejme se na to, jak změnit kód volat seznam objektů BLOB operace pro *kontejner kontejneru-1*. Tento kód je téměř totožný s kódem pro výpis kontejnerů, pouze rozdíly jsou IDENTIFIKÁTOR URI a jak analyzovat odpověď.

Pokud se podíváte na referenční dokumentaci pro [ListBlobs](/rest/api/storageservices/List-Blobs), zjistíte, že metoda je *GET* a RequestURI je:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

V ListContainersAsyncREST změňte kód, který nastaví identifikátor URI na rozhraní API pro ListBlobs. Název kontejneru je **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Pak kde zpracovat odpověď, změňte kód hledat objekty BLOB namísto kontejnerů.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Při spuštění této ukázky získáte výsledky, jako je následující:

**Kanonické hlavičky:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonizovaný zdroj:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Podpis zprávy:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Hlavička autorizace:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Následující hodnoty jsou z [Šumař](https://www.telerik.com/fiddler):

**Požadavek:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Hlavičky požadavků:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Stavový kód a hlavičky odpovědí vrácené po spuštění:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Tělo odpovědi (XML):** Tato odpověď XML zobrazuje seznam objektů BLOB a jejich vlastnosti.

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

V tomto článku jste se naučili, jak vytvořit požadavek na rozhraní REST API úložiště objektů blob. S požadavkem můžete načíst seznam kontejnerů nebo seznam objektů BLOB v kontejneru. Zjistili jste, jak vytvořit podpis autorizace pro volání rozhraní REST API a jak jej použít v požadavku REST. Nakonec jste se naučili, jak prozkoumat odpověď.

## <a name="next-steps"></a>Další kroky

- [Rozhraní REST API služby Blob Service](/rest/api/storageservices/blob-service-rest-api)
- [Rozhraní REST API služby File Service](/rest/api/storageservices/file-service-rest-api)
- [Rozhraní REST API služby Queue Service](/rest/api/storageservices/queue-service-rest-api)
- [Rozhraní REST API služby Table Service](/rest/api/storageservices/table-service-rest-api)
