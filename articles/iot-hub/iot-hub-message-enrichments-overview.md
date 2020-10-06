---
title: Přehled rozšíření zpráv IoT Hub Azure
description: V tomto článku se dozvíte o rozšířeních zpráv, která IoT Hub schopnost zasílat zprávy s dalšími informacemi ještě předtím, než se zprávy odešlou do určeného koncového bodu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 792486a78dfed606ce8317d9bc037221ae33d508
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767189"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Rozšíření zpráv pro zprávy ze zařízení na Cloud IoT Hub

*Rozšiřování zpráv* je schopnost IoT Hub k *razítku* zpráv s dalšími informacemi, než se zprávy odešlou do určeného koncového bodu. Jedním z důvodů použití rozšíření zpráv je zahrnutí dat, která je možné použít ke zjednodušení zpracování po směru. Například obohacení zpráv telemetrie zařízení pomocí značky s dvojitou silou zařízení může snížit zatížení zákazníky, aby pro tyto informace volalo volání rozhraní API zařízení.

![Tok rozšíření zpráv](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Rozšíření zprávy má tři klíčové prvky:

* Název nebo klíč obohacení

* Hodnota

* Jeden nebo více [koncových bodů](iot-hub-devguide-endpoints.md) , pro které by mělo být rozšíření použito.

**Klíč** je řetězec. Klíč může obsahovat jenom alfanumerické znaky nebo tyto speciální znaky: spojovník ( `-` ), podtržítko ( `_` ) a perioda ( `.` ).

**Hodnota** může být libovolná z následujících příkladů:

* Libovolný statický řetězec. Dynamické hodnoty, jako jsou podmínky, logika, operace a funkce, nejsou povoleny. Pokud například vyvíjíte aplikaci SaaS, kterou používá několik zákazníků, můžete každému zákazníkovi přiřadit identifikátor a tento identifikátor zpřístupnit v aplikaci. Po spuštění aplikace IoT Hub označí zprávy telemetrie zařízení pomocí identifikátoru zákazníka, aby bylo možné zpracovat zprávy jinak pro každého zákazníka.

* Název centra IoT, které posílá zprávu Tato hodnota je *$iothubname*.

* Informace z vlákna zařízení, například jeho cesta. Příklady by byly *$Twin. Tags. Field* a *$Twin. Tags. Zeměpisná šířka*.

   > [!NOTE]
   > V tuto chvíli pouze $iothubname, $twin. tagss, $twin. Properties. resired a $twin. Properties. hlášeny, podporované proměnné pro rozšíření zprávy.

Rozšíření zprávy jsou přidána jako vlastnosti aplikace do zpráv odesílaných na vybrané koncové body.  

## <a name="applying-enrichments"></a>Použití obohacení

Zprávy mohou pocházet z libovolného zdroje dat podporovaného [IoT Hub směrováním zpráv](iot-hub-devguide-messages-d2c.md), včetně následujících příkladů:

* telemetrie zařízení, jako je teplota nebo tlak
* oznámení o zdvojených změnách zařízení – změny v zařízení jsou v nevlákenné
* události životního cyklu zařízení, například při vytvoření nebo odstranění zařízení

Ke zprávám, které se předávají do předdefinovaného koncového bodu IoT Hub, můžete přidat rozšíření nebo zprávy směrované do vlastních koncových bodů, jako je například úložiště objektů BLOB v Azure, fronta Service Bus nebo Service Bus téma.

Můžete přidat rozšíření do zpráv, které jsou publikovány do Event Grid tím, že vyberete koncový bod jako Event Grid. Vytvoříme výchozí trasu v IoT Hub k telemetrie zařízení na základě vašeho předplatného Event Grid. Tato jediná trasa dokáže zvládnout všechna Vaše předplatná Event Grid. Po vytvoření odběru Event gridu pro telemetrii zařízení můžete nakonfigurovat rozšíření pro koncový bod služby Event Grid. Další informace najdete v tématu [IoT Hub a Event Grid](iot-hub-event-grid.md).

Pro každý koncový bod se aplikují obohacení. Pokud zadáte pro určitý koncový bod pět obohacení, budou se všechny zprávy, které se na tento koncový bod natištěny, natištěny se stejnými pěti rozšířeními.

Rozšíření lze konfigurovat pomocí následujících metod:

| **Metoda** | **Příkaz** |
| ----- | -----| 
| Portál | [Azure Portal](https://portal.azure.com) | Viz [kurz pro rozšíření zpráv](tutorial-message-enrichments.md) . | 
| Azure CLI   | [AZ IoT Hub Message-obohacení](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment) |

Přidání rozšíření zprávy nepřidá latenci do směrování zpráv.

Informace o tom, jak vyzkoušet rozšíření zpráv, najdete v [kurzu rozšíření zpráv](tutorial-message-enrichments.md) .

## <a name="limitations"></a>Omezení

* Můžete přidat až 10 rozšíření na jednu IoT Hub pro tato centra na úrovni Standard nebo Basic. Pro centra IoT na bezplatné úrovni můžete přidat až 2 rozšíření.

* V některých případech, pokud aplikujete obohacení s hodnotou nastavenou na značku nebo vlastnost v zařízení, bude tato hodnota označena jako řetězcová hodnota. Pokud je například hodnota obohacení nastavena na $twin. Tags. Field, zprávy budou označeny řetězcem "$twin. Tags. Field", nikoli hodnotou tohoto pole z vlákna. K tomu dochází v následujících případech:

   * Vaše IoT Hub jsou na úrovni Basic. Centra IoT úrovně Basic nepodporují vlákna zařízení.

   * Vaše IoT Hub jsou na úrovni Standard, ale zařízení, které posílá zprávu, nemá žádné nevlákenné zařízení.

   * Vaše IoT Hub jsou na úrovni Standard, ale cesta k tomuto zařízení použitá pro hodnotu rozšíření neexistuje. Pokud je například hodnota obohacení nastavena na $twin. Tags. Location a v případě, že se zařízení nezobrazuje v rámci značek, je zpráva označena řetězcem "$twin. Tags. Location". 

* Aktualizace vlákna zařízení může trvat až pět minut, než se odrazí v odpovídající hodnotě obohacení.

* Celková velikost zprávy, včetně rozšíření, nesmí překročit 256 KB. V případě, že velikost zprávy překračuje 256 KB, bude zpráva vynechá IoT Hub. Můžete použít [IoT Hub metriky](iot-hub-metrics.md) k identifikaci a ladění chyb při vyřazení zpráv. Například můžete monitorovat D2C. telemetrie. odchozí. neplatné.

* Rozšíření zpráv se nevztahují na události digitální události změny.

## <a name="pricing"></a>Ceny

Rozšíření zpráv je k dispozici bez dalších poplatků. V současné době se vám budou účtovat poplatky za odeslání zprávy do IoT Hub. U této zprávy se účtují jenom jednou, a to i v případě, že zpráva přejde do více koncových bodů.

## <a name="next-steps"></a>Další kroky

Další informace o směrování zpráv do IoT Hub najdete v těchto článcích:

* [Kurz rozšíření zpráv](tutorial-message-enrichments.md)

* [Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](iot-hub-devguide-messages-d2c.md)

* [Kurz: IoT Hub směrování](tutorial-routing.md)
