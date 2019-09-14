---
title: Použití místní brány dat pro zdroje dat Azure Virtual Network | Microsoft Docs
description: Naučte se, jak nakonfigurovat server, aby používal bránu pro zdroje dat ve virtuální síti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 675d8ecd3d6a3310a9b102df37df18bed02df3de
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958809"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat v Azure Virtual Network (VNet)

Tento článek popisuje vlastnost serveru **AlwaysUseGateway** , která se má použít, když se zdroje dat nacházejí v [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup serveru k datovým zdrojům virtuální sítě

Pokud jsou k vašim zdrojům dat přístup prostřednictvím virtuální sítě, váš Azure Analysis Services Server se musí připojit k těmto zdrojům dat, jako kdyby byly místní, ve vašem vlastním prostředí. Vlastnost serveru **AlwaysUseGateway** můžete nakonfigurovat tak, aby určovala Server pro přístup ke všem zdrojům dat prostřednictvím [místní brány](analysis-services-gateway.md). 

Azure SQL Database zdroje dat spravované instance se spouštějí v rámci virtuální sítě Azure pomocí privátní IP adresy. Pokud je u instance povolený veřejný koncový bod, brána se nevyžaduje. Pokud není veřejný koncový bod povolený, vyžaduje se místní brána dat a vlastnost AlwaysUseGateway musí být nastavená na hodnotu true.

> [!NOTE]
> Tato vlastnost je platná jenom v případě, že je nainstalovaná a nakonfigurovaná [místní brána dat](analysis-services-gateway.md) . Brána může být ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfigurace vlastnosti AlwaysUseGateway

1. V SSMS > **vlastnosti** > > serveru**Obecné**vyberte možnost **Zobrazit rozšířené (všechny) vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway**vyberte true ( **pravda**).

    ![Vždy používat vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Viz také:
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

