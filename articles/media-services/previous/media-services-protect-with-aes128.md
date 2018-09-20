---
title: Použití dynamického šifrování AES-128 a doručení klíče služby | Dokumentace Microsoftu
description: Doručování obsahu pomocí klíčů AES 128-bit šifrování, šifrován pomocí Microsoft Azure Media Services. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. Toto téma ukazuje, jak dynamicky šifrovat pomocí standardu AES-128 a používat službu doručování klíčů.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: juliako
ms.openlocfilehash: 9f3fe36eab7dc7fd1921c4e225b5a173fe2e9243
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364869"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Použití dynamického šifrování AES-128 a doručení klíče služby
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Pokud chcete získat nejnovější verzi sady Java SDK a začít s vývojem v jazyce Java, přečtěte si článek [Začínáme s klientskou sadou Java SDK pro Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Pokud si chcete stáhnout nejnovější sadu PHP SDK pro službu Media Services, najděte si v [úložišti Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) balíček Microsoft/WindowsAzure verze 0.5.7.  

## <a name="overview"></a>Přehled
> [!NOTE]
> Informace o tom, jak šifrování obsahu pomocí na Standard AES (Advanced Encryption) k doručení do prohlížeče Safari v systému macOS najdete v tématu [tento příspěvek na blogu](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
> Přehled o tom, jak Ochrana mediálního obsahu šifrováním AES, naleznete v tématu [toto video](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).
> 
> 

 Služba Media Services můžete použít k doručování HTTP Live Streaming (HLS) a technologie Smooth Streaming, šifrují pomocí AES pomocí 128bitového šifrování klíčů. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. Pokud chcete Media Services k šifrování assetu, přidružte šifrovací klíč assetu a také nakonfigurovat zásady autorizace pro klíč. Datový proud je žádost přehrávač, Media Services používá k dynamické šifrování obsahu pomocí šifrování AES se zadaným klíčem. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Pokud chcete zjistit, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu [jednoduchých webových tokenů](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) a [webových tokenů JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Další informace najdete v tématu [Konfigurace zásad autorizace klíče k obsahu](media-services-protect-with-aes128.md#configure_key_auth_policy).

Abyste mohli využívat dynamické šifrování, je třeba mít asset, který obsahuje sadu souborů MP4 s více přenosovými rychlostmi nebo zdrojové soubory technologie Smooth Streaming s více přenosovými rychlostmi. Také musíte nakonfigurovat zásady doručení pro asset (popsáno dále v tomto článku). Potom, na základě formátu určeného v adrese URL streamování, server streamování na vyžádání zajistí, aby byl datový proud doručen v protokolu podle vašeho výběru. V důsledku toho musíte uložit a platit jenom pro soubory v jednom úložném formátu. Služba Media Services sestaví a odešle odpovídající odpověď na základě žádosti od klientů.

Tento článek je užitečný pro vývojáře pracující na aplikacích, které doručují média chráněná. Tento článek ukazuje, jak nakonfigurovat službu doručování klíčů pomocí zásad autorizace tak, aby šifrovací klíče můžete dostávat pouze autorizovaní klienti. Také ukazuje, jak používat dynamické šifrování.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Dynamické šifrování AES-128 a doručení klíče služby pracovního postupu

Při šifrování vašich prostředků pomocí standardu AES pomocí doručení klíče služby Media Services a také použitím dynamického šifrování, proveďte následující postup:

1. [Vytvoření assetu a nahrání souborů do assetu](media-services-protect-with-aes128.md#create_asset).

2. [Kódování prostředku obsahujícího soubor, který má adaptivní přenosové rychlosti MP4 sady](media-services-protect-with-aes128.md#encode_asset).

3. [Vytvoření klíče k obsahu a přidružte jej k zakódovanému assetu](media-services-protect-with-aes128.md#create_contentkey). Ve službě Media Services obsahuje klíč obsahu šifrovací klíč prostředku.

4. [Konfigurace zásad autorizace klíče obsahu](media-services-protect-with-aes128.md#configure_key_auth_policy). Je třeba nakonfigurovat zásady autorizace klíče obsahu. Klient musí zásady splňovat, jinak mu nebude klíč obsahu poskytnut.

5. [Konfigurace zásad doručení pro asset](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurace zásad doručení zahrnuje adresu URL pro získání klíče a inicializačního vektoru (IV). (AES-128 vyžaduje stejné IV pro šifrování a dešifrování.) Konfigurace také zahrnuje doručovací protokol (například MPEG-DASH, HLS, technologie Smooth Streaming nebo všechny) a typ dynamického šifrování (například obálku nebo žádné dynamické šifrování).

    Na každý protokol stejného prostředku můžete použít jiné zásady. Můžete například použít šifrování PlayReady na protokol Smooth/DASH a šifrování pomocí standardu AES Envelope na protokol HLS. Všechny protokoly, které nejsou v zásadách doručení definovány jsou při streamování blokovány. (Příkladem je, když přidáte jedinou zásadu, která jako protokol určuje pouze HLS.) Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení prostředku. Pak budou všechny protokoly povolené v nešifrované podobě.

6. [Vytvořit lokátor OnDemand](media-services-protect-with-aes128.md#create_locator) získat adresu URL pro streamování.

Tento článek také ukazuje [jak klientská aplikace může požádat o klíč ze služby doručení klíče](media-services-protect-with-aes128.md#client_request).

Najdete kompletní [příklad .NET](media-services-protect-with-aes128.md#example) na konci tohoto článku.

Následující obrázek znázorňuje výše popsaný pracovní postup. Zde se k ověření používá token.

![Ochrana pomocí AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Zbývající část tohoto článku poskytuje vysvětlení, příklady kódu a odkazy na témata, která ukazují, jak dokončit výše popsané úlohy.

## <a name="current-limitations"></a>Aktuální omezení
Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.

## <a id="create_asset"></a>Vytvoření assetu a nahrání souborů do assetu
Aby bylo možné spravovat, kódovat a streamovat videa, musíte nejprve nahrát obsah do služby Media Services. Po nahrání bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování. 

Podrobné informace najdete v článku o [nahrání souborů do účtu služby Media Services](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Kódování prostředku obsahujícího soubor do sady souborů MP4 adaptivní přenosovou
V případě dynamického šifrování je třeba pouze vytvořit prostředek, který obsahuje sadu souborů MP4 s více přenosovými rychlostmi nebo zdrojové soubory technologie Smooth Streaming s více přenosovými rychlostmi. Potom podle formátu určeného v manifestu nebo fragment požadavek, server streamování na vyžádání zajistí datový proud obdrželi v protokolu, který jste vybrali. Pak jenom musíte ukládáte a platíte za soubory v jednom úložném formátu. Služba Media Services sestaví a odešle odpovídající odpověď na základě žádosti od klientů. Další informace najdete v tématu [Přehled dynamického balení](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu Spuštěno. 
>
>Navíc pokud chcete použít dynamické balení a dynamického šifrování, váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

Pokyny ke kódování najdete v článku o [kódování prostředku pomocí kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Vytvoření klíče k obsahu a jeho přiřazení k zakódovanému assetu
Klíč k obsahu ve službě Media Services obsahuje klíč, kterým chcete asset šifrovat.

Podrobné informace najdete v tématu [Vytvoření klíče k obsahu](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurace zásad autorizace klíče obsahu
Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Klient (přehrávač) musí zásady splňovat, než tento klíč se dá doručit do klienta. Zásady autorizace klíče obsahu může mít jeden nebo více omezení autorizace, otevřít, token omezení nebo omezení IP adres.

Další informace najdete v tématu [Konfigurace zásad autorizace klíče k obsahu](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Konfigurace zásad doručení prostředku
Nakonfigurujte zásady doručení pro asset. Konfigurace zásad doručení prostředku zahrnuje následující položky:

* Adresu URL pro získání klíče. 
* Inicializační vektor (IV) má použít pro šifrování obálky. AES-128 vyžaduje stejné IV pro šifrování a dešifrování. 
* Doručovací protokol prostředku (například MPEG DASH, HLS, technologie Smooth Streaming nebo všechny).
* Typ dynamického šifrování (například standardu AES envelope) nebo žádné dynamické šifrování. 

Podrobné informace najdete v tématu [Konfigurace zásad doručení prostředku](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Vytvoření lokátoru streamování OnDemand pro získání adresy URL streamování
Uživateli je třeba poskytnout adresu URL streamování pro protokol Smooth Streaming, DASH nebo HLS.

> [!NOTE]
> Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.
> 
> 

Pokyny k publikování assetu a vytvoření adresy URL streamování najdete v článku o [vytvoření adresy URL streamování](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Získání testovacího tokenu
Získejte testovací token na základě omezení s tokenem, které se používá v zásadách autorizace klíče.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

K testování datového proudu můžete použít [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a id="client_request"></a>Jak může váš klient požádat o klíč ze služby doručení klíče?
V předchozím kroku sestavit adresu URL, která odkazuje na soubor manifestu. Váš klient je potřeba extrahovat nezbytné informace z datových proudů soubory manifestu tak, aby žádost o službu doručování klíčů.

### <a name="manifest-files"></a>Soubory manifestu
Klient musí získat adresu URL (obsah, který také obsahuje klíč ID [dětský]) hodnotu ze souboru manifestu. Klient se pak pokusí získat šifrovací klíč ze služby doručení klíče. Klienta je také potřeba extrahovat hodnotu IV a použít ho k dešifrování datového proudu. Následující fragment kódu ukazuje <Protection> elementu v manifestu technologie Smooth Streaming:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

V případě HLS manifest kořenové rozdělené soubory segmentu. 

Je třeba manifest kořenové: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Obsahuje seznam názvů souborů segmentu.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Pokud je jeden segment soubor otevřete v textovém editoru (například http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video, format = m3u8-aapl), obsahuje #EXT-X-KEY, což znamená, že je soubor zašifrován.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Pokud máte v plánu přehrávání HLS šifrováním AES v prohlížeči Safari, přečtěte si téma [tento blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Požádat o klíč ze služby doručení klíče

Následující kód ukazuje, jak odeslat žádost o doručení klíče služby Media Services pomocí doručení klíče identifikátoru Uri (extrahovaný z manifestu) a tokenu. (Tento článek není vysvětlují, jak získat od služby STS SWTs.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Chránit obsah pomocí AES-128 s použitím rozhraní .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

2. Přidejte následující prvky appSettings, jak jsou definovány v souboru app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Příklad

Přepište kód v souboru Program.cs kódem zobrazeným v této části.
 
>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad Media Services (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Stejné ID zásad použijte, pokud vždy používáte stejné dny/přístupová oprávnění. Příkladem je zásada pro lokátory, které mají zůstat v platnosti delší dobu (zásady bez odesílání). Další informace najdete v části "Zásady omezení přístupu" v [spravovat prostředky a související entity pomocí sady Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Nezapomeňte aktualizovat proměnné tak, aby odkazovaly do složek, ve kterých jsou umístěné vaše vstupní soubory.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
