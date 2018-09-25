---
title: Porovnání služby Event Grid, směrování pro službu IoT Hub | Dokumentace Microsoftu
description: IoT Hub nabízí vlastní služby směrování zpráv, ale také integruje s Event gridu pro události publikování. Porovnejte dvě funkce.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 13cf5861bf39cdd9c192586979b95192a31e9399
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978671"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porovnání směrování zpráv a služby Event Grid pro službu IoT Hub

Azure IoT Hub poskytuje možnost streamování dat z připojených zařízení a integrace těchto dat do obchodních aplikací. IoT Hub nabízí dva způsoby integrace událostí IoT do jiných služeb Azure nebo obchodních aplikací. Tento článek popisuje dvě funkce, které poskytují tuto funkci, aby mohli vybrat, která možnost je pro váš scénář nejvhodnější.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

* **[Směrování zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md)**: funkce této služby IoT Hub umožňuje směrovat zprávy typu zařízení cloud do koncových bodů služby, jako jsou kontejnery služby Azure Storage, služby Event Hubs, fronty Service Bus a témat Service Bus. Směrování umožňuje dotazování funkce k filtrování dat před směrování do koncových bodů. Kromě zařízení telemetrická data, můžete také odeslat [bez telemetrie událostí](iot-hub-devguide-messages-d2c.md#non-telemetry-events) , který můžete použít k aktivaci akce. 
* **Integrace služby IoT Hub s využitím služby Event Grid**: Azure Event Grid je plně spravovaná služba Směrování událostí, který používá publikování-odběru modelu. IoT Hub a Event Grid společně [integrace událostí IoT Hub do Azure i mimo Azure services](iot-hub-event-grid.md), v téměř reálném čase. 

## <a name="similarities-and-differences"></a>Podobnosti a rozdíly

Při směrování zpráv a služby Event Grid povolit konfiguraci upozornění, existují některé hlavní rozdíly mezi nimi. Přečtěte si podrobnosti v následující tabulce:

| Funkce | Směrování zpráv služby IoT Hub | Integrace služby IoT Hub s využitím služby Event Grid |
| ------- | --------------- | ---------- |
| **Zprávy typu zařízení** | Ano, směrování zpráv je možné pro telemetrická data. | Ne, služby Event Grid jde použít jenom pro události služby IoT Hub-telemetrická data. |
| **Typ události** | Ano, směrování zpráv může hlásit dvojčete změny a události životního cyklu zařízení. | Ano, můžete sestavy služby Event Grid, když zařízení se vytvoří, odstraní, připojení a odpojení ze služby IoT Hub |
| **Řazení** | Ano, je zachováno řazení událostí.  | Ne, není zaručeno pořadí událostí. | 
| **Maximální velikost zprávy** | 256 KB, typu zařízení cloud | 64 kB |
| **Filtrování** | Bohaté možnosti filtrování na vlastnosti aplikace zprávu, systémové vlastnosti zprávy, text zprávy, značky dvojčat zařízení a zařízení dvojče vlastnosti. Příklady najdete v tématu [syntaxi dotazů směrování zpráv](iot-hub-devguide-routing-query-syntax.md). | Filtrování na základě přípony nebo předpony identifikátorů zařízení, což funguje dobře pro hierarchické služeb, jako je úložiště. |
| **Koncové body** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Fronta služby Service Bus</li> <li>Service Bus Topics</li></ul><br>Platí omezení na 10 vlastní koncové body IoT Hubu skladové jednotky (S1, S2 a S3). 100 trasy je možné vytvořit jednotlivé služby IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Vlastní témata</li> <li>Služby třetích stran pomocí Webhooků</li></ul><br>Nejaktuálnější seznam koncových bodů najdete v tématu [obslužné rutiny událostí služby Event Grid](../event-grid/overview.md#event-handlers). |
| **Náklady** | Neexistuje žádný samostatný poplatek za směrování zpráv. Účtuje se pouze příchozí telemetrická data do služby IoT Hub. Například pokud máte zprávu směrovat do tří různých koncových bodů, můžete se účtují jenom jedna zpráva. | Neplatí žádné poplatky ze služby IoT Hub. Event Grid nabízí prvních 100 000 operací za měsíc zdarma a potom 0,60 $ za milion operací po tomto. |

Směrování zpráv služby IoT Hub a Event Grid podobností příliš, z nichž některé jsou podrobně popsané v následující tabulce:

| Funkce | Směrování zpráv služby IoT Hub | Integrace služby IoT Hub s využitím služby Event Grid |
| ------- | --------------- | ---------- |
| **Spolehlivost** | Vysoká: Poskytuje každou zprávu do koncového bodu alespoň jednou pro každou trasu. Vypršení platnosti všech zpráv, které nejsou doručeny do jedné hodiny. | Vysoká: Přináší každou zprávu webhooku alespoň jednou pro každé předplatné. Vypršení platnosti všech událostí, které nejsou doručeny do 24 hodin. | 
| **Škálovatelnost** | Vysoká: Optimalizovány pro podporu miliony současně připojených zařízení, které posílají miliardy zpráv. | Vysoká: Umožňující směrování 10 000 000 událostí za sekundu v jedné oblasti. |
| **Latence** | Nízká: Téměř v reálném čase. | Nízká: Téměř v reálném čase. |
| **Odesílání do více koncových bodů** | Ano, odešlete do jedné zprávy do více koncových bodů. | Ano, odešlete do jedné zprávy do více koncových bodů.  | 
| **Zabezpečení** | IOT Hub poskytuje identitu zařízení a odvolatelný access control. Další informace najdete v tématu [řízení přístupu služby IoT Hub](iot-hub-devguide-security.md). | Event gridu poskytuje ověření na třech místech: odběry událostí, publikování událostí a doručování událostí webhooku. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak zvolit

Směrování zpráv služby IoT Hub a integrace služby IoT Hub s využitím služby Event Grid provádět různé akce, které podobného výsledku dosáhnout. Jak využít informace z vašeho řešení IoT Hub a předat jí ho tak, aby ostatní služby můžou reagovat. Takže jak rozhodnout, který se má použít? Kromě dat z předchozí části použijte následující otázky mohou pomoci rozhodnout: 

* **Jaká data jsou ke koncovým bodům odesílání?**

   Pomocí směrování zpráv služby IoT Hub, když máte k odesílání telemetrických dat do dalších služeb. Zpráva, která směrování umožňuje dotazování na vlastnosti aplikace a systém zpráv, text zprávy, značky dvojčat zařízení a vlastnosti dvojčat zařízení také.

   Integrace služby IoT Hub s využitím služby Event Grid funguje s událostmi, ke kterým dochází ve službě IoT Hub. Tyto události služby IoT Hub zahrnout zařízení vytvořit, odstranit, připojené a odpojené. 

* **Jaké koncové body je potřeba příjem těchto informací?**

   Směrování zpráv služby IoT Hub podporuje omezenou koncových bodů, ale můžete vytvořit konektory přesměrovat data a události na další koncové body. Úplný seznam podporovaných koncových bodů najdete v tabulce v předchozí části. 

   Integrace služby IoT Hub s využitím služby Event Grid podporuje další koncové body. Funguje i s webhooky k rozšíření směrování mimo ekosystém služby Azure a do třetí strany obchodních aplikací. 

* **Je důležité, pokud vaše data přibývají v pořadí?**

   Směrování zpráv služby IoT Hub udržuje pořadí, ve kterém jsou zprávy odesílány, takže doručení stejným způsobem.

   Event Grid nezaručuje, že koncové body se zobrazí události ve stejném pořadí, že k nim došlo. Schéma událostí obsahují časové razítko, které slouží k identifikaci pořadí po doručení událostí v koncovém bodě. 

## <a name="next-steps"></a>Další postup

* Další informace o [směrování zpráv IoT Hub](iot-hub-devguide-messages-d2c.md) a [koncové body IoT Hubu](iot-hub-devguide-endpoints.md).
* Další informace o [Azure Event Grid](../event-grid/overview.md)
* Zjistěte, jak vytvořit trasy zpráv, najdete v článku [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras](../iot-hub/tutorial-routing.md) kurzu.
* Vyzkoušejte si integraci služby Event Grid pomocí [odesílání e-mailová oznámení o událostech služby Azure IoT Hub pomocí Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md)