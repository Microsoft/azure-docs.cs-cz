---
title: Převzetí služeb při selhání a zotavení po havárii Video Indexeru
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
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87065412"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Převzetí služeb při selhání a zotavení po havárii Video Indexeru

Video Indexer služby Azure Media Services nenabízí okamžité převzetí služeb při selhání služby v případě výpadku nebo selhání regionálního datacentra. Tento článek vysvětluje, jak nakonfigurovat prostředí pro převzetí služeb při selhání, aby se zajistila optimální dostupnost aplikací a minimalizoval čas obnovení, pokud dojde k havárii.

Doporučujeme nakonfigurovat provozní kontinuitu a zotavení po havárii (BCDR) napříč oblastními páry, abyste mohli využívat výhody zásad izolace a dostupnosti Azure. Další informace najdete v tématu [spárované oblasti Azure](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Předpoklady

Předplatné Azure. Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Převzetí služeb při selhání sekundárním účtem

K implementaci BCDR musíte mít dva účty Video Indexer, abyste mohli řešit redundanci.

1. Vytvořte dva účty Video Indexer připojené k Azure (viz [Vytvoření účtu video indexer](connect-to-azure.md)). Vytvořte jeden účet pro vaši primární oblast a druhý do spárované oblasti Azure.
1. Pokud dojde k selhání v primární oblasti, přepněte se na indexování pomocí sekundárního účtu.

> [!TIP]
> BCDR můžete automatizovat tím, že nastavíte výstrahy protokolu aktivit pro oznámení o stavu služby podle [Upozornění na vytvoření protokolu aktivit v oznámeních služby](../../service-health/alerts-activity-log-service-notifications-portal.md).

Informace o používání více tenantů najdete v tématu [Správa více tenantů](manage-multiple-tenants.md). Pokud chcete implementovat BCDR, vyberte jednu z těchto dvou možností: [video indexer účet na tenanta](manage-multiple-tenants.md#video-indexer-account-per-tenant) nebo [předplatné Azure na tenanta](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Další kroky

[Spravujte účet video indexer připojený k Azure](manage-account-connected-to-azure.md).
