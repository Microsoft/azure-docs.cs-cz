---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 383cd286f89bde13f5e557792e980f0455e00917
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877182"
---
## <a name="deploy-and-configure-azure-media-services"></a>Nasazení a konfigurace Azure Media Services

Řešení používá účet Azure Media Services k uložení videoklipů detekce objektů, které provedla zařízení IoT Edge brány.

Když vytváříte účet Media Services:

- Je potřeba zadat název účtu, předplatné Azure, umístění, skupinu prostředků a účet úložiště. Při vytváření účtu Media Services vytvořte nový účet úložiště s použitím výchozích nastavení.

- Vyberte oblast **východní USA** pro dané umístění.

- Vytvořte novou skupinu prostředků s názvem *lva-RG* v oblasti **Východní USA** pro účty Media Services a úložiště. Po dokončení kurzů je snadné odebrat všechny prostředky odstraněním skupiny prostředků *lva-RG* .

Vytvořte [účet Media Services v Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Tyto kurzy používají **východní USA** oblasti ve všech příkladech. V případě potřeby můžete použít nejbližší oblast.

Poznamenejte si název **Media Services** účtu v souboru *scratchpad.txt* .

Po dokončení nasazení přejděte na stránku **vlastnosti** pro váš účet **Media Services** . Poznamenejte si **ID prostředku** v souboru *scratchpad.txt* . tuto hodnotu použijete později při konfiguraci modulu IoT Edge.

Dále nakonfigurujte Azure Active Directory instančního objektu pro prostředek Media Services. Vyberte **přístup přes rozhraní API** a pak **ověřování instančního objektu**. Vytvořte novou aplikaci Azure Active Directory se stejným názvem jako má prostředek Media Services a vytvořte tajný klíč s popisem *IoT Edge přístupu*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Konfigurace aplikace Azure A D pro Azure Media Services":::

Po vygenerování tajného klíče se posuňte dolů k části **kopírování přihlašovacích údajů pro připojení k oddílu aplikace instančního objektu** . Pak vyberte **JSON**. Všechny informace o přihlašovacích údajích můžete kopírovat odsud v jednom z nich. Poznamenejte si tyto informace v souboru *scratchpad.txt* , později je použijete při konfiguraci zařízení IoT Edge.

> [!WARNING]
> Tato možnost se dá zobrazit a uložit v tajnosti. Pokud ho ztratíte, budete muset vygenerovat další tajný klíč.

## <a name="create-the-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v rámci série kurzů k sestavení kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .

1. Přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k předplatnému Azure.

1. Pokud chcete začít vytvářet novou aplikaci IoT Central Azure, vyberte na stránce **sestavení** možnost **Nová aplikace** .

1. Vyberte **Retail (maloobchod**). Na stránce prodej se zobrazí několik šablon maloobchodních aplikací.

Vytvoření nové aplikace video Analytics:

1. Vyberte šablonu aplikace pro **video Analytics – objekt a detekce pohybu** . Tato šablona obsahuje šablony zařízení pro zařízení použitá v tomto kurzu. Šablona obsahuje ukázkové řídicí panely, které operátory můžou použít k provádění úloh, jako je monitorování a Správa fotoaparátů.

1. Volitelně můžete zvolit popisný **název aplikace**. Tato aplikace je založená na fiktivním maloobchodním obchodě s názvem Northwind Traders. V tomto kurzu se používá **název aplikace** *Northwind Traders video Analytics*.

    > [!NOTE]
    > Použijete-li popisný **název aplikace**, je stále nutné použít jedinečnou hodnotu pro **adresu URL**aplikace.

1. Pokud máte předplatné Azure, vyberte svůj **adresář**, **předplatné Azure**a **USA** jako **umístění**. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje. V tomto kurzu se používají tři zařízení – dva kamery a IoT Edge zařízení. Pokud nepoužijete bezplatnou zkušební verzi, bude se vám účtovat využití.

    Další informace o adresářích, předplatných a umístěních najdete v tématu [rychlý Start k vytvoření aplikace](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Vyberte **Vytvořit**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Konfigurace aplikace Azure A D pro Azure Media Services":::

### <a name="retrieve-the-configuration-data"></a>Načíst konfigurační data

Později v tomto kurzu při konfiguraci IoT Edge brány potřebujete některá konfigurační data z aplikace IoT Central. IoT Edge zařízení potřebuje tyto informace k registraci v nástroji a k aplikaci se připojí.

V části **Správa** vyberte **svou aplikaci** a poznamenejte si **adresu URL aplikace** a **ID aplikace** v souboru *scratchpad.txt* :

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Konfigurace aplikace Azure A D pro Azure Media Services":::

Vyberte **tokeny API** a vygenerujte nový token s názvem **LVAEdgeToken** pro roli **operátora** :

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Konfigurace aplikace Azure A D pro Azure Media Services":::

Poznamenejte si token v souboru *scratchpad.txt* pro pozdější provedení. Po zavření dialogového okna nelze token znovu zobrazit.

V části **Správa** vyberte **připojení zařízení**a pak vyberte **SAS-IoT-Devices**.

Poznamenejte si **primární klíč** pro zařízení v souboru *scratchpad.txt* . Pomocí tohoto *tokenu sdíleného přístupového podpisu primární skupiny* můžete později nakonfigurovat IoT Edge zařízení.
