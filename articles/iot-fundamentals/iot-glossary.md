---
title: Glosář Azure IoT – výrazy | Microsoft Docs
description: Příručka pro vývojáře – Glosář, který vysvětluje některé běžné výrazy používané v článcích Azure IoT.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: a45b8db1f17ead8e55bac2b2bf82aa44cb7f080e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304694"
---
# <a name="glossary-of-iot-terms"></a>Glosář pojmů IoT

V tomto článku jsou uvedené některé běžné výrazy, které se používají v článcích IoT.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Protokol rozšířené fronty zpráv

[Protokol AMQP (Advanced Message Queueing Protocol)](https://www.amqp.org/) je jedním z protokolů zasílání zpráv, které [IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o protokolech zasílání zpráv, které IoT Hub podporuje, najdete v tématu [posílání a přijímání zpráv pomocí IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="allocation-policy"></a>Zásady přidělování

Zásady přidělování ve [službě Device Provisioning](#device-provisioning-service)určují, jak služba přiřadí zařízení k [připojeným](#linked-iot-hub)centrům IoT.

### <a name="attestation-mechanism"></a>Mechanismus ověřování

V rámci [služby Device Provisioning](#device-provisioning-service)je mechanismus ověření metoda, která se používá k potvrzení identity zařízení. Mechanismus ověřování se konfiguruje při [registraci](#enrollment).

Mechanismy ověřování zahrnují certifikáty X. 509, důvěryhodné moduly platforem a symetrické klíče.

### <a name="automatic-deployment"></a>Automatické nasazení

V IoT Edge automatické nasazení konfiguruje cílovou sadu IoT Edgech zařízení, aby spouštěla sadu IoT Edge modulů. Každé nasazení průběžně zajišťuje, aby všechna zařízení, která odpovídají jeho cílovým podmínkám, používala zadanou sadu modulů, i když se vytvoří nová zařízení nebo se upraví tak, aby odpovídaly cílové podmínce. Každé zařízení IoT Edge přijímá jenom nasazení s nejvyšší prioritou, jehož cílová podmínka splňuje. Přečtěte si další informace o [IoT Edge automatickém nasazení](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Automatická konfigurace zařízení

Back-end vašeho řešení může pomocí [automatických konfigurací zařízení](../iot-hub/iot-hub-automatic-device-management.md) přiřazovat požadované vlastnosti do sady [vláken zařízení](#device-twin) a stav sestav pomocí systémových metrik a vlastních metrik.

### <a name="automatic-device-management"></a>Automatická správa zařízení

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy rozsáhlých loďstva zařízení v celém životním cyklu. Díky automatické správě zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a nechat IoT Hub aktualizovat zařízení pokaždé, když vstoupí do rozsahu.  Skládá se z [automatických konfigurací zařízení](../iot-hub/iot-hub-automatic-device-management.md) a [IoT Edgech automatických nasazení](../iot-edge/how-to-deploy-at-scale.md).

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital autosinus je platforma jako služba (PaaS), která slouží k vytváření digitálních reprezentace skutečných věcí, míst, obchodních procesů a lidí. Sestavujte grafy znalostí, které představují celá prostředí, a využijte je k získání přehledu o lepších produktech, optimalizaci operací a nákladech a vytváření zkušeností zákazníků. Další informace najdete v tématu [digitální vlákna Azure](../digital-twins/index.yml).

### <a name="azure-digital-twins-instance"></a>Instance digitálních vláken Azure

Jediná instance služby Azure Digital Service v rámci předplatného zákazníka. Zatímco služba Azure [Digital](#azure-digital-twins) prokáže odkazuje na službu Azure jako celek, vaše **instance** digitálního vlákna Azure je vaším individuálním prostředkem digitálních vláken Azure.

### <a name="azure-iot-device-sdks"></a>Sady SDK pro zařízení Azure IoT

K dispozici jsou sady _SDK pro zařízení_ , které vám umožní vytvářet [aplikace pro zařízení](#device-app) , které komunikují se službou IoT Hub. Kurzy IoT Hub ukazují, jak používat tyto sady SDK pro zařízení. Zdrojový kód a další informace o sadách SDK pro zařízení najdete v tomto [úložišti](https://github.com/Azure/azure-iot-sdks)GitHub.

### <a name="azure-iot-explorer"></a>Průzkumník Azure IoT

[Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) slouží k zobrazení telemetrie, kterou zařízení odesílá, práci s vlastnostmi zařízení a příkazy volání. K interakci a testování zařízení a ke správě [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device)můžete také použít Průzkumníka.

### <a name="azure-iot-service-sdks"></a>Sady SDK pro služby Azure IoT

K dispozici jsou sady _SDK služeb_ pro několik jazyků, které umožňují vytvářet [back-endové aplikace](#back-end-app) , které komunikují se službou IoT Hub. Výukové kurzy IoT Hub ukazují, jak používat tyto sady SDK služeb. Zdrojový kód a další informace o sadách SDK služby najdete v tomto [úložišti](https://github.com/Azure/azure-iot-sdks)GitHub.

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) jsou open source rozšíření Visual Studio Code pro různé platformy, které pomáhá spravovat IoT Hub a zařízení Azure v vs Code. Pomocí nástrojů Azure IoT můžou vývojáři IoT vyvíjet projekt IoT v VS Code snadno.

## <a name="b"></a>B

### <a name="back-end-app"></a>Back-endové aplikace

V souvislosti s [IoT Hub](#iot-hub)je back-endové aplikace aplikace, která se připojuje k jednomu z koncových bodů s přístupem ke službám ve službě IoT Hub. Například aplikace back-end může načítat zprávy [typu zařízení-Cloud](#device-to-cloud) nebo spravovat [registr identit](#identity-registry). Obvykle se jedná o back-endové aplikace v cloudu, ale v mnoha kurzech se back-endové aplikace spouštějí konzolové aplikace spuštěné na místním vývojovém počítači.

### <a name="built-in-endpoints"></a>Předdefinované koncové body

Typ [koncového bodu](#endpoint) , který je integrovaný do IoT Hub. Každé centrum IoT obsahuje integrovaný [koncový bod](../iot-hub/iot-hub-devguide-endpoints.md) , který je kompatibilní s centrem událostí. K čtení zpráv ze zařízení do cloudu z tohoto koncového bodu můžete použít libovolný mechanismus, který spolupracuje s Event Hubs.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Cloudová brána

Cloudová brána umožňuje připojení pro zařízení, která se nemůžou přímo připojit k [IoT Hub](#iot-hub). Cloudová brána je hostovaná v cloudu na rozdíl od [brány pole](#field-gateway) , která spouští místní zařízení. Typickým případem použití pro cloudovou bránu je implementace překladu protokolu pro vaše zařízení.

### <a name="cloud-to-device"></a>Z cloudu do zařízení

Odkazuje na zprávy odeslané ze služby IoT Hub na připojené zařízení. Tyto zprávy jsou často příkazy, které zařízení instruují, aby prověřilo nějakou akci. Další informace najdete v tématu [posílání a přijímání zpráv pomocí IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Příkazy

V technologie Plug and Play IoT příkazy definované v [rozhraní](#interface) představovaly metody, které lze spustit na základě [digitálního vlákna](#digital-twin). Například příkaz pro restartování zařízení.

### <a name="component"></a>Součást

Komponenty v IoT technologie Plug and Play a Azure Digital autoforms umožňují vytvářet [rozhraní](#interface) modelu jako sestavení jiných rozhraní. [Model zařízení](#device-model) může kombinovat více rozhraní jako komponenty. Model může například zahrnovat komponentu Switch a termostat. Více komponent v modelu může také používat stejný typ rozhraní. Model může například zahrnovat dvě komponenty termostatu.

### <a name="configuration"></a>Konfigurace

V souvislosti s [automatickou konfigurací zařízení](../iot-hub/iot-hub-automatic-device-management.md)definuje konfigurace v rámci IoT Hub požadovanou konfiguraci pro sadu zařízení s dvojitou přesností a poskytuje sadu metrik pro hlášení stavu a průběhu.

### <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězce v kódu aplikace použijete k zapouzdření informací potřebných pro připojení ke koncovému bodu. Připojovací řetězec obvykle obsahuje adresu koncového bodu a informace o zabezpečení, ale formáty připojovacích řetězců se liší v rámci služeb. Existují dva typy připojovacího řetězce přidruženého ke službě IoT Hub:

- *Připojovací řetězce zařízení* umožňují zařízením připojit se k koncovým bodům připojeným k zařízení ve službě IoT Hub.

- *Připojovací řetězce IoT Hub* umožňují back-endové aplikace připojit se k koncovým bodům přístupným pro službu ve službě IoT Hub.

### <a name="custom-endpoints"></a>Vlastní koncové body

V centru IoT můžete vytvořit vlastní [koncové body](../iot-hub/iot-hub-devguide-endpoints.md) pro doručování zpráv odeslaných [pravidlem směrování](#routing-rules). Vlastní koncové body se připojují přímo k centru událostí, Service Bus frontě nebo k Service Bus tématu.

### <a name="custom-gateway"></a>Vlastní brána

Brána umožňuje připojení pro zařízení, která se nemůžou přímo připojit k [IoT Hub](#iot-hub). Pomocí Azure IoT Edge můžete vytvářet vlastní brány, které implementují vlastní logiku pro zpracování zpráv, vlastní převody protokolů a jiné zpracování na hraničních zařízeních.

## <a name="d"></a>D

### <a name="data-point-message"></a>Zpráva datového bodu

Zpráva datového bodu je zpráva typu [zařízení-Cloud](#device-to-cloud) , která obsahuje data [telemetrie](#telemetry) , jako je třeba rychlost větru nebo teplota.

### <a name="default-component"></a>Výchozí součást

V technologie Plug and Play IoT mají všechny [modely zařízení](#device-model) výchozí komponentu. Model jednoduchého zařízení má pouze výchozí komponentu, což je také označováno jako žádné zařízení součásti. Složitější model má více komponent vnořených pod výchozí komponentu.

### <a name="deployment-manifest"></a>Manifest nasazení

V [IoT Edge](#iot-edge)manifest nasazení je dokument JSON obsahující informace, které mají být zkopírovány v jednom nebo IoT Edge více podmnožinách modulů, tras a souvisejících vlastností modulu.

### <a name="desired-configuration"></a>Požadovaná konfigurace

V souvislosti s [dvojitou](../iot-hub/iot-hub-devguide-device-twins.md)přesností zařízení se požadovaná konfigurace odkazuje na kompletní sadu vlastností a metadat v zařízení, které by se mělo synchronizovat se zařízením.

### <a name="desired-properties"></a>Požadované vlastnosti

V kontextu nepodmíněných [zařízení](../iot-hub/iot-hub-devguide-device-twins.md)je požadovaná vlastnost pododdílem vlákna zařízení, které se používá u [hlášených vlastností](#reported-properties) k synchronizaci konfigurace nebo podmínky zařízení. Požadované vlastnosti lze nastavit pouze v [back-endové aplikaci](#back-end-app) a jsou sledovány [aplikací pro zařízení](#device-app).

### <a name="device"></a>Zařízení

V kontextu IoT je zařízení obvykle malé, samostatné výpočetní zařízení, které může shromažďovat data nebo ovládat jiná zařízení. Zařízení může být například zařízení pro monitorování prostředí nebo kontroler pro vodní a ventilační systémy ve skleníku. [Katalog zařízení](https://catalog.azureiotsolutions.com/) nabízí seznam hardwarových zařízení, která jsou certifikována pro práci s [IoT Hub](#iot-hub).

### <a name="device-app"></a>Aplikace zařízení

Aplikace zařízení běží na vašem [zařízení](#device) a zpracovává komunikaci se službou [IoT Hub](#iot-hub). Při implementaci aplikace zařízení se obvykle používá jedna ze [sad SDK pro zařízení Azure IoT](#azure-iot-device-sdks) . V mnoha kurzech IoT se pro usnadnění používá [simulované zařízení](#simulated-device) .

### <a name="device-builder"></a>Tvůrce zařízení

Nástroj pro sestavovatele zařízení používá model a [rozhraní](#interface) [zařízení](#device-model) při implementaci kódu ke spuštění na [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). Tvůrci zařízení obvykle používají jednu ze [sad SDK pro zařízení Azure IoT](#azure-iot-device-sdks) k implementaci klienta zařízení.

### <a name="device-certification"></a>Certifikace zařízení

Program pro certifikaci zařízení IoT technologie Plug and Play ověří, jestli zařízení splňuje požadavky na certifikaci technologie Plug and Play IoT. Certifikované zařízení můžete přidat do seznamu veřejně [certifikovaných pro katalog zařízení Azure IoT](https://aka.ms/devicecatalog).

### <a name="device-condition"></a>Stav zařízení

Odkazuje na informace o stavu zařízení, jako je právě používané metoda připojení, jak je uvedeno v [aplikaci zařízení](#device-app). [Aplikace pro zařízení](#device-app) mohou také nahlásit své schopnosti. Můžete zadávat dotazy na informace o podmínkách a funkcích pomocí vláken zařízení.

### <a name="device-data"></a>Data zařízení

Data zařízení odkazují na data na zařízení uložená v [registru IoT Hub identity](#identity-registry). Tato data je možné importovat a exportovat.

### <a name="device-identity"></a>Identita zařízení

Identita zařízení (nebo ID zařízení) je jedinečný identifikátor přiřazený ke každému zařízení zaregistrovanému v IoT Hub [registru identit](#identity-registry).

### <a name="device-management"></a>Správa zařízení

Správa zařízení zahrnuje úplný životní cyklus přidružený ke správě zařízení v řešení IoT, včetně plánování, zřizování, konfigurace, monitorování a vyřazení z provozu.

### <a name="device-management-patterns"></a>Schémata správy zařízení

[IoT Hub](#iot-hub) umožňuje běžné vzory správy zařízení, včetně restartování, obnovení továrního nastavení a provádění aktualizací firmwaru na vašich zařízeních.

### <a name="device-model"></a>Model zařízení

Model zařízení je typ [modelu](#model) , který používá [jazyk s definicí digitálních vláken](#digital-twins-definition-language-dtdl) k popisu možností technologie Plug and Play zařízení IoT. Model jednoduchého zařízení používá jedno rozhraní k popisu možností zařízení. Složitější model zařízení obsahuje více komponent, z nichž každý popisuje sadu funkcí. Další informace najdete v tématu [komponenty IoT technologie Plug and Play v modelech](../iot-pnp/concepts-components.md).

### <a name="device-modeling"></a>Modelování zařízení

[Tvůrce zařízení](#device-builder) nebo [modul pro sestavovatele modulů](#module-builder)používá k modelování možností [technologie Plug and Play zařízení IoT](#iot-plug-and-play-device), [jazyk definice digitálních vláken](#digital-twins-definition-language-dtdl) . [Tvůrce řešení](#solution-builder) může nakonfigurovat řešení IoT z modelu.

### <a name="device-provisioning"></a>Zřizování zařízení

Zřizování zařízení je proces přidávání počátečních [dat zařízení](#device-data) do úložišť ve vašem řešení. Pokud chcete novému zařízení povolit připojení k vašemu rozbočovači, musíte do [registru IoT Hub identity](#identity-registry)přidat ID zařízení a klíče. V rámci procesu zřizování může být potřeba inicializovat data specifická pro zařízení v jiných úložištích řešení.

### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) je pomocná služba pro [IoT Hub](#iot-hub) , která se používá ke konfiguraci zařízení s nulovým dotykovým zřizováním pro zadané centrum IoT. S DPS můžete zřídit miliony zařízení zabezpečeným a škálovatelným způsobem.

### <a name="device-rest-api"></a>REST API zařízení

Pomocí [REST API zařízení](/rest/api/iothub/device) ze zařízení můžete odesílat zprávy typu zařízení-Cloud do služby IoT Hub a přijímat zprávy z [cloudu na zařízení](#cloud-to-device) ze služby IoT Hub. Obvykle byste měli použít jednu ze [sad SDK pro zařízení](#azure-iot-device-sdks) vyšší úrovně, jak je znázorněno v kurzech IoT Hub.

### <a name="device-twin"></a>Dvojče zařízení

Nevlákenná zařízení je dokument JSON, který obsahuje informace o stavu zařízení, jako jsou metadata, konfigurace a podmínky. IoT Hub zachovává pro každé zařízení, které zřizujete ve službě IoT Hub, vlákna zařízení. Vlákna zařízení umožňují synchronizovat podmínky a konfigurace zařízení mezi zařízením a back-endu řešení. Můžete se dotázat na vlákna zařízení a vyhledat konkrétní zařízení a stav dlouhotrvajících operací.

### <a name="device-to-cloud"></a>Zařízení do cloudu

Odkazuje na zprávy odeslané z připojeného zařízení na [IoT Hub](#iot-hub). Těmito zprávami může být [datový bod](#data-point-message) nebo [interaktivní](#interactive-message) zprávy. Další informace najdete v tématu [posílání a přijímání zpráv pomocí IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Digitální vlákna

Digitální vlákna je kolekce digitálních dat, která představuje fyzický objekt. Změny fyzického objektu se projeví v poli digitálního vlákna. V některých scénářích můžete použít digitální vlákna k manipulaci s fyzickým objektem. [Služba Azure Digital](../digital-twins/index.yml) reprezentujes používá [modely](#model) vyjádřené v [jazyce DTDL (Digital s Definition Language)](#digital-twins-definition-language-dtdl) , který představuje digitální vlákna fyzických zařízení nebo abstraktních základních obchodních konceptů vyšší úrovně a umožňuje široké spektrum cloudových digitálních vlákenných řešení. Zařízení [IoT technologie Plug and Play](../iot-pnp/index.yml) má digitální vlákna popsané [modelem zařízení](#device-model)DTDL.

### <a name="digital-twin-change-events"></a>Události změn digitálního dvojčete

Když je [zařízení iot technologie Plug and Play](#iot-plug-and-play-device) připojené ke službě IoT Hub, může centrum využít schopnost směrování k posílání oznámení o digitálních prostředcích, které se mění. Například pokaždé, když se v zařízení změní hodnota [vlastnosti](#properties) , IoT Hub může odeslat oznámení na koncový bod, jako je například centrum událostí.

### <a name="digital-twin-route"></a>Digitální dvojitá trasa

Trasa nastavená ve IoT Hub pro doručování [událostí digitální události změny](#digital-twin-change-events) do koncového bodu, jako je například centrum událostí.

### <a name="digital-twins-definition-language-dtdl"></a>Jazyk DTDL (Digital Twins Definition Language)

Jazyk JSON pro popis [modelů](#model) a [rozhraní](#interface) pro [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) a entit [digitálních vláken Azure](../digital-twins/index.yml) . K popisu možností [digitálního vlákna](#digital-twin) a k tomu, aby mohla platforma IoT a řešení IoT používat sémantiku této entity, použijte jazyk digitálního vytvářené [definice verze 2](https://github.com/Azure/opendigitaltwins-dtdl) . Jazyk definice digitálních vláken se často zkracuje jako DTDL.

### <a name="direct-method"></a>Direct – metoda

[Přímá metoda](../iot-hub/iot-hub-devguide-direct-methods.md) je způsob, jak aktivovat metodu, která se spustí na zařízení vyvoláním rozhraní API ve službě IoT Hub.

### <a name="downstream-services"></a>Služby pro příjem dat

Relativní pojem popisující služby, které přijímají data z aktuálního kontextu. Pokud se například zvažujete v kontextu digitálních vláken Azure, [Time Series Insights](../time-series-insights/index.yml) by se považoval za službu pro příjem dat, pokud nastavíte data, která se mají směrovat z digitálních vláken azure do Time Series Insights.

## <a name="e"></a>E

### <a name="endpoint"></a>Koncový bod

Pojmenovaná reprezentace služby směrování dat, která může přijímat data z jiných služeb.

IoT Hub zpřístupňuje několik [koncových bodů](../iot-hub/iot-hub-devguide-endpoints.md) , které umožní vašim aplikacím připojení ke službě IoT Hub. K dispozici jsou koncové body pro zařízení, které umožňují zařízením provádět operace, jako je posílání zpráv [typu zařízení-Cloud](#device-to-cloud) a příjem zpráv [typu cloud-zařízení](#cloud-to-device) . K dispozici jsou koncové body správy, které umožňují [back-endové aplikace](#back-end-app) provádět operace, jako je Správa [identit zařízení](#device-identity) a Správa vláken zařízení. Pro čtení zpráv ze zařízení do cloudu jsou k dispozici [integrované koncové body](#built-in-endpoints) pro služby. Můžete vytvořit [vlastní koncové body](#custom-endpoints) pro příjem zpráv typu zařízení-Cloud odeslaných [pravidlem směrování](#routing-rules).

### <a name="enrollment"></a>Registrace

V rámci [služby Device Provisioning](#device-provisioning-service)je zápis záznamem jednotlivých zařízení nebo skupin zařízení, která se můžou registrovat u [propojeného centra IoT](#linked-iot-hub) prostřednictvím automatického zřizování.

### <a name="enrollment-group"></a>Skupina registrace

Ve [službě Device Provisioning](#device-provisioning-service)skupina pro registraci identifikuje skupinu zařízení, která sdílejí [mechanismus ověřování](#attestation-mechanism)X. 509 nebo symetrického klíče.

### <a name="event-handlers"></a>Obslužné rutiny událostí

To může odkazovat na jakýkoli proces, který je aktivován přijetím události a provede nějakou akci zpracování. Jedním ze způsobů, jak vytvářet obslužné rutiny událostí, je přidání kódu pro zpracování událostí do funkce Azure a posílání dat pomocí [koncových bodů](#endpoint) a [Směrování událostí](#event-routing).

### <a name="event-hub-compatible-endpoint"></a>Koncový bod kompatibilní s centrem událostí

Chcete-li číst zprávy ze [zařízení do cloudu](#device-to-cloud) , které jsou odesílány do služby IoT Hub, můžete se připojit ke koncovému bodu na vašem rozbočovači a ke čtení těchto zpráv použít jakoukoli metodu kompatibilní s centrem událostí. Metody, které jsou kompatibilní s centrem událostí, zahrnují použití [sad Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) a [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

### <a name="event-routing"></a>Směrování událostí

Proces odeslání událostí a jejich dat z jednoho zařízení nebo služby na [koncový bod](#endpoint) jiného. 

V centru IoT Hub můžete definovat [pravidla směrování](#routing-rules) , která popisují, jak se mají zprávy odesílat. V případě digitálních vláken Azure jsou trasy událostí entity vytvořené pro tento účel. Trasy událostí digitálních vláken Azure můžou obsahovat filtry k omezení toho, jaké typy událostí se odesílají do každého koncového bodu.

## <a name="f"></a>F

### <a name="field-gateway"></a>Brána pole

Brána pole umožňuje připojení pro zařízení, která se nemůžou přímo připojit k [IoT Hub](#iot-hub) a jsou obvykle nasazená místně pomocí vašich zařízení. Další informace najdete v tématu [co je Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>brána

Brána umožňuje připojení pro zařízení, která se nemůžou přímo připojit k [IoT Hub](#iot-hub). Viz také [pole Brána](#field-gateway), [cloudová brána](#cloud-gateway)a [vlastní brána](#custom-gateway).

### <a name="gateway-device"></a>Zařízení brány

Zařízení je příkladem [brány pole](#field-gateway). Zařízením brány může být standardní [zařízení](#device) IoT nebo [zařízení IoT Edge](#iot-edge-device).

Zařízení brány umožňuje připojení pro podřízená zařízení, která se nemůžou přímo připojit k [IoT Hub](#iot-hub).

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Modul hardwarového zabezpečení

Modul hardwarového zabezpečení (HSM) se používá k zabezpečenému hardwarovému úložišti tajných kódů zařízení. Je to nejbezpečnější forma tajného úložiště pro zařízení. V modulu hardwarového zabezpečení (HSM) může být uložen jak certifikát X. 509, tak i symetrických klíčů. V rámci [služby Device Provisioning](#device-provisioning-service)může [mechanismus ověřování](#attestation-mechanism) používat modul hardwarového zabezpečení (HSM).

## <a name="i"></a>I

### <a name="id-scope"></a>Rozsah ID

Obor ID je jedinečná hodnota přiřazená k instanci [služby Device Provisioning Service (DPS)](#device-provisioning-service) , když je vytvořená.

IoT Central aplikace využívají instance DPS a zpřístupňují rozsah ID prostřednictvím uživatelského rozhraní IoT Central.

### <a name="identity-registry"></a>Registr identit

[Registr identit](../iot-hub/iot-hub-devguide-identity-registry.md) je integrovaná součást služby IoT Hub, která ukládá informace o jednotlivých zařízeních povolených pro připojení ke službě IoT Hub.

### <a name="individual-enrollment"></a>Jednotlivé registrace

V rámci [služby Device Provisioning](#device-provisioning-service)označuje jednotlivý zápis jedno zařízení, které jako [mechanismus ověřování](#attestation-mechanism)používá listový certifikát X. 509 nebo symetrický klíč.

### <a name="interactive-message"></a>Interaktivní zpráva

Interaktivní zpráva je zpráva typu [Cloud-zařízení](#cloud-to-device) , která aktivuje okamžitou akci v back-endu řešení. Zařízení může například odeslat alarm o selhání, které by se mělo automaticky přihlásit do systému CRM.

### <a name="interface"></a>Rozhraní

V IoT technologie Plug and Play rozhraní popisuje související možnosti, které jsou implementované pomocí [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) nebo [digitálního vlákna](#digital-twin). Můžete opakovaně používat rozhraní napříč různými [modely zařízení](#device-model). Když se v modelu zařízení používá rozhraní, definuje [komponentu](#component) zařízení. Jednoduché zařízení obsahuje pouze výchozí rozhraní.

V rámci digitálních vláken Azure se dá *rozhraní* použít k odkazování na položku kódu nejvyšší úrovně v definici modelu [DTDL](#digital-twins-definition-language-dtdl) .

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge umožňuje cloudové nasazení služeb Azure a kódu specifického pro řešení do místních zařízení. [Zařízení IoT Edge](#iot-edge-device) můžou agregovat data z jiných zařízení a provádět výpočetní a analytické prostředí před odesláním dat do cloudu. Další informace najdete v tématu [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agent IoT Edge

Součást modulu runtime IoT Edge zodpovědná za nasazení a monitorování modulů.

### <a name="iot-edge-device"></a>Zařízení IoT Edge

IoT Edge zařízení používá ke spouštění služeb Azure, služeb třetích stran nebo vlastního kódu kontejnerové IoT Edge [moduly](#modules) . V zařízení IoT Edge modul [runtime IoT Edge](#iot-edge-runtime) spravuje moduly. Zařízení IoT Edge můžete vzdáleně monitorovat a spravovat z cloudu.

### <a name="iot-edge-hub"></a>Centrum IoT Edge

Součást modulu runtime IoT Edge zodpovědná za modul komunikace, nadřazeného streamování (směrem k IoT Hub) a podřízeného (od IoT Hub) komunikace.

### <a name="iot-edge-runtime"></a>Modul runtime IoT Edge

IoT Edge runtime zahrnuje všechno, co Microsoft distribuuje, aby se nainstaloval na IoT Edge zařízení. Zahrnuje hraničního agenta, hraničního centra a IoT Edge démona zabezpečení.

### <a name="iot-extension-for-azure-cli"></a>Rozšíření IoT pro Azure CLI

[Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) je nástroj příkazového řádku pro různé platformy. Tento nástroj umožňuje spravovat zařízení v [registru identit](#identity-registry), odesílat a přijímat zprávy a soubory z vašich zařízení a monitorovat operace služby IoT Hub.

### <a name="iot-hub"></a>IoT Hub

IoT Hub je plně spravovaná služba Azure, která umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. Další informace najdete v tématu [co je Azure IoT Hub?](../iot-hub/about-iot-hub.md). Pomocí svého předplatného Azure můžete vytvořit centra IoT pro zpracování vašich úloh zasílání zpráv IoT.

### <a name="iot-hub-metrics"></a>IoT Hub metriky

IoT Hub metriky poskytují údaje o stavu služby IoT Hub ve vašem předplatném Azure. IoT Hub metriky umožňují posoudit celkový stav služby a zařízení, která jsou k ní připojená. IoT Hub metriky vám pomůžou zjistit, co se chystá ve službě IoT Hub, a prozkoumat problémy hlavní příčiny, aniž byste museli kontaktovat podporu Azure. Další informace najdete v tématu [monitorování IoT Hub](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>Dotazovací jazyk IoT Hub

[Dotazovací jazyk IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) je jazyk podobný jazyku SQL, který umožňuje dotazovat se na [úlohy](#job), digitální vlákna a vlákna zařízení.

### <a name="iot-hub-resource-rest-api"></a>REST API prostředků IoT Hub

Pomocí [REST API prostředků IoT Hub](/rest/api/iothub/iothubresource) můžete spravovat centra IoT v předplatném Azure, které provádí operace, jako je vytváření, aktualizace a odstraňování Center.

### <a name="iot-plug-and-play-bridge"></a>Přemostění IoT Plug and Play

IoT technologie Plug and Play most je open-source aplikace, která umožňuje stávající senzory a periferní zařízení připojená k bránám systému Windows nebo Linux připojit se jako [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-conventions"></a>Konvence IoT Plug and Play

U [zařízení](#iot-plug-and-play-device) IoT technologie Plug and Play se očekává, že se při výměně dat s řešením dodržujte se sadou konvencí.

### <a name="iot-plug-and-play-device"></a>Zařízení IoT technologie Plug and Play

Zařízení IoT technologie Plug and Play je typicky malé škálované samostatné výpočetní zařízení, které shromažďuje data nebo řídí jiná zařízení a spouští software nebo firmware, který implementuje [model zařízení](#device-model).  Například zařízení IoT technologie Plug and Play může být zařízením pro monitorování prostředí nebo kontroler pro zavlažovací systémy s inteligentním zemědělstvím. Zařízení IoT technologie Plug and Play může být implementováno přímo nebo jako IoT Edge modul. Řešení IoT hostované v cloudu můžete napsat pro příkazy, řízení a příjem dat ze zařízení IoT technologie Plug and Play.

### <a name="iot-solution-accelerators"></a>Akcelerátory řešení IoT

Akcelerátory řešení Azure IoT společně více služeb Azure do řešení zabalí. Tato řešení vám umožní rychle začít pracovat s komplexními implementacemi běžných scénářů IoT. Další informace najdete v tématu [co jsou akcelerátory řešení Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>Úloha

Back-end vašeho řešení může pomocí [úloh](../iot-hub/iot-hub-devguide-jobs.md) plánovat a sledovat aktivity na sadě zařízení zaregistrovaných ve službě IoT Hub. Mezi aktivity patří aktualizace [požadovaných vlastností](#desired-properties)nečinnosti zařízení, aktualizace dvojitých [značek](#tags)zařízení a vyvolávání [přímých metod](#direct-method). [IoT Hub](#iot-hub) používá také k [importu a exportu](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [registru identit](#identity-registry).

## <a name="l"></a>L

### <a name="leaf-device"></a>Zařízení typu list

V [IoT Edge](#iot-edge)je na listovém zařízení zařízení bez navazujícího zařízení.

### <a name="lifecycle-event"></a>Událost životního cyklu

U digitálních vláken Azure se tento typ události aktivuje, když se z instance digitálního vlákna Azure vytvoří nebo odstraní datová položka, například digitální vlákna, vztah nebo obslužná rutina události.

### <a name="linked-iot-hub"></a>Propojené centrum IoT

[Služba Device Provisioning (DPS)](#device-provisioning-service)umožňuje zřídit zařízení do služby IoT Hub, které s ní byly propojené. Propojení služby IoT Hub s instancí DPS umožňuje službě zaregistrovat ID zařízení a nastavit počáteční konfiguraci v zařízení s dvojitou inicializací.

## <a name="m"></a>M

### <a name="model"></a>Modelování

Model definuje typ entity ve vašem fyzickém prostředí, včetně jejích vlastností, telemetrií, komponent a občas i dalších informací. Modely slouží k vytváření [digitálních vláken](#digital-twin) , která představují konkrétní fyzické objekty tohoto typu. Modely jsou napsané v [jazyce definice digitálních vláken](#digital-twins-definition-language-dtdl).

V rámci [služby digitálních vláken Azure](../digital-twins/index.yml)mohou modely definovat zařízení nebo abstraktní obchodní koncepce vyšší úrovně. V [technologie Plug and Play IoT](../iot-pnp/index.yml)jsou [modely zařízení](#device-model) používány k popisu zařízení konkrétně.

### <a name="model-id"></a>ID modelu

Když se zařízení IoT technologie Plug and Play připojí k IoT Hub, pošle **ID modelu** [DTDL](#digital-twins-definition-language-dtdl) modelu, který implementuje. Toto ID umožňuje řešení najít model zařízení.

### <a name="model-repository"></a>Úložiště modelů

Úložiště modelu ukládá [modely zařízení](#device-model) a [rozhraní](#interface).

### <a name="model-repository-rest-api"></a>REST API úložiště modelu

Rozhraní API pro správu a interakci s úložištěm modelu. Rozhraní API můžete například použít k přidání a hledání [modelů zařízení](#device-model).

### <a name="module-builder"></a>Tvůrce modulů

Tvůrce modulů používá [model zařízení](#device-model) a [rozhraní](#interface) při implementaci kódu ke spuštění na [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). Tvůrci modulů implementují kód jako modul nebo modul IoT Edge k nasazení do modulu runtime IoT Edge na zařízení.

### <a name="module-identity"></a>Identita modulu

Identita modulu je jedinečný identifikátor přiřazený ke každému modulu, který patří do zařízení. Identita modulu je zaregistrovaná taky v [registru identit](#identity-registry).

Modul identifikuje podrobnosti zabezpečovacích přihlašovacích údajů, které modul používá k ověření pomocí [IoT Hub](#iot-hub) nebo, v případě IoT Edge modulu do [centra IoT Edge](#iot-edge-hub).

### <a name="module-image"></a>Obrázek modulu

Obrázek Docker, který modul [runtime IoT Edge](#iot-edge-runtime) používá k vytvoření instance instancí modulu.

### <a name="module-twin"></a>Modul je nevlákenný

Podobně jako u zařízení je nevlákenný modul a je dokument JSON, který ukládá informace o stavu modulu, jako jsou metadata, konfigurace a podmínky. IoT Hub uchovává modul s dvojitou identitou pro každou identitu modulu, kterou zřídíte v rámci identity zařízení ve službě IoT Hub. Vlákna modulu umožňují synchronizovat podmínky a konfigurace modulů mezi modulem a back-endu řešení. Můžete se dotazovat na vlákna modulu a vyhledat konkrétní moduly a zadat dotaz na stav dlouhotrvajících operací.

### <a name="modules"></a>Moduly

Na straně zařízení vám sady SDK pro IoT Hub zařízení umožňují vytvářet [moduly](../iot-hub/iot-hub-devguide-module-twins.md) , ve kterých každá z nich otevře nezávislé připojení k IoT Hub. Tato funkce umožňuje používat samostatné obory názvů pro různé komponenty na vašem zařízení.

Identita modulu a modul s dvojitou přesností poskytují stejné možnosti jako [Identita zařízení](#device-identity) a [vyzdvojené zařízení](#device-twin) , ale s jemnější členitosti. Tato přesnější členitost umožňuje zařízením, jako jsou zařízení s operačním systémem nebo zařízení firmwaru, spravovat víc komponent, izolovat konfiguraci a podmínky pro každou z těchto součástí.

V [IoT Edge](#iot-edge)modul je kontejner Docker, který můžete nasadit do IoT Edge zařízení. Provede konkrétní úlohu, například ingestování zprávy ze zařízení, transformuje zprávu nebo posílá zprávu do služby IoT Hub. Komunikuje s ostatními moduly a odesílá data do [modulu runtime IoT Edge](#iot-edge-runtime).

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) je jedním z protokolů zasílání zpráv, které [IoT Hub](#iot-hub) podporuje pro komunikaci se zařízeními. Další informace o protokolech zasílání zpráv, které IoT Hub podporuje, najdete v tématu [posílání a přijímání zpráv pomocí IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="ontology"></a>Ontology

Sada modelů pro určitou doménu, jako jsou například reality, inteligentní města, systémy IoT, energetické mřížky a další. Ontologie se často používají jako schémata pro znalostní grafy, jako jsou ta v [Azure Digital provlákna](#azure-digital-twins), protože poskytují výchozí bod na základě oborových standardů a osvědčených postupů.

Další informace o ontologie najdete v tématu [co je to Ontology?](../digital-twins/concepts-ontologies.md)

### <a name="operations-monitoring"></a>Monitorování operací

IoT Hub [monitorování provozu](../iot-hub/iot-hub-operations-monitoring.md) vám umožní monitorovat stav operací ve službě IoT Hub v reálném čase. [IoT Hub](#iot-hub) sleduje události napříč několika kategoriemi operací. Můžete se rozhodnout, že budete odesílat události z jedné nebo více kategorií na IoT Hub koncový bod pro zpracování. Můžete monitorovat data pro chyby nebo nastavit složitější zpracování na základě vzorců dat.

## <a name="p"></a>P

### <a name="physical-device"></a>Fyzické zařízení

Fyzické zařízení je reálné zařízení, jako je například malin PI, který se připojuje ke centra IoT. Mnohé z IoT Hubových kurzů pro usnadnění práce využívají [simulovaná zařízení](#simulated-device) , která umožňují spouštět ukázky na místním počítači.

### <a name="primary-and-secondary-keys"></a>Primární a sekundární klíče

Když se připojíte ke koncovému bodu s přístupem k zařízení nebo ke službě v centru IoT Hub, váš [připojovací řetězec](#connection-string) obsahuje klíč, který vám udělí přístup. Když přidáte zařízení do [registru identity](#identity-registry) nebo přidáte [zásadu sdíleného přístupu](#shared-access-policy) do svého rozbočovače, služba vygeneruje primární a sekundární klíč. Použití dvou klíčů vám umožní při aktualizaci klíče přejít z jednoho klíče na jiný, aniž by došlo ke ztrátě přístupu ke službě IoT Hub.

### <a name="properties"></a>Vlastnosti

Vlastnosti jsou datová pole definovaná v [rozhraní](#interface) , které představuje nějaký trvalý stav [digitálního vlákna](#digital-twin). Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Vlastnosti jen pro čtení, například sériové číslo, jsou nastaveny pomocí kódu spuštěného v rámci samotného [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device) . Vlastnosti s možností zápisu, jako je například prahová hodnota pro alarm, jsou obvykle nastaveny z cloudového řešení IoT.

### <a name="property-change-event"></a>Událost změny vlastnosti

Událost, která je výsledkem změny vlastnosti v rámci [digitálního vlákna](#digital-twin).

### <a name="protocol-gateway"></a>Protokolová brána

Brána protokolu je obvykle nasazená v cloudu a poskytuje služby překladu protokolů pro zařízení, která se připojují k [IoT Hub](#iot-hub). Další informace najdete v tématu [co je Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="registration"></a>Registrace

Registrace je záznam zařízení v [registru IoT Hub identity](#identity-registry). K automatizaci registrace zařízení můžete použít [službu Device Provisioning](#device-provisioning-service) nebo zařízení přímo registrovat.

### <a name="registration-id"></a>ID registrace

ID registrace slouží k jednoznačné identifikaci [registrace](#registration) zařízení ve [službě Device Provisioning](#device-provisioning-service). ID registrace může mít stejnou hodnotu jako [Identita zařízení](#device-identity).

### <a name="relationship"></a>Relace

Ve službě [Azure Digital](../digital-twins/index.yml) probíhají vztahy se používají k propojení [digitálních vláken](#digital-twin) do diagramů znalostí, které digitálně představují celé fyzické prostředí. Typy vztahů, které může mít vaše vlákna, jsou definovány jako součást definic [modelů](#model) s dvojitou platností – model [DTDL](#digital-twins-definition-language-dtdl) pro určitý typ vlákna obsahuje informace o tom, jaké relace může mít jiné vlákna.

### <a name="reported-configuration"></a>Nahlášená konfigurace

V kontextu [vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md)nahlášená konfigurace odkazuje na kompletní sadu vlastností a metadat v zařízení, které by měly být hlášeny do back-endu řešení.

### <a name="reported-properties"></a>Hlášené vlastnosti

V kontextu zařízení, které je s [dvojitou](../iot-hub/iot-hub-devguide-device-twins.md)podmínkou, jsou hlášené vlastnosti pododdílem vlákna v zařízení, které se používá s [požadovanými vlastnostmi](#desired-properties) k synchronizaci konfigurace nebo podmínky zařízení. Hlášené vlastnosti může nastavit jenom [aplikace zařízení](#device-app) a můžou je číst a dotazovat v [back-endové aplikaci](#back-end-app).

### <a name="retry-policy"></a>Zásady opakování

Zásady opakování se používají ke zpracování [přechodných chyb](/azure/architecture/best-practices/transient-faults) při připojování ke cloudové službě.

### <a name="routing-rules"></a>Pravidla směrování

[Pravidla směrování](../iot-hub/iot-hub-devguide-messages-read-custom.md) ve službě IoT Hub nakonfigurujete tak, aby směrovala zprávy typu zařízení-Cloud do [integrovaného koncového bodu](#built-in-endpoints) nebo do [vlastních koncových bodů](#custom-endpoints) pro zpracování back-endu vašeho řešení.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN je protokol, který protokol AMQP používá k přenosu tokenů zabezpečení.

### <a name="service-operations-endpoint"></a>Koncový bod operací služby

[Koncový bod](#endpoint) pro správu nastavení služby, které používá správce služby. Například ve [službě Device Provisioning](#device-provisioning-service) se ke správě registrací používá koncový bod služby.

### <a name="service-rest-api"></a>Rozhraní API služby REST

Ke správě svých zařízení můžete použít [REST API služby](/rest/api/iothub/service/configuration) z back-endu řešení. Rozhraní API umožňuje načítat a aktualizovat vlastnosti, které jsou v [zařízení](#device-twin) , vyvolat [přímé metody](#direct-method)a plánovat [úlohy](#job). Obvykle byste měli použít jednu ze [sad SDK služby](#azure-iot-service-sdks) vyšší úrovně, jak je znázorněno v kurzech IoT Hub.

### <a name="shared-access-policy"></a>Zásady sdíleného přístupu

Zásady sdíleného přístupu definují oprávnění udělená všem, kteří mají platný [primární nebo sekundární klíč](#primary-and-secondary-keys) přidružený k této zásadě. Zásady sdíleného přístupu a klíče pro vaše centrum můžete spravovat na portálu.

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

Sdílené přístupové podpisy (SAS) jsou mechanismy ověřování založené na zabezpečených hodnotách hash nebo identifikátorech URI SHA-256. Ověřování SAS má dvě komponenty: _zásady sdíleného přístupu_ a _sdílený přístupový podpis_ (často se označuje jako token). Zařízení používá SAS k ověřování ve službě IoT Hub. [Back-endové aplikace](#back-end-app) taky používají k ověřování koncových bodů s přístupem ke službám ve službě IoT Hub také SAS. Obvykle zahrnete token SAS do [připojovacího řetězce](#connection-string) , který aplikace používá k navázání připojení ke službě IoT Hub.

### <a name="simulated-device"></a>Simulované zařízení

Mnohé z IoT Hubových kurzů pro usnadnění práce využívají simulovaná zařízení, která umožňují spouštět ukázky na místním počítači. Naproti tomu je [fyzické zařízení](#physical-device) reálné zařízení, jako je například Malina, která se připojuje ke centru IoT.

### <a name="solution"></a>Řešení

_Řešení_ může odkazovat na řešení sady Visual Studio, které obsahuje jeden nebo více projektů. _Řešení_ může také znamenat řešení IoT, které zahrnuje prvky, jako jsou zařízení, [aplikace zařízení](#device-app), Centrum IoT, další služby Azure a [back-endové aplikace](#back-end-app).

### <a name="solution-builder"></a>Tvůrce řešení

Tvůrce řešení vytvoří back-end řešení. Tvůrce řešení obvykle funguje s prostředky Azure, jako jsou IoT Hub a [úložiště modelu](#model-repository).

### <a name="system-properties"></a>Systémové vlastnosti

V kontextu [vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md)jsou systémové vlastnosti jen pro čtení a obsahují informace týkající se využití zařízení, jako je například čas poslední aktivity a stav připojení.

## <a name="t"></a>T

### <a name="tags"></a>Značky

V kontextu [vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md)jsou značky metadata zařízení uložená a načtená back-end řešení ve formě dokumentu JSON. Značky nejsou pro aplikace na zařízení viditelné.

### <a name="target-condition"></a>Cílová podmínka

V nasazení IoT Edge cílový stav vybere cílová zařízení nasazení, například **tag. Environment = prod**. Cílová podmínka se průběžně vyhodnocuje tak, aby zahrnovala nová zařízení, která splňují požadavky, nebo odebrat zařízení, která už nedělají.

### <a name="telemetry"></a>Telemetrie

Zařízení shromažďují data telemetrie, například rychlost větru nebo teplotu, a používají zprávy datových bodů k odeslání telemetrie do služby IoT Hub.

V technologie Plug and Play IoT a digitálních podudálostech Azure jsou pole telemetrie definovaná v [rozhraní](#interface) představovat měření. Tato měření jsou obvykle hodnoty, jako jsou například čtení senzorů, které jsou posílány pomocí zařízení, jako jsou například [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device)jako datový proud dat.

Na rozdíl od [vlastností](#properties)telemetrie není uložená na [digitálním vlákna](#digital-twin); Jedná se o datový proud událostí s datovou vazbou, které je potřeba zpracovat při jejich výskytu.

### <a name="telemetry-event"></a>Událost telemetrie

Událost, která indikuje doručení dat telemetrie.

### <a name="token-service"></a>Služba tokenů

Službu tokenů můžete použít k implementaci mechanismu ověřování pro vaše zařízení. Používá [zásady sdíleného přístupu](#shared-access-policy) IoT Hub s oprávněními **DeviceConnect** k vytváření tokenů v *oboru zařízení* . Tyto tokeny umožňují zařízení připojit se ke službě IoT Hub. Zařízení používá k ověření pomocí služby tokenu vlastní mechanismus ověřování. Pokud se zařízení úspěšně ověřuje, služba tokenu vydá token SAS, který zařízení používá pro přístup ke službě IoT Hub.

### <a name="twin-graph-or-digital-twin-graph"></a>Nevlákenný graf (nebo digitální nevlákenný graf)

Ve službě [Digital](../digital-twins/index.yml) propojování Azure můžete propojit [digitální vlákna](#digital-twin) s [relacemi](#relationship) a vytvořit tak znalostní grafy, které digitálně reprezentují celé fyzické prostředí. Jedna [instance digitálního vlákna Azure](#azure-digital-twins-instance) může hostovat mnoho odpojených grafů nebo jeden jeden propojený graf.

### <a name="twin-queries"></a>Zdvojené dotazy

Nenáročné [dotazy na zařízení a moduly](../iot-hub/iot-hub-devguide-query-language.md) používají dotazovací jazyk, který se používá IoT Hub SQL k načítání informací z vašich vláken zařízení nebo vláken modulu. Pomocí stejného dotazovacího jazyka IoT Hub můžete načíst informace o [úloze](#job) běžící ve službě IoT Hub.

### <a name="twin-synchronization"></a>Dvojitá synchronizace

Dvojitá synchronizace používá k nastavování zařízení nebo modulů [požadované vlastnosti](#desired-properties) ve vašich prostředcích nebo v modulech, aby je bylo možné nakonfigurovat a načíst z nich [hlášené vlastnosti](#reported-properties) , aby je bylo možné ukládat.

## <a name="u"></a>U

### <a name="upstream-services"></a>Nadřazené služby

Relativní pojem popisující služby, které zakládá data do aktuálního kontextu. Pokud se například zvažujete v kontextu digitálních vláken Azure, IoT Hub se považuje za nadřazeného služby, protože data se IoT Hub do digitálních vláken Azure.

## <a name="x"></a>×

### <a name="x509-client-certificate"></a>Certifikát klienta X. 509

Zařízení může použít certifikát X. 509 k ověření pomocí [IoT Hub](#iot-hub). Použití certifikátu X. 509 je alternativou k použití [tokenu SAS](#shared-access-signature).
