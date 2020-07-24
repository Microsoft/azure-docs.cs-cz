---
title: Nejčastější dotazy – Azure Event Hubs pro Apache Kafka
description: V tomto článku se dozvíte, jak můžou zákazníci a výrobci, kteří používají různé protokoly (AMQP, Apache Kafka a HTTPS), vyměňovat události při použití Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8bdd86d9f299a69d5f2d05bb8ec526ed94780608
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031679"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Nejčastější dotazy – Event Hubs pro Apache Kafka 
Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se migrace na Event Hubs pro Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Spouštíte Apache Kafka?

Ne.  U Event Hubs infrastruktury provedeme operace rozhraní API pro Kafka.  Vzhledem k tomu, že existuje těsná korelace mezi funkcemi Apache Kafka a Event Hubs AMQP (to znamená výroba, příjem, Správa atd.), můžeme získat známou spolehlivost Event Hubs na Kafka PaaS prostor.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Skupina příjemců Event Hubs vs. Kafka
Jaký je rozdíl mezi skupinou uživatelů centra událostí a skupinou uživatelů Kafka na Event Hubs? Skupiny uživatelů Kafka na Event Hubs jsou plně odlišné od standardních Event Hubsch skupin příjemců.

**Event Hubs skupiny příjemců**

- Spravují se pomocí operací vytvoření, načtení, aktualizace a odstranění (CRUD) prostřednictvím portálu, sady SDK nebo šablon Azure Resource Manager. Skupiny příjemců Event Hubs nejde vytvořit znovu.
- Jsou to podřízené entity centra událostí. To znamená, že stejný název skupiny uživatelů lze znovu použít mezi centry událostí ve stejném oboru názvů, protože se jedná o samostatné entity.
- Nepoužívají se k ukládání posunů. Orchestrovaná spotřeba AMQP se provádí pomocí externího kompenzačního úložiště, například Azure Storage.

**Skupiny příjemců Kafka**

- Vytvářejí se z nich.  Skupiny Kafka se dají spravovat prostřednictvím rozhraní API skupin uživatelů Kafka.
- Můžou ukládat posuny ve službě Event Hubs.
- Používají se jako klíče v tom, co je efektivně posunutím úložiště hodnot klíčů. Pro jedinečnou dvojici `group.id` a `topic-partition` ukládáme posun do Azure Storage (replikace 3x). Event Hubs uživatelé neúčtují dodatečné náklady na úložiště z ukládání posunů Kafka. Posuny jsou manipulable prostřednictvím rozhraní API skupiny příjemců Kafka, ale *účty* úložiště offset nejsou přímo viditelné nebo manipulable pro uživatele centra událostí.  
- Přidávají rozsah názvů. Použití stejného názvu skupiny Kafka pro více aplikací na více tématech znamená, že všechny aplikace a jejich klienti Kafka budou převyváženi vždy, když se bude vyžadovat opětovné vyrovnávání jedné aplikace.  Vyberte názvy skupin.
- Úplně se liší od Event Hubsch spotřebitelských skupin. Nemusíte používat $Default, ani si **nemusíte** dělat starosti s tím, jak klienti Kafka nenarušují AMQP úlohy.
- Nedají se zobrazit v Azure Portal. Informace o skupině uživatelů jsou přístupné prostřednictvím rozhraní Kafka API.

## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs a Event Hubs pro Kafka najdete v následujících článcích:  

- [Apache Kafka příručka pro vývojáře pro Event Hubs](apache-kafka-developer-guide.md)
- [Průvodce migrací Apache Kafka pro Event Hubs](apache-kafka-migration-guide.md)
- [Průvodce odstraňováním potíží s Apache Kafka pro Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Doporučené konfigurace](apache-kafka-configurations.md)

