---
title: Co dělat v případě Azure služby přerušení, která má dopad na Azure Cloud Services | Microsoft Docs
description: Zjistěte, co dělat v případě přerušení služby Azure, který má dopad na Azure Cloud Services.
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
ms.author: mmccrory
ms.openlocfilehash: 7028417c95aa6969793c00d0bb270c96e56164fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co dělat v případě Azure služby přerušení, která má dopad na Azure Cloud Services
Ve společnosti Microsoft můžeme fungovat pevného a ujistěte se, že našich služeb jsou vždy k dispozici a když je potřebujete. Vynutí nad rámec naše řízení někdy vliv nám způsoby, které způsobit přerušení poskytování služeb neplánované.

Společnost Microsoft poskytuje smlouvy úroveň služeb (SLA) pro jeho služby jako závazek provozu a připojení. Smlouva SLA pro jednotlivé služby Azure najdete na [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

Azure již obsahuje mnoho funkcí integrovanou platformu, které podporují aplikace s vysokou dostupností. Další informace o těchto službách, přečtěte si [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek se zabývá true zotavení po havárii, když dojde výpadku způsobeného hlavní přírodní katastrofě nebo přerušení služeb rozšířeným celou oblast. Toto jsou výjimečných výskytů, ale je nutné připravit možnost, že je k výpadku celou oblast. Když celou oblast dojde přerušení služby, místně redundantní kopie dat by být dočasně k dispozici. Pokud jste povolili geografická replikace, tři další kopie objektů BLOB služby Azure Storage a tabulek jsou uložené v jiné oblasti. V případě výpadku dokončení místní nebo havárii, ve kterém není použitelná pro obnovení primární oblasti znovu mapuje Azure, všechny položky DNS pro danou oblast geograficky replikované.

> [!NOTE]
> Uvědomte si, že nemáte žádné kontrolu nad tento proces a se vztahuje pouze na přerušení datacenter úrovni služeb. Z tohoto důvodu musíte také spoléhat na další specifické pro aplikaci Zálohování strategie k dosažení nejvyšší úroveň dostupnosti. Další informace najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace založené na Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Pokud chcete být schopni ovlivnit vlastní převzetí služeb při selhání, můžete chtít zvažte použití [geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), která vytvoří kopii dat jen pro čtení v jiné oblasti.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Možnost 1: Použití zálohování nasazení prostřednictvím Azure Traffic Manager
Většina robustní řešení zotavení po havárii zahrnuje zachování více nasazení vaší aplikace v různých oblastech a potom pomocí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) směrovat provoz mezi nimi. Azure Traffic Manager poskytuje více [metody směrování](../traffic-manager/traffic-manager-routing-methods.md), takže si můžete vybrat, jestli se mají spravovat nasazení pomocí modelu primární nebo zálohování nebo rozdělit provoz mezi nimi.

![Vyrovnávání Azure Cloud Services v oblastech Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Pro nejrychlejší odpověď ztrátu oblasti, je důležité, nakonfigurovat Traffic Manager [monitorování koncového bodu](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Možnost 2: Nasazení aplikace do nové oblasti
Udržování víc aktivní nasazení, jak je popsáno v předchozí možnost způsobuje další průběžných nákladů. Pokud je dostatečně flexibilní cíli času obnovení (RTO) a budete mít k původní kód nebo kompilované balíčku cloudové služby, můžete vytvořit novou instanci aplikace v jiné oblasti a aktualizovat svoje záznamy DNS tak, aby odkazoval na nové nasazení.

Další podrobnosti o tom, jak vytvořit a nasadit aplikace cloudové služby, najdete [postup vytvoření a nasazení cloudové služby](cloud-services-how-to-create-deploy-portal.md).

V závislosti na vašich zdrojů dat aplikace musíte ke kontrole postupy obnovení pro zdroj dat aplikace.

* U zdrojů dat úložiště Azure, najdete v části [replikace Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) kontrolu na možnosti, které jsou k dispozici na základě modelu pokusit replikace pro vaši aplikaci.
* Databáze SQL zdroje, najdete v tématu [přehled: obchodní kontinuitu a databáze zotavení po havárii s databází SQL v cloudu](../sql-database/sql-database-business-continuity.md) kontrolu na možnosti, které jsou k dispozici na základě modelu zvolené replikace pro vaši aplikaci.


## <a name="option-3-wait-for-recovery"></a>Možnost 3: Počkejte obnovení
V takovém případě není třeba žádné akce z vaší strany, ale služby nebude k dispozici, dokud je obnoven oblasti. Aktuální stav služby můžete zobrazit na [řídicím panelu stavu služeb Azure](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Další postup
Další informace o tom, jak implementovat strategie vysoké dostupnosti a zotavení po havárii, najdete v části [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

K vývoji podrobné technické vysvětlení funkcí Cloudová platforma, najdete v části [technické pokyny Azure odolnosti](../resiliency/resiliency-technical-guidance.md).