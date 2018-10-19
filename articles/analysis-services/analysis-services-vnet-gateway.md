---
title: Použití na místní bránu dat pro zdroje dat Azure Virtual Network | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat server pro použití brány pro zdroje dat ve virtuální síti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 62f6413854ecdfc257655fd864167d8e24d35e06
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427299"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat ve službě Azure Virtual Network (VNet)

Tento článek popisuje **AlwaysUseGateway** vlastnost serveru k použití pro zdroje dat jsou na [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup k virtuální síti zdroje dat serveru

Pokud jsou vaše zdroje dat přístupné prostřednictvím virtuální sítě, musí váš server Azure Analysis Services připojit k příslušným zdrojům dat, jako by šlo místně, ve vašem prostředí. Můžete nakonfigurovat **AlwaysUseGateway** server vlastnosti a určit server pro přístup k datům všechny zdroje dat pomocí [místní brána](analysis-services-gateway.md). 

> [!NOTE]
> Tato vlastnost je účinné pouze tehdy, když [On-premises data gateway](analysis-services-gateway.md) nainstalovaná a nakonfigurovaná. Brána lze ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfiguraci AlwaysUseGateway vlastnosti

1. V aplikaci SSMS > server > **vlastnosti** > **Obecné**vyberte **(vše) zobrazit rozšířené vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway**vyberte **true**.

    ![Vždy používejte vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Další informace najdete v tématech
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

