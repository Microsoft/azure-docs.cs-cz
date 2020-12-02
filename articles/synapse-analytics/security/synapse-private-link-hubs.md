---
title: Připojení k synapse studiu pomocí privátních odkazů
description: Tento článek vás seznámí s tím, jak se připojit k Azure synapse studiu pomocí privátních odkazů.
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 2613a4fd931ad49a4f40a4221ea20e8c25f185fe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501473"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Připojení k Azure synapse studiu pomocí Center privátních odkazů Azure 

V tomto článku se dozvíte, jak se k zabezpečenému připojení k synapse studiu používají privátní odkazy na centrálních linkách služby Azure synapse Analytics. 

## <a name="azure-private-link-hubs"></a>Centra privátních odkazů Azure 
Pomocí privátních odkazů se můžete bezpečně připojit ke službě Azure synapse Studio z vaší virtuální sítě Azure. Centra pro privátní propojení Azure synapse Analytics jsou prostředky Azure, které fungují jako konektory mezi vaší zabezpečenou sítí a webovým prostředím synapse studia. 

Existují dva kroky pro připojení k synapse studiu pomocí privátních odkazů. Nejprve musíte vytvořit prostředek rozbočovače privátního propojení. V druhém případě je nutné vytvořit privátní koncový bod z vaší virtuální sítě Azure do tohoto rozbočovače privátního propojení. Pak můžete pomocí privátních koncových bodů bezpečně komunikovat s synapse Studio. Privátní koncové body je potřeba integrovat s vaším řešením DNS, ať už vaše místní řešení, nebo Privátní DNS Azure. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Centra privátních odkazů Azure a Azure synapse Studio
K privátnímu připojení ke všem pracovním prostorům Azure synapse Analytics pomocí Azure synapse studia můžete použít jeden prostředek centra privátních propojení Azure synapse. Pracovní prostory nemusí být ve stejné oblasti jako synapse centrum privátních odkazů Azure. U připojení k pracovním prostorům synapse v různých předplatných nebo klientech Azure AD se dá použít taky prostředek centra privátních odkazů Azure synapse.

Své soukromé centrum odkazů můžete vytvořit tak, že v Azure Portal vyhledáte centra privátních *odkazů synapse* a v části služby vyberete **Azure synapse Analytics (centra privátních odkazů)** . Podle pokynů v průvodci se [připojte k prostředkům pracovního prostoru z omezené sítě](./how-to-connect-to-workspace-from-restricted-network.md) , kde najdete podrobnosti.

>[!NOTE]
>Rozbočovače privátních odkazů jsou určené k bezpečnému načítání statického obsahu synapse studia přes privátní odkazy. Je nutné vytvořit **samostatné privátní koncové body** k prostředkům, ke kterým se chcete připojit v rámci pracovního prostoru, jako jsou například zřízené/vyhrazené fondy SQL nebo fondy Spark. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Centra privátních odkazů Azure a Azure Virtual Network
Virtuální síť Azure se musí připojit k prostředku rozbočovače privátního propojení synapse, aby bylo možné zabezpečit koncové připojení k synapse studiu. V takovém případě je nutné vytvořit privátní koncový bod z vaší virtuální sítě k vytvořenému rozbočovači privátního propojení. Můžete použít Azure Portal pro vaše privátní centrum odkazů a přejít na oddíl privátního koncového bodu. Vyberte + privátní koncový bod a vytvořte nový privátní koncový bod, který se připojí k vašemu privátnímu centru odkazů.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Vytvoření privátního koncového bodu na centrum privátních odkazů":::

Ujistěte se, že na kartě prostředek zvolíte typ prostředku Microsoft. synapse/privateLinkHubs. :::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Vytvoření privátního koncového bodu do centra privátních odkazů":::

Na kartě Konfigurace vyberte privatelink.azuresynapse.net pro zóny Privátní DNS při integraci s vaší virtuální sítí a privátní zónou DNS.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Vytvoření privátního koncového bodu na centrum privátních odkazů":::

## <a name="next-steps"></a>Další kroky

[Připojení k prostředkům pracovního prostoru z omezené sítě](./how-to-connect-to-workspace-from-restricted-network.md)

Další informace o [spravovaném pracovním prostoru Virtual Network](./synapse-workspace-managed-vnet.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)

[Připojení k pracovnímu prostoru synapse pomocí privátních koncových bodů](./how-to-connect-to-workspace-with-private-links.md)

