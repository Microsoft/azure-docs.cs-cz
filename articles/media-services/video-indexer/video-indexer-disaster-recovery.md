---
title: Převzetí služeb při selhání video indexeru a zotavení po havárii
titleSuffix: Azure Media Services
description: Zjistěte, jak převzetí služeb při selhání sekundární video indexer účet, pokud dojde k selhání místní datové centrum nebo dojde k havárii.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499621"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Převzetí služeb při selhání video indexeru a zotavení po havárii

Azure Media Services Video Indexer neposkytuje okamžité převzetí služeb při selhání služby, pokud dojde k výpadku nebo selhání místního datového centra. Tento článek vysvětluje, jak nakonfigurovat prostředí pro převzetí služeb při selhání, aby byla zajištěna optimální dostupnost pro aplikace a minimální doba obnovení, pokud dojde k havárii.

Doporučujeme nakonfigurovat obnovení zotavení po havárii kontinuity podnikání (BCDR) napříč regionálními páry, abyste mohli využívat zásady izolace a dostupnosti Azure. Další informace najdete v tématu [Azure spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Požadavky

Předplatné Azure. Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Převzetí služeb při selhání na sekundární účet

Chcete-li implementovat BCDR, musíte mít dva účty Video Indexer pro zpracování redundance.

1. Vytvořte dva účty Video Indexer připojený k Azure (viz [Vytvoření účtu Video Indexer).](connect-to-azure.md) Vytvořte jeden účet pro primární oblast a druhý pro spárovanou oblast azure.
1. Pokud dojde k selhání ve vaší primární oblasti, přepněte na indexování pomocí sekundárního účtu.

> [!TIP]
> BcDR můžete automatizovat nastavením výstrah protokolu aktivit pro oznámení o stavu služby podle [vytvoření výstrah protokolu aktivit u oznámení o službách](../../service-health/alerts-activity-log-service-notifications.md).

Informace o používání více klientů naleznete v tématu [Správa více klientů](manage-multiple-tenants.md). Chcete-li implementovat BCDR, zvolte jednu z těchto dvou možností: [Účet Video Indexer na klienta](manage-multiple-tenants.md#video-indexer-account-per-tenant) nebo [předplatné Azure na tenanta](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Další kroky

[Správa účtu Video Indexer připojeného k Azure](manage-account-connected-to-azure.md).
