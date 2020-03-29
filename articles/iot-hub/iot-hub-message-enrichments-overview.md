---
title: Přehled obohacení zpráv služby Azure IoT Hub
description: Tento článek ukazuje obohacení zpráv, které poskytují službu IoT Hub možnost razítko zprávy s další informace před zprávy jsou odesílány do určeného koncového bodu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429123"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Obohacení zpráv pro zprávy služby IoT Hub mezi zařízeními

*Obohacení zpráv* je schopnost služby IoT Hub *razítko* zprávy s další informace před zprávy jsou odeslány do určeného koncového bodu. Jedním z důvodů, proč používat obohacení zpráv, je zahrnout data, která lze použít ke zjednodušení následného zpracování. Například obohacení telemetrických zpráv zařízení pomocí značky dvojčete zařízení může snížit zatížení zákazníků, aby tato informace mohla volat rozhraní API dvojčete zařízení.

![Tok obohacení zpráv](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Obohacení zprávy má tři klíčové prvky:

* Název obohacení nebo klíč

* Hodnota

* Jeden nebo více [koncových bodů,](iot-hub-devguide-endpoints.md) pro které by mělo být použito obohacení.

**Klíčem** je řetězec. Klávesa může obsahovat pouze alfanumerické znaky`-`nebo tyto`_`speciální znaky: pomlčka ( ), podtržítko ( ) a tečka (`.`).

**Hodnota** může být libovolný z následujících příkladů:

* Libovolný statický řetězec. Dynamické hodnoty, jako jsou podmínky, logika, operace a funkce, nejsou povoleny. Pokud například vyvíjíte aplikaci SaaS, kterou používá několik zákazníků, můžete každému zákazníkovi přiřadit identifikátor a tento identifikátor zpřístupnit v aplikaci. Při spuštění aplikace ioT Hub orazítkuje telemetrické zprávy zařízení s identifikátorem zákazníka, takže je možné zpracovat zprávy odlišně pro každého zákazníka.

* Název služby IoT hub odesílající zprávu. Tato hodnota je *$iothubname*.

* Informace z dvojčete zařízení, například jeho cesta. Příkladem mohou být *$twin.tags.field* a *$twin.tags.latitude*.

   > [!NOTE]
   > V současné době pouze $iothubname, $twin.tags, $twin.properties.desired a $twin.properties.reported jsou podporované proměnné pro obohacení zpráv.

Obohacení zpráv jsou přidány jako vlastnosti aplikace do zpráv odeslaných do vybraného koncového bodu.  

## <a name="applying-enrichments"></a>Použití obohacení

Zprávy mohou pocházet z libovolného zdroje dat podporovaného [směrováním zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md), včetně následujících příkladů:

* telemetrie zařízení, jako je teplota nebo tlak
* oznámení o změně dvojčete zařízení – změny v dvojčeti zařízení
* události životního cyklu zařízení, například když je zařízení vytvořeno nebo odstraněno

Obohacení můžete přidat ke zprávám, které se nacházejí do integrovaného koncového bodu služby IoT Hub, nebo zpráv, které jsou směrovány do vlastních koncových bodů, jako je úložiště objektů Blob Azure, fronta služby Service Bus nebo téma služby Service Bus.

Obohacení zpráv, které jsou publikovány do mřížky událostí, můžete přidat výběrem koncového bodu jako mřížky událostí. V centru IoT hub uvedeme výchozí trasu telemetrii zařízení na základě vašeho předplatného Služby Event Grid. Tato jediná trasa může zpracovat všechna vaše odběry služby Event Grid. Můžete nakonfigurovat obohacení pro koncový bod mřížky událostí po vytvoření odběr mřížky událostí pro telemetrii zařízení. Další informace naleznete [v tématu Iot Hub a Event Grid](iot-hub-event-grid.md).

Obohacení se aplikuje na jeden koncový bod. Pokud zadáte pět obohacení, které mají být označeny pro konkrétní koncový bod, všechny zprávy, které přejdou do tohoto koncového bodu jsou označeny stejnými pěti obohacení.

Obohacení lze konfigurovat pomocí následujících metod:

| **Metoda** | **Příkaz** |
| ----- | -----| 
| Portál | [Portál Azure](https://portal.azure.com) | Podívejte se na [kurz obohacení zpráv](tutorial-message-enrichments.md) | 
| Azure CLI   | [az iot hub obohacení zpráv](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

Přidání obohacení zprávy nepřidá latence směrování zpráv.

Chcete-li vyzkoušet obohacení zpráv, podívejte se na [kurz obohacení zpráv](tutorial-message-enrichments.md)

## <a name="limitations"></a>Omezení

* Můžete přidat až 10 obohacení na IoT Hub pro tyto rozbočovače ve standardní nebo základní vrstvě. Pro IoT Hubs ve volné vrstvě můžete přidat až 2 obohacení.

* V některých případech, pokud používáte obohacení s hodnotou nastavenou na značku nebo vlastnost v dvojčeti zařízení, hodnota bude označena jako hodnota řetězce. Pokud je například hodnota obohacení nastavena na $twin.tags.field, budou zprávy označeny řetězcem "$twin.tags.field" spíše než hodnotou tohoto pole z dvojčete. K tomu dochází v následujících případech:

   * Váš IoT Hub je v základní vrstvě. Základní vrstvy služby IoT huby nepodporují dvojčata zařízení.

   * Vaše služba IoT Hub je ve standardní úrovni, ale zařízení odesílající zprávu nemá žádné dvojče zařízení.

   * Vaše ioT hub je ve standardní vrstvě, ale cesta dvojčete zařízení používá pro hodnotu obohacení neexistuje. Pokud je například hodnota obohacení nastavena na $twin.tags.location a dvojče zařízení nemá vlastnost umístění pod značkami, je zpráva označena řetězcem "$twin.tags.location". 

* Aktualizace dvojčete zařízení může trvat až pět minut, než se projeví v odpovídající hodnotě obohacení.

* Celková velikost zprávy, včetně obohacení, nesmí překročit 256 kB. Pokud velikost zprávy překročí 256 kB, služba IoT Hub zprávu vysadí. [Metriky ioT hubu](iot-hub-metrics.md) můžete použít k identifikaci a ladění chyb při vynechání zpráv. Můžete například sledovat d2c.telemetry.egress.invalid.

* Obohacení zpráv se nevztahuje na události změny digitálních dvojčat (část [veřejné verze IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="pricing"></a>Ceny

Obohacení zpráv je k dispozici bez dalších poplatků. V současné době se vám účtují poplatky při odeslání zprávy do služby IoT Hub. Za tuto zprávu se vám budou účtovat jenom jednou, i když zpráva přejde do více koncových bodů.

## <a name="next-steps"></a>Další kroky

Další informace o směrování zpráv do služby IoT Hub najdete v těchto článcích:

* [Kurz obohacení zpráv](tutorial-message-enrichments.md)

* [Směrování zpráv služby IoT Hub slouží k odesílání zpráv mezi zařízeními a cloudy do různých koncových bodů](iot-hub-devguide-messages-d2c.md)

* [Kurz: Směrování služby IoT Hub](tutorial-routing.md)
