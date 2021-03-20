---
title: Konfigurace Azure Analysis Services pro zdroje dat virtuální sítě | Microsoft Docs
description: Naučte se, jak nakonfigurovat server Azure Analysis Services, aby používal bránu pro zdroje dat v Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 912dab16cd854ae940b7bbdfe88a8da7adf1c5e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84197199"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat ve virtuální síti Azure

Tento článek popisuje vlastnost serveru Azure Analysis Services **AlwaysUseGateway** , která se má použít, když se zdroje dat nacházejí v [Virtual Network Azure (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup serveru k datovým zdrojům virtuální sítě

Pokud jsou k vašim zdrojům dat přístup prostřednictvím virtuální sítě, váš Azure Analysis Services Server se musí připojit k těmto zdrojům dat, jako kdyby byly místní, ve vašem vlastním prostředí. Vlastnost serveru **AlwaysUseGateway** můžete nakonfigurovat tak, aby určovala Server pro přístup ke všem zdrojům dat prostřednictvím [místní brány](analysis-services-gateway.md). 

Zdroje dat spravované instance Azure SQL běží v rámci virtuální sítě Azure s privátní IP adresou. Pokud je u instance povolený veřejný koncový bod, brána se nevyžaduje. Pokud není veřejný koncový bod povolený, vyžaduje se místní brána dat a vlastnost AlwaysUseGateway musí být nastavená na hodnotu true.

> [!NOTE]
> Tato vlastnost je platná jenom v případě, že je nainstalovaná a nakonfigurovaná [místní brána dat](analysis-services-gateway.md) . Brána může být ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfigurace vlastnosti AlwaysUseGateway

1. V SSMS > **vlastnosti**> serveru  >  **Obecné** vyberte možnost **Zobrazit rozšířené (všechny) vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway** vyberte true ( **pravda**).

    ![Vždy používat vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Viz také
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Azure Virtual Network (virtuální síť)](../virtual-network/virtual-networks-overview.md)   

