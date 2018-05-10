---
title: Azure IoT Hub Glosář termínů | Microsoft Docs
description: Příručka vývojáře – Glosář běžných termínů týkající se Azure IoT Hub.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: aae72618da42db53304075506b4969945ff0165f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="glossary-of-iot-hub-terms"></a>Glosář termínů služby IoT Hub
V tomto článku jsou uvedeny některé z běžných termínů používaných v článcích IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Pokročilé Message Queueing Protocol
[Rozšířené zpráv služby Řízení front Protocol (AMQP)](https://www.amqp.org/) je jedním z zasílání zpráv protokoly, které [IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o zasílání zpráv protokolů, které podporuje Centrum IoT najdete v tématu [odesílat a přijímat zprávy službou IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Správa automatického zařízení
Automatická správa zařízení ve službě Azure IoT Hub automatizuje řadu opakovaných a složitější úlohy správy loďstev velké zařízení přes celého jejich životních cyklů. S automatickou správu zařízení můžete cílí na skupiny zařízení, na základě jejich vlastností, zadejte požadované konfigurace a nechat IoT Hub, aktualizujte si zařízení vždy, když se do oboru.  Se skládá z [konfigurací automatické zařízení](iot-hub-auto-device-config.md) a [IoT Edge automatické nasazení](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Konfigurace automatického zařízení
Můžete použít váš back-end řešení [konfigurací automatické zařízení](iot-hub-auto-device-config.md) přiřadit sadu požadovaných vlastností [dvojčata zařízení](#device-twin) a zprávy o stavu pomocí systému metriky a vlastní metriky. 

## <a name="azure-cli"></a>Azure CLI
[Rozhraní příkazového řádku Azure](../cli-install-nodejs.md) je nástroj příkaz napříč platformami, open source, založený na prostředí pro vytváření a správu prostředků v Microsoft Azure. Tato verze rozhraní příkazového řádku je implementovaná pomocí Node.js.

## <a name="azure-cli-20"></a>Azure CLI 2.0
[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) je nástroj příkaz napříč platformami, open source, založený na prostředí pro vytváření a správu prostředků v Microsoft Azure. Tato verze preview rozhraní příkazového řádku je implementovaná pomocí Python.


## <a name="azure-iot-device-sdks"></a>Azure SDK zařízení IoT
Existují _sady SDK pro zařízení_ k dispozici více jazyků, které vám umožní vytvořit [aplikací pro zařízení](#device-app) které komunikují s služby IoT hub. IoT Hub kurzy vám ukážou, jak používat tyto sady SDK zařízení. Zdrojový kód a další informace o zařízení sady SDK můžete najít v této Githubu [úložiště](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Služby sady SDK služby Azure IoT
Existují _služby sady SDK_ k dispozici více jazyků, které vám umožní vytvořit [back-end aplikace](#back-end-app) které komunikují s služby IoT hub. IoT Hub kurzy vám ukážou, jak používat tyto služby SDK. Zdrojový kód a další informace o sadách SDK služby můžete najít v této Githubu [úložiště](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-portal"></a>Azure Portal
[Portálu Microsoft Azure](https://portal.azure.com) je centrálním místem, kde můžete zřizovat a správě prostředků Azure. Slouží k uspořádání obsahu pomocí _okna_.

## <a name="azure-powershell"></a>Azure PowerShell
[Prostředí Azure PowerShell](/powershell/azure/overview) je kolekce rutiny můžete použít ke správě Azure pomocí prostředí Windows PowerShell. Můžete použít rutiny k vytváření, testování, nasadit a spravovat řešení a služby se doručují prostřednictvím platformy Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) umožňuje pracovat s prostředky ve vašem řešení jako se skupinou. Můžete nasadit, aktualizovat nebo odstranit prostředky pro vaše řešení v rámci jediné koordinované operace.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) poskytuje povolenou podporu cloudu komunikace s podnikových způsobů zasílání zpráv a přenosu komunikace, které vám pomůžou připojit místní řešení s cloudem. Některé služby IoT Hub kurzy využívat Service Bus [fronty](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Úložiště Azure](../storage/common/storage-introduction.md) je řešení cloudového úložiště. Zahrnuje službu úložiště objektů Blob, který můžete použít k ukládání nestrukturovaných dat objektů. Některé služby IoT Hub kurzy používat úložiště objektů blob.

## <a name="back-end-app"></a>Back-end aplikace
V kontextu [IoT Hub](#iot-hub), back-end aplikace je aplikace, která se připojuje k jeden z koncových bodů služby orientovaný na služby IoT hub. Například může načíst back-end aplikačním [zařízení cloud](#device-to-cloud)zprávy nebo spravovat [registru identit](#identity-registry). Obvykle back-end aplikace běží v cloudu, ale v mnoha kurzů k back-end aplikace jsou aplikace konzoly, spuštěna na místním vývojovém počítači.

## <a name="built-in-endpoints"></a>Integrované koncové body
Každé centrum IoT obsahuje integrované [koncový bod](iot-hub-devguide-endpoints.md) který je kompatibilní s centrem událostí. Můžete použít všechny mechanismu, který funguje s Event Hubs umožní číst zprávy typu zařízení cloud ze tento koncový bod.

## <a name="cloud-gateway"></a>Cloudová brána
Cloudové brány umožňuje připojení pro zařízení, která nelze se připojit přímo na [IoT Hub](#iot-hub). Cloudové brány je hostovaná v cloudu v rozdíl a [brána pole](#field-gateway) používající místní do vašeho zařízení. Typické použití případu pro cloudové brány je implementace překlad protokolu pro vaše zařízení.

## <a name="cloud-to-device"></a>Cloud-zařízení
Odkazuje na zprávy odeslané ze služby IoT hub na připojené zařízení. Tyto zprávy jsou často příkazy, které dáte pokyn zařízení o provedení akce. Další informace najdete v tématu [odesílat a přijímat zprávy službou IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfigurace
V kontextu [konfigurace automatického zařízení](iot-hub-auto-device-config.md), konfigurace v rámci služby IoT Hub definuje požadovanou konfiguraci pro skupinu zařízení dvojčata a poskytuje sadu metriky pro zprávy o stavu a průběhu.

## <a name="connection-string"></a>Připojovací řetězec
V kódu aplikace použijete připojovací řetězce pro zapouzdření informace požadované pro připojení ke koncovému bodu. Připojovací řetězec obvykle zahrnuje adresu koncového bodu a informace o zabezpečení, ale připojovací řetězec, který formáty lišit ve službách. Existují dva typy připojovacího řetězce, které jsou spojené s touto službou IoT Hub:
- *Připojovací řetězce zařízení* zařízením povolit, aby připojení k zařízení směřujících koncové body služby IoT hub.
- *Připojovací řetězce centra IoT* povolit back-end aplikace pro připojení ke koncovým bodům služby orientovaný na služby IoT hub.

## <a name="custom-endpoints"></a>Vlastní koncové body
Můžete vytvořit vlastní [koncové body](iot-hub-devguide-endpoints.md) na služby IoT hub pro doručování zpráv odeslaných pomocí [pravidlo směrování](#routing-rules). Vlastní koncové body připojit přímo do centra událostí, fronty sběrnice nebo téma sběrnice.

## <a name="custom-gateway"></a>Vlastní brány
Brána umožňuje připojení pro zařízení, která nelze se připojit přímo na [IoT Hub](#iot-hub). Můžete použít [Azure IoT Edge](#azure-iot-edge) vytvářet vlastní brány, které implementují vlastní logiky pro zpracování zprávy, vlastní protokol převody a další zpracování na okraj.

## <a name="data-point-message"></a>Zpráva datového bodu
Zpráva datového bodu [zařízení cloud](#device-to-cloud) zprávu, která obsahuje [telemetrie](#telemetry) data, jako jsou rychlosti větru nebo teploty.

## <a name="desired-configuration"></a>Požadované konfigurace
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), desired configuration odkazuje na úplnou sadu vlastností a metadata v dvojče zařízení, která má být synchronizována s zařízení.

## <a name="desired-properties"></a>Požadované vlastnosti
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), požadovaného vlastnosti je dílčí části dvojče zařízení, která se používá s [hlášené vlastnosti](#reported-properties) k synchronizaci konfigurace zařízení nebo podmínku. Požadované vlastnosti lze nastavit pouze [back-end aplikačním](#back-end-app) a jsou dodržovat [aplikaci zařízení](#device-app).

## <a name="device-to-cloud"></a>Zařízení-cloud
Odkazuje na zprávy odeslané z připojených zařízení a [IoT Hub](#iot-hub). Tyto zprávy mohou být [datového bodu](#data-point-message) nebo [interaktivní](#interactive-message) zprávy. Další informace najdete v tématu [odesílat a přijímat zprávy službou IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Zařízení
V kontextu IoT zařízení je obvykle malého rozsahu, samostatné výpočetní zařízení, které může shromažďovat data nebo jiné zařízení. Například zařízení může být zařízení s prostředí monitorování nebo řadičem pro napájení a ventilace systémy v skleníkových. [Zařízení katalogu](https://catalog.azureiotsuite.com/) obsahuje seznam hardwarových zařízení certifikovány pro práci s [IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplikace zařízení
Spuštění aplikace zařízení na vaše [zařízení](#device) a zpracovává komunikaci s vaší [služby IoT hub](#iot-hub). Obvykle, použijte jednu z [sady SDK pro zařízení Azure IoT](#azure-iot-device-sdks) při implementaci aplikace na zařízení. V mnoha kurzy IoT používáte [simulované zařízení](#simulated-device) ke zvýšení pohodlí.

## <a name="device-condition"></a>Stav zařízení
Odkazuje na informace o stavu zařízení, jako je například metoda připojení aktuálně používán vykazované [aplikaci zařízení](#device-app). [Aplikace pro zařízení](#device-app) může také nahlásit jejich možnosti. Pro informace o stavu a schopností pomocí dvojčata zařízení se můžete dotazovat.

## <a name="device-data"></a>Data v zařízení
Data zařízení odkazuje na zařízení data uložená ve službě IoT Hub [registru identit](#identity-registry). Je možné importovat a exportovat tato data.

## <a name="device-explorer"></a>Průzkumník zařízení
[Explorer zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) je nástroj, který běží v systému Windows a umožňuje je spravovat zařízení pomocí [registru identit](#identity-registry). Tento nástroj může také odesílat a přijímat zprávy do zařízení.

## <a name="device-identities-rest-api"></a>Rozhraní REST API identit zařízení
[Zařízení identity REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) umožňuje spravovat vaše zařízení registrovaná v [registru identit](#identity-registry) pomocí rozhraní REST API. Obvykle, měli byste použít jeden z vyšší úrovni [služby sady SDK](#azure-iot-service-sdks) jak je znázorněno v kurzech IoT Hub.

## <a name="device-identity"></a>Identita zařízení
Identita zařízení je jedinečný identifikátor přiřazený k každé zařízení registrovaná v [registru identit](#identity-registry).

## <a name="module-identity"></a>Modul identity
Identita modulu je jedinečný identifikátor přiřazený k každý modul, který patří do zařízení. Modul identity se registruje v [registru identit](#identity-registry).

## <a name="device-management"></a>Správa zařízení
Správa zařízení zahrnuje celý životní spojené se správou zařízení ve vašem řešení IoT, včetně plánování, zřizování, konfiguraci, monitorování a vyřazení.

## <a name="device-management-patterns"></a>Schémata správy zařízení
[Centrum IoT](#iot-hub) umožňuje běžné způsoby správy zařízení včetně restartování, provádění obnovení továrního nastavení a provádění aktualizace firmwaru v zařízení.

## <a name="device-messaging-rest-api"></a>Rozhraní REST API zasílání zpráv zařízení
Můžete použít [rozhraní API REST pro zasílání zpráv zařízení](https://docs.microsoft.com/rest/api/iothub/httpruntime) ze zařízení k odesílání zpráv typu zařízení cloud do služby IoT hub a příjmu [cloud zařízení](#cloud-to-device) zpráv ze služby IoT hub. Obvykle, měli byste použít jeden z vyšší úrovni [sady SDK pro zařízení](#azure-iot-device-sdks) jak je znázorněno v kurzech IoT Hub.

## <a name="device-provisioning"></a>Zřizování zařízení
Zřizování zařízení je proces přidávání počáteční [data zařízení](#device-data) do úložišť ve vašem řešení. Pokud chcete povolit nové zařízení pro připojení do vašeho centra, musíte přidat ID zařízení a klíče do služby IoT Hub [registru identit](#identity-registry). Jako součást procesu zřizování může být potřeba inicializovat data specifická pro zařízení v jiné řešení úložiště.

## <a name="device-twin"></a>Dvojče zařízení
A [dvojče zařízení](iot-hub-devguide-device-twins.md) je dokument JSON, který ukládá informace o stavu zařízení například metadata, konfigurace a podmínky. [IoT Hub](#iot-hub) potrvají dvojče zařízení pro každé zařízení, která zřídit ve službě IoT hub. Dvojčata zařízení umožňují synchronizovat [zařízení podmínky](#device-condition) a konfigurací mezi zařízením a řešení back-end. Můžete dotazovat dvojčata zařízení dotaz na stav dlouhotrvající operace a vyhledat konkrétní zařízení.

## <a name="module-twin"></a>Modul twin
Podobně jako u dvojče zařízení, modul twin je dokument JSON, který ukládá informace o stavu modulu například metadata, konfigurace a podmínky. IoT Hub trvá twin modulu pro každou identitu modulu, který zřídíte pod identitou zařízení ve službě IoT hub. Modul dvojčata umožňují synchronizovat modul podmínky a konfigurací mezi modul a back-end řešení. Modul dvojčata vyhledat konkrétní moduly a dotaz na stav dlouhotrvající operace se můžete dotazovat.

## <a name="twin-queries"></a>Dotazy Twin
[Zařízení a modul dotazů twin](iot-hub-devguide-query-language.md) použít k načtení informací z dvojčata zařízení nebo modul dvojčata dotazovacího jazyka SQL jako IoT Hub. Stejný dotaz jazyk IoT Hub můžete použít k načtení informací o [úlohy](#job) spuštěná ve službě IoT hub.

## <a name="device-twin-rest-api"></a>Rozhraní API REST Twin zařízení
Můžete použít [zařízení Twin REST API](https://docs.microsoft.com/rest/api/iothub/devicetwinapi) z řešení back-end ke správě vašeho dvojčata zařízení. Rozhraní API umožňuje načíst a aktualizovat [dvojče zařízení](#device-twin) vlastnosti a vyvolání [přímé metody](#direct-method). Obvykle, měli byste použít jeden z vyšší úrovni [služby sady SDK](#azure-iot-service-sdks) jak je znázorněno v kurzech IoT Hub.

## <a name="twin-synchronization"></a>Synchronizace Twin
Twin synchronizace používá [potřeby vlastnosti](#desired-properties) v dvojčata zařízení nebo modul dvojčata ke konfiguraci zařízení nebo moduly a načíst [hlášené vlastnosti](#reported-properties) z nich uložit twin.

## <a name="direct-method"></a>Přímá metoda
A [přímá metoda](iot-hub-devguide-direct-methods.md) je způsob můžete aktivovat metodu spusťte na zařízení tak, že volání rozhraní API ve službě IoT hub.

## <a name="endpoint"></a>Koncový bod
IoT hub zpřístupní více [koncové body](iot-hub-devguide-endpoints.md) které umožňují vaší aplikace pro připojení ke službě IoT hub. Koncové body směřujících zařízení, které umožňují zařízení k provádění operací, například odeslání [zařízení cloud](#device-to-cloud) zpráv a příjem [cloud zařízení](#cloud-to-device) zprávy. Koncové body správy orientované na služby, které umožňují [back-end aplikace](#back-end-app) k provádění operací, jako [identitu zařízení](#device-identity) správu a twin správy zařízení. Existují služby směřujících [předdefinované koncové body](#built-in-endpoints) pro čtení zpráv typu zařízení cloud. Můžete vytvořit [vlastní koncové body](#custom-endpoints) pro příjem zpráv typu zařízení cloud se odesílají prostřednictvím [pravidlo směrování](#routing-rules).

## <a name="event-hubs-service"></a>Služba centra událostí
[Služba Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) je služba příchozího vysoce škálovatelné data, která dokáže přijímat miliony událostí za sekundu. Služba umožňuje zpracovávat a analyzovat masivní objemy dat vytvářených připojených zařízení a aplikací. Porovnání se službou IoT Hub najdete v tématu [porovnání služeb Azure IoT Hub a Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Koncový bod kompatibilní s centrem událostí
Číst [zařízení cloud](#device-to-cloud) zprávy odeslané do služby IoT hub můžete připojit k koncový bod na rozbočovače a použít libovolnou metodu kompatibilní s centrem událostí ke čtení těchto zpráv. Zahrnout metody kompatibilní s centrem událostí pomocí [SDK centra událostí](../event-hubs/event-hubs-programming-guide.md) a [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Brána pole
Brána pole umožňuje připojení pro zařízení, která nelze se připojit přímo na [IoT Hub](#iot-hub) a je obvykle nasazena místně ve vašich zařízeních. Další informace najdete v tématu [co je Azure IoT Hub?](iot-hub-what-is-iot-hub.md)

## <a name="free-account"></a>Bezplatný účet
Můžete vytvořit [bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/) k dokončení kurzy IoT Hub a experimentovat s službu IoT Hub (a jinými službami Azure).

## <a name="gateway"></a>brána
Brána umožňuje připojení pro zařízení, která nelze se připojit přímo na [IoT Hub](#iot-hub). Viz také [pole brány](#field-gateway), [cloudové brány](#cloud-gateway), a [vlastní brány](#custom-gateway).

## <a name="identity-registry"></a>Registr identit
[Registru identit](iot-hub-devguide-identity-registry.md) k připojení do služby IoT hub je povoleno integrované komponenta služby IoT hub, který obsahuje informace o jednotlivých zařízeních.

## <a name="interactive-message"></a>Interaktivní zprávy
Interaktivní zpráva [cloud zařízení](#cloud-to-device) zprávu, která spustí okamžitou akci v back-end řešení. Zařízení může například odeslat alarm o selhání, který by měl být automaticky přihlášeni k systému CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
Back-end řešení IoT Hub je plně spravovaná služba Azure, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení. Další informace najdete v tématu [co je Azure IoT Hub?](iot-hub-what-is-iot-hub.md) Pomocí vašeho [předplatného Azure](#subscription), můžete vytvořit centra IoT pro zpracování vaše IoT úlohy pro zasílání zpráv.

## <a name="iot-hub-metrics"></a>Metriky služby IoT Hub
[IoT Hub metriky](iot-hub-metrics.md) poskytují údaje o stavu služby IoT hubs ve vaší [předplatného Azure](#subscription). IoT Hub metriky umožňují posouzení celkového stavu služby a zařízení k němu připojená. IoT Hub metriky můžete zobrazit, co se děje službou IoT hub a zkoumání příčin problémů bez nutnosti požádejte podporu Azure.

## <a name="iot-hub-query-language"></a>IoT Hub dotazovací jazyk
[IoT Hub dotazovací jazyk](iot-hub-devguide-query-language.md) je podobné jazyku SQL jazyk, který vám umožňuje dotazu vaše [úlohy](#job) a dvojčata zařízení.

## <a name="iot-hub-resource-provider-rest-api"></a>Zprostředkovatel prostředků služby IoT Hub rozhraní REST API
Můžete použít [IoT Hub prostředků zprostředkovatele REST API](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) ke správě centra IoT v vaše [předplatného Azure](#subscription) provádění operací, jako je například vytváření, aktualizace a odstranění rozbočovače.

## <a name="iot-solution-accelerators"></a>Akcelerátorů řešení IoT
Azure IoT řešení akcelerátorů balíček společně více služeb Azure do řešení. Tato řešení umožňují rychle začít používat začátku do konce implementace běžné scénáře IoT. Další informace najdete v tématu [co jsou akcelerátorů řešení Azure IoT?](../iot-suite/iot-suite-overview.md)

## <a name="the-iot-extension-for-azure-cli-20"></a>Rozšíření IoT pro Azure CLI 2.0
[Rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) je nástroj pro různé platformy, příkazového řádku. Tento nástroj umožňuje spravovat zařízení pomocí [registru identit](#identity-registry), odesílání a příjem zpráv a soubory z vašich zařízení a sledovat vaše operace centra IoT.

## <a name="job"></a>Úloha
Můžete použít váš back-end řešení [úlohy](iot-hub-devguide-jobs.md) k plánování a sledování činností na sadu zařízení registrovaná službou IoT hub. Aktivity zahrnují aktualizace dvojče zařízení [potřeby vlastnosti](#desired-properties), aktualizuje dvojče zařízení [značky](#tags)a vyvolání [přímé metody](#direct-method). [IoT Hub](#iot-hub) také používá úloh [pro import a export](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [registru identit](#identity-registry).

## <a name="jobs-rest-api"></a>Úlohy rozhraní REST API
[Úloh REST API](https://docs.microsoft.com/rest/api/iothub/jobapi) umožňuje spravovat [úlohy](#job) spuštěná ve službě IoT hub.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) je jedním z zasílání zpráv protokoly, které [IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o zasílání zpráv protokolů, které podporuje Centrum IoT najdete v tématu [odesílat a přijímat zprávy službou IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorování operací
IoT Hub [operations monitorování](iot-hub-operations-monitoring.md) umožňuje sledovat stav operace ve službě IoT hub v reálném čase. [IoT Hub](#iot-hub) sleduje události napříč několika kategorií operací. Můžete se taky rozhodnout do odesílání událostí z jedné nebo více kategorií pro koncový bod služby IoT Hub pro zpracování. Můžete sledovat data chyby nebo nastavit složitější zpracování vámi data.

## <a name="physical-device"></a>Fyzické zařízení
Fyzické zařízení je skutečné zařízení, jako je například malin platformy, která se připojuje ke službě IoT hub. Pro usnadnění práce řadu kurzy IoT Hub pomocí [simulované zařízení](#simulated-device) umožnit ke spuštění ukázky v místním počítači.

## <a name="primary-and-secondary-keys"></a>Primární a sekundární klíče
Při připojení k zařízení přístupem nebo přístupem služby koncového bodu na centrum IoT, vaše [připojovací řetězec](#connection-string) zahrnuje klíč udělit přístup. Když přidáte zařízení a [registru identit](#identity-registry) nebo přidejte [sdílené zásady přístupu](#shared-access-policy) do vašeho centra služby vygeneruje primární a sekundární klíč. Dva klíče, můžete přejít z jednoho klíče na jiné při aktualizaci klíče bez ztráty přístupu ke službě IoT hub.

## <a name="protocol-gateway"></a>Brána protokolu
Brána protokolu je obvykle nasazený v cloudu a poskytuje protokol služby překladu pro zařízení připojující se k [IoT Hub](#iot-hub). Další informace najdete v tématu [co je Azure IoT Hub?](iot-hub-what-is-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóty a omezování
Existují různé [kvóty](iot-hub-devguide-quotas-throttling.md) které se týkají používání [IoT Hub](#iot-hub), řadu kvóty lišit v závislosti na vrstvě služby IoT hub. [IoT Hub](#iot-hub) platí také [omezí generovaný](iot-hub-devguide-quotas-throttling.md) k používání služby za běhu.

## <a name="reported-configuration"></a>Hlášené konfigurace
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), hlášené konfigurace odkazuje na úplnou sadu vlastností a metadata v dvojče zařízení, která by měla být oznámeny back-end řešení.

## <a name="reported-properties"></a>Hlášené vlastnosti
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), oznámila vlastnosti je dílčí části dvojče zařízení použít s [požadovaných vlastností](#desired-properties) k synchronizaci konfigurace zařízení nebo podmínku. Hlášené vlastnosti lze nastavit pouze [aplikaci zařízení](#device-app) a můžete číst a zadají dotaz [back-end aplikačním](#back-end-app).

## <a name="resource-group"></a>Skupina prostředků
[Azure Resource Manager](#azure-resource-manager) používá k seskupení souvisejících prostředků skupiny prostředků. Skupiny prostředků můžete použít k provádění operací na všechny prostředky ve skupině současně.

## <a name="retry-policy"></a>Zásady opakování
Použijte zásady opakovaných pokusů pro zpracování [přechodné chyby](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) při připojení ke cloudové službě.

## <a name="routing-rules"></a>Pravidla směrování
Nakonfigurujete [pravidla směrování](iot-hub-devguide-messages-read-custom.md) ve službě IoT hub pro směrování zpráv typu zařízení cloud do [vestavěným koncovým bodem](#built-in-endpoints) nebo [vlastní koncové body](#custom-endpoints) pro zpracování v back end vašeho řešení .

## <a name="sasl-plain"></a>PROSTÝ SASL
PROSTÝ SASL je protokol, který [AMQP](#advanced-message-queue-protocol) protokol používá k přenosu tokeny zabezpečení.

## <a name="shared-access-signature"></a>Sdílený přístupový podpis
Podpisy sdíleného přístupu (SAS) se mechanismus ověřování na základě zabezpečeného hodnoty hash SHA-256 nebo identifikátory URI. Ověřování SAS má dvě součásti: _zásada sdíleného přístupu_ a _sdíleného přístupového podpisu_ (často říká token). Zařízení používá SAS k ověření pomocí služby IoT hub. [Back-end aplikace](#back-end-app) také použít SAS k ověření koncových bodů služby orientovaný na služby IoT hub. Obvykle zahrnují tokenu SAS v [připojovací řetězec](#connection-string) , některá aplikace používá k navázání připojení do služby IoT hub.

## <a name="shared-access-policy"></a>Zásada sdíleného přístupu
Oprávnění udělená každý, kdo má platnou definuje zásady sdíleného přístupu [primární nebo sekundární klíč](#primary-and-secondary-keys) přidružené k této zásadě. Můžete spravovat zásady sdíleného přístupu a klíče pro vaše centrum v [portál](#azure-portal).

## <a name="simulated-device"></a>Simulované zařízení
Pro usnadnění práce řadu kurzy IoT Hub Simulovaná zařízení použít k povolení spouštět ukázky na místním počítači. Naproti tomu [fyzického zařízení](#physical-device) je skutečné zařízení, jako je například malin platformy, která se připojuje ke službě IoT hub.

## <a name="solution"></a>Řešení
A _řešení_ mohou odkazovat na řešení sady Visual Studio, která obsahuje jeden nebo více projektů. A _řešení_ může se také podívat na řešení IoT, který obsahuje prvky, jako jsou například zařízení, [aplikací pro zařízení](#device-app), služby IoT hub, jinými službami Azure a [back-end aplikace](#back-end-app).

## <a name="subscription"></a>Předplatné
Předplatné Azure je, kde probíhá fakturace. Každý prostředek Azure vytvoříte nebo je přidružen k jednomu předplatnému služby Azure, které používáte. Mnoho kvóty vztahovat na úrovni předplatného.

## <a name="system-properties"></a>Systémové vlastnosti
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), vlastnosti systému jsou jen pro čtení a zahrnují informace o využití zařízení, jako je například poslední stav aktivity čas a připojení.

## <a name="tags"></a>Značky
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), značky jsou zařízení metadata uložená a načíst back-end řešení v podobě dokumentu JSON. Značky nejsou viditelné pro aplikace na zařízení.

## <a name="telemetry"></a>Telemetrická data
Shromažďování telemetrických dat, jako je například rychlosti větru nebo teploty, zařízení a používat [data bodu zprávy](#data-point-messages) k odesílání telemetrie do služby IoT hub.

## <a name="token-service"></a>Služba tokenu
Token služby můžete použít k implementaci ověřovací mechanismus pro vaše zařízení. Používá služby IoT Hub [sdílené zásady přístupu](#shared-access-policy) s **DeviceConnect** oprávnění k vytvoření *obor zařízení* tokeny. Tyto tokeny povolit zařízení pro připojení do služby IoT hub. Zařízení používá k ověření pomocí služby tokenů vlastní ověřování. Pokud zařízení úspěšně ověří, služba tokenu vydá token SAS pro zařízení, které chcete používat pro přístup k službě IoT hub.

## <a name="x509-client-certificate"></a>Klientský certifikát X.509
Zařízení můžete k ověřování použít certifikát X.509 [IoT Hub](#iot-hub). Pomocí certifikátu X.509. certifikát je alternativu k použití [tokenu SAS](#shared-access-signature).
