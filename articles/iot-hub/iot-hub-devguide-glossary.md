---
title: Azure IoT Hub Glosář termínů | Dokumentace Microsoftu
description: Příručka pro vývojáře – Glosář běžných termínů vztahující se ke službě Azure IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: dobett
ms.openlocfilehash: 3725117b90ec2574737686881e47967f3d9a9e39
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320082"
---
# <a name="glossary-of-iot-hub-terms"></a>Glosář termínů služby IoT Hub
Tento článek uvádí některé běžné pojmy používané v článcích služby IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Rozšířené zprávy protokolu zařazení do fronty
[Pokročilé protokolu služby Řízení front zpráv (AMQP)](https://www.amqp.org/) je jeden z zasílání zpráv, které protokoly [služby IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o protokoly zasílání zpráv, které podporuje službu IoT Hub najdete v tématu [odesílání a příjem zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatická správa zařízení
Automatická správa zařízení ve službě Azure IoT Hub umožňuje automatizovat mnoho opakovaných a složité úlohy správy flotily nebo velké zařízení přes celého jejich životního cyklu. Automatická správa zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definují požadovanou konfiguraci a nechat pokaždé, když se do rozsahu sem přišli aktualizaci zařízení služby IoT Hub.  Se skládá z [automatické konfigurace](iot-hub-auto-device-config.md) a [automatické nasazení IoT Edge](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Konfigurace automatického zařízení
Back-endem řešení můžete použít [automatické konfigurace](iot-hub-auto-device-config.md) přiřadit sadu požadovaných vlastností [dvojčata zařízení](#device-twin) a hlášení stavu pomocí systémové metriky a vlastní metriky. 

## <a name="azure-classic-cli"></a>Azure Classic CLI
[Klasické rozhraní příkazového řádku Azure](../cli-install-nodejs.md) je nástroj pro různé platformy, open source, na základě prostředí, příkaz k vytváření a správě prostředků v Microsoft Azure. Tato verze rozhraní příkazového řádku by měla sloužit pro pouze nasazení classic.

## <a name="azure-cli"></a>Azure CLI
[Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) je nástroj pro různé platformy, open source, na základě prostředí, příkaz k vytváření a správě prostředků v Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>Azure IoT SDK pro zařízení
Existují _sady SDK pro zařízení_ dostupné pro různé jazyky, které vám umožní vytvořit [aplikací pro zařízení](#device-app) , které komunikovat s centrem IoT. Kurzy služby IoT Hub ukazují, jak používat tyto sady SDK pro zařízení. V tomto Githubu můžete najít zdrojový kód a další informace o sadách SDK pro zařízení [úložiště](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Sady SDK pro služby Azure IoT
Existují _služby sady SDK_ dostupné pro různé jazyky, které vám umožní vytvořit [back endové aplikace](#back-end-app) , které komunikovat s centrem IoT. IoT Hub kurzy ukazují, jak pomocí sady SDK pro tyto služby. V tomto Githubu můžete najít zdrojový kód a další informace o sadách SDK služby [úložiště](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT Tools
[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) je různé platformy, open source rozšíření Visual Studio Code, který vám pomůže spravovat zařízení v nástroji VS Code a Azure IoT Hub. Nástroje pro Azure IoT může IoT vývojáři vyvíjet pro projekty IoT v nástroji VS Code s lehkostí a elegancí.

## <a name="azure-portal"></a>portál Azure
[Portálu Microsoft Azure](https://portal.azure.com) je centrálním místem, kde můžete zřizovat a spravovat prostředky Azure. Slouží k uspořádání obsahu pomocí _oken_.

## <a name="azure-powershell"></a>Azure PowerShell
[Prostředí Azure PowerShell](/powershell/azure/overview) je sada rutin, které můžete použít ke správě Azure pomocí Windows Powershellu. Můžete použít rutiny k vytváření, testování, nasazení a správu řešení a služeb poskytovaných prostřednictvím platformy Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) umožňuje pracovat s prostředky ve vašem řešení jako se skupinou. Můžete nasadit, aktualizovat nebo odstranit prostředky pro vaše řešení v rámci jediné koordinované operace.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) poskytuje cloudovou komunikaci s podnikovým zasíláním zpráv a komunikaci přes předávací službu, která vám pomůže propojit místní řešení s cloudem. Některé služby IoT Hub kurzy využívají služby Service Bus [fronty](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md) je řešení cloudového úložiště. Zahrnuje ve službě Blob Storage, který můžete použít k uložení nestrukturovaných dat objektů. Nějaké kurzy služby IoT Hub pomocí úložiště objektů blob.

## <a name="back-end-app"></a>Back endové aplikace
V rámci [služby IoT Hub](#iot-hub), back endové aplikace je aplikace, která se připojuje k jednomu z koncových bodů určených pro služby ve službě IoT hub. Například může načíst back endové aplikace [typu zařízení cloud](#device-to-cloud) zprávy nebo spravovat [registr identit](#identity-registry). Obvykle back endové aplikace spouští v cloudu, ale v mnoha z kurzů back endové aplikace jsou aplikace konzoly spuštěné v místním vývojovém počítači.

## <a name="built-in-endpoints"></a>Integrované koncové body
Každé centrum IoT obsahuje integrovaný [koncový bod](iot-hub-devguide-endpoints.md) , který je kompatibilní s centrem událostí. Můžete použít libovolný mechanismus, který funguje s Event Hubs slouží ke čtení zpráv typu zařízení cloud z tohoto koncového bodu.

## <a name="cloud-gateway"></a>Cloudová brána
Cloudová brána umožňuje připojení pro zařízení, která se nemůže připojit přímo na [služby IoT Hub](#iot-hub). Cloudové brány je hostované v cloudu rozdíl od [hraniční brána](#field-gateway) , který spustí místní do zařízení. Typické použití případu pro cloudové brány je provádět překlad protokolu pro vaše zařízení.

## <a name="cloud-to-device"></a>Cloud-zařízení
Odkazuje na zprávy odeslané ze služby IoT hub na připojené zařízení. Tyto zprávy jsou často, příkazy, které dáte pokyn, aby zařízení a provést akci. Další informace najdete v tématu [odesílání a příjem zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfigurace
V rámci [automatické konfigurace](iot-hub-auto-device-config.md), konfigurace ve službě IoT Hub definují požadovanou konfiguraci pro dvojčata a poskytuje sadu metrik pro zprávy o stavu a průběhu sadu zařízení.

## <a name="connection-string"></a>Připojovací řetězec
Použít připojovací řetězce v kódu vaší aplikace k zapouzdření informace požadované pro připojení na koncový bod. Připojovací řetězec obvykle obsahuje adresu koncového bodu a informace o zabezpečení, ale připojovací řetězec, který formáty lišit napříč službami. Existují dva typy připojovacího řetězce související se službou IoT Hub:
- *Zařízení připojovací řetězce* zařízením povolit, aby připojení ke koncovým bodům určených pro zařízení ve službě IoT hub.
- *Připojovací řetězce centra IoT* povolit back endové aplikace pro připojení ke koncovým bodům služby určené ve službě IoT hub.

## <a name="custom-endpoints"></a>Vlastní koncové body
Můžete vytvořit vlastní [koncové body](iot-hub-devguide-endpoints.md) ve službě IoT hub doručení zprávy odesílané [pravidlo směrování](#routing-rules). Vlastní koncové body připojení přímo do centra událostí, fronty Service Bus nebo téma služby Service Bus.

## <a name="custom-gateway"></a>Vlastní bránu
Brána umožňuje propojení pro zařízení, která se nemůže připojit přímo na [služby IoT Hub](#iot-hub). Můžete použít [Azure IoT Edge](#azure-iot-edge) vytvářet vlastní brány, které implementují vlastní logiku ke zpracování zprávy, vlastní protokol převody a další zpracování na hraničních zařízeních.

## <a name="data-point-message"></a>Zpráva datového bodu
Zpráva datový bod [typu zařízení cloud](#device-to-cloud) zprávu, která obsahuje [telemetrie](#telemetry) data, jako jsou rychlost větru nebo teploty.

## <a name="desired-configuration"></a>Požadované konfigurace
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), požadované konfigurace odkazuje na kompletní sadu vlastností a metadat ve dvojčeti zařízení, která se mají synchronizovat zařízení.

## <a name="desired-properties"></a>Požadované vlastnosti
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), požadované vlastnosti je dílčí část objektu dvojče zařízení, která se použije [ohlášené vlastnosti](#reported-properties) k synchronizaci konfigurace zařízení nebo podmínku. Požadované vlastnosti lze nastavit pouze [back endové aplikace](#back-end-app) a jsou dodržovat [aplikace pro zařízení s](#device-app).

## <a name="device-to-cloud"></a>Zařízení-cloud
Odkazuje na zprávy odeslané z připojených zařízení a [služby IoT Hub](#iot-hub). Tyto zprávy mohou být [datový bod](#data-point-message) nebo [interaktivní](#interactive-message) zprávy. Další informace najdete v tématu [odesílání a příjem zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Zařízení
V souvislosti s IoT se zařízením je obvykle rozsáhlé, samostatné výpočetní zařízení, které může shromažďovat data nebo ovládání zařízení. Zařízení může být například prostředí monitorování zařízení, nebo kontroleru pro útok typu watering a ventilace systémy skleníkových. [Katalog zařízení](https://catalog.azureiotsuite.com/) obsahuje seznam hardwarových zařízení certifikovány pro práci s [služby IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplikace pro zařízení
Aplikace pro zařízení běží na vašich [zařízení](#device) a zpracovává komunikaci s vaší [služby IoT hub](#iot-hub). Obvykle, použijte jednu z [sady SDK pro zařízení Azure IoT](#azure-iot-device-sdks) při implementaci aplikace pro zařízení. V mnoha kurzy IoT používáte [simulovaného zařízení](#simulated-device) ke zvýšení pohodlí.

## <a name="device-condition"></a>Stav zařízení
Odkazuje na informace o stavu zařízení, například pro metodu připojení momentálně používané udávaný rutinou [aplikace pro zařízení s](#device-app). [Aplikace pro zařízení](#device-app) může také nahlásit jejich schopnosti. Můžete zadat dotaz na informace o podmínku a možnost použití dvojčat zařízení.

## <a name="device-data"></a>Data zařízení
Zařízení data odkazují na jednotlivá zařízení data uložená ve službě IoT Hub [registr identit](#identity-registry). Je možné pro import a export tato data.

## <a name="device-explorer"></a>Device Explorer
[Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) je nástroj, který běží na Windows a umožňuje spravovat vaše zařízení [registr identit](#identity-registry). Nástroj může také odesílání a příjem zpráv pro vaše zařízení.

## <a name="device-identity"></a>Identita zařízení
Identita zařízení je jedinečný identifikátor přiřazený do všech zařízení zaregistrované v [registr identit](#identity-registry).

## <a name="device-management"></a>Správa zařízení
Správa zařízení zahrnuje celý životní cyklus spojené se správou zařízení ve vašem řešení IoT, včetně plánování, zřízení, konfigurace, monitorování a vyřazování z provozu.

## <a name="device-management-patterns"></a>Schémata správy zařízení
[Služby IoT hub](#iot-hub) umožňuje běžných schémat správy zařízení, včetně restartování, provádět obnovení továrního nastavení a provádění aktualizací firmwaru v zařízeních.

## <a name="device-rest-api"></a>Rozhraní REST API pro zařízení
Můžete použít [rozhraní REST API zařízení](https://docs.microsoft.com/rest/api/iothub/device) ze zařízení k odesílání zpráv typu zařízení cloud do služby IoT hub a příjem [typu cloud zařízení](#cloud-to-device) zpráv ze služby IoT hub. Obvykle, měli použít jeden z vyšší úrovni [sady SDK pro zařízení](#azure-iot-device-sdks) jak je znázorněno v kurzech služby IoT Hub.

## <a name="device-provisioning"></a>Zřizování zařízení
Zřizování zařízení je proces přidávání počáteční [data zařízení](#device-data) do úložišť ve vašem řešení. Povolit nové zařízení pro připojení k centru, je nutné přidat ID zařízení a klíče do služby IoT Hub [registr identit](#identity-registry). Jako součást procesu zřizování může být potřeba inicializovat data specifická pro zařízení v jiných úložištích řešení.

## <a name="device-twin"></a>Dvojče zařízení
A [dvojče zařízení](iot-hub-devguide-device-twins.md) je dokument JSON, který ukládá informace o stavu zařízení jako jsou metadata, konfigurace a podmínky. [IoT Hub](#iot-hub) udržuje takové dvojče pro každé zařízení, zřídíte ve službě IoT hub. Dvojčata zařízení umožní synchronizaci [zařízení podmínky](#device-condition) a konfigurací mezi zařízením a řešení back-endu. Dvojčata zařízení k vyhledání konkrétních zařízení a zjistit stav dlouhotrvající operace se můžete dotazovat.

## <a name="direct-method"></a>Přímá metoda
A [přímá metoda](iot-hub-devguide-direct-methods.md) je způsob, jakým můžete aktivovat metodu ke spuštění na zařízení prostřednictvím volání rozhraní API ve službě IoT hub.

## <a name="endpoint"></a>Koncový bod
Služba IoT hub zpřístupní více [koncové body](iot-hub-devguide-endpoints.md) umožňující, aby vaše aplikace pro připojení ke službě IoT hub. Koncové body s přístupem k zařízení, které umožňují zařízení k provádění operací, jako je odeslání [typu zařízení cloud](#device-to-cloud) zpráv a příjem [typu cloud zařízení](#cloud-to-device) zprávy. Existují koncových bodů správy určených pro služby, které umožňují [back endové aplikace](#back-end-app) k provádění operací, jako [identitu zařízení](#device-identity) správy dvojče zařízení a správy. Existují určených pro službu [integrovaných koncových bodech](#built-in-endpoints) pro čtení zpráv typu zařízení cloud. Můžete vytvořit [vlastní koncové body](#custom-endpoints) pro příjem zpráv typu zařízení cloud odesílané [pravidlo směrování](#routing-rules).

## <a name="event-hubs-service"></a>Službu Event Hubs
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) je vysoce škálovatelná datové služby příchozího přenosu dat, která dokáže zpracovat miliony událostí za sekundu. Služba umožňuje zpracovávat a analyzovat masivní objemy dat vytvářené vašimi připojenými zařízeními a aplikacemi. Porovnání se službou IoT Hub najdete v tématu [porovnání služeb Azure IoT Hub a Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Koncový bod kompatibilní s centrem událostí
Přečíst [typu zařízení cloud](#device-to-cloud) zprávy odeslané do služby IoT hub, můžete připojit k koncový bod na rozbočovače a číst tyto zprávy použít libovolnou metodu kompatibilní s centrem událostí. Zahrnují metody kompatibilního s centrem událostí pomocí [sady SDK pro Event Hubs](../event-hubs/event-hubs-programming-guide.md) a [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Brána pole
Hraniční brána umožňuje propojení pro zařízení, která se nemůže připojit přímo na [služby IoT Hub](#iot-hub) a je obvykle nasazena místně ve vašich zařízeních. Další informace najdete v tématu [co je Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Bezplatný účet
Můžete vytvořit [bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/) dokončování služby IoT Hub a experimentovat s služby IoT Hub (a dalších služeb Azure).

## <a name="gateway"></a>brána
Brána umožňuje propojení pro zařízení, která se nemůže připojit přímo na [služby IoT Hub](#iot-hub). Viz také [pole brány](#field-gateway), [cloudu brány](#cloud-gateway), a [vlastní bránu](#custom-gateway).

## <a name="identity-registry"></a>Registr identit
[Registr identit](iot-hub-devguide-identity-registry.md) integrované komponenty služby IoT hub, která uchovává informace o jednotlivých zařízeních oprávnění k připojení do služby IoT hub.

## <a name="interactive-message"></a>Interaktivní okno zprávy
Interaktivní zpráva [typu cloud zařízení](#cloud-to-device) zprávu, která spustí okamžitou akci v back-endu řešení. Zařízení třeba může odesílat alarm o selhání, které by měl být automaticky přihlášeni do systému CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT Hub je plně spravovaná služba Azure, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení. Další informace najdete v tématu [co je Azure IoT Hub?](about-iot-hub.md) Pomocí vaší [předplatného Azure](#subscription), můžete vytvořit IoT hub pro zpracování IoT úloh zasílání zpráv.

## <a name="iot-hub-metrics"></a>Metriky služby IoT Hub
[Metriky služby IoT Hub](iot-hub-metrics.md) poskytují data o stavu centra IoT hub ve vašich [předplatného Azure](#subscription). Metriky služby IoT Hub umožňují posouzení celkového stavu služby a zařízení k němu připojená. Metriky služby IoT Hub můžete vidět, co se děje ve vašem Centru IoT a zkoumání příčin problémů bez nutnosti kontaktovat podporu Azure.

## <a name="iot-hub-query-language"></a>Dotazovací jazyk služby IoT Hub
[Dotazovací jazyk služby IoT Hub](iot-hub-devguide-query-language.md) je jazyce podobném jazyku SQL, která vám umožní dotazu vaše [](#job) a dvojčata zařízení.

## <a name="iot-hub-resource-rest-api"></a>Zdroj rozhraní REST API služby IoT Hub
Můžete použít [rozhraní REST API služby IoT Hub prostředků](https://docs.microsoft.com/rest/api/iothub/iothubresource) ke správě centra IoT hub ve vaší [předplatného Azure](#subscription) provádění operací, jako je vytváření, aktualizaci a odstraňování rozbočovače.

## <a name="iot-solution-accelerators"></a>Akcelerátory řešení IoT
Akcelerátory řešení Azure IoT zabalit společně několik služeb Azure do řešení. Tato řešení umožňují rychle začít s implementací začátku do konce běžné scénáře IoT. Další informace najdete v tématu [co jsou akcelerátory řešení Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Rozšíření IoT pro Azure CLI 
[Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) je nástroj pro různé platformy, příkazového řádku. Nástroj umožňuje spravovat vaše zařízení [registr identit](#identity-registry), odesílání a příjem zpráv a souborů ze zařízení a monitorovat vaše operace centra IoT.

## <a name="job"></a>Úloha
Back-endem řešení můžete použít [úlohy](iot-hub-devguide-jobs.md) k plánování a sledování aktivit na sadu zařízení registrovaná ve službě IoT hub. Aktivity zahrnout aktualizace dvojčete zařízení [požadované vlastnosti](#desired-properties), aktualizace dvojčete zařízení [značky](#tags)a vyvolání [přímé metody](#direct-method). [IoT Hub](#iot-hub) používá také k [importovat a exportovat](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [registr identit](#identity-registry).

## <a name="modules"></a>Moduly
Sady SDK pro zařízení služby IoT Hub na straně zařízení umožňují vytvářet [moduly](iot-hub-devguide-module-twins.md) kde každý z nich se otevře nezávislé připojení ke službě IoT Hub. Tato funkce umožňuje používat samostatné obory názvů pro různé součásti na vašem zařízení.

Modul identity a dvojče zařízení poskytují stejné funkce jako [identitu zařízení](#device-identity) a [dvojče zařízení](#device-twin) ale podrobnější rozlišovací schopnosti. Tento rozlišovací schopnosti povolí podporuje zařízení, jako jsou založené na operační systém nebo zařízení firmwaru správy více komponenty a izolovat konfigurace a podmínky pro každou z těchto komponent.

## <a name="module-identity"></a>Modul identity
Identita modulu je jedinečný identifikátor přiřazený každého modulu, který patří do zařízení. Modul identity je zaregistrovaná taky v [registr identit](#identity-registry).

## <a name="module-twin"></a>Dvojče zařízení
Podobně jako u dvojče zařízení, dvojče modulu je dokument JSON, který ukládá informace o stavu modulu například metadata, konfigurace a podmínky. IoT Hub udržuje takové dvojče modulu pro každou identitu modulu, který zřídíte s identitou zařízení služby IoT hub. Dvojčaty modulů umožňují synchronizovat modul ujednání a konfigurací modulu a back-endu řešení. Můžete zadávat dotazy dvojčaty modulů k vyhledání konkrétních modulů a zjistit stav dlouhotrvající operace.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) je jeden z zasílání zpráv, které protokoly [služby IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o protokoly zasílání zpráv, které podporuje službu IoT Hub najdete v tématu [odesílání a příjem zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorování operací
IoT Hub [monitorování operací](iot-hub-operations-monitoring.md) vám umožní monitorovat stav operací ve službě IoT hub v reálném čase. [IoT Hub](#iot-hub) sleduje události napříč několika kategoriemi těchto operací. Můžete se rozhodnout do odesílání událostí z jedné nebo více kategorií pro koncový bod služby IoT Hub pro zpracování. Můžete monitorovat data o chybách nebo nastavení složitější zpracování na základě způsobů data.

## <a name="physical-device"></a>Fyzické zařízení
Fyzické zařízení je skutečné zařízení, jako je Raspberry Pi, která se připojuje do služby IoT hub. Pro usnadnění práce řadu kurzů služby IoT Hub pomocí [Simulovaná zařízení](#simulated-device) umožňuje spuštění ukázek v místním počítači.

## <a name="primary-and-secondary-keys"></a>Primární a sekundární klíče
Když se připojíte ke koncovému bodu určených pro zařízení nebo přístupem k služby ve službě IoT hub, vaše [připojovací řetězec](#connection-string) klíčem k udělení přístupu. Když přidáte zařízení, které chcete [registr identit](#identity-registry) nebo přidat [sdílené zásady přístupu](#shared-access-policy) k centru, generuje tato služba primární a sekundární klíč. Existence dvou klíčů umožňuje přejít z jednoho klíče na jiné při aktualizaci klíče, aniž by ztratily přístup ke službě IoT hub.

## <a name="protocol-gateway"></a>Brána protokolu
Brána protokolu obvykle nasazuje v cloudu a poskytuje protokol překladatelské služby pro připojení k zařízení [služby IoT Hub](#iot-hub). Další informace najdete v tématu [co je Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóty a omezování
Existují různé [kvóty](iot-hub-devguide-quotas-throttling.md) , které se týkají používání [služby IoT Hub](#iot-hub), mnoho kvót se liší v závislosti na úrovni služby IoT hub. [IoT Hub](#iot-hub) platí také [omezuje](iot-hub-devguide-quotas-throttling.md) na používání služby za běhu.

## <a name="reported-configuration"></a>Ohlášené konfigurace
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), hlášené konfigurace odkazuje na kompletní sadu vlastností a metadat ve dvojčeti zařízení, která by se měly hlásit k back-endu řešení.

## <a name="reported-properties"></a>Ohlášené vlastnosti
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), oznámila vlastnosti je dílčí část objektu dvojčeti zařízení používat s [požadované vlastnosti](#desired-properties) k synchronizaci konfigurace zařízení nebo podmínku. Ohlášené vlastnosti lze nastavit pouze [aplikace pro zařízení s](#device-app) a může číst a dotazovat [back endové aplikace](#back-end-app).

## <a name="resource-group"></a>Skupina prostředků
[Azure Resource Manageru](#azure-resource-manager) k seskupení souvisejících prostředků používá skupiny prostředků. Skupinu prostředků můžete použít k provádění operací na všechny prostředky ve skupině současně.

## <a name="retry-policy"></a>Zásady opakování
Použijte zásady opakování pro zpracování [přechodné chyby](/azure/architecture/best-practices/transient-faults) při připojení ke cloudové službě.

## <a name="routing-rules"></a>Pravidla směrování
Nakonfigurujete [pravidla směrování](iot-hub-devguide-messages-read-custom.md) pro směrování zpráv typu zařízení cloud do služby IoT hub [integrovaný koncový bod](#built-in-endpoints) nebo [vlastní koncové body](#custom-endpoints) pro zpracování v back-endem řešení .

## <a name="sasl-plain"></a>PROSTÝ SASL
PROSTÝ SASL je protokol, který [AMQP](#advanced-message-queue-protocol) protokol používá k přenosu tokeny zabezpečení.

## <a name="service-rest-api"></a>Rozhraní API služby REST
Můžete použít [rozhraní REST API služby](https://docs.microsoft.com/rest/api/iothub/service) z řešení back-endu ke správě svých zařízení. Rozhraní API umožňuje načíst a aktualizovat [dvojče zařízení](#device-twin) vyvolání vlastností [přímé metody](#direct-method)a plán [úlohy](#job). Obvykle, měli použít jeden z vyšší úrovni [služby sady SDK](#azure-iot-service-sdks) jak je znázorněno v kurzech služby IoT Hub.

## <a name="shared-access-signature"></a>Sdílený přístupový podpis
Sdílených přístupových podpisů (SAS) je mechanismus ověřování na základě zabezpečené hodnoty hash SHA-256 nebo identifikátory URI. Ověřování SAS má dvě součásti: _zásady sdíleného přístupu_ a _sdílený přístupový podpis_ (často označované jako token). Zařízení SAS používá k ověření pomocí služby IoT hub. [Back endové aplikace](#back-end-app) také používají SAS k ověření koncových bodů určených pro služby ve službě IoT hub. Obvykle zahrnují token SAS v [připojovací řetězec](#connection-string) , že aplikace používá k navázání připojení do služby IoT hub.

## <a name="shared-access-policy"></a>Zásada sdíleného přístupu
Definuje zásady sdíleného přístupu oprávnění udělená každý, kdo má platný [primárním nebo sekundárním klíčem](#primary-and-secondary-keys) přidružená k této zásadě. Zásady sdíleného přístupu a klíčů můžete spravovat centra [portál](#azure-portal).

## <a name="simulated-device"></a>Simulované zařízení
Pro usnadnění práce řadu kurzů služby IoT Hub pomocí simulovaného zařízení můžete spouštět ukázky v místním počítači. Naproti tomu [fyzické zařízení](#physical-device) je skutečných zařízení, jako je Raspberry Pi, která se připojuje do služby IoT hub.

## <a name="solution"></a>Řešení
A _řešení_ mohou odkazovat na řešení sady Visual Studio, který obsahuje jeden nebo více projektů. A _řešení_ může také odkazovat na řešení IoT, který obsahuje prvky, jako jsou zařízení, [aplikací pro zařízení](#device-app), služby IoT hub, dalšími službami Azure a [back endové aplikace](#back-end-app).

## <a name="subscription"></a>Předplatné
Předplatné Azure je, kde probíhá fakturace. Každý prostředek Azure vytvoříte nebo je přidružen v rámci jednoho předplatného služby Azure, které používáte. Mnoho kvóty, platí také na úrovni předplatného.

## <a name="system-properties"></a>Systémové vlastnosti
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), systémové vlastnosti jsou jen pro čtení a obsahují informace týkající se využití zařízení, jako je poslední aktivita čas a připojení stavu.

## <a name="tags"></a>Značky
V kontextu [dvojče zařízení](iot-hub-devguide-device-twins.md), klíčová slova jsou metadata zařízení uložené a načítají pomocí back-end řešení ve formě dokumentu JSON. Značky nejsou viditelné pro aplikace na zařízení.

## <a name="telemetry"></a>Telemetrická data
Zařízení shromažďování telemetrických dat, jako je například rychlost větru nebo teploty a použijte [datového bodu zprávy](#data-point-messages) odesílání telemetrických dat do služby IoT hub.

## <a name="token-service"></a>Služba tokenů
Token služby můžete implementovat mechanismus ověřování pro vaše zařízení. Pomocí služby IoT Hub [sdílené zásady přístupu](#shared-access-policy) s **DeviceConnect** oprávnění k vytvoření *rozsahem zařízení* tokeny. Tyto tokeny umožňují zařízení pro připojení ke službě IoT hub. Vlastní ověřovací mechanismus zařízení používá k ověření pomocí služby tokenů. Pokud úspěšně ověří zařízení, služba tokenů vydá token SAS pro zařízení používat pro přístup k službě IoT hub.

## <a name="twin-queries"></a>Dotazy dvojčete
[Dotazů na dvojčata zařízení a modul](iot-hub-devguide-query-language.md) použijte dotazovací jazyk služby IoT Hub podobném SQL načtěte informace z dvojčat zařízení nebo dvojčaty modulů. Stejné dotazovací jazyk služby IoT Hub můžete použít k načtení informací o [](#job) spuštěná ve službě IoT hub.

## <a name="twin-synchronization"></a>Dvojče synchronizace
Dvojče používá synchronizaci [požadované vlastnosti](#desired-properties) dvojčat zařízení nebo dvojčaty modulů ke konfiguraci zařízení nebo moduly a načíst [ohlášené vlastnosti](#reported-properties) z nich k uložení ve dvojčeti.

## <a name="x509-client-certificate"></a>Klientského certifikátu X.509
Zařízení slouží k ověření pomocí certifikátu X.509 [služby IoT Hub](#iot-hub). Pomocí certifikátu X.509 se o alternativu k použití [SAS token](#shared-access-signature).
