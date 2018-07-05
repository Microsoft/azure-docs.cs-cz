---
title: Použití na místní bránu dat pro zdroje dat Azure Virtual Network | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat server pro použití brány pro zdroje dat ve virtuální síti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1cf447f27509bc62cc71a9c64237ff7dc3d7b30
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446062"
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

