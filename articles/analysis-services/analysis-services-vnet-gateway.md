---
title: Konfigurace služby Azure Analysis Services pro zdroje dat virtuální sítě | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat server Služby Azure Analysis Services tak, aby používal bránu pro zdroje dat ve virtuální síti Azure (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572263"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat ve virtuální síti Azure (Virtuální síť)

Tento článek popisuje vlastnost serveru **AlwaysUseGateway** služby Azure Analysis Services pro použití, když jsou zdroje dat ve [virtuální síti Azure (VNet).](../virtual-network/virtual-networks-overview.md)

## <a name="server-access-to-vnet-data-sources"></a>Přístup serveru ke zdrojům dat virtuální sítě

Pokud jsou vaše zdroje dat přístupné prostřednictvím virtuální sítě, musí se server Služby Azure Analysis Services k těmto zdrojům dat připojit, jako by byly místní, ve vašem vlastním prostředí. Vlastnost server **AlwaysUseGateway** můžete nakonfigurovat tak, aby určila server pro přístup ke všem zdrojům dat prostřednictvím [místní brány](analysis-services-gateway.md). 

Zdroje dat spravované instance Azure SQL Database se spouštějí v rámci virtuální sítě Azure s privátní IP adresou. Pokud je veřejný koncový bod povolen v instanci, brána není vyžadována. Pokud veřejný koncový bod není povolena, je vyžadována místní brána dat a vlastnost AlwaysUseGateway musí být nastavena na hodnotu true.

> [!NOTE]
> Tato vlastnost je účinná pouze v případě, že je nainstalována a [nakonfigurována místní brána dat.](analysis-services-gateway.md) Brána může být ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfigurovat vlastnost AlwaysUseGateway

1. V ssms > server > **vlastnosti** > **obecné**, vyberte Zobrazit **rozšířené (všechny) vlastnosti**.
2. V poli **ASPaaS\AlwaysUseGateway**vyberte **hodnotu true**.

    ![Vždy používat vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Viz také
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Virtuální síť Azure (Virtuální síť)](../virtual-network/virtual-networks-overview.md)   

