---
title: Nejčastější dotazy – Azure Event Hubs pro Apache Kafka
description: V tomto článku najdete odpovědi na časté otázky týkající se podpory Azure Event Hubs pro Apache Kafka klientů, kteří nejsou pokryti jinde.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ca54bf314d795b88b727ddb648f3e1e74133fd3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061457"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Nejčastější dotazy – Event Hubs pro Apache Kafka 
Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se migrace na Event Hubs pro Apache Kafka.

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Běží Azure Event Hubs v Apache Kafka?

No. Azure Event Hubs je nativní cloudový zprostředkovatel s podporou různých protokolů, které vyvíjí a udržuje společnost Microsoft a nepoužívá žádný kód Apache Kafka. Jedním z podporovaných protokolů je protokol Kafka RPC pro zákaznická rozhraní API klienta Kafka a výrobce. Event Hubs funguje s řadou vašich stávajících aplikací Kafka. Další informace najdete v tématu [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). Vzhledem k tomu, že koncepce Apache Kafka a Azure Event Hubs jsou velmi podobné (ale ne totožné), můžeme zákazníkům s existujícím Apache Kafkam investic nabídnout neshodnou spolehlivost Azure Event Hubs. 

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

