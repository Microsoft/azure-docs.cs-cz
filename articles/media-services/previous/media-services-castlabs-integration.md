---
title: Použití castLabs k doručování licencí Widevine Azure Media Services | Microsoft Docs
description: Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) doručovat datový proud, který je dynamicky zašifrovaný pomocí AMS, pomocí PlayReady i Widevine několikanásobnou.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 17b54b2adb21419de61d2309752987f6e4a48e41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264483"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí castLabs

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Přehled

Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) doručovat datový proud, který je dynamicky zašifrovaný pomocí AMS, pomocí PlayReady i Widevine několikanásobnou. Licence PlayReady pochází z Media Services licenční server PlayReady a licence Widevine se doručují prostřednictvím licenčního serveru **castLabs** .

Pokud chcete přehrávat obsah streamování chráněný pomocí CENC (PlayReady nebo Widevine), můžete použít  [Azure Media Player](https://aka.ms/azuremediaplayer). Podrobnosti najdete v [dokumentu amp](https://amp.azure.net/libs/amp/latest/docs/) .

Následující diagram znázorňuje architekturu integrace na nejvyšší úrovni Azure Media Services a castLabs.

![spolupráci](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typické nastavení systému

* Mediální obsah je uložený v AMS.
* ID klíčů klíčů obsahu se ukládají jak v castLabs, tak v AMS.
* castLabs a AMS mají i integrované ověřování tokenů. Následující části popisují ověřovací tokeny. 
* Když klient požaduje streamování videa, obsah se dynamicky zašifruje pomocí **Common Encryption** (CENC) a dynamicky balí v AMS do Smooth Streaming a pomlčky. Pro protokol streamování HLS zajišťujeme také základní šifrování datového proudu PlayReady M2TS.
* Licence PlayReady se načte z licenčního serveru AMS a licence Widevine se načte z licenčního serveru castLabs. 
* Media Player automaticky rozhoduje o tom, která licence se má načíst na základě schopnosti klientské platformy. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generování ověřovacího tokenu pro získání licence

CastLabs i AMS podporují formát tokenu JWT (JSON Web Token), který slouží k autorizaci licence. 

### <a name="jwt-token-in-ams"></a>Token JWT v AMS

Následující tabulka popisuje token JWT v AMS. 

| Vystavitel | Řetězec vystavitele z vybrané služby tokenů zabezpečení (STS) |
| --- | --- |
| Cílová skupina |Řetězec cílové skupiny z použité služby STS |
| Deklarace identit |Sada deklarací identity |
| NotBefore |Počáteční platnost tokenu |
| Platnost vyprší |Konec platnosti tokenu |
| SigningCredentials |Klíč sdílený mezi licenčním serverem PlayReady, castLabs licenčním serverem a službou STS, může být buď symetrický, nebo asymetrický klíč. |

### <a name="jwt-token-in-castlabs"></a>Token JWT v castLabs

Následující tabulka popisuje token JWT v castLabs. 

| Název | Popis |
| --- | --- |
| optData |Řetězec JSON obsahující informace o vás. |
| promítací |Řetězec JSON obsahující informace o assetu, licenční informace a práva k přehrávání. |
| IAT |Aktuální hodnota DateTime v epocha. |
| jti |Jedinečný identifikátor tohoto tokenu (každý token se dá použít jenom jednou v castLabs systému). |

## <a name="sample-solution-setup"></a>Nastavení ukázkového řešení

[Ukázkové řešení](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) se skládá ze dvou projektů:

* Konzolová aplikace, která se dá použít k nastavení omezení DRM pro již příjmový prostředek pro PlayReady i Widevine.
* Webová aplikace, která se dodávají tokeny, které by se mohly zobrazit jako velmi ZJEDNODUŠENá verze služby STS.

Použití konzolové aplikace:

1. Změňte app.config pro nastavení přihlašovacích údajů AMS, přihlašovacích údajů castLabs, konfigurace STS a sdíleného klíče.
2. Nahrajte Asset do AMS.
3. Získat UUID z nahraného Assetu a změnit řádek 32 v souboru Program.cs:
   
      var objIAsset = _context. Assets. Where (x => x.Id = = "NB: CID: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. Použijte AssetId pro pojmenování assetu v systému castLabs (řádek 44 v souboru Program.cs).
   
   Je nutné nastavit AssetId pro **castLabs**; musí se jednat o jedinečný alfanumerický řetězec.
5. Spustíte program.

Použití webové aplikace (STS):

1. Změňte web.config, aby se castlabs nastavení obchodního ID, konfigurace služby STS a sdíleného klíče.
2. Nasazení na Azure websites.
3. Přejděte na web.

## <a name="playing-back-a-video"></a>Přehrávání videa

Pokud chcete přehrát video šifrované se společným šifrováním (PlayReady nebo Widevine), můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Při spuštění konzolové aplikace se ID klíče obsahu a adresa URL manifestu vrátí do ozvěny.

1. Otevřete novou kartu a spusťte svoji službu STS: http://[yourStsName]. azurewebsites. NET/API/token/AssetID/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Přejít na [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Vložte adresu URL streamování.
4. Klikněte na zaškrtávací políčko **Upřesnit možnosti** .
5. V rozevíracím seznamu **ochrana** vyberte PlayReady nebo Widevine.
6. Do textového pole tokenu vložte token, který jste získali ze své služby STS. 
   
   Licenční server castLab nepotřebuje před tokenem předponu "Bearer =". Před odesláním tokenu ho prosím odeberte.
7. Aktualizujte přehrávač.
8. Video by mělo být přehráváno.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

