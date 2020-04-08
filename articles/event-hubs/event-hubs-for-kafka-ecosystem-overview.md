---
title: Použití centra událostí z aplikace Apache Kafka – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o podpoře Apache Kafka pomocí Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 91e2d70bab8c1be4b3e5b400ce21122eccb1e9eb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811378"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Používání Center událostí Azure z aplikací Apache Kafka
Event Hubs poskytuje koncový bod Kafka, který může být použit pro vaše stávající aplikace založené na Kafka jako alternativu ke spuštění vlastního clusteru Kafka. Event Hubs podporuje [protokol Apache Kafka 1.0 a novější](https://kafka.apache.org/documentation/)a pracuje s vašimi stávajícími aplikacemi Kafka, včetně MirrorMakeru.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co centra událostí pro Kafku poskytují?

Funkce Event Hubs for Kafka poskytuje vedoucí protokolu nad Azure Event Hubs, který je binární kompatibilní s Kafka verze 1.0 a novější pro čtení z a zápis do kafka témata. Můžete začít používat koncový bod Kafka z vašich aplikací bez změny kódu, ale minimální změny konfigurace. Připojovací řetězec v konfiguracích aktualizujete tak, aby ukazoval na koncový bod Kafka vystavený centrem událostí namísto odkazování na cluster Kafka. Potom můžete spustit streamování událostí z vašich aplikací, které používají protokol Kafka do centra událostí. Tato integrace také podporuje architektury, jako je [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), který je v současné době ve verzi Preview. 

Koncepčně Kafka a Event Hubs jsou téměř identické: jsou to oba rozdělené protokoly vytvořené pro streamování dat. V následující tabulce jsou mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Koncepční mapování Kafka a Event Hub

| Koncept Kafka | Koncept centra událostí|
| --- | --- |
| Cluster | Obor názvů |
| Téma | Centrum událostí |
| Oddíl | Oddíl|
| Skupina spotřebitelů | Skupina spotřebitelů |
| Posun | Posun|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Klíčové rozdíly mezi Kafkou a Event Huby

Zatímco [Apache Kafka](https://kafka.apache.org/) je software, který můžete spouštět, ať si vyberete, Event Hubs je cloudová služba podobná Azure Blob Storage. Nejsou k dispozici žádné servery nebo sítě, které by bylo možné spravovat, a žádné zprostředkovatele, které by bylo možné konfigurovat. Vytvoříte obor názvů, což je hlavní název, ve kterém jsou vaše témata aktivní, a pak vytvoříte centra událostí nebo témata v rámci tohoto oboru názvů. Další informace o rozbočovačích událostí a oborech názvů najdete v [tématu Funkce centra událostí](event-hubs-features.md#namespace). Jako cloudová služba centra událostí používá jako koncový bod jednu stabilní virtuální IP adresu, takže klienti nemusí vědět o zprostředkovatelích nebo počítačích v rámci clusteru. 

Škálování v rozbočovačích událostí je řízeno tím, kolik jednotek propustnosti zakoupíte, přičemž každá jednotka propustnosti vás opravňuje k 1 MB za sekundu nebo 1000 událostí za sekundu příchozího přenosu dat. Ve výchozím nastavení se centra událostí zvětšují jednotky propustností, když dosáhnete limitu pomocí funkce [Automatické nafouknutí;](event-hubs-auto-inflate.md) Tato funkce funguje také s funkcí Event Hubs for Kafka. 

### <a name="security-and-authentication"></a>Zabezpečení a ověřování
Pokaždé, když publikujete nebo spotřebováváte události z centra událostí pro Kafka, váš klient se pokouší získat přístup k prostředkům Centra událostí. Chcete zajistit, aby prostředky byly přístupné pomocí autorizované entity. Při použití protokolu Apache Kafka se svými klienty můžete nastavit konfiguraci pro ověřování a šifrování pomocí mechanismů SASL. Při použití Event Hubs pro Kafka vyžaduje šifrování TLS (jako všechna data při přenosu s Event Hubs je TLS šifrované). To lze provést určení SASL_SSL možnost v konfiguračním souboru. 

Azure Event Hubs poskytuje několik možností autorizace přístupu k zabezpečeným prostředkům. 

- OAuth
- Sdílený přístupový podpis (SAS)

#### <a name="oauth"></a>OAuth
Event Hubs se integruje s Azure Active Directory (Azure AD), který poskytuje centralizovaný autorizovaný autorizační server kompatibilní s **OAuth** 2.0. S Azure AD, můžete použít řízení přístupu na základě rolí (RBAC) udělit jemně odstupňovaná oprávnění k identitám klienta. Tuto funkci můžete použít s klienty Kafka zadáním **SASL_SSL** pro protokol a **OAUTHBEARER** pro mechanismus. Podrobnosti o rolích RBAC a úrovních pro přístup k oborům najdete [v tématu Autorizace přístupu pomocí služby Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sdílený přístupový podpis (SAS)
Centra událostí také poskytuje **sdílené přístupové podpisy (SAS)** pro delegovaný přístup k event hubům pro prostředky Kafka. Autorizace přístupu pomocí mechanismu založeného na tokenech OAuth 2.0 poskytuje vynikající zabezpečení a snadné použití oproti SAS. Předdefinované role mohou také eliminovat potřebu autorizace založené na přístupové mlze, která musí být udržována a spravována uživatelem. Tuto funkci můžete použít s klienty Kafka zadáním **SASL_SSL** pro protokol a **PLAIN** pro mechanismus. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>ukázky 
**Kurz** s podrobnými pokyny k vytvoření centra událostí a přístup u něj pomocí SAS nebo OAuth najdete v [tématu Rychlý start: Streamování dat pomocí centra událostí pomocí protokolu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Další **ukázky,** které ukazují, jak používat OAuth s event huby pro Kafka, najdete [v ukázkách na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Další funkce Event Hubs dostupné pro Kafka

Funkce Event Hubs for Kafka umožňuje psát pomocí jednoho protokolu a číst pomocí jiného, takže vaši stávající producenti Kafka můžou pokračovat v publikování přes Kafka a můžete přidávat čtečky pomocí event hubů, jako je Azure Stream Analytics nebo Azure Functions. Kromě toho funkce Event Hubs, jako je [zachycení](event-hubs-capture-overview.md) a [geografické zotavení po havárii,](event-hubs-geo-dr.md) také pracují s funkcí Event Hubs pro Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkce, které ještě nejsou podporovány 

Zde je seznam funkcí Kafka, které ještě nejsou podporovány:

*   Idempotentní výrobce
*   Transakce
*   Komprese
*   Uchovávání podle velikosti
*   Zhutnění protokolu
*   Přidání oddílů do existujícího tématu
*   Podpora rozhraní HTTP Kafka API
*   Kafka potoky

## <a name="next-steps"></a>Další kroky
Tento článek poskytl úvod do event hubů pro Kafka. Další informace najdete v [tématu Apache Kafka developer guide for Azure Event Hubs](apache-kafka-developer-guide.md).


