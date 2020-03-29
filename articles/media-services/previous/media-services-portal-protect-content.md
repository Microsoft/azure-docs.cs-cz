---
title: Konfigurace zásad ochrany obsahu pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak pomocí portálu Azure ke konfiguraci zásad ochrany obsahu. Článek také ukazuje, jak povolit dynamické šifrování pro vaše datové zdroje.
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
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978198"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurace zásad ochrany obsahu pomocí portálu Azure

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).   > Do služby Media Services v2 nejsou přidávány žádné nové funkce ani funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)
>

 Pomocí Azure Media Services můžete zabezpečit média od okamžiku, kdy opustí váš počítač, prostřednictvím úložiště, zpracování a doručení. Pomocí služby Media Services můžete pomocí 128bitových šifrovacích klíčů dodávat obsah zašifrovaný dynamicky pomocí standardu Rozšířenéšifrování (Advanced Encryption Standard). Můžete jej také použít se společným šifrováním (CENC) pomocí playready a/nebo widevine správy digitálních práv (DRM) a Apple FairPlay. 

Služba Media Services poskytuje službu pro doručování licencí DRM a jasných klíčů AES oprávněným klientům. Portál Azure můžete použít k vytvoření jedné zásady autorizace klíče a licence pro všechny typy šifrování.

Tento článek ukazuje, jak nakonfigurovat zásady ochrany obsahu pomocí portálu. Článek také ukazuje, jak použít dynamické šifrování na vaše datové zdroje.

## <a name="start-to-configure-content-protection"></a>Spuštění konfigurace ochrany obsahu
Chcete-li pomocí portálu konfigurovat globální ochranu obsahu pomocí účtu Mediální služby, postupujte takto:

1. Na [portálu](https://portal.azure.com/)vyberte účet Mediálních služeb.

1. Vyberte **Nastavení** > **ochrany obsahu**.

    ![Ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zásady autorizace klíče/licence
Služba Media Services podporuje několik způsobů ověřování uživatelů, kteří požadují použití klíčů nebo licencí. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Váš klient pak musí splnit zásady před klíč / licence může být doručena do něj. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem.

Portál můžete použít k vytvoření jedné zásady autorizace klíče/licence pro všechny typy šifrování.

### <a name="open-authorization"></a>Otevřít autorizaci
Otevřené omezení znamená, že systém doručí klíč každému, kdo požádá o klíč. Toto omezení může být užitečné pro účely testování. 

### <a name="token-authorization"></a>Autorizace tokenu
Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu jednoduchých webových tokenů (SWT) a webových tokenů JSON (JWT). Mediální služby neposkytují sts. Můžete vytvořit vlastní STS nebo použít službu Řízení přístupu Azure k vydávání tokenů. Služba STS musí být nakonfigurována tak, aby vytvořila token podepsaný zadaným klíčem a vydala deklarace identity, které jste zadali v konfiguraci omezení tokenu. Pokud je token platný a deklarace identity v tokenu odpovídají deklaracím nakonfigurovaným pro klíč (nebo licenci), služba doručování klíčů Mediální služby vrátí požadovaný klíč (nebo licenci) klientovi.

Při konfiguraci zásad s omezeným přístupem tokenu je nutné zadat primární ověřovací klíč, vystavitel a parametry publika. Primární ověřovací klíč obsahuje klíč, se kterým byl token podepsán. Vystavitel je služba zabezpečeného tokenu, která vydává token. Cílová skupina (někdy označovaná jako obor) popisuje záměr tokenu nebo prostředku, ke kterému token autorizuje přístup. Služba doručování klíčů Mediální služby ověří, zda tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

![Zásady autorizace klíče/licence](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Šablona licencování PlayReady
Šablona licence PlayReady nastavuje funkce, které jsou povoleny v licenci PlayReady. Další informace o licenční šabloně PlayReady naleznete v [přehledu šablony licence Služby Media Services PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Netrvalá
Pokud nakonfigurujete licenci jako netrvalopersistentovou, bude uložena v paměti pouze v době, kdy ji přehrávač používá.  

![Netrvalá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Trvalý
Pokud nakonfigurujete licenci jako trvalou, uloží se v trvalém úložišti v klientovi.

![Trvalá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Šablona licencování Widevine
Šablona licence Widevine nastavuje funkce, které jsou povoleny na licencích Widevine.

### <a name="basic"></a>Basic
Když vyberete **Základní**, šablona se vytvoří se všemi výchozími hodnotami.

### <a name="advanced"></a>Upřesnit
Další informace o šabloně práv Widevine naleznete v [přehledu šablony licence Widevine](media-services-widevine-license-template-overview.md).

![Pokročilá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfigurace FairPlay
Chcete-li povolit šifrování FairPlay, vyberte **možnost Konfigurace FairPlay**. Pak vyberte **certifikát aplikace** a zadejte **tajný klíč aplikace**. Další informace o konfiguraci a požadavcích fairplayna najdete v [tématu Ochrana obsahu HLS pomocí Apple FairPlay nebo Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Konfigurace FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Použití dynamického šifrování na váš datový zdroj
Chcete-li využít výhod dynamického šifrování, zakódujte zdrojový soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Vyberte datový zdroj, který chcete zašifrovat.
Chcete-li zobrazit všechny datové zdroje, vyberte **možnost Nastavení** > **datových zdrojů**.

![Možnost Aktiv](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Šifrování pomocí AES nebo DRM
Když vyberete **Šifrovat** pro datový zdroj, zobrazí se dvě možnosti: **AES** nebo **DRM**. 

#### <a name="aes"></a>AES
AES clear key encryption je povolena na všech protokolech streamování: Smooth Streaming, HLS a MPEG-DASH.

![Konfigurace šifrování](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>Drm
1. Po výběru **technologie DRM**se zobrazí různé zásady ochrany obsahu (které musí být v tomto bodě nakonfigurovány) a sada protokolů streamování:

    a. **PlayReady a Widevine s MPEG-DASH** dynamicky šifruje váš mpeg-dash stream s PlayReady a Widevine DRM.

    b. **PlayReady a Widevine s MPEG-DASH + FairPlay s HLS** dynamicky šifrovat mpeg-DASH stream s PlayReady a Widevine DRM. Tato možnost také šifruje vaše HLS streamy pomocí FairPlay.

    c. **Funkce PlayReady pouze s funkcemi Smooth Streaming, HLS a MPEG-DASH** dynamicky šifruje streamy Plynulé streamování, HLS a MPEG-DASH pomocí funkce PlayReady DRM.

    d. **Widevine pouze s MPEG-DASH** dynamicky šifruje váš MPEG-DASH s Widevine DRM.
    
    e. **FairPlay pouze s HLS** dynamicky šifruje váš HLS stream s FairPlay.

1. Chcete-li povolit šifrování FairPlay, vyberte v okně **Ochrana obsahu globální nastavení** **konfiguraci FairPlay**. Potom vyberte **certifikát aplikace**a zadejte **tajný klíč aplikace**.

    ![Typ šifrování](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Po výběru šifrování vyberte **Použít**.

>[!NOTE] 
>Pokud máte v plánu hrát Hls šifrované AES v Safari, podívejte se na blogový příspěvek [Encrypted HLS v Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

