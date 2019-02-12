---
title: Konfigurace zásad ochrany obsahu pomocí webu Azure portal | Dokumentace Microsoftu
description: Tento článek ukazuje, jak pomocí webu Azure portal nakonfigurovat zásady ochrany obsahu. Tento článek také ukazuje, jak povolit dynamické šifrování pro vaše prostředky.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: dc5439c98b5abc69b6cb1ba144e015426bb887ce
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996927"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurace zásad ochrany obsahu pomocí webu Azure portal
 Pomocí služby Azure Media Services můžete zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Služba Media Services můžete použít k doručování dynamicky šifrovaných s na Standard AES (Advanced Encryption) s použitím 128bitové šifrování klíče obsahu. Také můžete ho s používat standard common encryption (CENC) pomocí technologie PlayReady nebo Widevine správy digitálních práv (DRM) a Apple FairPlay. 

Služba Media Services poskytuje službu k doručování licencí DRM a standardu AES Vymazat klíče autorizovaným klientům. Na webu Azure portal můžete vytvořit jednu zásadu autorizace klíčů nebo licencí pro všechny typy šifrování.

Tento článek ukazuje, jak nakonfigurovat zásady ochrany obsahu pomocí portálu. Tento článek také ukazuje, jak použít dynamické šifrování na vaše prostředky.

## <a name="start-to-configure-content-protection"></a>Počáteční konfigurace ochrany obsahu
Konfigurace globální ochrany obsahu pomocí účtu Media Services pomocí portálu, proveďte následující kroky:

1. V [portál](https://portal.azure.com/), vyberte svůj účet Media Services.

1. Vyberte **nastavení** > **Content protection**.

    ![Ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Zásady autorizace klíčů nebo licencí
Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíčů nebo licencí. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Váš klient pak musí zásady splňovat, než správu klíčů/licencí může doručit do ní. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem.

Na portálu můžete vytvořit jednu zásadu autorizace klíčů nebo licencí pro všechny typy šifrování.

### <a name="open-authorization"></a>Otevřít autorizace
Otevřít omezení znamená, že systém poskytuje všem uživatelům, kteří vytvoří klíče požadavek klíč. Toto omezení může být užitečná pro účely testování. 

### <a name="token-authorization"></a>Token autorizace
Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve jednoduchý webový token (SWT) a formátů JSON Web Token (JWT). Služba Media Services neposkytuje služby tokenů zabezpečení. Můžete vytvořit vlastní službu STS, nebo použít Azure Access Control Service pro vydávání tokenů. Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Pokud je token platný a deklarace identity v tokenu odpovídají těm nakonfigurovaný pro klíče (nebo licencí), doručení klíče služby Media Services vrátí klientovi požadovaný klíč (nebo licencí).

Když konfigurujete zásady omezení tokenem, musíte zadat primární ověřovací klíč, vydavatele a parametry cílovou skupinu. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je služba tokenů zabezpečení, který vydá token. Cílová skupina (říká se jim oboru) by měl popisovat záměr tokenu nebo prostředek token, který autorizuje přístup k. Služba Media Services doručení klíče ověří, že tyto hodnoty v tokenu odpovídají hodnotám v šabloně.

![Zásady autorizace klíčů nebo licencí](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Šablona licencování PlayReady
Šablona licencování PlayReady nastaví funkci, která je povolena na vaše licence PlayReady. Další informace o šabloně licence PlayReady, najdete v článku [Přehled šablon licencování Media Services PlayReady](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Zajišťováno
Pokud nakonfigurujete jako nonpersistent licenci, je udržována v paměti pouze tehdy, když hráč používá licence.  

![Zajišťováno ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Trvalé
Pokud konfigurujete licenci jako trvalé, se uloží do trvalého úložiště na straně klienta.

![Trvalá ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Šablona licencování Widevine
Šablona licencování Widevine nastaví funkci, která je povolená licencí Widevine.

### <a name="basic"></a>Basic
Když vyberete **základní**, vytvoření šablony s použitím všech výchozích hodnot.

### <a name="advanced"></a>Rozšířený 
Další informace o šablona práv Widevine, najdete v článku [Přehled šablon licencování Widevine](media-services-widevine-license-template-overview.md).

![Rozšířená ochrana obsahu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Konfigurace FairPlay
Pokud chcete povolit šifrování FairPlay, vyberte **konfigurace FairPlay**. Vyberte **certifikát aplikace** a zadejte **tajný klíč aplikace**. Další informace o požadavcích a konfigurace FairPlay najdete v tématu [chránit obsah pomocí Apple FairPlay nebo Microsoft PlayReady HLS](media-services-protect-hls-with-FairPlay.md).

![Konfigurace FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Použít dynamické šifrování na prostředek
Abyste mohli využívat dynamické šifrování, zdrojový soubor zakódujte do sady souborů MP4 s adaptivní přenosovou rychlostí.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Vyberte asset, který chcete zašifrovat
Pokud chcete zobrazit všechny vaše prostředky, vyberte **nastavení** > **prostředky**.

![Možnost prostředky](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Šifrování s licencemi DRM nebo AES
Když vyberete **šifrovat** pro určitý prostředek, se zobrazí dvě možnosti: **AES** nebo **DRM**. 

#### <a name="aes"></a>AES
AES je jasné, že je povoleno šifrování klíče na všechny streamovací protokoly: Technologie Smooth Streaming, HLS a MPEG-DASH.

![Konfigurace šifrování](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Po výběru **DRM**, najdete v článku různých obsahu zásad ochrany (které je nutné nakonfigurovat tento bod) a sada streamovací protokoly:

    a. **PlayReady a Widevine s MPEG-DASH** dynamicky šifruje pomocí PlayReady DRM nebo Widevine technologiemi DRM streamů MPEG-DASH.

    b. **PlayReady a Widevine s MPEG-DASH + FairPlay s HLS** dynamicky šifrovat pomocí PlayReady DRM nebo Widevine technologiemi DRM streamů MPEG-DASH. Tato možnost taky šifruje vaše HLS datové proudy s FairPlay.

    c. **PlayReady jenom s technologie Smooth Streaming, HLS a MPEG-DASH** dynamicky šifruje datové proudy technologie Smooth Streaming, HLS a MPEG-DASH pomocí technologie PlayReady DRM.

    d. **Jenom Widevine s MPEG-DASH** dynamicky šifruje vaše MPEG-DASH pomocí Widevine DRM.
    
    e. **Jenom FairPlay s HLS** dynamicky šifruje datový proud s FairPlay HLS.

1. Povolit šifrování FairPlay, na **globální nastavení ochrany obsahu** okně vyberte **konfigurace FairPlay**. Vyberte **certifikát aplikace**a zadejte **tajný klíč aplikace**.

    ![Typ šifrování](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Po výběru možnosti šifrování, vyberte **použít**.

>[!NOTE] 
>Pokud máte v plánu přehrávání HLS šifrováním AES v prohlížeči Safari, najdete v blogovém příspěvku [HLS se šifrováním v prohlížeči Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

