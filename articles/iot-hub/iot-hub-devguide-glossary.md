---
title: Glosář termínů Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – glosář vysvětlující některé běžné termíny používané v článcích služby Azure IoT Hub.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 3471bd7297118dc75fb97390c4601179f61e6416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497537"
---
# <a name="glossary-of-iot-hub-terms"></a>Glosář termínů služby IoT Hub
Tento článek uvádí některé běžné termíny používané v článcích služby IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protokol rozšířeného zařazení do fronty zpráv
[Protokol AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) je jedním z protokolů pro zasílání zpráv, které [služba IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o protokolech zasílání zpráv, které služba IoT Hub podporuje, najdete v tématu [Odesílání a přijímání zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatická správa zařízení
Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy velkých vozových parků zařízení po celou dobu jejich životního cyklu. Pomocí automatické správy zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a nechat zařízení služby IoT Hub aktualizovat vždy, když se dostanou do oboru.  Skládá se z [automatických konfigurací zařízení](iot-hub-auto-device-config.md) a [automatických nasazení IoT Edge](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Automatická konfigurace zařízení
Back-end vašeho řešení můžete použít [automatické konfigurace zařízení](iot-hub-auto-device-config.md) přiřadit požadované vlastnosti sady dvojčat [zařízení](#device-twin) a stav sestavy pomocí systémových metrik a vlastní metriky. 

## <a name="azure-classic-cli"></a>Azure Classic CLI
[Klasické rozhraní příkazového nastavení Azure](../cli-install-nodejs.md) je nástroj pro vytváření a správu prostředků v Microsoft Azure s otevřeným zdrojovým kódem založeným na prostředí. Tato verze cli by měla být použita pouze pro klasická nasazení.

## <a name="azure-cli"></a>Azure CLI
Rozhraní [příkazového nastavení Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) je nástroj pro vytváření a správu prostředků v Microsoft Azure s otevřeným zdrojovým kódem založeným na prostředí.


## <a name="azure-iot-device-sdks"></a>Sady SDK zařízení Azure IoT
Sady _SDK zařízení_ jsou k dispozici pro více jazyků, které umožňují vytvářet [aplikace zařízení,](#device-app) které interagují s centrem IoT. Kurzy ioT Hub uvidíte, jak používat tyto sady SDK zařízení. Zdrojový kód a další informace o sadách SDK zařízení najdete v tomto [úložišti](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-service-sdks"></a>Sady SDK služby Azure IoT
Sady _SDK služby_ jsou k dispozici pro více jazyků, které umožňují vytvářet [back-endové aplikace,](#back-end-app) které interagují s centrem IoT. Kurzy služby IoT Hub ukazují, jak používat tyto sady SDK služby. Zdrojový kód a další informace o sadách SDK služby naleznete v tomto [úložišti](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-tools"></a>Azure IoT Tools
[Nástroje Azure IoT je](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) rozšíření kódu Visual Studia s otevřeným zdrojovým kódem napříč platformami, které vám pomůže spravovat Azure IoT Hub a zařízení v kódu VS. Pomocí nástrojů Azure IoT mohou vývojáři IoT snadno vyvíjet projekt IoT ve VS Code.

## <a name="azure-portal"></a>portál Azure
[Portál Microsoft Azure](https://portal.azure.com) je centrální místo, kde můžete zřazovat a spravovat prostředky Azure. Organizuje svůj obsah pomocí _nožů_.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) je kolekce rutin, které můžete použít ke správě Azure pomocí Windows PowerShellu. Pomocí rutin můžete vytvářet, testovat, nasazovat a spravovat řešení a služby poskytované prostřednictvím platformy Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/management/overview.md) umožňuje pracovat s prostředky ve vašem řešení jako skupina. Prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v jedné koordinované operaci.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) poskytuje komunikaci s podporou cloudu s podnikovými zprávami a přenosovou komunikací, která vám pomůže připojit místní řešení s cloudem. Některé kurzy služby IoT Hub používají [fronty služby](../service-bus-messaging/service-bus-messaging-overview.md)Service Bus .

## <a name="azure-storage"></a>Azure Storage
[Azure Storage](../storage/common/storage-introduction.md) je cloudové úložiště řešení. Zahrnuje službu úložiště objektů blob, kterou můžete použít k ukládání nestrukturovaných dat objektů. Některé kurzy služby IoT Hub používají úložiště objektů blob.

## <a name="back-end-app"></a>Back-endová aplikace
V kontextu [IoT Hub](#iot-hub), back-end aplikace je aplikace, která se připojuje k jednomu z koncových bodů pro služby v centru IoT. Například back-endová aplikace může načítat zprávy [mezi zařízeními](#device-to-cloud) nebo spravovat [registr identit](#identity-registry). Back-endová aplikace se obvykle spouští v cloudu, ale v mnoha kurzech jsou back-endové aplikace konzolovými aplikacemi spuštěnými na vašem místním vývojovém počítači.

## <a name="built-in-endpoints"></a>Předdefinované koncové body
Každé centrum IoT obsahuje předdefinovaný [koncový bod,](iot-hub-devguide-endpoints.md) který je kompatibilní s centrem událostí. Můžete použít libovolný mechanismus, který pracuje s Event Hubs ke čtení zpráv zařízení cloud z tohoto koncového bodu.

## <a name="cloud-gateway"></a>Cloudová brána
Cloudová brána umožňuje připojení pro zařízení, která se nemohou připojit přímo k [centru IoT Hub](#iot-hub). Cloudová brána je hostovaná v cloudu na rozdíl od [brány pole,](#field-gateway) která běží místní do vašich zařízení. Typickým případem použití pro cloudovou bránu je implementace překladu protokolu pro vaše zařízení.

## <a name="cloud-to-device"></a>Cloud-to-device
Odkazuje na zprávy odeslané z centra IoT do připojeného zařízení. Tyto zprávy jsou často příkazy, které instruují zařízení k akci. Další informace najdete v tématu [Odesílání a přijímání zpráv pomocí centra IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfigurace
V kontextu [automatické konfigurace zařízení](iot-hub-auto-device-config.md)konfigurace v rámci služby IoT Hub definuje požadovanou konfiguraci pro sadu dvojčat zařízení a poskytuje sadu metrik pro hlášení stavu a průběhu.

## <a name="connection-string"></a>Připojovací řetězec
Připojovací řetězce v kódu aplikace se používají k zapouzdření informací potřebných k připojení ke koncovému bodu. Připojovací řetězec obvykle obsahuje adresu koncového bodu a informace o zabezpečení, ale formáty připojovacího řetězce se v jednotlivých službách liší. Ke službě IoT Hub jsou přidruženy dva typy připojovacího řetězce:
- *Připojovací řetězce zařízení* umožňují zařízením připojit se ke koncovým bodům směřujícím k zařízení v centru IoT.
- *Připojovací řetězce služby IoT Hub* umožňují back-endovým aplikacím připojit se ke koncovým bodům orientovaným na služby v centru IoT.

## <a name="custom-endpoints"></a>Vlastní koncové body
Můžete vytvořit vlastní [koncové body](iot-hub-devguide-endpoints.md) v centru IoT pro doručování zpráv odeslaných [pravidlem směrování](#routing-rules). Vlastní koncové body se připojují přímo k centru událostí, frontě služby Service Bus nebo tématu service bus.

## <a name="custom-gateway"></a>Vlastní brána
Brána umožňuje připojení pro zařízení, která se nemohou připojit přímo k [službě IoT Hub](#iot-hub). Azure IoT Edge můžete použít k vytvoření vlastních bran, které implementují vlastní logiku pro zpracování zpráv, převody vlastních protokolů a další zpracování na hraničních zařízeních.

## <a name="data-point-message"></a>Zpráva datového bodu
Zpráva datového bodu je zpráva mezi zařízeními a [cloudy,](#device-to-cloud) která obsahuje [telemetrická](#telemetry) data, jako je rychlost větru nebo teplota.

## <a name="desired-configuration"></a>Požadovaná konfigurace
V kontextu [dvojčete zařízení](iot-hub-devguide-device-twins.md)požadovaná konfigurace odkazuje na úplnou sadu vlastností a metadat v dvojčeti zařízení, které by měly být synchronizovány se zařízením.

## <a name="desired-properties"></a>Požadované vlastnosti
V kontextu [dvojčete zařízení](iot-hub-devguide-device-twins.md)je požadované vlastnosti podčástí dvojčete zařízení, která se používá s [ohlášenými vlastnostmi](#reported-properties) k synchronizaci konfigurace nebo stavu zařízení. Požadované vlastnosti lze nastavit pouze [pomocí back-endové aplikace](#back-end-app) a jsou sledovány [aplikací zařízení](#device-app).

## <a name="device-to-cloud"></a>Zařízení ke cloudu
Odkazuje na zprávy odeslané z připojeného zařízení do [ioT hubu](#iot-hub). Tyto zprávy mohou být [datový bod](#data-point-message) nebo [interaktivní](#interactive-message) zprávy. Další informace najdete v tématu [Odesílání a přijímání zpráv pomocí centra IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Zařízení
V kontextu IoT zařízení je obvykle malé, samostatné výpočetní zařízení, které může shromažďovat data nebo ovládat jiná zařízení. Zařízení může být například zařízení pro monitorování životního prostředí nebo regulátor pro zavlažovací a ventilační systémy ve skleníku. [Katalog zařízení](https://catalog.azureiotsolutions.com/) poskytuje seznam hardwarových zařízení certifikovaných pro práci s [službou IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplikace zařízení
Aplikace zařízení běží na vašem [zařízení](#device) a zpracovává komunikaci s centrem [IoT hub](#iot-hub). Obvykle se při implementaci aplikace zařízení používá tezi s některou ze [sad SDK zařízení Azure IoT.](#azure-iot-device-sdks) V mnoha kurzech IoT použijete pro větší pohodlí [simulované zařízení.](#simulated-device)

## <a name="device-condition"></a>Stav zařízení
Odkazuje na informace o stavu zařízení, jako je například metoda připojení, která se právě používá, jak je hlášena [aplikací zařízení](#device-app). [Aplikace pro zařízení](#device-app) mohou také hlásit své možnosti. Můžete dotaz na informace o stavu a schopnosti pomocí dvojčatzařízení.

## <a name="device-data"></a>Data zařízení
Data zařízení se vztahují k datům pro zařízení uloženým v [registru identit služby](#identity-registry)IoT Hub . Tato data je možné importovat a exportovat.

## <a name="device-explorer"></a>Device Explorer
[Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) je nástroj, který běží v systému Windows a umožňuje spravovat zařízení v [registru identit](#identity-registry). Nástroj může také odesílat a přijímat zprávy do vašich zařízení.

## <a name="device-identity"></a>Identita zařízení
Identita zařízení je jedinečný identifikátor přiřazený každému zařízení registrovanému v [registru identit](#identity-registry).

## <a name="device-management"></a>Správa zařízení
Správa zařízení zahrnuje celý životní cyklus spojený se správou zařízení ve vašem řešení IoT, včetně plánování, zřizování, konfigurace, monitorování a vyřazení.

## <a name="device-management-patterns"></a>Schémata správy zařízení
[IoT hub](#iot-hub) umožňuje běžné vzorce správy zařízení, včetně restartování, provádění obnovení továrního nastavení a provádění aktualizací firmwaru na vašich zařízeních.

## <a name="device-rest-api"></a>Rozhraní REST zařízení API
Rozhraní REST [API zařízení](https://docs.microsoft.com/rest/api/iothub/device) ze zařízení můžete použít k odesílání zpráv mezi zařízeními a cloudy do centra IoT a přijímat zprávy z [cloudu na zařízení](#cloud-to-device) z centra IoT. Obvykle byste měli použít jeden z vyšší úrovně [zařízení sady SDK,](#azure-iot-device-sdks) jak je znázorněno v kurzech služby IoT Hub.

## <a name="device-provisioning"></a>Zřizování zařízení
Zřizování zařízení je proces přidávání [počátečnídata zařízení](#device-data) do úložišť ve vašem řešení. Chcete-li povolit připojení nového zařízení k rozbočovači, musíte do [registru identit služby](#identity-registry)IoT Hub přidat ID zařízení a klíče . Jako součást procesu zřizování může být nutné inicializovat data specifická pro zařízení v jiných úložištích řešení.

## <a name="device-twin"></a>Dvojče zařízení
Dvojče [zařízení](iot-hub-devguide-device-twins.md) je dokument JSON, který ukládá informace o stavu zařízení, jako jsou metadata, konfigurace a podmínky. [IoT Hub](#iot-hub) zachová dvojče zařízení pro každé zařízení, které zřídíte ve svém centru IoT Hub. Dvojčata zařízení umožňují synchronizovat [podmínky zařízení](#device-condition) a konfigurace mezi zařízením a back-endem řešení. Můžete dotaz dvojčata zařízení najít konkrétní zařízení a dotaz na stav dlouhotrvající operace.

## <a name="direct-method"></a>Přímá metoda
[Přímá metoda](iot-hub-devguide-direct-methods.md) je způsob, jak spustit metodu spuštění na zařízení vyvoláním rozhraní API v centru IoT.

## <a name="endpoint"></a>Koncový bod
Centrum IoT poskytuje více [koncových bodů,](iot-hub-devguide-endpoints.md) které umožňují aplikacím připojení k centru IoT. Existují koncové body směřující k zařízení, které umožňují zařízením provádět operace, jako je odesílání zpráv [mezi zařízeními](#device-to-cloud) a přijímání zpráv [z cloudu na zařízení.](#cloud-to-device) Existují koncové body správy pro správu pro služby, které umožňují [back-endovým aplikacím](#back-end-app) provádět operace, jako je správa [identit zařízení](#device-identity) a správa dvojčat zařízení. Existují [integrované koncové body](#built-in-endpoints) pro čtení zpráv zařízení cloud. Můžete vytvořit [vlastní koncové body](#custom-endpoints) pro příjem zpráv zařízení cloud odeslaných [pravidlem směrování](#routing-rules).

## <a name="event-hubs-service"></a>Služba Event Hubs
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) je vysoce škálovatelná služba příchozího přenosu dat, která může ingestovat miliony událostí za sekundu. Tato služba umožňuje zpracovávat a analyzovat obrovské množství dat vytvořených připojenými zařízeními a aplikacemi. Porovnání se službou IoT Hub najdete [v tématu Porovnání Azure IoT Hub a Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Koncový bod kompatibilní s centrem událostí
Chcete-li číst zprávy [mezi zařízeními a cloudy](#device-to-cloud) odeslané do služby IoT hub, můžete se připojit ke koncovému bodu ve vašem centru a použít libovolnou metodu kompatibilní s centrem událostí ke čtení těchto zpráv. Mezi metody kompatibilní s centrem událostí patří použití sad [SDK centra událostí](../event-hubs/event-hubs-programming-guide.md) a [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Brána pole
Brána pole umožňuje připojení pro zařízení, která se nemohou připojit přímo k [ioT hubu](#iot-hub) a obvykle se nasazují místně s vašimi zařízeními. Další informace najdete v tématu [Co je Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Bezplatný účet
Můžete vytvořit [bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/) k dokončení kurzů služby IoT Hub a experimentovat se službou IoT Hub (a dalšími službami Azure).

## <a name="gateway"></a>brána
Brána umožňuje připojení pro zařízení, která se nemohou připojit přímo k [službě IoT Hub](#iot-hub). Viz také [Field Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway)a Custom [Gateway](#custom-gateway).

## <a name="identity-registry"></a>Registr identit
[Registr identit](iot-hub-devguide-identity-registry.md) je integrovaná součást služby IoT hub, která ukládá informace o jednotlivých zařízeních povolených pro připojení k centru IoT.

## <a name="interactive-message"></a>Interaktivní zpráva
Interaktivní zpráva je zpráva [z cloudu na zařízení,](#cloud-to-device) která aktivuje okamžitou akci v back-endu řešení. Zařízení může například odeslat alarm o selhání, které by mělo být automaticky přihlášeno k systému CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT Hub je plně spravovaná služba Azure, která umožňuje spolehlivou a bezpečnou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. Další informace najdete v tématu [Co je Azure IoT Hub?](about-iot-hub.md) Pomocí [předplatného Azure](#subscription)můžete vytvářet centra IoT pro zpracování úloh zasílání zpráv IoT.

## <a name="iot-hub-metrics"></a>Metriky IoT Hubu
[Metriky služby IoT Hub](iot-hub-metrics.md) poskytují data o stavu center IoT hub ve vašem [předplatném Azure](#subscription). Metriky služby IoT Hub umožňují posoudit celkový stav služby a zařízení k ní připojeným. Metriky služby IoT Hub vám můžou pomoct zjistit, co se děje s vaším centrem IoT hub, a prozkoumat hlavní příčiny problémů, aniž byste museli kontaktovat podporu Azure.

## <a name="iot-hub-query-language"></a>Dotazovací jazyk centra IoT Hub
[Dotazovací jazyk ioT hubu](iot-hub-devguide-query-language.md) je jazyk podobný SQL, který umožňuje dotaz na dvojčata [](#job) a dvojčata zařízení.

## <a name="iot-hub-resource-rest-api"></a>Rozhraní REST centra IoT
Rozhraní API [REST centra IoT hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) umíte ke správě center IoT ve vašem [předplatném Azure,](#subscription) které provádějí operace, jako je vytváření, aktualizace a odstraňování hubů.

## <a name="iot-solution-accelerators"></a>Akcelerátory řešení IoT
Akcelerátory řešení Azure IoT společně zabalí do řešení několik služeb Azure. Tato řešení umožňují rychle začít s implementací začátku do konce běžných scénářů IoT. Další informace najdete v tématu [Co jsou akcelerátory řešení Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Rozšíření IoT pro Azure CLI 
[Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) je nástroj příkazového řádku pro různé platformy. Tento nástroj umožňuje spravovat zařízení v registru identit , odesílat a přijímat zprávy a soubory ze zařízení a sledovat operace [služby](#identity-registry)IoT hub.

## <a name="job"></a>Úloha
Back-end řešení můžete použít [úlohy](iot-hub-devguide-jobs.md) k plánování a sledování aktivit na sadu zařízení registrovaných v centru IoT. Mezi aktivity patří aktualizace [požadovaných vlastností](#desired-properties)dvojčete zařízení , aktualizace [značek](#tags)dvojčete zařízení a vyvolání [přímých metod](#direct-method). [IoT Hub](#iot-hub) také používá k [importu a exportu](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [registru identit](#identity-registry).

## <a name="modules"></a>Moduly
Na straně zařízení umožňují sady SDK zařízení IoT Hub vytvářet [moduly,](iot-hub-devguide-module-twins.md) kde každý z nich otevře nezávislé připojení k centru IoT Hub. Tato funkce umožňuje používat samostatné obory názvů pro různé součásti v zařízení.

Identita modulu a dvojče modulu poskytují stejné funkce jako [identita zařízení](#device-identity) a [dvojče zařízení,](#device-twin) ale s jemnější granularitou. Tato jemnější rozlišovací schopnost umožňuje schopným zařízením, jako jsou zařízení s operačním systémem nebo zařízení firmwaru spravující více součástí, izolovat konfiguraci a podmínky pro každou z těchto součástí.

## <a name="module-identity"></a>Identita modulu
Identita modulu je jedinečný identifikátor přiřazený každému modulu, který patří zařízení. Identita modulu je také registrována v [registru identit](#identity-registry).

## <a name="module-twin"></a>Dvojče modulu
Podobně jako dvojče zařízení je dvojče modulu dokument JSON, který ukládá informace o stavu modulu, jako jsou metadata, konfigurace a podmínky. Služba IoT Hub zachová dvojče modulu pro každou identitu modulu, kterou zřídíte pod identitou zařízení ve vašem centru IoT Hub. Dvojčata modulu umožňují synchronizovat podmínky modulu a konfigurace mezi modulem a back-endem řešení. Můžete dotaz dvojčata modulu vyhledejte konkrétní moduly a dotaz na stav dlouhotrvající operace.

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) je jeden z protokolů pro zasílání zpráv, které [služba IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o protokolech zasílání zpráv, které služba IoT Hub podporuje, najdete v tématu [Odesílání a přijímání zpráv pomocí služby IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorování operací
Monitorování operací [ioT](iot-hub-operations-monitoring.md) hubu umožňuje sledovat stav operací ve vašem centru IoT hub v reálném čase. [IoT Hub](#iot-hub) sleduje události v několika kategoriích operací. Můžete se přihlásit k odesílání událostí z jedné nebo více kategorií do koncového bodu centra IoT Hub pro zpracování. Můžete sledovat data na chyby nebo nastavit složitější zpracování na základě datových vzorů.

## <a name="physical-device"></a>Fyzické zařízení
Fyzické zařízení je skutečné zařízení, jako je Například Raspberry Pi, které se připojuje k centru IoT. Pro větší pohodlí používá mnoho kurzů ioT hubu [simulovaná zařízení,](#simulated-device) která umožňují spouštět ukázky v místním počítači.

## <a name="primary-and-secondary-keys"></a>Primární a sekundární klíče
Když se připojíte ke koncovému bodu orientovanému na zařízení nebo službu v centru IoT, váš [připojovací řetězec](#connection-string) obsahuje klíč, který vám udělí přístup. Když přidáte zařízení do [registru identit](#identity-registry) nebo přidáte [zásady sdíleného přístupu](#shared-access-policy) do vašeho centra, služba vygeneruje primární a sekundární klíč. S dvěma klíči umožňuje převrátit z jednoho klíče do druhého při aktualizaci klíče bez ztráty přístupu k centru IoT.

## <a name="protocol-gateway"></a>Brána protokolu
Brána protokolu se obvykle nasadí v cloudu a poskytuje služby překladu protokolů pro zařízení, která se připojují k [službě IoT Hub](#iot-hub). Další informace najdete v tématu [Co je Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóty a omezování
Existují různé [kvóty,](iot-hub-devguide-quotas-throttling.md) které se vztahují na vaše používání [služby IoT Hub](#iot-hub), mnoho kvót se liší v závislosti na úrovni služby IoT hub. [Služba IoT Hub](#iot-hub) také používá omezení pro vaše používání [služby](iot-hub-devguide-quotas-throttling.md) za běhu.

## <a name="reported-configuration"></a>Hlášená konfigurace
V kontextu [dvojčete zařízení](iot-hub-devguide-device-twins.md), ohlášená konfigurace odkazuje na úplnou sadu vlastností a metadat v dvojčeti zařízení, které by měly být hlášeny back-endřešení.

## <a name="reported-properties"></a>Hlášené vlastnosti
V kontextu [dvojčete zařízení](iot-hub-devguide-device-twins.md)jsou hlášené vlastnosti podčástí dvojčete zařízení, které se používá s [požadovanými vlastnostmi](#desired-properties) k synchronizaci konfigurace nebo stavu zařízení. Hlášené vlastnosti lze nastavit pouze [aplikací zařízení](#device-app) a lze je číst a dotazovat [pomocí back-endové aplikace](#back-end-app).

## <a name="resource-group"></a>Skupina prostředků
[Azure Resource Manager](#azure-resource-manager) používá skupiny prostředků k seskupení souvisejících prostředků dohromady. Skupinu prostředků můžete použít k provádění operací se všemi prostředky ve skupině současně.

## <a name="retry-policy"></a>Zásady opakování
Zásady opakování se používají ke zpracování [přechodných chyb](/azure/architecture/best-practices/transient-faults) při připojení ke cloudové službě.

## <a name="routing-rules"></a>Pravidla směrování
Ve službě IoT hub nakonfigurujete [pravidla směrování](iot-hub-devguide-messages-read-custom.md) pro směrování zpráv mezi zařízeními a cloudy do [integrovaného koncového bodu](#built-in-endpoints) nebo do [vlastních koncových bodů](#custom-endpoints) pro zpracování back-endem vašeho řešení.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN je protokol, který protokol AMQP používá k přenosu tokenů zabezpečení.

## <a name="service-rest-api"></a>Rozhraní API služby REST
Rozhraní REST [API služby](https://docs.microsoft.com/rest/api/iothub/service/configuration) můžete použít z back-endu řešení ke správě zařízení. Rozhraní API umožňuje načíst a aktualizovat vlastnosti [dvojčete zařízení,](#device-twin) vyvolat [přímé metody](#direct-method)a naplánovat [úlohy](#job). Obvykle byste měli použít jeden z vyšší [úrovně služby Sady SDK,](#azure-iot-service-sdks) jak je znázorněno v kurzech služby IoT Hub.

## <a name="shared-access-signature"></a>Sdílený přístupový podpis
Sdílené přístupové podpisy (SAS) jsou mechanismus ověřování založený na zabezpečených hashech SHA-256 nebo identifikátorech URI. Ověřování SAS má dvě součásti: _zásady sdíleného přístupu_ a _sdílený přístupový podpis_ (často nazývaný token). Zařízení používá SAS k ověření pomocí služby IoT hub. [Back-endové aplikace](#back-end-app) také používají SAS k ověření pomocí koncových bodů pro služby v centru IoT. Obvykle zahrnete token SAS do [připojovacího řetězce,](#connection-string) který aplikace používá k navázání připojení k centru IoT.

## <a name="shared-access-policy"></a>Zásady sdíleného přístupu
Zásady sdíleného přístupu definují oprávnění udělená všem uživatelům, kteří mají k této zásadě přidružený platný [primární nebo sekundární klíč.](#primary-and-secondary-keys) Zásady sdíleného přístupu a klíče pro centrum můžete spravovat na [portálu](#azure-portal).

## <a name="simulated-device"></a>Simulované zařízení
Pro větší pohodlí používá mnoho kurzů ioT hubu simulovaná zařízení, která umožňují spouštět ukázky v místním počítači. Naproti tomu [fyzické zařízení](#physical-device) je skutečné zařízení, jako je Raspberry Pi, které se připojuje k centru IoT hub.

## <a name="solution"></a>Řešení
_Řešení_ může odkazovat na řešení sady Visual Studio, které obsahuje jeden nebo více projektů. _Řešení_ může také odkazovat na řešení IoT, které obsahuje prvky, jako jsou zařízení, [aplikace zařízení](#device-app), služby IoT hub, další služby Azure a [back-endové aplikace](#back-end-app).

## <a name="subscription"></a>Předplatné
Předplatné Azure je místo, kde probíhá fakturace. Každý prostředek Azure, který vytvoříte, nebo služba Azure, kterou používáte, je přidružený k jedinému předplatnému. Mnoho kvót platí také na úrovni předplatného.

## <a name="system-properties"></a>Systémové vlastnosti
V kontextu [dvojčete zařízení](iot-hub-devguide-device-twins.md)jsou vlastnosti systému jen pro čtení a obsahují informace týkající se využití zařízení, jako je čas poslední aktivity a stav připojení.

## <a name="tags"></a>Značky
V kontextu [dvojčete zařízení](iot-hub-devguide-device-twins.md)jsou značky metadata zařízení uložená a načtená back-endem řešení ve formě dokumentu JSON. Značky nejsou viditelné pro aplikace na zařízení.

## <a name="telemetry"></a>Telemetrie
Zařízení shromažďují telemetrická data, jako je rychlost větru nebo teplota, a používají zprávy datových bodů k odeslání telemetrie do centra IoT.

## <a name="token-service"></a>Služba tokenů
Službu tokenů můžete použít k implementaci mechanismu ověřování pro vaše zařízení. Používá [zásady sdíleného přístupu služby](#shared-access-policy) IoT Hub s oprávněními **DeviceConnect** k vytváření tokenů *s rozsahem zařízení.* Tyto tokeny umožňují zařízení pro připojení k centru IoT. Zařízení používá vlastní mechanismus ověřování k ověření pomocí služby tokenu. Pokud se zařízení úspěšně ověří, služba tokenu vydá token SAS, který má zařízení použít pro přístup k vašemu centru IoT.

## <a name="twin-queries"></a>Dvojité dotazy
[Dotazy na dvojče zařízení a modul](iot-hub-devguide-query-language.md) používají dotazovací jazyk IoT Hub podobné SQL k načtení informací z dvojčat zařízení nebo dvojčat modulu. Můžete použít stejný dotazovací jazyk IoT [](#job) Hub k načtení informací o spuštění ve vašem centru IoT.

## <a name="twin-synchronization"></a>Dvojitá synchronizace
Twin synchronizace používá [požadované vlastnosti](#desired-properties) v dvojčata zařízení nebo dvojčata modulu ke konfiguraci zařízení nebo moduly a načíst [hlášené vlastnosti](#reported-properties) z nich uložit do dvojčete.

## <a name="x509-client-certificate"></a>Klientský certifikát X.509
Zařízení může k ověření pomocí služby [IoT Hub](#iot-hub)použít certifikát X.509 . Použití certifikátu X.509 je alternativou k použití [tokenu SAS](#shared-access-signature).
