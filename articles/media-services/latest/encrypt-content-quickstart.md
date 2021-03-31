---
title: Šifrování obsahu pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat šifrování obsahu pomocí Azure Media Services v Azure Portal.
ms.topic: quickstart
ms.date: 08/31/2020
ms.openlocfilehash: e42b2d4487f2f51c082f22068160a02b3dcbb0a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89263633"
---
# <a name="quickstart-use-portal-to-encrypt-content"></a>Rychlý Start: použití portálu k šifrování obsahu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pomocí Azure Media Services můžete lépe zabezpečit vaše média v době, kdy počítač opustí, a to prostřednictvím úložiště, zpracování a doručování. Pomocí Media Services můžete doručovat živý obsah na vyžádání a dynamicky šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo kteréhokoli ze tří hlavních systémů DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine a Apple FairPlay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) autorizovaným klientům. 
 
Pokud chcete v datovém proudu určit možnosti šifrování (pokud existují), použijte **zásady streamování** a přidružte je k lokátoru streamování. **Zásadu pro klíč obsahu** vytvoříte pro konfiguraci způsobu doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim **prostředkům**) koncovým klientům. Je nutné nastavit požadavky (omezení) na zásady klíče obsahu, které musí být splněny, aby bylo možné klíče se zadanou konfigurací doručovat klientům. 

> [!NOTE]
> Zásady klíče obsahu není potřeba pro vymazání streamování nebo stahování.

Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování obsahu pomocí nezašifrovaného klíče AES nebo šifrování DRM. Aby se datový proud dešifroval, přehrávač požádá o klíč od Media Services služby doručování klíčů nebo služby pro doručování klíčů, kterou jste zadali. Pokud chcete zjistit, jestli má uživatel autorizaci získat klíč, služba vyhodnotí  **zásadu klíče obsahu** , kterou jste zadali pro tento klíč.

V tomto rychlém startu se dozvíte, jak vytvořit zásadu klíče obsahu, kde můžete určit, jaké šifrování se má u prostředku při streamování použít. V tomto rychlém startu se také dozvíte, jak nastavit nakonfigurované šifrování prostředku.

### <a name="suggested-pre-reading"></a>Navrhované před čtením

* [Dynamické šifrování a doručování klíčů](content-protection-overview.md)
* [Lokátory streamování](streaming-locators-concept.md)
* [Zásady streamování](streaming-policy-concept.md)
* [Zásady symetrických klíčů](content-key-policy-concept.md)

## <a name="prerequisites"></a>Požadavky

Nahrajte a zpracujte svůj obsah, jak je popsáno v tématu [Správa prostředků v Azure Portal](manage-assets-quickstart.md)

## <a name="create-a-content-key-policy"></a>Vytvoření zásad pro klíč obsahu

Vytvořte **zásady klíče obsahu** , abyste mohli nakonfigurovat, jak se má koncovým klientům doručovat klíč obsahu (který poskytuje zabezpečený přístup k vašim **prostředkům**).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte účet Media Services a klikněte na něj.
1. Vyberte **zásady pro klíč obsahu (nové)**.
1. V horní části okna stiskněte **a přidejte zásady pro klíč obsahu** . 

Zobrazí se okno **vytvořit zásadu klíče obsahu** . V tomto okně si zvolíte možnosti šifrování. Můžete si vybrat, že chcete své médium chránit volbou možnosti DRM (Správa digitálních práv), standardu AES (Advanced Encryption Standard) nebo obou.  

![Vytvoření zásad pro klíč obsahu](./media/encrypt-content-quickstart/create-content-key-policy.png)

Bez ohledu na to, jestli vyberete jednu z možností DRM nebo možnost Vymazat klíč AES-128, doporučujeme, abyste určili, jak chcete nakonfigurovat omezení. Můžete zvolit, aby bylo omezení Open nebo tokenu. Podrobné vysvětlení najdete v tématu [řízení přístupu k obsahu](content-protection-overview.md#controlling-content-access).

### <a name="add-a-drm-content-key"></a>Přidat klíč obsahu DRM

Můžete se rozhodnout chránit svůj obsah pomocí Microsoft PlayReady nebo Google Widevine nebo Apple FairPlay. Každý typ doručení licence ověří klíče obsahu na základě vašich přihlašovacích údajů v šifrovaném formátu.

#### <a name="license-templates"></a>Šablony licencí

Podrobnosti o šablonách licencí najdete v těchto tématech:

* [Šablona licencí Google Widevine](widevine-license-template-overview.md)

    > [!NOTE]
    > Můžete vytvořit prázdnou šablonu licence bez hodnot, stačí " {} ." Pak se vytvoří šablona licence s výchozími hodnotami. Výchozí hodnota funguje ve většině případů.
* [Konfigurace a licenční požadavky pro Apple FairPlay](fairplay-license-overview.md)
* [Šablona licencování PlayReady](playready-license-template-overview.md)

### <a name="add-aes-clear-key"></a>Přidat jasný klíč AES

K vašemu obsahu můžete také přidat šifrované šifrování klíče AES-128. Klíč obsahu se přenáší klientovi v nešifrovaném formátu.

![Nezašifrovaný klíč AES](./media/encrypt-content-quickstart/aes-clear-key-policy.png)

## <a name="create-a-streaming-locator-for-your-asset"></a>Vytvoření lokátoru streamování pro váš prostředek

1. Vyhledejte účet Media Services a klikněte na něj.
1. Vyberte **assety (nové)**.
1. V seznamu prostředků vyberte ten, který chcete zašifrovat.  
1. V části **Lokátor streamování** pro vybraný prostředek stiskněte **+ Přidat Lokátor streamování**. 
1. Vyberte **zásadu streamování** , která je vhodná pro **zásady klíče obsahu** , které jste nakonfigurovali.

    Téma [zásady streamování](streaming-policy-concept.md) poskytuje podrobné informace o tom, jaké zásady streamování odpovídají zásadám pro klíč obsahu.
1. Jakmile vyberete vhodné zásady streamování, můžete v rozevíracím seznamu vybrat zásadu klíče obsahu.
1. Stisknutím tlačítka **Přidat** přidejte Lokátor streamování do assetu.

    Tím se publikuje Asset a vygeneruje se adresy URL streamování.

![Lokátor streamování](./media/encrypt-content-quickstart/multi-drm.png)

## <a name="cleanup-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento rychlý Start, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

[Správa prostředků](manage-assets-quickstart.md)
