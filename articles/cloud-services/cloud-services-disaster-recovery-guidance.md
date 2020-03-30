---
title: Zpracování přerušení služeb Azure, které má vliv na cloudové služby Azure
titleSuffix: Azure Cloud Services
description: Zjistěte, co dělat v případě přerušení služby Azure, které má vliv na cloudové služby Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157502"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co dělat v případě přerušení služby Azure, které ovlivní Azure Cloud Services
Ve společnosti Microsoft usilovně pracujeme na tom, abychom zajistili, že naše služby budou vždy dostupné, když je budete potřebovat. Síly mimo naši kontrolu nás někdy ovlivňují způsoby, které způsobují neplánované přerušení služeb.

Společnost Microsoft poskytuje smlouvu o úrovni služeb (SLA) pro své služby jako závazek pro provozunostia a připojení. Smlouvu SLA pro jednotlivé služby Azure najdete na [webu Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure už má mnoho integrovaných funkcí platformy, které podporují vysoce dostupné aplikace. Další informace o těchto službách načtete [zotavení po havárii a vysokou dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek popisuje skutečný scénář zotavení po havárii, kdy celý region dojde k výpadku v důsledku závažné přírodní katastrofy nebo rozsáhlé přerušení služby. Jedná se o vzácné události, ale musíte se připravit na možnost, že dojde k výpadku celé oblasti. Pokud dojde k přerušení služby v celé oblasti, místně redundantní kopie dat by dočasně nebyly k dispozici. Pokud jste povolili geografickou replikaci, tři další kopie objektů blob a tabulky azure storage jsou uloženy v jiné oblasti. V případě úplného regionálního výpadku nebo havárie, ve které není obnovitelná primární oblast, Azure přemapuje všechny položky DNS do geograficky replikované oblasti.

> [!NOTE]
> Uvědomte si, že nemáte žádnou kontrolu nad tímto procesem a dojde pouze pro přerušení služby celého datového centra. Z tohoto důvodu musíte také spoléhat na jiné strategie zálohování specifické pro aplikaci k dosažení nejvyšší úrovně dostupnosti. Další informace najdete [v tématu Zotavení po havárii a vysoká dostupnost pro aplikace postavené na Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Pokud chcete mít možnost ovlivnit vlastní převzetí služeb při selhání, můžete zvážit použití [geograficky redundantního úložiště pro čtení (RA-GRS),](../storage/common/storage-redundancy.md)které vytvoří kopii dat jen pro čtení v jiné oblasti.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Možnost 1: Použití nasazení zálohy prostřednictvím Azure Traffic Manageru
Nejrobustnější řešení zotavení po havárii zahrnuje udržování více nasazení vaší aplikace v různých oblastech a pak pomocí [Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md) k přímému provozu mezi nimi. Azure Traffic Manager poskytuje více [metod směrování](../traffic-manager/traffic-manager-routing-methods.md), takže si můžete vybrat, jestli chcete spravovat nasazení pomocí primárního a záložního modelu nebo rozdělit provoz mezi nimi.

![Vyvažování cloudových služeb Azure napříč oblastmi pomocí Azure Traffic Manageru](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Pro nejrychlejší odpověď na ztrátu oblasti je důležité nakonfigurovat sledování [koncového bodu](../traffic-manager/traffic-manager-monitoring.md)Traffic Manageru .

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Možnost 2: Nasazení aplikace do nové oblasti
Udržování více aktivních nasazení, jak je popsáno v předchozí možnosti, způsobuje další průběžné náklady. Pokud je cíl doby obnovení (RTO) dostatečně flexibilní a máte původní kód nebo zkompilovaný balíček cloudových služeb, můžete vytvořit novou instanci aplikace v jiné oblasti a aktualizovat záznamy DNS tak, aby ukazovaly na nové nasazení.

Další podrobnosti o tom, jak vytvořit a nasadit aplikaci cloudové služby, najdete v [tématu Jak vytvořit a nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).

V závislosti na zdrojích dat aplikace může být nutné zkontrolovat postupy obnovení pro zdroj dat aplikace.

* Zdroje dat Azure Storage najdete v tématu [redundance Azure Storage](../storage/common/storage-redundancy.md) a zkontrolujte možnosti, které jsou k dispozici na základě zvoleného modelu redundance pro vaši aplikaci.
* Pro zdroje databáze SQL si přečtěte [přehled: Kontinuita podnikání v cloudu a obnovení databáze po havárii s databází SQL](../sql-database/sql-database-business-continuity.md) a zkontrolujte možnosti, které jsou k dispozici na základě zvoleného modelu replikace pro vaši aplikaci.


## <a name="option-3-wait-for-recovery"></a>Možnost 3: Počkejte na obnovení
V takovém případě není nutná žádná akce z vaší strany, ale vaše služba nebude k dispozici, dokud nebude oblast obnovena. Aktuální stav služby najdete na [řídicím panelu stavu služby Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Další kroky
Další informace o implementaci strategie zotavení po havárii a strategie vysoké dostupnosti najdete v [tématu Zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Podrobné technické znalosti o možnostech cloudové platformy najdete v [technických příručkách odolnosti azure](/azure/architecture/checklist/resiliency-per-service).