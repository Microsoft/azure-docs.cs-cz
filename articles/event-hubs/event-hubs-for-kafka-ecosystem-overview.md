---
title: Použití centra událostí z aplikací Apache Kafka – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o podporovaných Apache Kafka se službou Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: fc81226e754178ad0edfff96a494dd7522662261
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368504"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Použití služby Azure Event Hubs z aplikací Apache Kafka
Služba Event Hubs poskytuje Kafka koncového bodu, který lze použít ve stávajících Kafka aplikací založených na jako alternativu ke spuštění clusteru Kafka. Event Hubs podporuje [Apache Kafka protokol 1,0 a novější](https://kafka.apache.org/documentation/)a funguje s vašimi stávajícími aplikacemi Kafka, včetně nástroje MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co Event Hubs pro systém Kafka poskytuje?

Služba Event Hubs pro funkci Kafka poskytuje head protokolu nad Azure Event Hubs, která je binární kompatibilní s Kafka verze 1.0 a novějším pro čtení i zápis témat Kafka. Můžete začít pomocí koncového bodu Kafka z vašich aplikací se žádné změny kódu, ale minimální konfiguraci změnit. Můžete aktualizovat připojovací řetězec v konfiguracích tak, aby odkazoval na koncový bod Kafka vystavené vašeho centra událostí místo směřující do vašeho clusteru Kafka. Pak můžete začít, streamování událostí z aplikací, které používají protokol Kafka do služby Event Hubs. Tato integrace také podporuje architektury, jako je [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), která je aktuálně ve verzi Preview. 

Koncepční Kafka a Event Hubs jsou téměř identické: Jedná se o segmentované protokoly sestavené pro streamovaná data. Následující tabulka mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka a Centrum událostí koncepční mapování

| Koncept Kafka | Event Hubs koncept|
| --- | --- |
| Cluster | Obor názvů |
| Téma | Centrum událostí |
| Oddíl | Oddíl|
| Skupiny příjemců | Skupiny příjemců |
| Posun | Posun|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Hlavní rozdíly mezi Kafka a Event Hubs

I když [Apache Kafka](https://kafka.apache.org/) je software, který můžete spustit všude, kde si zvolíte, Event Hubs je cloudová služba, která se podobá službě Azure Blob Storage. Neexistují žádné servery nebo sítě pro správu a bez zprostředkovatele konfigurace. Vytvoření oboru názvů, což je plně kvalifikovaný název domény, ve kterém témat pracovat, a pak vytvořte službu Event Hubs nebo témat v daném oboru názvů. Další informace o Event Hubs a oborech názvů najdete v tématu [Event Hubs funkce](event-hubs-features.md#namespace). Jako cloudová služba Event Hubs používá jedinou stabilní virtuální IP adresu jako koncový bod, takže klienti nepotřebují znát o vzdálení nebo počítačích v clusteru. 

Škálování ve službě Event Hubs je řízen prostřednictvím kolik jednotek propustnosti nákupu, s každou jednotkou propustnosti opravňující je 1 MB za sekundu nebo 1000 událostí za sekundu z příchozího přenosu. Ve výchozím nastavení Event Hubs škáluje jednotky propustnosti, když dosáhnete svého limitu pomocí funkce [automatického rozplochení](event-hubs-auto-inflate.md) ; Tato funkce funguje taky s funkcí Event Hubs pro Kafka. 

### <a name="security-and-authentication"></a>Zabezpečení a ověřování
Při každém publikování nebo zpracování událostí z Event Hubs pro Kafka se klient pokouší získat přístup k prostředkům Event Hubs. Chcete zajistit, aby k prostředkům byl přistup pomocí autorizované entity. Při použití Apache Kafka protokolu u klientů můžete nastavit konfiguraci pro ověřování a šifrování pomocí mechanismů SASL. Při použití Event Hubs pro Kafka se vyžaduje šifrování TLS (protože všechna data v přenosu s Event Hubs jsou šifrovaná TLS). Můžete to udělat zadáním možnosti SASL_SSL v konfiguračním souboru. 

Azure Event Hubs poskytuje několik možností, jak autorizovat přístup k vašim zabezpečeným prostředkům. 

- OAuth
- Sdílený přístupový podpis (SAS)

#### <a name="oauth"></a>OAuth
Event Hubs se integruje s Azure Active Directory (Azure AD), která poskytuje centralizovaný autorizační server kompatibilní s **OAuth** 2,0. Pomocí Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení jemně odstupňovaných oprávnění pro identity klientů. Tuto funkci můžete použít u klientů Kafka zadáním **SASL_SSL** pro protokol a **OAUTHBEARER** pro mechanismus. Podrobnosti o rolích a úrovních RBAC pro přístup k oboru najdete v tématu [autorizace přístupu pomocí Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sdílený přístupový podpis (SAS)
Event Hubs taky poskytuje **sdílené přístupové podpisy (SAS)** pro delegovaný přístup k Event Hubs prostředkům Kafka. Autorizaci přístupu pomocí mechanismu OAuth 2,0 s tokenem založeným na tokenech zabezpečení poskytuje prvotřídní zabezpečení a snadné použití prostřednictvím SAS. Předdefinované role mohou také eliminovat nutnost ověřování na základě seznamu ACL, které musí uživatel spravovat a spravovat. Tuto funkci můžete použít spolu s klienty Kafka zadáním **SASL_SSL** pro protokol a pro mechanismus **jako prostý** . 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Ukázky 
**Kurz** s podrobnými pokyny k vytvoření centra událostí s povoleným Kafka a přístup k němu pomocí SAS nebo OAuth najdete v tématu [rychlý Start: streamování dat pomocí Event Hubs pomocí protokolu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Další **ukázky** , které ukazují, jak používat OAuth s Event Hubs pro Kafka, najdete v tématu [ukázky na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Další funkce Event Hubs k dispozici pro systém Kafka

Event Hubs pro funkci Kafka umožňuje zápis s jeden protokol a čtení sebou, tak, aby vaše aktuální Kafka výrobci můžete pokračovat v publikování prostřednictvím Kafka, a přidáte čtenáře s využitím služby Event Hubs, jako je Azure Stream Analytics nebo Azure Functions. Kromě toho Event Hubs funkce, jako je například [zachycení](event-hubs-capture-overview.md) a [geografická havárie – obnovení](event-hubs-geo-dr.md) , fungují také s funkcí Event Hubs pro Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkce, které se zatím nepodporují 

Tady je seznam Kafka funkce, které se zatím nepodporují:

*   Výrobce Idempotentní
*   Transakce
*   Komprese
*   Uchovávání informací podle velikosti
*   Komprese protokolu
*   Přidání oddílů do stávajícího tématu
*   Podpora HTTP Kafka API
*   Rozhraní Kafka Streams

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje úvod do služby Event Hubs pro systém Kafka. Další informace najdete v následujících tématech:

- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí s podporou Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí s podporou Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrace připojení Kafka s centrem událostí s podporou Kafka](event-hubs-kafka-connect-tutorial.md)
- [Připojení Akka Streams k centru událostí s podporou Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)


