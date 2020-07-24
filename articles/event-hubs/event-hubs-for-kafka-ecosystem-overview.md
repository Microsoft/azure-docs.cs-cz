---
title: Použití centra událostí z aplikace Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o podpoře Apache Kafka službou Azure Event Hubs.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 6dad797f6769e9804cce7d47ed74794fdddcfb0b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002474"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Použití Azure Event Hubs z Apache Kafkach aplikací
Event Hubs poskytuje koncový bod Kafka, který můžou stávající aplikace založené na Kafka použít jako alternativu ke spuštění vlastního clusteru Kafka. Event Hubs podporuje [Apache Kafka protokol 1,0 a novější](https://kafka.apache.org/documentation/)a funguje s vašimi stávajícími aplikacemi Kafka, včetně nástroje MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co Event Hubs for Kafka poskytnout?

Funkce Event Hubs for Kafka poskytuje hlavní hlavičku protokolu pro Azure Event Hubs, která je binární kompatibilní s Kafka verzemi 1,0 a novějšími pro čtení i zápis do Kafka témat. Můžete začít používat Kafka koncový bod z vašich aplikací bez změny kódu, ale minimální změna konfigurace. Připojovací řetězec v konfiguracích aktualizujete tak, aby odkazoval na koncový bod Kafka vystavený centrem událostí, a nemusíte ukazovat na svůj cluster Kafka. Pak můžete spustit streamování událostí z aplikací, které používají protokol Kafka, do Event Hubs. Tato integrace také podporuje architektury, jako je [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), která je aktuálně ve verzi Preview. 

Koncepční Kafka a Event Hubs jsou téměř identické: Jedná se o segmentované protokoly sestavené pro streamovaná data. Následující tabulka mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka a konceptuální mapování centra událostí

| Koncept Kafka | Event Hubs koncept|
| --- | --- |
| Cluster | Obor názvů |
| Téma | Centrum událostí |
| Oddíl | Oddíl|
| Skupina uživatelů | Skupina uživatelů |
| Posun | Posun|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Klíčové rozdíly mezi Kafka a Event Hubs

I když [Apache Kafka](https://kafka.apache.org/) je software, který můžete spustit všude, kde si zvolíte, Event Hubs je cloudová služba, která se podobá službě Azure Blob Storage. Neexistují žádné servery nebo sítě, které by bylo možné spravovat, a žádné zprostředkovatele pro konfiguraci. Vytvoříte obor názvů, který je plně kvalifikovaný název domény, ve kterém jsou témata živá, a pak vytvoříte Event Hubs nebo témata v rámci tohoto oboru názvů. Další informace o Event Hubs a oborech názvů najdete v tématu [Event Hubs funkce](event-hubs-features.md#namespace). Jako cloudová služba Event Hubs používá jedinou stabilní virtuální IP adresu jako koncový bod, takže klienti nepotřebují znát o vzdálení nebo počítačích v clusteru. 

Škála v Event Hubs se řídí počtem jednotek propustnosti, které zakoupíte, s každou jednotkou propustnosti entitling na 1 MB za sekundu nebo za 1000 událostí za sekundu příchozího přenosu dat. Ve výchozím nastavení Event Hubs škáluje jednotky propustnosti, když dosáhnete svého limitu pomocí funkce [automatického rozplochení](event-hubs-auto-inflate.md) ; Tato funkce funguje taky s funkcí Event Hubs pro Kafka. 

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
**Kurz** s podrobnými pokyny k vytvoření centra událostí a přístup k němu pomocí SAS nebo OAuth najdete v tématu [rychlý Start: streamování dat pomocí Event Hubs pomocí protokolu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Další **ukázky** , které ukazují, jak používat OAuth s Event Hubs pro Kafka, najdete v tématu [ukázky na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Další funkce Event Hubs dostupné pro Kafka

Funkce Event Hubs for Kafka umožňuje psát pomocí jednoho protokolu a číst s jiným, takže stávající výrobci Kafka mohou pokračovat v publikování prostřednictvím Kafka a můžete přidat čtenáře s Event Hubs, jako je například Azure Stream Analytics nebo Azure Functions. Kromě toho Event Hubs funkce, jako je například [zachycení](event-hubs-capture-overview.md) a [geografická havárie – obnovení](event-hubs-geo-dr.md) , fungují také s funkcí Event Hubs pro Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkce, které ještě nejsou podporované 

Tady je seznam funkcí Kafka, které ještě nejsou podporované:

*   Transakce
*   Komprese
*   Uchovávání na základě velikosti
*   Komprimace protokolů
*   Podpora rozhraní HTTP Kafka API
*   Datové proudy Kafka

## <a name="next-steps"></a>Další kroky
Tento článek poskytuje Úvod do Event Hubs pro Kafka. Další informace najdete v tématu [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md).


