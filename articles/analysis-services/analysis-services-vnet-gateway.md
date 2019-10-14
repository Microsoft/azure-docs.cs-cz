---
title: Použití místní brány dat pro zdroje dat Azure Virtual Network | Microsoft Docs
description: Naučte se, jak nakonfigurovat server, aby používal bránu pro zdroje dat ve virtuální síti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301216"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat v Azure Virtual Network (VNet)

Tento článek popisuje vlastnost serveru **AlwaysUseGateway** , která se má použít, když se zdroje dat nacházejí v [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup serveru k datovým zdrojům virtuální sítě

Pokud jsou k vašim zdrojům dat přístup prostřednictvím virtuální sítě, váš Azure Analysis Services Server se musí připojit k těmto zdrojům dat, jako kdyby byly místní, ve vašem vlastním prostředí. Vlastnost serveru **AlwaysUseGateway** můžete nakonfigurovat tak, aby určovala Server pro přístup ke všem zdrojům dat prostřednictvím [místní brány](analysis-services-gateway.md). 

Azure SQL Database zdroje dat spravované instance se spouštějí v rámci virtuální sítě Azure pomocí privátní IP adresy. Pokud je u instance povolený veřejný koncový bod, brána se nevyžaduje. Pokud není veřejný koncový bod povolený, vyžaduje se místní brána dat a vlastnost AlwaysUseGateway musí být nastavená na hodnotu true.

> [!NOTE]
> Tato vlastnost je platná jenom v případě, že je nainstalovaná a nakonfigurovaná [místní brána dat](analysis-services-gateway.md) . Brána může být ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfigurace vlastnosti AlwaysUseGateway

1. V SSMS > serveru > **vlastnosti** > **Obecné**vyberte možnost **Zobrazit rozšířené (všechny) vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway**vyberte true ( **pravda**).

    ![Vždy používat vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Viz také:
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Azure Virtual Network (virtuální síť)](../virtual-network/virtual-networks-overview.md)   

