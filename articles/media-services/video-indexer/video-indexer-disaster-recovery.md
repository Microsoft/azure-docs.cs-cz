---
title: Video Indexer převzetí služeb při selhání a zotavení po havárii
titleSuffix: Azure Media Services
description: Naučte se převzetí služeb při selhání u sekundárního Video Indexer účtu, pokud dojde k selhání nebo selhání regionálního datacentra.
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
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499621"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Video Indexer převzetí služeb při selhání a zotavení po havárii

Azure Media Services Video Indexer neposkytuje okamžité převzetí služeb při selhání, pokud dojde k výpadku nebo selhání regionálního datacentra. Tento článek vysvětluje, jak nakonfigurovat prostředí pro převzetí služeb při selhání, aby se zajistila optimální dostupnost aplikací a minimalizoval čas obnovení, pokud dojde k havárii.

Doporučujeme, abyste nakonfigurovali zotavení po havárii pro provozní kontinuitu (BCDR) napříč místními páry a využili jsme zásady izolace a dostupnosti Azure. Další informace najdete v tématu [spárované oblasti Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Požadavky

Předplatné Azure. Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Převzetí služeb při selhání sekundárním účtem

K implementaci BCDR musíte mít dva účty Video Indexer, abyste mohli řešit redundanci.

1. Vytvořte dva účty Video Indexer připojené k Azure (viz [Vytvoření účtu video indexer](connect-to-azure.md)). Vytvořte jeden účet pro vaši primární oblast a druhý do spárované oblasti Azure.
1. Pokud dojde k selhání v primární oblasti, přepněte se na indexování pomocí sekundárního účtu.

> [!TIP]
> BCDR můžete automatizovat tím, že nastavíte výstrahy protokolu aktivit pro oznámení o stavu služby podle [Upozornění na vytvoření protokolu aktivit v oznámeních služby](../../service-health/alerts-activity-log-service-notifications.md).

Informace o používání více tenantů najdete v tématu [Správa více tenantů](manage-multiple-tenants.md). Pokud chcete implementovat BCDR, vyberte jednu z těchto dvou možností: [video indexer účet na tenanta](manage-multiple-tenants.md#video-indexer-account-per-tenant) nebo [předplatné Azure na tenanta](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Další kroky

[Spravujte účet video indexer připojený k Azure](manage-account-connected-to-azure.md).
