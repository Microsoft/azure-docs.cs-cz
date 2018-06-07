---
title: Brána dat použijte místní zdroje dat v Azure Virtual Network | Microsoft Docs
description: Postup konfigurace serveru pro použití brány zdroje dat na virtuální síť.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7cb1fb7bcd3a50532d5ff994afac56e226fb2018
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596902"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použít bránu pro zdroje dat na virtuální síti Azure (VNet)

Tento článek popisuje **AlwaysUseGateway** vlastnost serveru k použití pro zdroje dat jsou na [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup k serveru ke zdrojům dat virtuální sítě

Pokud zdroje dat jsou přístupné prostřednictvím sítě VNet, serveru Azure Analysis Services musí připojit těchto zdrojů dat, jako v případě, že jsou na místních počítačích ve svém vlastním prostředí. Můžete nakonfigurovat **AlwaysUseGateway** server vlastnosti k určení serveru pro přístup k datům všechny zdroje dat pomocí [místní brána](analysis-services-gateway.md). 

> [!NOTE]
> Tato vlastnost je platná pouze tehdy, když [místní brána dat](analysis-services-gateway.md) je nainstalovaný a nakonfigurovaný. Brána může být ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Nastavení vlastnosti AlwaysUseGateway

1. V aplikaci SSMS > serveru > **vlastnosti** > **Obecné**, vyberte **zobrazit Upřesnit (All) vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway**, vyberte **true**.

    ![Vždy používat vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Další informace najdete v tématech
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Nainstalujte a nakonfigurujte bránu místní data](analysis-services-gateway-install.md)   
[Virtuální síť Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

