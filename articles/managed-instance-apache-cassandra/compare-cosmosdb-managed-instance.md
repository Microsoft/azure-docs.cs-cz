---
title: Rozdíly mezi spravovanou instancí Azure pro Apache Cassandra a Azure Cosmos DB rozhraní API Cassandra
description: Přečtěte si o rozdílech mezi spravovanou instancí Azure pro Apache Cassandra a rozhraní API Cassandra v Azure Cosmos DB. Naučíte se také výhodami jednotlivých služeb a jejich výběru.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748526"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Rozdíly mezi spravovanou instancí Azure pro Apache Cassandra (Preview) a Azure Cosmos DB rozhraní API Cassandra 

V tomto článku se dozvíte o rozdílech mezi spravovanou instancí Azure pro Apache Cassandra a rozhraní API Cassandra v Azure Cosmos DB. V tomto článku najdete doporučení, jak volit mezi oběma službami nebo kdy hostovat vlastní prostředí Apache Cassandra.

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Klíčové rozdíly

Spravovaná instance Azure pro Apache Cassandra poskytuje automatizované nasazení, škálování a operace, které udržují stav uzlů pro open source instance Apache Cassandra v Azure. Nabízí taky možnost škálovat kapacitu stávajících místních nebo cloudových clusterů Apache Cassandra. Škáluje se tím, že se do stávajícího okruhu clusteru přidají spravovaná datacentra Cassandra.

[Rozhraní API Cassandra](../cosmos-db/cassandra-introduction.md) v Azure Cosmos DB je vrstva kompatibility přes globálně distribuovanou databázovou službu Microsoftu, která je v cloudu [Azure Cosmos DB](../cosmos-db/index.yml). Kombinace těchto služeb v Azure nabízí Continuum možností pro uživatele Apache Cassandra ve složitých hybridních cloudových prostředích.

## <a name="how-to-choose"></a>Jak vybrat?

V následující tabulce jsou uvedeny běžné scénáře, požadavky na úlohy a nahodilé případy, kdy se každý z těchto nasazení blíží:

| |Místní hostování Apache Cassandra místně nebo v Azure | Spravovaná instance Azure pro Apache Cassandra | API Cassandra v Azure Cosmos DB |
|---------|---------|---------|---------|
|**Typ nasazení**| Máte vysoce přizpůsobené nasazení Apache Cassandra s vlastními opravami nebo snitches. | Máte standardní Open Source nasazení Apache Cassandra bez jakéhokoli vlastního kódu. | Máte obsah s platformou, která není Apache Cassandra, ale je kompatibilní se všemi Open Source ovladači klienta na úrovni [přenosového protokolu](../cosmos-db/cassandra-support.md) . |
| **Provozní režie**| Máte stávající odborníky na Cassandra, kteří můžou nasazovat, konfigurovat a udržovat clustery.  | Chcete snížit provozní režii pro svůj stav uzlu Apache Cassandra, ale zachovat kontrolu nad konfiguracemi na úrovni platformy, jako je replikace a konzistence. | Pomocí plně spravované databáze typu platforma jako služba v cloudu chcete eliminovat provozní režii. |
| **Požadavky na operační systém**| Máte požadavek na údržbu vlastních nebo zlatých imagí operačního systému virtuálních počítačů. | Můžete použít image Vanilla, ale chcete mít kontrolu nad SKU, pamětí, disky a IOPS. | Chcete, aby bylo zřizování kapacity zjednodušené a vyjádřené jako jediná normalizovaná metrika s relací 1:1 a propustností, jako je například [jednotka žádosti](../cosmos-db/request-units.md) v Azure Cosmos DB. |
| **Cenový model**| Chcete používat software pro správu, jako jsou nástroje DataStax, a máte spokojeně s náklady na licencování. | Upřednostňujete čisté Open Source licencování a cenu založenou na instancích virtuálních počítačů. | Chcete využívat ceny cloudu Native, mezi které patří [Automatické škálování](../cosmos-db/manage-scale-cassandra.md#use-autoscale) a nabídky bez [serveru](../cosmos-db/serverless.md) . |
| **Analýzy**| Chcete mít plnou kontrolu nad zřizováním analytických kanálů bez ohledu na režii při jejich sestavování a údržbě. | Chcete používat cloudové analytické služby, jako je Azure Databricks. | Pro Cosmos DB máte k dispozici hybridní transakční analýzy prakticky v reálném čase, které jsou integrované v rámci platformy, pomocí [služby Azure synapse Link](../cosmos-db/synapse-link.md). |
| **Vzor úlohy**| Vaše úlohy jsou poměrně ustálené a vy nepotřebujete často škálovat uzly v clusteru. | Vaše úloha je nestálá a je nutné, abyste byli schopni horizontální navýšení nebo snížení kapacity uzlů v datovém centru nebo snadného přidávání a odebírání datových center. | Vaše úloha je často nestálá a je nutné, abyste mohli rychle škálovat a škálovat směrem nahoru a na významném svazku. |
| **Smlouvy SLA**| S vašimi procesy máte na SLA konzistenci, propustnost, dostupnost a zotavení po havárii. | S vašimi procesy jste spokojeni s údržbou SLA konzistence, propustnosti a dostupnosti, ale potřebujete nápovědu k zálohování. | Chcete plně komplexní SLA o konzistenci, propustnosti, dostupnosti a zotavení po havárii. |

## <a name="next-steps"></a>Další kroky

Začněte s jedním z našich rychlých startů:

* [Vytvoření clusteru spravované instance z Azure Portal](create-cluster-portal.md)