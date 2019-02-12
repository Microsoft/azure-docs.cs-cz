---
title: Seznámení s řešením propojené továrny – Azure | Microsoft Docs
description: Popis akcelerátoru řešení Azure IoT pro propojenou továrnu a jeho architektury.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 19e340609e80998037938bdad59e9e6e74894bad
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098302"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>Seznámení s akcelerátorem řešení propojené továrny

[Akcelerátor řešení][lnk-preconfigured-solutions] propojené továrny je implementace komplexního průmyslového řešení, které:

* Se připojuje k simulovaným průmyslovým zařízením se servery OPC UA na simulovaných výrobních linkách i ke skutečným zařízením serveru OPC UA. Další informace o OPC UA najdete v tématu [Propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md).
* Ukazuje klíčové ukazatele výkonu a celkovou efektivitu těchto zařízení a výrobních linek.
* Ukazuje možnosti použití cloudové aplikace k interakci se serverovými systémy OPC UA.
* Umožňuje připojení vlastních zařízení serveru OPC UA.
* Umožňuje procházení a úpravy dat na serveru OPC UA.
* Se integruje se službou Azure Time Series Insights (TSI), aby mohlo poskytovat přizpůsobená zobrazení dat ze serverů OPC UA.

Můžete ho použít jako výchozí bod pro vlastní implementaci a [přizpůsobit][lnk-customize] si ho tak, aby splňovalo vaše konkrétní obchodní požadavky.

Tento článek vás provede některými z klíčových prvků řešení propojené továrny, aby vám pomohl pochopit, jak toto řešení funguje. Tento článek také popisuje tok dat v řešení. Díky tomu budete moct:

* Odstraňovat potíže v řešení.
* Naplánujte, jak řešení přizpůsobit podle konkrétních požadavků.
* Navrhněte vlastní řešení IoT, které používá služby Azure.

Další informace najdete v tématu [Propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md).

## <a name="logical-architecture"></a>Logická architektura

Následující diagram popisuje logické součásti tohoto akcelerátoru řešení:

![Logická architektura propojené továrny][connected-factory-logical]

## <a name="communication-patterns"></a>Vzory komunikace

Toto řešení k odesílání telemetrických dat OPC UA do služby IoT Hub ve formátu JSON používá [specifikaci publikování a odběru OPC UA](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/). Řešení pro tento účel používá modul [vydavatele OPC](https://github.com/Azure/iot-edge-opc-publisher) pro IoT Edge.

Řešení má také ve webové aplikaci integrovaného klienta OPC UA, který navazuje připojení k místním serverům OPC UA. Klient používá [reverzní proxy](https://wikipedia.org/wiki/Reverse_proxy) a s pomocí služby IoT Hub vytváří připojení, aniž by vyžadoval otevřené porty na místní bráně firewall. Tento vzor komunikace se nazývá komunikace s asistencí služby. Řešení pro tento účel používá modul [proxy serveru OPC](https://github.com/Azure/iot-edge-opc-proxy/) pro IoT Edge.


## <a name="simulation"></a>Simulace

Simulované stanice a systémy řízení výroby tvoří výrobní linku továrny. Simulovaná zařízení a modul vydavatele OPC jsou založeny na [standardu OPC UA .NET][lnk-OPC-UA-NET-Standard] vydaném nadací OPC Foundation.

Proxy server OPC a vydavatel OPC jsou implementovány jako moduly založené na [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Každá Simulovaná výrobní linka má bránu připojit.

Všechny simulované komponenty jsou spuštěné v kontejnerech Dockeru hostovaných na virtuálních počítačích Azure s Linuxem. Simulace je ve výchozím nastavení nakonfigurovaná tak, aby bylo spuštěno 8 simulovaných výrobních linek.

## <a name="simulated-production-line"></a>Simulovaná výrobní linka

Výrobní linka vyrábí součásti. Se skládá z různých stanic: montážní stanice, testovací stanice a balicí stanice.

Simulace zpracovává a aktualizuje data, která je k dispozici prostřednictvím uzlů OPC UA. Všechny stanice simulované výrobní linky jsou orchestrované systémem řízení výroby (MES) prostřednictvím OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Simulovaný systém řízení výroby

Systém řízení výroby monitoruje všechny stanice na výrobní lince prostřednictvím OPC UA a zjišťuje tak změny stavu stanic. OPC UA volá metody, které řídí stanice a předává produkt z jedné stanice na další, dokud se nedokončí.

## <a name="gateway-opc-publisher-module"></a>Modul vydavatele brány OPC

Modul vydavatele OPC se připojuje ke staničním serverům OPC UA a přihlašuje se k odběru uzlů OPC, které se zřídí. Modul:

1. Převádí data z uzlu do formátu JSON.
1. Šifruje ve formátu JSON.
1. Odešle ve formátu JSON do služby IoT Hub jako zprávy OPC UA Pub/Sub.

Modul vydavatele OPC vyžaduje pouze výchozí port HTTPS (443) a může fungovat se stávající podnikovou infrastrukturou.

## <a name="gateway-opc-proxy-module"></a>Modul proxy serveru brány OPC

Modul proxy serveru brány OPC UA tuneluje binární příkazy OPC UA a řídicí zprávy a vyžaduje pouze výchozí port HTTPS (443). Může fungovat s existující podnikovou infrastrukturou, včetně webových proxy serverů.

Metody IoT Hub Device používá k přenosu balíčků zabalená data protokolu TCP/IP v aplikační vrstvě pro zajištění zabezpečení koncových bodů, šifrování dat a integritu pomocí protokolu SSL/TLS.

Samotný binární protokol OPC UA předávaný přes proxy server používá ověřování a šifrování pomocí UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Modul vydavatele brány OPC se přihlašuje k odběru uzlů serveru OPC UA, aby mohl zjišťovat změny hodnot dat. Pokud se v některém z uzlů zjistí změna dat, modul odešle zprávy do služby Azure IoT Hub.

Služba IoT Hub poskytuje zdroj událostí pro službu Azure TSI. TSI data ukládá po dobu 30 dní na základě časových razítek připojených ke zprávám. Mezi tato data patří:

* Identifikátor OPC UA ApplicationUri
* Identifikátor OPC UA NodeId
* Hodnota uzlu
* Časové razítko zdroje
* Název OPC UA DisplayName

V současné době neumožňuje TSI zákazníkům přizpůsobit, jak dlouho budou chtít zachovat data.

Dotazy služby TSI na data uzlu pomocí podle času **SearchSpan** a agregace podle **OPC UA ApplicationUri** nebo **OPC UA NodeId** nebo **OPC UA DisplayName**.

Pokud chcete načíst data pro měřidla celkové efektivity zařízení a klíčových ukazatelů výkonu a grafů časových řad, řešení agreguje data o daný počet událostí, **součet**, **Avg**, **Min**, a  **Maximální počet**.

Časové řady se sestavují jiným způsobem. Toto řešení vypočítá hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu ze základních dat stanic a bubliny hodnoty pro výrobní linky, továrny a enterprise.

Kromě toho se časové řady pro celkovou efektivitu zařízení a klíčové ukazatele výkonu počítají v aplikaci kdykoli, kdy je připravený zobrazený časový rozsah. Například zobrazení dne se aktualizuje každou celou hodinu.

Zobrazení časových řad dat uzlu přichází přímo z TSI a používá agregaci časového rozsahu.

## <a name="iot-hub"></a>IoT Hub
Služba [IoT Hub][lnk-IoT Hub] přijímá data odesílaná z modulu vydavatele OPC do cloudu a zpřístupňuje je službě Azure TSI. 

Služba IoT Hub v tomto řešení také:
- Udržuje registr identit, ve kterém jsou uložena ID všech modulů vydavatele OPC a všech modulů proxy serveru OPC.
- Pro obousměrnou komunikaci modulu proxy serveru OPC používá přenosový kanál.

## <a name="azure-storage"></a>Azure Storage
Řešení používá jako diskové úložiště pro virtuální počítač a k ukládání dat nasazení službu Azure Blob Storage.

## <a name="web-app"></a>Webová aplikace
Webová aplikace nasazená jako součást akcelerátor řešení zahrnuje integrovaného klienta OPC UA, zpracování upozornění a vizualizace telemetrie.

## <a name="telemetry-data-flow"></a>Tok telemetrických dat

![Tok telemetrických dat](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Kroky toku

1. Vydavatel OPC načte požadované certifikáty OPC UA X509 a zabezpečovací přihlašovací údaje ke službě IoT Hub z místního úložiště certifikátů.
    - V případě potřeby vydavatel OPC všechny chybějící certifikáty vytvoří a uloží do úložiště certifikátů.

2. Vydavatel OPC se zaregistruje ve službě IoT Hub.
    - Použije nakonfigurovaný protokol. Může použít jakýkoli protokol podporovaný klientskou sadou SDK služby IoT Hub. Výchozí protokol je MQTT.
    - Komunikace protokolu je zabezpečená pomocí protokolu TLS.

3. Vydavatel OPC načte konfigurační soubor.

4. Vydavatel OPC vytvoří relaci OPC s každým nakonfigurovaným serverem OPC UA.
    - Použije připojení přes protokol TCP.
    - Vydavatel OPC a server OPC UA se navzájem ověří pomocí certifikátů X509.
    - Veškerý následný provoz OPC UA je šifrovaný nakonfigurovaným šifrovacím mechanismem OPC UA.
    - Vydavatel OPC v každé relaci OPC pro všechny nakonfigurované intervaly publikování vytvoří předplatné OPC.
    - Vytvoří v předplatném OPC monitorované položky OPC, které budou publikovat uzly OPC.

5. Pokud se změní hodnota monitorovaného uzlu OPC, server OPC UA odešle aktualizace vydavateli OPC.

6. Vydavatel OPC převede novou hodnotu.
    - Pokud je povolené dávkování, spojí více změn do dávky.
    - Vytvoří zprávu pro službu IoT Hub.

7. Vydavatel OPC odešle zprávu do služby IoT Hub.
    - Použije nakonfigurovaný protokol.
    - Komunikace je zabezpečená pomocí protokolu TLS.

8. Time Series Insights (TSI) přečte zprávy ze služby IoT Hub.
    - Použije rozšířený protokol řízení front zpráv (AMQP) přes protokol TCP/TLS.
    - Tento krok se provádí interně v datovém centru.

9. Neaktivní uložená data jsou ve službě TSI.

10. Webová aplikace propojené továrny ve službě Azure App Service zadá dotaz na požadovaná data ze služby TSI.
    - Použije zabezpečenou komunikaci přes protokol TCP/TLS.
    - Tento krok se provádí interně v datovém centru.

11. Webový prohlížeč se připojí k webové aplikaci propojené továrny.
    - Zobrazí řídicí panel připojené továrny.
    - Připojí se přes protokol HTTPS.
    - Přístup k aplikaci propojené továrny vyžaduje ověření uživatele přes službu Azure Active Directory.
    - Všechna volání aplikace propojené továrny z webového rozhraní API jsou zabezpečená pomocí tokenů proti padělání.

12. Při aktualizaci dat odešle webová aplikace propojené továrny aktualizovaná data do webového prohlížeče.
    - Použije protokol SignalR.
    - Zabezpečení zajišťuje protokol TCP/TLS.

## <a name="browsing-data-flow"></a>Procházení toku dat

![Procházení toku dat](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Kroky toku

1. Spustí se proxy server OPC (součást serveru).
    - Načte sdílené přístupové klíče z místního úložiště.
    - V případě potřeby do úložiště uloží chybějící klíče.

2. Proxy server OPC (součást serveru) se zaregistruje ve službě IoT Hub.
    - Načte ze služby IoT Hub všechna známá zařízení.
    - Použije protokol MQTT přes protokol TLS přes soket nebo zabezpečený WebSocket.

3. Webový prohlížeč se připojí k webové aplikace připojené továrny a zobrazí řídicí panel připojené továrny.
    - Použije protokol HTTPS.
    - Uživatel vybere server OPC UA, ke kterému se má připojit.

4. Webová aplikace propojené továrny vytvoří relaci OPC UA s vybraným serverem OPC UA.
    - Použije sadu OPC UA.

5. Přenos proxy serveru OPC přijme ze sady OPC UA žádost o navázání připojení k serveru OPC UA přes soket TCP.
    - Pouze načte datovou část protokolu TCP a použije ji beze změny.
    - Tento krok probíhá interně ve webové aplikaci propojené továrny.

6. Proxy server OPC (součást serveru) vyhledá zařízení proxy serveru OPC (součást serveru) v registru zařízení služby IoT Hub. Pak zavolá metodu zařízení proxy serveru OPC (součást serveru) ve službě IoT Hub.
    - K vyhledání proxy serveru OPC použije protokol HTTPS přes protokol TCP/TLS.
    - K navázání připojení k serveru OPC UA přes soket TCP použije protokol HTTPS přes protokol TCP/TLS.
    - Tento krok se provádí interně v datovém centru.

7. IoT Hub zavolá metodu zařízení proxy serveru OPC (součást serveru).
    - K navázání připojení k serveru OPC UA přes soket TCP použije navázané připojení MQTT přes protokol TLS přes soket nebo zabezpečený WebSocket.

8. Proxy server OPC (součást serveru) odešle datovou část protokolu TCP do provozní sítě.

9. Server OPC UA zpracuje datovou část a odešle zpět odpověď.

10. Odpověď přijme soket proxy serveru OPC (součást serveru).
    - Proxy server OPC odešle data jako návratovou hodnotu metody zařízení do služby IoT Hub a na proxy server OPC (součást serveru).
    - Tato data se doručí do sady OPC UA v aplikaci propojené továrny.

11. Webová aplikace propojené továrny vrátí do webového prohlížeče k vykreslení uživatelské rozhraní OPC v prohlížeči obohacené o informace specifické pro OPC UA, které získala ze serveru OPC UA.
    - Zatímco uživatel prochází adresního prostoru OPC a funkce se vztahuje na uzly v adresním prostoru OPC, klient UX prohlížeče OPC používá volání AJAX přes protokol HTTPS zabezpečená pomocí tokenů proti padělání k získání dat z webové aplikace připojené továrny.
    - V případě potřeby klient použije k výměně informací se serverem OPC UA komunikaci popsanou v krocích 4–10.

> [!NOTE]
> Proxy server OPC (součást serveru) a součást proxy serveru OPC (klient) provedou kroky 4–10 pro veškerý provoz přes protokol TCP související s komunikací OPC UA.

> [!NOTE]
> U serveru OPC UA a sady OPC UA v rámci webové aplikace propojené továrny je komunikace proxy serveru OPC transparentní a používají se všechny ověřovací a šifrovací funkce zabezpečení OPC UA.

## <a name="next-steps"></a>Další postup

Další informace o akcelerátorech řešení IoT najdete v následujících článcích:

* [Oprávnění na webu azureiotsuite.com][lnk-permissions]
* [Nasazení brány pro akcelerátor řešení propojené továrny ve Windows nebo v Linuxu](iot-accelerators-connected-factory-gateway-deployment.md)
* [Referenční implementace vydavatele OPC](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md)

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
