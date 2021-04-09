---
title: Instalace Azure Firewall ve virtuálním centru WAN
titleSuffix: Azure Virtual WAN
description: Postup konfigurace Azure Firewall ve virtuálním centru WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983673"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Konfigurace Azure Firewall ve virtuálním centru WAN

**Zabezpečené centrum** je Azure Virtual WAN hub s Azure firewall. Tento článek vás provede jednotlivými kroky, jak převést virtuální rozbočovač WAN na zabezpečený rozbočovač tím, že nainstalujete Azure Firewall přímo ze stránek portálu Azure Virtual WAN.

## <a name="before-you-begin"></a>Než začnete

V krocích v tomto článku se předpokládá, že jste už nasadili virtuální síť WAN s jedním nebo více rozbočovači.

Pokud chcete vytvořit novou virtuální síť WAN a nové centrum, použijte postup v následujících článcích:

* [Vytvoření virtuální sítě WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Vytvoření rozbočovače](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Zobrazit virtuální rozbočovače

Na stránce **Přehled** vaší virtuální sítě WAN se zobrazuje seznam virtuálních Center a zabezpečených Center. Následující obrázek ukazuje virtuální síť WAN bez zabezpečených Center.

[![Snímek obrazovky zobrazující stránku s přehledem pro virtuální síť WAN se seznamem virtuálních rozbočovačů.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Převést na zabezpečené centrum

1. Na stránce **Přehled** pro vaši virtuální síť WAN vyberte centrum, které chcete převést na zabezpečené centrum. Na stránce virtuální rozbočovač se zobrazí dvě možnosti nasazení Azure Firewall do tohoto centra. Vyberte jednu z možností.

   [![Snímek obrazovky se zobrazí stránka s přehledem pro vaši virtuální síť WAN, kde můžete vybrat možnost převést na zabezpečená centra nebo Azure firewall.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Po výběru jedné z možností se zobrazí stránka **převést na zabezpečený střed** . Vyberte rozbočovač, který chcete převést, a potom v dolní části stránky vyberte **Další: Azure firewall** .

   [![Vybrat centrum](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Po dokončení pracovního postupu vyberte **Potvrdit**.

   [![Snímek obrazovky se zobrazí v podokně převést do zabezpečeného centra s vybraným potvrzením.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Po převedení centra na zabezpečený rozbočovač si ho můžete zobrazit na stránce s **přehledem** virtuální sítě WAN.

   [![Zobrazit zabezpečené centrum](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Zobrazení prostředků centra

Na stránce s **přehledem** virtuální sítě WAN vyberte zabezpečené centrum. Na stránce centra můžete zobrazit všechny prostředky virtuálního rozbočovače, včetně Azure Firewall.

[![zobrazení prostředků centra](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Pokud chcete zobrazit nastavení Azure Firewall ze zabezpečeného centra, vyberte v části **zabezpečení** možnost **Zabezpečená nastavení virtuálního rozbočovače**.
[![Zobrazit nastavení centra](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Konfigurovat další nastavení

Pokud chcete nakonfigurovat další nastavení Azure Firewall pro virtuální rozbočovač, vyberte odkaz na **Azure firewall Manager**. Informace o zásadách brány firewall najdete v tématu [Azure firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![Další nastavení](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Chcete-li se vrátit na stránku **Přehled** centra, můžete přejít zpět kliknutím na cestu, jak je znázorněno na šipce na následujícím obrázku.

[![návrat k přehledu](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
