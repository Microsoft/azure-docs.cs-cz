---
title: Zpracování přerušení služby Azure, které ovlivňuje Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Seznamte se s tím, co dělat v případě výpadku služby Azure, který ovlivňuje Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: d4f869c8b4ae6e90cfe64a2bf3d13839d72727be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84015295"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Co dělat v případě přerušení služby Azure, které ovlivní Azure Cloud Services
V Microsoftu pracujeme na tom, abychom zajistili, že naše služby jsou vždycky k dispozici, až je budete potřebovat. Síly nad rámec našeho ovládacího prvku někdy ovlivňují způsob, jakým způsobují neplánované výpadky služeb.

Společnost Microsoft poskytuje smlouva SLA (SLA) pro své služby jako závazek pro dobu provozu a připojení. Smlouvu SLA pro jednotlivé služby Azure najdete na stránce [smlouvy o úrovni služeb Azure](https://azure.microsoft.com/support/legal/sla/).

Azure už obsahuje mnoho vestavěných funkcí platformy, které podporují vysoce dostupné aplikace. Další informace o těchto službách najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek se zabývá skutečným scénářem zotavení po havárii, když celá oblast dojde k výpadku, protože došlo k závažné přirozené havárii nebo rozšířenému přerušení služby. Jedná se o vzácná opakování, ale musíte připravit na možnost, že dojde k výpadku celé oblasti. Pokud v celé oblasti dojde k přerušení služby, místně redundantní kopie vašich dat nebudou dočasně k dispozici. Pokud jste povolili geografickou replikaci, budou se tři další kopie Azure Storage objektů BLOB a tabulek ukládat v jiné oblasti. V případě úplného výpadku v oblasti regionu nebo havárie, ve kterém se primární oblast nedá obnovit, Azure přemapuje všechny položky DNS do geografické replikované oblasti.

> [!NOTE]
> Mějte na paměti, že k tomuto procesu nemáte žádnou kontrolu a dojde k tomu jenom pro přerušení služeb v rámci datového centra. Z tohoto důvodu musíte také spoléhat na jiné strategie zálohování specifické pro aplikace, abyste dosáhli nejvyšší úrovně dostupnosti. Další informace najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace založené na Microsoft Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Pokud chcete mít možnost ovlivnit vlastní převzetí služeb při selhání, můžete zvážit použití [geograficky redundantního úložiště s přístupem pro čtení (RA-GRS)](../storage/common/storage-redundancy.md), které v jiné oblasti vytvoří kopii vašich dat jen pro čtení.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Možnost 1: použití nasazení zálohy prostřednictvím Azure Traffic Manager
Nejbezpečnější řešení pro zotavení po havárii zahrnuje údržbu více nasazení aplikace v různých oblastech a následné použití [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) k přímému směrování provozu mezi nimi. Azure Traffic Manager poskytuje několik [metod směrování](../traffic-manager/traffic-manager-routing-methods.md), takže si můžete vybrat, jestli chcete spravovat nasazení pomocí primárního nebo záložního modelu, nebo rozdělit přenos mezi nimi.

![Vyvážení Cloud Services Azure napříč oblastmi pomocí Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Pro nejrychlejší reakci na ztrátu oblasti je důležité nakonfigurovat [monitorování koncového bodu](../traffic-manager/traffic-manager-monitoring.md)Traffic Manager.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Možnost 2: nasazení aplikace do nové oblasti
Údržba několika aktivních nasazení, jak je popsáno v předchozí možnosti, jsou další průběžné náklady. Pokud je váš cíl doby obnovení (RTO) dostatečně flexibilní a máte původní kód nebo kompilovaný Cloud Services balíček, můžete vytvořit novou instanci aplikace v jiné oblasti a aktualizovat záznamy DNS tak, aby odkazovaly na nové nasazení.

Další informace o tom, jak vytvořit a nasadit aplikaci cloudové služby, najdete v tématu [jak vytvořit a nasadit cloudovou službu](cloud-services-how-to-create-deploy-portal.md).

V závislosti na zdrojích dat aplikací může být nutné ověřit postupy obnovení pro zdroj dat aplikace.

* U Azure Storage zdrojů dat si Projděte [Azure Storage redundance](../storage/common/storage-redundancy.md) a zkontrolujte, jaké možnosti jsou k dispozici na základě zvoleného modelu redundance pro vaši aplikaci.
* Informace o SQL Databasech zdrojích najdete v tématu [Přehled: cloudová Kontinuita podnikových prostředí a zotavení po havárii databáze pomocí SQL Database](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) ke kontrole dostupných možností na základě zvoleného modelu replikace pro vaši aplikaci.


## <a name="option-3-wait-for-recovery"></a>Možnost 3: čekání na obnovení
V takovém případě se nevyžaduje žádná akce s vaší částí, ale vaše služba nebude dostupná, dokud se oblast neobnoví. Aktuální stav služby můžete zobrazit na [řídicím panelu Azure Service Health](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak implementovat strategii zotavení po havárii a vysokou dostupnost, najdete v tématu [zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Podrobné technické porozumění funkcím cloudové platformy najdete v tématu [technické pokyny k odolnosti Azure](/azure/architecture/checklist/resiliency-per-service).