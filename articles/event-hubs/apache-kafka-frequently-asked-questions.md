---
title: Nejčastější dotazy – Azure Event Hubs pro Apache Kafka
description: Tento článek ukazuje, jak spotřebitelé a výrobci, kteří používají různé protokoly (AMQP, Apache Kafka a HTTPS) můžete vyměňovat události při používání Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606740"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Nejčastější dotazy - Event Hubs for Apache Kafka 
Tento článek obsahuje odpovědi na některé často kladené otázky týkající se migrace do centra událostí pro Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Řídíte Apache Kafku?

Ne.  Provádíme operace Rozhraní API Kafka s infrastrukturou Event Hubs.  Vzhledem k tomu, že existuje těsná korelace mezi funkcí Apache Kafka a Event Hubs AMQP (tj. vyrábět, přijímat, spravovat a tak dále.), jsme schopni přenést známou spolehlivost event hubů do prostoru Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Skupina spotřebitelů Event Hubs vs. Skupina spotřebitelů Kafka
Jaký je rozdíl mezi skupinou spotřebitelů Centra událostí a skupinou spotřebitelů Kafka v centru událostí? Skupiny spotřebitelů Kafka v centru událostí se plně liší od standardních skupin spotřebitelů Event Hubs.

**Skupiny spotřebitelů Centra událostí**

- Jsou spravovány pomocí operací vytváření, načítání, aktualizace a odstraňování (CRUD) prostřednictvím portálu, sady SDK nebo šablon Azure Resource Manager. Skupiny spotřebitelů centra událostí nelze automaticky vytvořit.
- Jedná se o podřízené entity centra událostí. To znamená, že stejný název skupiny příjemce lze znovu použít mezi rozbočovači událostí ve stejném oboru názvů, protože se jedná o samostatné entity.
- Nepoužívají se pro ukládání odsazení. Organizovaná spotřeba AMQP se provádí pomocí externího ofsetového úložiště, například Azure Storage.

**Spotřebitelské skupiny Kafka**

- Jsou vytvořeny automaticky.  Skupiny Kafka lze spravovat prostřednictvím api skupiny kafka.
- Mohou ukládat posuny ve službě Event Hubs.
- Používají se jako klíče v co je efektivně posun uskladnění hodnoty klíče. Pro jedinečný pár `group.id` `topic-partition`a , ukládáme posun v Azure Storage (3x replikace). Uživatelům event hubů nevznikají další náklady na úložiště při ukládání kompenzací Kafka. Posuny jsou manipulovatelné prostřednictvím souborů API skupiny spotřebitelů Kafka, ale *účty* úložiště protiúčtu nejsou přímo viditelné nebo manipulovatelné pro uživatele Centra událostí.  
- Jsou rozloženy na obor názvů. Použití stejného názvu skupiny Kafka pro více aplikací na více témat znamená, že všechny aplikace a jejich klienti Kafka budou znovu vyváženy vždy, když pouze jedna aplikace potřebuje vyvažování.  Vyberte si názvy skupin moudře.
- Plně se liší od skupin spotřebitelů Event Hubs. Nemusíte **don't** používat "$Default", ani se nemusíte starat o klienty Kafka, kteří zasahují do úloh AMQP.
- Na webu Azure Portal se nedají zobrazit. Informace o skupině spotřebitelů jsou přístupné prostřednictvím api Kafka.

## <a name="next-steps"></a>Další kroky
Další informace o centru událostí a centru událostí pro Kafku najdete v následujících článcích:  

- [Průvodce vývojáři Apache Kafka pro Centra událostí](apache-kafka-developer-guide.md)
- [Průvodce migrací Apache Kafka pro centra událostí](apache-kafka-migration-guide.md)
- [Průvodce odstraňováním potíží apache Kafka pro centra událostí](apache-kafka-troubleshooting-guide.md)
- [Doporučené konfigurace](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

