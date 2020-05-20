---
title: Konfigurace Azure Analysis Services pro zdroje dat virtuální sítě | Microsoft Docs
description: Naučte se, jak nakonfigurovat server Azure Analysis Services, aby používal bránu pro zdroje dat v Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7622b6b5855850a0150f43863fa117828e23d4bd
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698867"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat v Azure Virtual Network (VNet)

Tento článek popisuje vlastnost serveru Azure Analysis Services **AlwaysUseGateway** , která se má použít, když se zdroje dat nacházejí v [Virtual Network Azure (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup serveru k datovým zdrojům virtuální sítě

Pokud jsou k vašim zdrojům dat přístup prostřednictvím virtuální sítě, váš Azure Analysis Services Server se musí připojit k těmto zdrojům dat, jako kdyby byly místní, ve vašem vlastním prostředí. Vlastnost serveru **AlwaysUseGateway** můžete nakonfigurovat tak, aby určovala Server pro přístup ke všem zdrojům dat prostřednictvím [místní brány](analysis-services-gateway.md). 

Azure SQL Database zdroje dat spravované instance se spouštějí v rámci virtuální sítě Azure pomocí privátní IP adresy. Pokud je u instance povolený veřejný koncový bod, brána se nevyžaduje. Pokud není veřejný koncový bod povolený, vyžaduje se místní brána dat a vlastnost AlwaysUseGateway musí být nastavená na hodnotu true.

> [!NOTE]
> Tato vlastnost je platná jenom v případě, že je nainstalovaná a nakonfigurovaná [místní brána dat](analysis-services-gateway.md) . Brána může být ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfigurace vlastnosti AlwaysUseGateway

1. V SSMS > **vlastnosti**> serveru  >  **Obecné**vyberte možnost **Zobrazit rozšířené (všechny) vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway**vyberte true ( **pravda**).

    ![Vždy používat vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Viz také
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Azure Virtual Network (virtuální síť)](../virtual-network/virtual-networks-overview.md)   

