---
title: Používejte dynamické šifrování AES-128 a službu doručování klíčů | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak dynamicky šifrovat pomocí AES-128 a používat službu doručování klíčů.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269728"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Použití dynamického šifrování AES-128 a služby doručování klíčů
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Pomocí služby Media Services můžete pomocí 128bitových šifrovacích klíčů doručovat služby HTTP Live Streaming (HLS) a Plynulé streamování šifrované pomocí aes. Služba Media Services také poskytuje službu doručování klíčů, která poskytuje šifrovací klíče oprávněným uživatelům. Pokud chcete, aby media services šifrovat prostředek, přidružit šifrovací klíč s datovým zdrojem a také nakonfigurovat zásady autorizace pro klíč. Pokud je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu pomocí šifrování AES. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. Chcete-li zjistit, zda je uživatel oprávněn získat klíč, služba vyhodnotí zásady autorizace, které jste zadali pro klíč.

Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu [jednoduchých webových tokenů](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) a [webových tokenů JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Další informace najdete v tématu [Konfigurace zásad autorizace klíče k obsahu](media-services-protect-with-aes128.md#configure_key_auth_policy).

Abyste mohli využívat dynamické šifrování, je třeba mít asset, který obsahuje sadu souborů MP4 s více přenosovými rychlostmi nebo zdrojové soubory technologie Smooth Streaming s více přenosovými rychlostmi. Musíte také nakonfigurovat zásady doručování pro prostředek (popsané dále v tomto článku). Potom, na základě formátu určeného v adrese URL streamování, server streamování na vyžádání zajistí, aby byl datový proud doručen v protokolu podle vašeho výběru. V důsledku toho je třeba ukládat a platit pouze za soubory v jednom formátu úložiště. Služba Media Services sestaví a odešle odpovídající odpověď na základě žádosti od klientů.

Tento článek je užitečný pro vývojáře, kteří pracují na aplikacích, které poskytují chráněná média. V článku se zobrazí postup konfigurace služby doručování klíčů se zásadami autorizace tak, aby šifrovací klíče mohli přijímat pouze autorizovaní klienti. Ukazuje také, jak používat dynamické šifrování.

Informace o šifrování obsahu pomocí standardu Advanced Encryption Standard (AES) pro doručení do Safari v systému macOS naleznete v [tomto příspěvku blogu](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Přehled ochrany mediálního obsahu pomocí šifrování AES naleznete v [tomto videu](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamické šifrování a pracovní postup služby doručování klíčů

Při šifrování datových zdrojů pomocí služby AES pomocí služby Media Services a také pomocí dynamického šifrování proveďte následující obecné kroky:

1. [Vytvořte datový zdroj a nahrajte do něj soubory](media-services-protect-with-aes128.md#create_asset).

2. [Zakódujte datový zdroj, který obsahuje soubor, do sady MP4 s adaptivní přenosovou rychlostí](media-services-protect-with-aes128.md#encode_asset).

3. [Vytvořte klíč obsahu a přidružte ho k kódovanému datovému zdroji](media-services-protect-with-aes128.md#create_contentkey). Ve službě Media Services obsahuje klíč obsahu šifrovací klíč prostředku.

4. [Nakonfigurujte zásady autorizace klíče obsahu](media-services-protect-with-aes128.md#configure_key_auth_policy). Je třeba nakonfigurovat zásady autorizace klíče obsahu. Klient musí zásady splňovat, jinak mu nebude klíč obsahu poskytnut.

5. [Nakonfigurujte zásady doručování pro datový zdroj](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurace zásad doručení zahrnuje adresu URL pořízení klíče a inicializační vektor (IV). (AES-128 vyžaduje stejné IV pro šifrování a dešifrování.) Konfigurace také zahrnuje doručovací protokol (například MPEG-DASH, HLS, Smooth Streaming nebo všechny) a typ dynamického šifrování (například obálka nebo žádné dynamické šifrování).

    Na každý protokol stejného prostředku můžete použít jiné zásady. Můžete například použít šifrování PlayReady na protokol Smooth/DASH a šifrování pomocí standardu AES Envelope na protokol HLS. Všechny protokoly, které nejsou definovány v zásadách doručování, jsou blokovány streamování. (Příkladem je, pokud přidáte jednu zásadu, která určuje pouze HLS jako protokol.) Výjimkou je, pokud nemáte žádné zásady doručování majetku definovány vůbec. Pak budou všechny protokoly povolené v nešifrované podobě.

6. [Vytvořte lokátor OnDemand](media-services-protect-with-aes128.md#create_locator) a získejte adresu URL streamování.

Článek také [ukazuje, jak může klientská aplikace požadovat klíč ze služby doručování klíčů](media-services-protect-with-aes128.md#client_request).

Úplný [příklad .NET](media-services-protect-with-aes128.md#example) najdete na konci článku.

Následující obrázek znázorňuje výše popsaný pracovní postup. Zde se k ověření používá token.

![Chraňte pomocí AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Zbývající část tohoto článku obsahuje vysvětlení, příklady kódu a odkazy na témata, která ukazují, jak dosáhnout dříve popsaných úkolů.

## <a name="current-limitations"></a>Aktuální omezení
Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Vytvoření datového zdroje a nahrání souborů do datového zdroje
Aby bylo možné spravovat, kódovat a streamovat videa, musíte nejprve nahrát obsah do služby Media Services. Po nahrání bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování. 

Podrobné informace najdete v článku o [nahrání souborů do účtu služby Media Services](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Kódování prostředku obsahujícího soubor do sady souborů MP4 s adaptivní přenosovou rychlostí
V případě dynamického šifrování je třeba pouze vytvořit prostředek, který obsahuje sadu souborů MP4 s více přenosovými rychlostmi nebo zdrojové soubory technologie Smooth Streaming s více přenosovými rychlostmi. Potom na základě zadaného formátu v požadavku manifestu nebo fragmentu server streamování na vyžádání zajistí, že obdržíte datový proud ve vybraném protokolu. Potom stačí pouze ukládat a platit za soubory v jednom formátu úložiště. Služba Media Services sestaví a odešle odpovídající odpověď na základě žádosti od klientů. Další informace najdete v tématu [Přehled dynamického balení](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu Spuštěno. 
>
>Chcete-li také používat dynamické balení a dynamické šifrování, musí váš datový zdroj obsahovat sadu souborů MP4 s adaptivním datovým tokem nebo adaptivní datové toky.

Pokyny ke kódování najdete v článku o [kódování prostředku pomocí kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Vytvoření klíče k obsahu a jeho přiřazení k zakódovanému assetu.
Klíč k obsahu ve službě Media Services obsahuje klíč, kterým chcete asset šifrovat.

Podrobné informace najdete v tématu [Vytvoření klíče k obsahu](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurace zásad autorizace klíče obsahu
Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Klient (hráč) musí splnit zásady před klíč může být dodáno klientovi. Zásady autorizace klíče obsahu mohou mít jedno nebo více omezení autorizace, buď otevřené, omezení tokenu nebo omezení IP adresy.

Další informace najdete v tématu [Konfigurace zásad autorizace klíče k obsahu](media-services-dotnet-configure-content-key-auth-policy.md).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurace zásad doručení prostředku
Nakonfigurujte zásady doručení pro asset. Konfigurace zásad doručení prostředku zahrnuje následující položky:

* Adresa URL pořízení klíče. 
* Inicializační vektor (IV), který se má použít pro šifrování obálky. AES-128 vyžaduje stejné IV pro šifrování a dešifrování. 
* Doručovací protokol prostředku (například MPEG DASH, HLS, technologie Smooth Streaming nebo všechny).
* Typ dynamického šifrování (například obálka AES) nebo žádné dynamické šifrování. 

Podrobné informace najdete v tématu [Konfigurace zásad doručení prostředku](media-services-dotnet-configure-asset-delivery-policy.md).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Vytvoření lokátoru streamování OnDemand pro získání adresy URL streamování
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

K testování datového proudu můžete použít [Azure Media Services Player](https://aka.ms/azuremediaplayer).

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Jak může váš klient požádat o klíč ze služby doručování klíčů?
V předchozím kroku jste vytvořili adresu URL, která odkazuje na soubor manifestu. Váš klient potřebuje extrahovat potřebné informace ze souborů manifestu streamování, aby mohl požádat o službu doručování klíčů.

### <a name="manifest-files"></a>Soubory manifestu
Klient potřebuje extrahovat hodnotu URL (která také obsahuje ID klíče obsahu [kid]) ze souboru manifestu. Klient se pak pokusí získat šifrovací klíč ze služby doručování klíčů. Klient také potřebuje extrahovat hodnotu IV a použít ji k dešifrování datového proudu. Následující úryvek zobrazuje `<Protection>` prvek manifestu Plynulé streamování:

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

V případě HLS je kořenový manifest rozdělen do segmentových souborů. 

Kořenový manifest je například:\/http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Obsahuje seznam názvů segmentových souborů.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Pokud otevřete jeden ze segmentových souborů v textovém\/editoru (například http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), obsahuje #EXT-X-KEY, což znamená, že soubor je zašifrován.

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
>Pokud máte v plánu hrát AES-šifrované HLS v Safari, viz [tento blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Vyžádat si klíč od služby doručování klíčů

Následující kód ukazuje, jak odeslat požadavek na službu doručování klíčů mediálníslužby pomocí doručení klíče Uri (který byl extrahován z manifestu) a token. (Tento článek nevysvětluje, jak získat SWTs z STS.)

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Chraňte svůj obsah pomocí AES-128 pomocí rozhraní .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte vývojové prostředí a naplňte soubor app.config informacemi o připojení, jak je popsáno ve [vývoji mediálních služeb pomocí rozhraní .NET](media-services-dotnet-how-to-use.md).

2. Přidejte do aplikace Následující prvky, jak je definováno v souboru app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Příklad

Přepište kód v souboru Program.cs kódem zobrazeným v této části.
 
>[!NOTE]
>Existuje limit 1 000 000 zásad pro různé zásady mediálních služeb (například pro zásady locator nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny/přístupová oprávnění, použijte stejné ID zásad. Příkladem je zásada pro lokátory, které mají zůstat v platnosti delší dobu (zásady bez odesílání). Další informace naleznete v části Omezit zásady přístupu v části [Správa datových zdrojů a souvisejících entit pomocí sady Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Nezapomeňte aktualizovat proměnné tak, aby odkazovaly do složek, ve kterých jsou umístěné vaše vstupní soubory.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
