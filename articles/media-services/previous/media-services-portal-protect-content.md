---
title: Nakonfigurujte zásady ochrany obsahu pomocí portálu Azure | Microsoft Docs
description: Tento článek ukazuje, jak pomocí portálu Azure ke konfiguraci zásad ochrany obsahu. Článek také ukazuje, jak povolit dynamické šifrování pro vaše prostředky.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 8603716d30e1061ca9d600f2c053e90ff50c2433
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Nakonfigurujte zásady ochrany obsahu pomocí portálu Azure
 Pomocí služby Azure Media Services můžete zabezpečit médiu od okamžiku, kdy by poté počítač prostřednictvím úložiště, zpracování a doručení. Služba Media Services můžete použít k doručení obsahu dynamicky šifrovat s Standard AES (Advanced Encryption) pomocí klíčů 128bitové šifrování. Také můžete se s běžným šifrováním (CENC) pomocí PlayReady nebo Widevine správy digitálních práv (DRM) a Apple FairPlay. 

Služba Media Services poskytuje službu k doručování licencí DRM a AES zrušte klíče autorizovaným klientům. Na portálu Azure můžete vytvořit jeden klíč nebo licenčních zásad autorizace pro všechny typy šifrování.

Tento článek ukazuje, jak nakonfigurovat zásadu ochrany obsahu pomocí portálu. Článek také ukazuje, jak chcete použít dynamické šifrování pro vaše prostředky.

## <a name="start-to-configure-content-protection"></a>Počáteční konfigurace ochrany obsahu
Použití portálu ke konfiguraci globálních ochrana obsahu pomocí vašeho účtu Media Services, proveďte následující kroky:

1. V [portál](https://portal.azure.com/), vyberte svůj účet Media Services.

2. Vyberte **nastavení** > **obsahu ochrany**.

    ![Ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zásady autorizace klíčů nebo licencí
Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč nebo licencí. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Váš klient pak musí splňovat zásady předtím, než mohou být zajišťovány klíč nebo licence, které jsou k němu. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem.

Na portálu můžete vytvořit jeden klíč nebo licenčních zásad autorizace pro všechny typy šifrování.

### <a name="open-authorization"></a>Otevřete autorizace
Otevřete omezení znamená, že systém poskytuje klíč pro každý, kdo požádá klíče. Toto omezení může být užitečná pro účely testování. 

### <a name="token-authorization"></a>Token autorizace
Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve jednoduchého webového tokenu (SWT) a formátů JSON Web Token (JWT). Služba Media Services neposkytuje služby tokenů zabezpečení. Můžete vytvořit vlastní službu tokenů zabezpečení nebo pomocí služby Řízení přístupu Azure na tokeny problém. Služba tokenů zabezpečení musí být nakonfigurované vytvořit token podepsané zadaný klíč a vystavování deklarací identity, které jste zadali v nastavení omezení s tokenem. Pokud token je platný a deklarace identity v tokenu shodovat s nakonfigurovaným pro klíč (nebo licencí), vrátí doručení klíče služby Media Services požadovaný klíč (nebo licencí) do klienta.

Když konfigurujete zásady omezený token, musíte zadat ověření primární klíč, vystavitele a cílová skupina parametry. Ověření primární klíč obsahuje klíč, který byl podepsaný token. Vystavitel je zabezpečený tokenu služba, která vydá token. Cílová skupina (někdy nazývané oboru) popisuje záměr tokenu nebo prostředek token povolí přístup k. Služba Media Services doručení klíče ověří, jestli tyto hodnoty v tokenu shodují s hodnotami v šabloně.

![Zásady autorizace klíčů nebo licencí](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Šablona licence PlayReady
Šablona licence PlayReady na licence PlayReady nastaví funkci, která je povolena. Další informace o šabloně licence PlayReady najdete v tématu [přehled šablonu licence Media Services PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Zajišťováno
Pokud nakonfigurujete jako zajišťováno licenci, je udržována v paměti pouze tehdy, když přehrávač používá licence.  

![Zajišťováno ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Trvalé
Pokud konfigurujete licenci jako trvalé, se uloží do trvalého úložiště na straně klienta.

![Trvalá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Šablona licence Widevine
Šablona licence Widevine nastaví funkci, která je povolena na licence na Widevine.

### <a name="basic"></a>Basic
Když vyberete **základní**, vytvoření šablony s všechny výchozí hodnoty.

### <a name="advanced"></a>Advanced
Další informace o šabloně rights Widevine najdete v tématu [přehled šablonu licence Widevine](media-services-widevine-license-template-overview.md).

![Rozšířená ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfigurace FairPlay
Chcete-li povolit šifrování FairPlay, vyberte **FairPlay konfigurace**. Vyberte **certifikát aplikace** a zadejte **tajný klíč aplikace**. Další informace o konfiguraci FairPlay a požadavky najdete v tématu [chránit váš obsah s Apple FairPlay nebo Microsoft PlayReady HLS](media-services-protect-hls-with-FairPlay.md).

![Konfigurace FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Použít na asset dynamické šifrování
Abyste mohli využívat dynamické šifrování, zakódujte váš zdrojový soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Vyberte asset, který chcete zašifrovat
Pokud chcete zobrazit všechny prostředky, vyberte **nastavení** > **prostředky**.

![Možnost prostředky](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Šifrování AES nebo DRM
Když vyberete **šifrovat** majetku, uvidíte dvě možnosti: **AES** nebo **DRM**. 

#### <a name="aes"></a>AES
Vymazat na všechny protokoly streamování je povolené šifrování klíče AES: technologie Smooth Streaming, HLS a MPEG-DASH.

![Konfigurace šifrování](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Po výběru **DRM**, najdete v části různých obsahu zásad ochrany (které musí být nakonfigurované pomocí tohoto bodu) a sadu streamování protokoly:

    a. **PlayReady a Widevine s MPEG-DASH** dynamicky šifruje MPEG-DASH datového proudu s technologií PlayReady a Widevine technologiemi DRM.

    b. **PlayReady a Widevine s MPEG-DASH + FairPlay s HLS** dynamicky šifrovat MPEG-DASH datového proudu s technologií PlayReady a Widevine technologiemi DRM. Tato možnost taky šifruje vaše datové proudy HLS s FairPlay.

    c. **PlayReady pouze s technologie Smooth Streaming, HLS a MPEG-DASH** dynamicky šifruje datové proudy technologie Smooth Streaming, HLS a MPEG-DASH s technologií PlayReady DRM.

    d. **Widevine pouze s MPEG-DASH** dynamicky šifruje vaše MPEG-DASH pomocí Widevine DRM.
    
    e. **FairPlay pouze s HLS** dynamicky šifruje HLS datového proudu s FairPlay.

2. Chcete-li povolit šifrování FairPlay, na **obsahu globální nastavení ochrany** vyberte **FairPlay konfigurace**. Vyberte **certifikát aplikace**a zadejte **tajný klíč aplikace**.

    ![Typ šifrování](./media/media-services-portal-content-protection/media-services-content-protection009.png)

3. Když provedete výběr šifrování, vyberte **použít**.

>[!NOTE] 
>Pokud máte v plánu k přehrávání HLS šifrované AES v prohlížeči Safari, naleznete v příspěvku blogu [šifrované HLS v prohlížeči Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

