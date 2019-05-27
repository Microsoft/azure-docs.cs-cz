---
title: Co dělat v případě Azure služby přerušení, které ovlivní Azure Cloud Services | Dokumentace Microsoftu
description: Zjistěte, co můžete dělat v případě výpadku služby Azure, které ovlivní Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: 976bb43fd3e6d6fdb19c733affd4afa2e49e482c
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967684"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co dělat v případě Azure služby přerušení, které ovlivní Azure Cloud Services
V Microsoftu usilovně pracujeme na Ujistěte se, že naše služby jsou vždy k dispozici, a když je potřebujete. Vynutí nad rámec naší ovládací prvek někdy ovlivnit nám takovým způsobem, který způsobit přerušení neplánované služeb.

Společnost Microsoft poskytuje Smlouva o úrovni služeb (SLA) pro své služby jako závazek dostupnosti a konektivity. Smlouva SLA pro jednotlivé služby Azure lze nalézt v [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

Azure už má mnoho funkcí integrované platformy, které podporují aplikace s vysokou dostupností. Další informace o těchto službách najdete v článku [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek popisuje na scénář zotavení po havárii true po celou oblast dojde k výpadku z důvodu větší přírodní havárie nebo přerušení rozšířených služby. Toto jsou vzácné opakování, ale musíte připravit pro možnost, že dojde k výpadku celé oblasti. Pokud celou oblast dojde k přerušení služeb, místně redundantní kopie vašich dat by být dočasně není k dispozici. Pokud jste povolili geografickou replikaci, tři další kopie objekty Azure Storage BLOB a tabulek jsou uložené v jiné oblasti. V případě kompletní oblastnímu výpadku nebo havárii, ve kterém primární oblasti se nedá vrátit zpátky Azure změní všechny položky DNS do oblasti geograficky replikovaný.

> [!NOTE]
> Mějte na paměti, že nemáte žádné kontrolu nad tento proces a se vztahuje pouze k přerušení služeb na úrovni celého datového centra. Z tohoto důvodu musíte také spoléhat na další specifické pro aplikaci Zálohování strategie pro dosažení nejvyšší úroveň dostupnosti. Další informace najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace založené na Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Pokud chcete mít možnost mít vliv na vlastní převzetí služeb při selhání, můžete chtít zvážit využití [geograficky redundantní úložiště jen pro čtení (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), který vytvoří kopii dat jen pro čtení v jiné oblasti.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Option 1: Použít záložní nasazení prostřednictvím Azure Traffic Manageru
Většina robustní řešení pro zotavení po havárii zahrnuje udržování více nasazení vaší aplikace v různých oblastech a pak pomocí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) ke směrování provozu mezi nimi. Azure Traffic Manager poskytuje více [metod směrování](../traffic-manager/traffic-manager-routing-methods.md), takže si můžete zvolit, jestli se ke správě vašich nasazení s využitím modelu primární/zálohování nebo pro rozdělení přenosy mezi nimi.

![Vyrovnávání cloudových služeb Azure napříč oblastmi pomocí Azure Traffic Manageru](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Nejrychlejší odpověď ztrátou oblasti, je důležité, konfiguraci Traffic Manageru [monitorování koncových bodů](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Option 2: Nasazení aplikace do nové oblasti
Správa více aktivních nasazení, jak je popsáno v předchozí způsob s sebou nese náklady další průběžné náklady. Pokud je dostatečně flexibilní, plánovaná doba obnovení (RTO) a vy musíte původní kód nebo kompilované balíček cloudové služby, můžete vytvořit novou instanci vaší aplikace v jiné oblasti a aktualizovat svoje záznamy DNS tak, aby odkazoval na nové nasazení.

Další podrobnosti o tom, jak vytvářet a nasazovat aplikace cloudové služby, najdete v části [jak vytvořit a nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).

V závislosti na zdrojích dat aplikace budete muset zkontrolovat postupy obnovení pro zdroj dat aplikace.

* Zdroje dat služby Azure Storage, najdete v části [replikace Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) podívejte se na možnosti, které jsou k dispozici na základě modelu vyberte možnost replikace pro vaši aplikaci.
* Zdroje SQL Database, přečtěte si [přehled: Cloudové obchodní kontinuity podnikových procesů a databáze zotavení po havárii pomocí SQL Database](../sql-database/sql-database-business-continuity.md) můžete zkontrolovat na možnosti, které jsou k dispozici na základě zvolené replikace modelu pro vaši aplikaci.


## <a name="option-3-wait-for-recovery"></a>Možnost 3: Vyčkat, než obnovení
V takovém případě není nutná žádná akce z vaší strany, ale vaše služba nebude k dispozici, dokud oblasti se obnoví. Zobrazí aktuální stav služby na [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Další postup
Další informace o tom, jak implementovat strategie vysoké dostupnosti a zotavení po havárii najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

K vývoji podrobné technické informace o možnosti cloudové platformy, najdete v článku [technické pokyny k odolnosti Azure](../resiliency/resiliency-technical-guidance.md).