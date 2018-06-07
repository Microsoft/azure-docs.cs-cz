---
title: Volání operace Azure Storage Services REST API, včetně ověřování | Microsoft Docs
description: Volání operace Azure Storage Services REST API, včetně ověřování
services: storage
author: tamram
manager: twooley
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: tamram
ms.openlocfilehash: 6009ebd18eb089b21c98d6f7d9f49044a8d96098
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650447"
---
# <a name="using-the-azure-storage-rest-api"></a>Použití rozhraní REST API pro službu Azure Storage

Tento článek ukazuje, jak používat rozhraní API REST služby úložiště objektů Blob a postup ověření volání do služby. Je zapsán z hlediska osoby, která neví nic o REST a žádné nápad postup volání REST, ale je vývojář. Jsme podívejte se na referenční dokumentaci k nástroji pro volání REST a zjistit, jak přeloží ji do vlastní volání REST – pole, která přejděte kde? Po naučit, jak nastavit volání REST, můžete využít tyto znalosti k používání některé z dalších rozhraní API REST služby úložiště.

## <a name="prerequisites"></a>Požadavky 

Aplikace zobrazí seznam kontejnerů v úložišti objektů blob pro účet úložiště. Můžete vyzkoušet na kód v tomto článku, potřebujete následující položky: 

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) s následující úlohy:
    - Vývoj pro Azure

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Účet úložiště pro obecné účely. Pokud ještě nemáte účet úložiště, najdete v části [vytvořit účet úložiště](storage-quickstart-create-account.md).

* V příkladu v tomto článku ukazuje, jak seznam kontejnery v účtu úložiště. Pokud chcete zobrazit výstup, přidejte některé kontejnery úložiště objektů blob v účtu úložiště před zahájením.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Vzorová aplikace je konzolové aplikace napsané v jazyce C#.

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Otevřete řešení sady Visual Studio, vyhledejte složku storage-dotnet-rest-api-with-auth, otevřete ho a dvakrát klikněte na StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Co je REST?

REST znamená *přenosu representational stavu*. Pro konkrétní definici, podívejte se na [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

V podstatě, REST je architekturu můžete použít při volání rozhraní API nebo vytváření pro volání rozhraní API. Je nezávislý na co se děje na obou stranách a jaké další software se používá při odesílání nebo přijímání ZBÝVAJÍCÍ volání. Můžete napsat aplikaci, která běží na Macu, Windows, Linux, telefon se systémem Android nebo tabletu, iPhone, iPod nebo webový server a použít stejné rozhraní REST API pro všechny tyto platformy. Data mohou být předána nebo se při volání rozhraní REST API. Rozhraní REST API nepodporuje stará z jaké platformy, se označuje jako – co je důležité, je předaná v žádosti o informace a data poskytnutá v odpovědi.

Zároveň budete vědět, jak používat REST je užitečné znalostí. Azure produktový tým často uvolní nové funkce. Kolikrát, nové funkce jsou dostupné přes rozhraní REST, ale ještě nebyly byla prezentované prostřednictvím **všechny** knihovny klienta úložiště nebo uživatelského rozhraní (například portál Azure). Pokud chcete vždy použít nejnovější a největší, učení REST je požadavek. Navíc pokud chcete napsat vlastní knihovny pro interakci s Azure Storage, nebo chcete pro přístup k úložišti Azure pomocí programovacího jazyka, který nemá klientské knihovny SDK nebo úložiště, můžete použít rozhraní REST API.

## <a name="about-the-sample-application"></a>O ukázkové aplikace

Ukázková aplikace zobrazí seznam kontejnery v účtu úložiště. Jakmile budete rozumět tomu, jak informace v dokumentaci rozhraní REST API koreluje s skutečné kódu, se snadněji a pokuste se zjistit jiná volání REST. 

Pokud si prohlédnete [rozhraní API REST služby objektů Blob](/rest/api/storageservices/fileservices/Blob-Service-REST-API), uvidíte všechny operace můžete provádět na úložiště objektů blob. Knihovny klienta úložiště jsou obálky kolem rozhraní REST API – se snadno můžete získávat přístup k úložišti bez použití rozhraní REST API přímo. Ale uvedených výše, někdy budete chtít použít rozhraní API REST místo Klientská knihovna pro úložiště.

## <a name="rest-api-reference-list-containers-api"></a>Referenční dokumentace rozhraní API REST: Seznam kontejnery rozhraní API

Podívejme se na stránce v referenční dokumentace rozhraní API REST pro [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) operace, takže víte, že pokud některá z těchto polí pocházejí z v požadavku a odpovědi v další části kódem.

**Metoda požadavku**: získat. Tento příkaz je metoda HTTP, který zadáte jako vlastnost objektu požadavku. Ostatní hodnoty pro tento příkaz zahrnují HEAD, PUT a DELETE, v závislosti na rozhraní API jsou volání.

**Identifikátor URI požadavku je**: https://myaccount.blob.core.windows.net/?comp=list je vytvořena z koncového bodu účtu úložiště objektů blob `http://myaccount.blob.core.windows.net` a řetězec prostředku `/?comp=list`.

[Parametry identifikátoru URI](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): existují další parametry dotazu můžete při volání metody ListContainers. Několik z těchto parametrů jsou *časový limit* volání (v sekundách) a *předponu*, který se používá k filtrování.

Další užitečné parametr *shluku:* Pokud než tato hodnota jsou k dispozici více kontejnerů, bude obsahovat text odpovědi *NextMarker* element, který označuje další kontejner určený k vrácení při dalším požadavek. Chcete-li tuto funkci používat, je zadat *NextMarker* hodnoty jako *značky* parametr v identifikátoru URI, když provedete další požadavek. Při použití této funkce je obdobou stránkování přes výsledky. 

Chcete-li používat další parametry, připojte je na řetězec prostředku s hodnotou, jako tento ukázkový:

```
/?comp=list&timeout=60&maxresults=100
```

[Hlavičky požadavku](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** této části jsou uvedené hlavičky žádosti požadované a volitelné. Tři hlavičky jsou požadovány: *autorizace* záhlaví, *x-ms datum* (obsahuje čas UTC požadavku), a *x-ms-version* (určuje verzi REST Rozhraní API používat). Včetně *x-ms-client-request-id* v hlavičkách je nepovinný – můžete nastavit hodnotu pro toto pole k ničemu; je zapsán do úložiště analýzy protokolů, pokud je povoleno protokolování.

[Text žádosti](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** neexistuje žádný text žádosti pro ListContainers. Textu požadavku se používá na všechny operace PUT při nahrávání objekty BLOB, jakož i SetContainerAccessPolicy, který umožňuje odeslat v seznamu XML uložené přístup zásad k použití. Zásady přístupu uložené, jsou popsané v článku [pomocí sdíleného přístupového podpisy (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Stavový kód odpovědi](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** Tells žádné stavové kódy, je nutné znát. V tomto příkladu je kód stavu HTTP 200 ok. Úplný seznam stavové kódy HTTP, podívejte se na [definice stavových kódů](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Kódy chyb, které jsou specifické pro rozhraní API REST úložiště najdete v sekci [kódy chyb společné rozhraní REST API](/rest/api/storageservices/common-rest-api-error-codes)

[Hlavičky odpovědi](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** patří mezi ně *typ obsahu*; *x-ms-request-id* (id požadavku je předán, pokud je k dispozici); *x-ms-version* (označuje verzi služby objektů Blob používat) a *datum* (UTC, řekne, co čas byl požadavek).

[Text odpovědi](/rest/api/storageservices/fileservices/List-Containers2#response-body): Toto pole je struktury XML poskytuje požadovaná data. V tomto příkladu je odpověď seznam kontejnery a jejich vlastnosti.

## <a name="creating-the-rest-request"></a>Vytváření žádosti o REST

Několik poznámek před zahájením – zabezpečení při spuštění v produkčním prostředí, vždycky používat protokol HTTPS, nikoli protokol HTTP. Pro účely tohoto cvičení byste měli používat protokol HTTP, můžete zobrazit data požadavku a odpovědi. Chcete-li zobrazit informace o požadavku a odpovědi v skutečné volání REST, si můžete stáhnout [Fiddler](http://www.telerik.com/fiddler) nebo jiné aplikace. V řešení sady Visual Studio název účtu úložiště a klíč jsou pevně zakódované ve třídě, a metodu ListContainersAsyncREST předá název účtu úložiště a klíč účtu úložiště metody, které se používají k vytvoření různých součástí požadavku REST . V reálné aplikaci název účtu úložiště a klíč by se nacházejí v konfiguračním souboru, proměnné prostředí, nebo načíst z Azure Key Vault.

V našem ukázkový projekt je kód pro vytvoření autorizační hlavičky v samostatné třídy, za účelem, že může trvat celou třídu a přidat do vlastní řešení a použít ji "tak, jak je. Autorizační kód záhlaví funguje pro většinu volání rozhraní REST API pro Azure Storage.

Chcete-li vytvořit požadavek, který je objekt HttpRequestMessage, přejděte na ListContainersAsyncREST v souboru Program.cs. Kroky pro vytváření žádosti jsou: 

* Vytvořte identifikátor URI, který se má použít pro volání služby. 
* Vytvoření objektu HttpRequestMessage a nastavení datové části. Vzhledem k tomu, že jsme v nic nepředáváme má hodnotu null pro ListContainersAsyncREST datové části.
* Přidání hlavičky požadavku pro x-ms-date a x-ms-version.
* Získat hlavičku autorizace a přidejte ji.

Některé základní informace, které potřebujete: 

*  Pro ListContainers **metoda** je `GET`. Tato hodnota nastavena při vytváření instance žádosti. 
*  **Prostředků** je část dotazu identifikátoru URI, která určuje, který volá rozhraní API, tak, aby hodnota `/?comp=list`. Jak již bylo uvedeno dříve, prostředek je na stránce dokumentace odkaz s informacemi o [ListContainers API](/rest/api/storageservices/fileservices/List-Containers2).
*  Identifikátor URI je vytvořený pomocí vytvoření koncový bod služby objektů Blob pro daný účet úložiště a zřetězení prostředku. Hodnota **identifikátor URI požadavku je** se bude nakonec `http://contosorest.blob.core.windows.net/?comp=list`.
*  Pro ListContainers **requestBody** má hodnotu null a neexistují žádné další **hlavičky**.

Různé rozhraní API může mít jiné parametry k předání v například *ifMatch*. Příkladem, kde může používat ifMatch není při volání metody PutBlob. V takovém případě nastavíte ifMatch na značku eTag a zároveň pouze aktualizuje objekt blob, pokud značka eTag, které poskytnete odpovídá aktuální eTag u objektu blob. Pokud někdo od načítání značku eTag aktualizoval objektu blob, nebude možné přepsat jejich změnu. 

Nastavte nejprve, `uri` a `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Dále vytvořte instanci žádosti, nastavení metodu `GET` a poskytnutí identifikátoru URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Přidání hlavičky požadavku pro x-ms-date a x-ms-version. Toto místo v kódu je také kde přidat žádné další žádosti hlavičky požadované pro volání. V tomto příkladu nejsou žádná další záhlaví. Příkladem rozhraní API, který předává v další hlavičky je SetContainerACL. Pro úložiště objektů Blob přidá hlavičku s názvem "x-ms-blob veřejný přístup" a hodnota pro úroveň přístupu.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Volání metody, která vytvoří hlavičku autorizace a přidat jej do hlavičky žádosti. Zobrazí se postup vytvoření autorizační hlavičky později v článku. Název metody je GetAuthorizationHeader, který můžete vidět v tento fragment kódu:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

V tomto okamžiku `httpRequestMessage` obsahuje kompletní s hlavičky ověření požadavku REST. 

## <a name="call-the-rest-api-with-the-request"></a>Volání rozhraní REST API s požadavkem

Teď, když máte žádost, můžete volat SendAsync k odeslání požadavku REST. SendAsync zavolá rozhraní API a získá odpověď zpět. Zkontrolujte odpověď StatusCode (200 je OK), pak analyzovat odpověď. V takovém případě můžete získat seznam XML kontejnerů. Podívejme se na kód pro volání metody GetRESTRequest vytvořit požadavek, provedení požadavku a potom si prohlédněte odpovědi na seznamu kontejnerů.

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

Pokud spustíte jiného programu pro sítě, jako [Fiddler](https://www.telerik.com/fiddler) při provádění volání SendAsync, zobrazí se informace o požadavku a odpovědi. Podívejme se. Název účtu úložiště je *contosorest*.

**Žádost:**

```
GET /?comp=list HTTP/1.1
```

**Hlavičky požadavku:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Stavový kód a odpovědi hlavičky se vrátí po spuštění:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Text odpovědi (XML):** pro ListContainers zobrazí seznam kontejnery a jejich vlastnosti.

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

Teď, když chápete, jak vytvořit požadavek, zavolá službu a analyzovat výsledky, podíváme se, jak vytvořit autorizační hlavičky. Vytvoření této hlavičky je složité, ale Dobrá zpráva je, že jakmile máte kód práce, funguje pro všechny rozhraní API REST služby úložiště.

## <a name="creating-the-authorization-header"></a>Vytváření autorizační hlavičky

> [!TIP]
> Úložiště Azure nyní podporuje integraci služby Azure Active Directory (Azure AD) pro služby objektů Blob a fronty (Preview). Azure AD nabízí mnohem jednodušší prostředí pro ověřování požadavek do služby Azure Storage. Další informace o autorizaci operace REST pomocí Azure AD najdete v tématu [ověřit s Azure Active Directory (Preview)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Přehled integrace Azure AD s Azure Storage najdete v tématu [ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview)](storage-auth-aad.md).

Je článek, který vysvětluje koncepčně (žádný kód) jak provádět [ověřování pro služby Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Umožňuje generovat tohoto článku dolů na přesně je nezbytné a kód.

První používejte ověření sdíleným klíčem. Formát hlavičky ověřování vypadá takto:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole podpisu je Hash-based ověřování kódu metoda HMAC (Message) vytvořit z požadavku a vypočítány používá algoritmus SHA256 pak kódované pomocí kódování Base64. Tu, která? (Přečnívat zde, nebyly i slyšeli slovo *kanonizovaného* ještě.)

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

Většina těchto polí se zřídka používají. Pro úložiště objektů Blob zadejte příkaz, md5, délka obsahu, kanonizovaného hlavičky a kanonizovaného prostředků. Ostatní můžete nechat prázdné (ale put v `\n` tak bude vědět, že jsou prázdné).

Co jsou CanonicalizedHeaders a CanonicalizedResource? Dobrý otázku. Ve skutečnosti jaké jsou kanonizovaného střední? Aplikace Microsoft Word i nerozpoznal ho jako slovo. Tady je co [Wikipedia uvádí o kanonizace](http://en.wikipedia.org/wiki/Canonicalization): *v oblasti vědy, kanonizace (někdy standardizace nebo normalizaci) je proces pro převod data, která má více než jeden možný reprezentace do "standard", "normální" nebo kanonickém tvaru.* V normální řeči, to znamená využít seznam položek (například hlavičky v případě kanonizovaného hlavičky) a standardizovat je do požadovaný formát. V podstatě Microsoft se rozhodli formátu a je potřeba ho.

Začněme s tyto dvě kanonizovaného pole, protože jsou vyžadovány k vytvoření autorizační hlavičky.

**Kanonizovaného hlavičky**

Chcete-li vytvořit tuto hodnotu, načíst seznam hlaviček, které začínat "x - ms-" a seřadit je a pak je do řetězce formátu `[key:value\n]` instance, zřetězen do jednoho řetězce. V tomto příkladu kanonizovaného hlavičky vypadat například takto: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Tady je kód používaný k vytvoření tento výstup:

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

Tato součást řetězce identifikátoru podpis představuje účet úložiště, který je cílem žádosti. Mějte na paměti, že je identifikátor URI požadavku `<http://contosorest.blob.core.windows.net/?comp=list>`, s názvem skutečné účtu (`contosorest` v tomto případě). V tomto příkladu se vrátí:

```
/contosorest/\ncomp:list
```

Pokud máte parametry dotazu, jedná se o těch, které také. Zde je kód, který zpracovává také další parametry dotazu a parametrů dotazu s více hodnotami. Mějte na paměti, kterou sestavujete tento kód fungovat pro všechny rozhraní REST API, které chcete zahrnout všechny možnosti, i v případě, že metoda ListContainers nepotřebuje všechny z nich.

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

Teď, když jsou nastavené kanonizovaného řetězce, podíváme, jak vytvořit autorizační hlavičky sám sebe. Začněte vytvořením řetězec podpis zprávy ve formátu StringToSign dříve zobrazí v tomto článku. Tento koncept je snazší vysvětlit, pomocí komentáře v kódu, proto zde je, poslední metodu, která vrátí hlavičku autorizace:

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

Když spustíte tento kód, výsledná MessageSignature vypadá takto:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Tady je konečná hodnota pro AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader je poslední záhlaví umístěny v hlavičkách žádostí před publikováním odpovědi.

Která obsahuje všechno, co potřebujete vědět, spolu s kódem, dávat dohromady třídu, kterou můžete použít k vytvoření žádosti o který se má použít k volání rozhraní API REST služby úložiště.

## <a name="how-about-another-example"></a>Jak o další příklad? 

Podívejme se na tom, jak změnit kód volání ListBlobs pro kontejner *kontejneru-1*. Toto je téměř stejný jako kód pro výpis kontejnery, pouze rozdíly se identifikátor URI a jak analyzovat odpověď. 

Pokud se podíváte na referenční dokumentaci k nástroji pro [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), zjistíte, že je metoda *získat* a je RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

V ListContainersAsyncREST změňte kód, který nastaví identifikátor URI k rozhraní API pro ListBlobs. Název kontejneru je **kontejneru-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Kde zpracovat odpověď, a potom změňte kód a Hledat objekty BLOB místo kontejnery.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Když tuto ukázku spustit, zobrazí výsledky takto:

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

**Hlavičky požadavku:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Stavový kód a odpovědi hlavičky se vrátí po spuštění:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Text odpovědi (XML):** tento XML odpovědi obsahuje seznam objektů BLOB a jejich vlastnosti. 

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

V tomto článku jste zjistili, jak vytvořit požadavek do úložiště objektů blob REST API pro načtení seznamu kontejnery nebo seznam objektů BLOB v kontejneru. Také jste zjistili, jak vytvořit podpis autorizace pro volání rozhraní REST API, způsobu jeho použití v požadavku REST a jak prozkoumat odpovědi.

## <a name="next-steps"></a>Další postup

* [Rozhraní API REST služby objektů BLOB](/rest/api/storageservices/blob-service-rest-api)
* [Rozhraní API REST služby souboru](/rest/api/storageservices/file-service-rest-api)
* [Rozhraní API REST služby fronty](/rest/api/storageservices/queue-service-rest-api)