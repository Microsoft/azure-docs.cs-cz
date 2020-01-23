---
title: Video Indexer provozní kontinuita a zotavení po havárii – Azure
description: Převzetí služeb při selhání pro sekundární Video Indexer účet, pokud dojde k výpadku nebo selhání regionálního datacentra.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 2f54c340226a9ea78643df8e0a984c8ed8475c94
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513571"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Zpracování Video Indexer provozní kontinuita a zotavení po havárii

Azure Media Services Video Indexer neposkytuje rychlé převzetí služeb při selhání služby, pokud dojde k výpadku nebo selhání regionálního datacentra. Tento článek vysvětluje, jak nakonfigurovat prostředí pro převzetí služeb při selhání, aby se zajistila optimální dostupnost aplikací a minimalizoval čas obnovení, pokud dojde k havárii.

Doporučujeme, abyste nakonfigurovali zotavení po havárii pro provozní kontinuitu (BCDR) napříč místními páry a využili jsme zásady izolace a dostupnosti Azure. Další informace najdete v tématu [spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Požadavky 

Předplatné Azure. Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Převzetí služeb při selhání sekundárním účtem

Aby bylo možné implementovat BCDR, musíte mít dva účty Video Indexer, abyste mohli řešit redundanci.

1. Vytvořte dva účty Video Indexer připojené k Azure (viz [Vytvoření účtů](connect-to-azure.md)). Jednu pro vaši primární oblast a druhou pro spárované oblasti Azure. 
1. Pokud v primární oblasti dojde k selhání, přepněte se na indexování pomocí sekundárního účtu.

> [!TIP]
> BCDR můžete automatizovat tím, že nastavíte výstrahy protokolu aktivit pro oznámení o stavu služby podle [Upozornění na vytvoření protokolu aktivit v oznámeních služby](../../service-health/alerts-activity-log-service-notifications.md).

Informace o používání více tenantů najdete v tématu [Správa více tenantů](manage-multiple-tenants.md). Pokud chcete implementovat BCDR, vyberte jednu z těchto dvou možností: [video indexer účet na tenanta](manage-multiple-tenants.md#video-indexer-account-per-tenant) nebo [předplatné Azure na tenanta](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Další kroky

[Spravujte účet video indexer připojený k Azure](manage-account-connected-to-azure.md).
