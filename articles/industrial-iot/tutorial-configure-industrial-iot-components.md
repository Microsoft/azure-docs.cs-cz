---
title: Konfigurace komponent Azure pro průmyslové služby IoT
description: V tomto kurzu se naučíte, jak změnit výchozí hodnoty konfigurace.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787653"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Kurz: Konfigurace komponent pro průmyslové IoT

Skript nasazení automaticky nakonfiguruje všechny součásti tak, aby se navzájem spolupracovaly s použitím výchozích hodnot. Nastavení výchozích hodnot ale můžete změnit tak, aby splňovalo vaše požadavky.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobení konfigurace komponent


Tady jsou některé z důležitých nastavení přizpůsobení pro tyto komponenty:
* IoT Hub
    * Sítě → veřejný přístup: Konfigurace přístupu k Internetu, například filtry IP
    * Připojení privátních koncových bodů sítě →: Vytvoření koncového bodu, který není přístupný prostřednictvím Internetu, a může být interně využíván jinými službami Azure nebo místními zařízeními (například prostřednictvím připojení VPN).
    * IoT Edge: Správa konfigurace hraničních zařízení, která jsou připojená k serverům OPC UA 
* Cosmos DB
    * Globální replikace dat: Konfigurace redundance dat
    * Brána firewall a virtuální sítě: Konfigurace přístupu k Internetu a virtuální síti a filtrů IP adres
    * Připojení privátních koncových bodů: Vytvoření koncového bodu, který není přístupný prostřednictvím Internetu 
* Key Vault
    * Tajné kódy: Správa nastavení platformy
    * Zásady přístupu: umožňuje spravovat, které aplikace a uživatelé budou mít přístup k datům v Key Vault a které operace (například čtení, zápis, výpis, odstranění) můžou provádět v síti, bráně firewall, virtuální síti a privátních koncových bodech.
* Azure Active Directory (AAD) → Registrace aplikací
    * <APP_NAME>-web → ověřování: spravovat identifikátory URI odpovědi, což je seznam identifikátorů URI, které lze použít jako cílové stránky po úspěšném ověření. Skript nasazení nemusí být v některých scénářích schopen konfigurovat automaticky, například nedostatečná oprávnění správce AAD. Při změně názvu hostitele webové aplikace můžete přidat nebo upravit identifikátory URI, například číslo portu používaného localhost pro ladění.
* App Service
    * Konfigurace: Správa proměnných prostředí, které řídí služby nebo uživatelské rozhraní
* Virtuální počítač
    * Sítě: Konfigurace podporovaných sítí a pravidel brány firewall
    * Sériová konzola: přístup SSH k získání přehledů nebo ladění, získání přihlašovacích údajů z výstupu skriptu nasazení nebo resetování hesla
* IoT Hub → IoT Edge
    * Spravujte identity IoT Edge zařízení, která mají přístup k centru, nakonfigurujte, které moduly se mají nainstalovat a kterou konfiguraci používají, například parametry kódování pro vydavatele OPC.
* IoT Hub → IoT Edge → \<DEVICE> → set modules → OpcPublisher (pouze pro samostatnou operaci vydavatele OPC)


## <a name="configuration-options"></a>Možnosti konfigurace

|Možnost konfigurace (zkrácený/úplný název)    |    Popis   |
|----------------------------------------------|------------------|
PF/publishfile |Název souboru pro konfiguraci uzlů k publikování. Pokud je tato možnost zadána, OPC Publisher do samostatného režimu.
LF/logfile |Název souboru protokolu, který se má použít
vše/LogLevel |Úroveň protokolu, která se má použít (povoleno: závažná, chyba, upozornění, informace, ladění, verbose).
Já/MessageEncoding |Kódování zpráv pro odchozí zprávy má povolené hodnoty: JSON, UADP
mm/messagingmode |Režim zasílání zpráv pro odchozí zprávy povoluje hodnoty: PubSub, Samples
FM/fullfeaturedmessage |Úplný doporučený režim pro zprávy (všechna pole jsou vyplněna). Výchozí hodnota je true, u starší verze kompatibility používá false.
AA/AutoAccept |Vydavatel důvěřuje všem serverům, ke kterým je připojení.
BS/BatchSize |Počet zpráv OPC UA – Změna dat, které mají být uloženy do mezipaměti pro dávkování.
si/iothubsendinterval |Interval dávkování triggeru v sekundách
MS/iothubmessagesize |Maximální velikost zprávy (IoT D2C).
Om/maxoutgressmessages |Maximální velikost výstupní vyrovnávací paměti zprávy (IoT D2C).
di/diagnosticsinterval |Zobrazuje diagnostické informace vydavatele v zadaném intervalu v sekundách (vyžaduje informace o úrovni protokolu). -1 zakáže vzdálený diagnostický protokol a výstup diagnostiky.
lt/logflugtimespan |Časový interval v sekundách, kdy má být soubor protokolu vyprázdněn.
ih/iothubprotocol |Protokol, který se má použít pro komunikaci s rozbočovačem. Povolené hodnoty: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, AMQP, MQTT, TCP, WebSocket, any
neheartbeatinterval |Vydavatel používá tuto výchozí hodnotu v sekundách pro nastavení intervalu prezenčního signálu uzlů bez nastavení intervalu prezenčního signálu.
ot/OperationTimeout |Časový limit operace klienta vydavatele OPC UA v MS
OL/opcmaxstringlen |Maximální délka řetězce OPC může být odeslána nebo přijata.
Oi/opcsamplinginterval |Výchozí hodnota v milisekundách, která vyžádá servery o vzorové hodnoty
op/opcpublishinginterval |Výchozí hodnota v milisekundách pro nastavení intervalu publikování předplatných na serveru OPC UA.
dal/createsessiontimeout |Interval odesílání zpráv Keep Alive mezi servery OPC na koncových bodech, ke kterým je připojeno, posílá Vydavatel v sekundách.
kt/keepalivethresholt |Zadejte počet paketů Keep Alive, na které může server přijít, než se relace odpojí.
TM/trustmyself |Certifikát vydavatele je automaticky umístěn do důvěryhodného úložiště.
at/appcertstoretype |Vlastní typ úložiště certifikátů aplikace (povoleno: adresář, X509Store).


## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak změnit výchozí hodnoty konfigurace, můžete 

> [!div class="nextstepaction"]
> [Vyžádat IIoT data do ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Vizualizace a analýza dat pomocí Time Series Insights](tutorial-visualize-data-time-series-insights.md)
