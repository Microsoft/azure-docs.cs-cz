---
title: Konfigurace zásad ochrany obsahu pomocí Azure Portal | Microsoft Docs
description: Tento článek ukazuje, jak pomocí Azure Portal nakonfigurovat zásady ochrany obsahu. Tento článek také ukazuje, jak povolit dynamické šifrování pro vaše prostředky.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 061d66f08e79f935c778eb84dd47f71cab1a62d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266625"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurace zásad ochrany obsahu pomocí Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).   > do Media Services V2 se přidávají žádné nové funkce ani funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .
>

 Pomocí Azure Media Services můžete své médium zabezpečit od okamžiku, kdy počítač opustí úložiště, zpracování a doručování. Media Services můžete použít k dynamickému šifrování obsahu pomocí standard AES (Advanced Encryption Standard) (AES) pomocí 128 bitových šifrovacích klíčů. Můžete ji také použít se společným šifrováním (CENC) pomocí technologie PlayReady nebo Widevine DRM (Správa digitálních práv) a Apple FairPlay. 

Media Services poskytuje službu pro doručování licencí DRM a nezašifrovaných klíčů AES do autorizovaných klientů. Pomocí Azure Portal můžete vytvořit jednu zásadu autorizace klíče/licence pro všechny typy šifrování.

Tento článek ukazuje, jak nakonfigurovat zásady ochrany obsahu pomocí portálu. Tento článek také ukazuje, jak použít dynamické šifrování pro vaše prostředky.

## <a name="start-to-configure-content-protection"></a>Zahájení konfigurace ochrany obsahu
Pokud chcete použít portál ke konfiguraci globální ochrany obsahu pomocí účtu Media Services, proveďte následující kroky:

1. Na [portálu](https://portal.azure.com/)vyberte svůj účet Media Services.

1. Vyberte **Nastavení**  >  **Ochrana obsahu**.

    ![Ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zásady autorizace klíčů a licencí
Media Services podporuje více způsobů ověřování uživatelů, kteří vytvářejí požadavky na klíč nebo licenci. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Váš klient pak musí zásadu splnit, aby bylo možné do ní doručovat klíč nebo licenci. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem.

Portál můžete použít k vytvoření jedné zásady autorizace klíče a licence pro všechny typy šifrování.

### <a name="open-authorization"></a>Otevřít autorizaci
Otevřené omezení znamená, že systém doručuje klíč všem, kdo vytvoří požadavek na klíč. Toto omezení může být užitečné pro testovací účely. 

### <a name="token-authorization"></a>Autorizace tokenu
Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu jednoduchých webových tokenů (SWT) a webových tokenů JSON (JWT). Media Services neposkytuje službu STS. Můžete vytvořit vlastní STS nebo použít Azure Access Control Service k vydávání tokenů. Služba STS musí být nakonfigurovaná tak, aby vytvořila token podepsaný pomocí zadaného klíče a vydávala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Pokud je token platný a deklarace identity v tokenu se shodují s hodnotami nakonfigurovanými pro klíč (nebo licenci), služba doručování Media Services Key vrátí požadovaný klíč (nebo licenci) klientovi.

Když konfigurujete zásady s omezením tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, pomocí kterého byl token podepsán. Vystavitel je služba tokenů zabezpečení, která vydává token. Cílová skupina (někdy označovaná jako obor) popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba doručení Media Services Key ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

![Zásady autorizace klíčů a licencí](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Šablona licencování PlayReady
Šablona licence PlayReady nastaví funkce, které jsou povolené v licenci PlayReady. Další informace o šabloně licence PlayReady najdete v tématu [Přehled šablon licencí](media-services-playready-license-template-overview.md)playready pro Media Services.

### <a name="nonpersistent"></a>Netrvalé
Pokud nakonfigurujete licenci jako netrvalou, bude uložena v paměti pouze v případě, že hráč používá licenci.  

![Netrvalá Ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Trvalý
Pokud licenci nakonfigurujete jako trvalou, uloží se do trvalého úložiště na klientovi.

![Trvalá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Šablona licencování Widevine
Šablona licence Widevine nastaví funkce, které jsou povolené na vašich licencích Widevine.

### <a name="basic"></a>Základní
Když vyberete **základní**, vytvoří se šablona se všemi výchozími hodnotami.

### <a name="advanced"></a>Pokročilý
Další informace o šabloně Widevine Rights najdete v tématu [Přehled šablon licencí Widevine](media-services-widevine-license-template-overview.md).

![Pokročilá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfigurace FairPlay
Pokud chcete povolit šifrování FairPlay, vyberte **Konfigurace Fairplay**. Pak vyberte **certifikát aplikace** a zadejte **tajný klíč aplikace**. Další informace o konfiguraci a požadavcích FairPlay najdete v tématu [Ochrana obsahu HLS pomocí Apple Fairplay nebo Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Konfigurace FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Použití dynamického šifrování u prostředku
Pokud chcete využít výhod dynamického šifrování, zakódujte zdrojový soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Vyberte prostředek, který chcete zašifrovat.
Pokud chcete zobrazit všechny prostředky, vyberte **Nastavení**  >  **prostředky**.

![Možnost assetů](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Šifrování pomocí AES nebo DRM
Když pro prostředek vyberete možnost **šifrování** , zobrazí se dvě možnosti: **AES** nebo **DRM**. 

#### <a name="aes"></a>AES
Šifrování standardu AES je povolené na všech protokolech streamování: Smooth Streaming, HLS a MPEG-POMLČKa.

![Konfigurace šifrování](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DIGITÁLNÍCH
1. Po výběru **DRM**se zobrazí různé zásady ochrany obsahu (které se musí konfigurovat tímto bodem) a sada protokolů streamování:

    a. **PlayReady a Widevine s MPEG-pomlčkou** dynamicky šifruje váš datový proud MPEG-spojovník pomocí PlayReady a Widevine několikanásobnou.

    b. **PlayReady a Widevine s MPEG-pomlčkou + Fairplay a HLS** dynamicky šifrují svůj Stream MPEG-spojovník pomocí PlayReady a Widevine několikanásobnou. Tato možnost také šifruje datové proudy HLS pomocí FairPlay.

    c. **PlayReady jenom s Smooth Streaming, HLS a MPEG-pomlčkou** dynamicky šifruje Smooth Streaming, HLS a streamy MPEG-spojovníky pomocí technologie PlayReady DRM.

    d. **Widevine jenom s MPEG-pomlčkou** dynamicky šifruje MPEG-pomlčku pomocí Widevine DRM.
    
    e. **Fairplay jenom s HLS** dynamicky šifruje datový proud HLS pomocí Fairplay.

1. Pokud chcete povolit šifrování FairPlay, v okně **Content Protection globální nastavení** vyberte **Konfigurace Fairplay**. Pak vyberte **certifikát aplikace**a zadejte **tajný klíč aplikace**.

    ![Typ šifrování](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Až provedete výběr šifrování, vyberte **použít**.

>[!NOTE] 
>Pokud v prohlížeči Safari chcete přehrát HLS šifrovaný standardem AES, přečtěte si Blogový příspěvek [zašifrovaný HLS v Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
