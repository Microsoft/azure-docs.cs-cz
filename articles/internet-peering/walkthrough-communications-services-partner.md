---
title: Návod ke službě Azure Internet peering pro Communications Services
titleSuffix: Azure
description: Návod ke službě Azure Internet peering pro Communications Services
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498952"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Návod ke službě Azure Internet peering pro Communications Services

V této části najdete popis kroků, které Poskytovatel komunikačních služeb potřebuje k navázání přímého propojení s Microsoftem.

**Poskytovatelé komunikačních služeb:**  Poskytovatelé komunikačních služeb jsou organizace, které nabízejí komunikační služby (komunikace, zasílání zpráv, konference atd.) a chtějí integrovat infrastrukturu komunikačních služeb (bránu mezipaměť softwarové sběrnice/SIP atd.).  s komunikačními službami Azure a Microsoft Teams. 

Služba Azure peer peering podporuje poskytovatele komunikačních služeb a navázat přímé propojení s Microsoftem na kterékoli z jeho hraničních lokalit (umístění pop). Seznam všech webů veřejné hrany je k dispozici v [PeeringDB](https://www.peeringdb.com/net/694).

Partnerský vztah Azure na Internetu poskytuje vysoce spolehlivou a technologii QoS (Quality of Service) povolenou komunikaci pro komunikační služby, aby se zajistila vysoká dostupnost služeb pro zajištění vysoké kvality a výkonu.

## <a name="technical-requirements"></a>Technické požadavky
Mezi technické požadavky týkající se navázání přímého propojení pro komunikační služby patří následující:
-   Partner musí poskytovat vlastní číslo autonomního systému (ASN), které musí být veřejné.
-   Partnerský uzel musí mít redundantní propojení (PNI) v každém umístění propojení, aby bylo zajištěno místní redundance.
-   Partner musí mít geografickou redundanci, aby zajistil převzetí služeb při selhání v případě selhání lokality v oblasti/metro.
-   Partnerský uzel musí mít relace protokolu BGP jako aktivní – aktivní, aby zajistil vysokou dostupnost a rychlejší sbližování a neměl by být zřízený jako primární a záložní.
-   Partner musí udržovat poměr 1:1 pro směrovače partnerských partnerských vztahů až po okruhy partnerských vztahů a neuplatní se žádné omezení přenosové rychlosti.
-   Partnerský uzel musí poskytovat a inzerovat svůj vlastní veřejně směrovatelný adresní prostor IPv4 adres používaný koncovými body komunikační služby v partnerském prostředí (např. mezipaměť softwarové sběrnice). 
-   Partnerský uzel musí poskytovat podrobné informace o tom, jaká třída provozu a koncových bodů je umístěna v každé inzerované podsíti. 
-   Partnerský uzel musí spustit protokol BGP přes obousměrné předávání směrování (BFD), aby se usnadnila konvergence trasy sub Second.
-   Všechny předpony komunikační infrastruktury jsou zaregistrované v Azure Portal a inzerované pomocí řetězce komunity 8075:8007.
-   Partnerský uzel nesmí ukončit partnerský vztah na zařízení, na kterém běží stavová brána firewall. 
-   Microsoft bude ve výchozím nastavení konfigurovat všechny odkazy propojení jako PRODLEVy (sady odkazů), takže partner musí podporovat LACP (Link Control Control Protocol) na odkazech propojení.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Vytvoření přímého propojení s Microsoftem pro komunikační služby.

K navázání přímého propojení pomocí internetového partnerského vztahu Azure použijte následující postup:

**1. přidružte veřejné číslo ASN partnerského vztahu k předplatnému Azure:**

V případě, že se partnerský uzel už přidružil k předplatnému Azure, tento krok prosím ignorujte.

[Přidružení partnerského čísla ASN k předplatnému Azure pomocí portálu – Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

Dalším krokem je vytvoření přímého partnerského připojení pro službu partnerského vztahu.

> [!NOTE]
> Po schválení přidružení ASN kontaktujte nás na adrese peeringservices@microsoft.com ASN a ID předplatného a přidružte své předplatné ke komunikačním službám. 

**2. Vytvořte připojení s přímým partnerským vztahem pro službu partnerského vztahu:**

Při [vytváření nebo úpravě přímého partnerského vztahu pomocí portálu](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal) postupujte podle pokynů.

Ujistěte se, že splňuje požadavek na vysokou dostupnost.

Ujistěte se prosím, že na stránce vytvořit partnerský vztah vybíráte následující možnosti:

Typ partnerského vztahu:   **Přímá**

Síť Microsoft:  **8075**

SKU:        **Premium zdarma**


V části stránka s přímým partnerským připojením vyberte následující možnosti:

Zprostředkovatel adres relace:   **Microsoft**

Použití pro služby partnerského vztahu:   **povoleno**

> [!NOTE] 
> Při výběru aktivace pro služby partnerských vztahů ignorujte následující zprávu.
> *Nepovolujte, pokud jste se nekontaktovali peering@microsoft.com o poskytovatele map.*


  **2a. použít stávající přímé partnerské připojení pro služby partnerského vztahu**

Pokud máte existující přímý partnerský vztah, který chcete použít k podpoře služby partnerského vztahu, můžete ho aktivovat na Azure Portal.
1.  Podle pokynů [převeďte starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
Podle potřeby objednání dalších okruhů, aby splňovaly požadavky na vysokou dostupnost.

2.  Postupujte podle kroků a [Povolte službu partnerského](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) vztahu s přímým partnerským vztahem pomocí portálu.




**3. Zaregistrujte předpony pro optimalizované směrování.**

Pro vyoptimalizované směrování pro předpony infrastruktury komunikačních služeb byste měli zaregistrovat všechny své předpony pomocí partnerských vztahů mezi propojeními.
[Registrovat službu Azure peering Service – Azure Portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

Klíč předpony je vyplněný pro partnery komunikačních služeb, takže partner nemusí k registraci použít žádný klíč předpony. 

Zajistěte, aby se předpony, které jsou zaregistrované, nahlásily přes přímá propojení vytvořená pro danou oblast.


## <a name="faqs"></a>Nejčastější dotazy:

**Č.**  Mám menší podsítě (</24) pro moje komunikační služby. Můžu dostat i menší podsítě?

**Určitého.**  Ano, Microsoft Azure Partnerská služba podporuje také menší směrování předpon. Ujistěte se prosím, že registrujete předpony pro směrování a že jsou stejné jako v rámci propojení.

**Č.**  Jaké trasy Microsoftu obdržíme prostřednictvím těchto propojení?

**Určitého.** Společnost Microsoft oznamuje všem těmto předponám veřejné služby Microsoftu přes tyto vzájemná propojení. Tím se zajistí, že ze stejného propojení budou přístupná jenom komunikace, ale i jiné cloudové služby.

**Č.**  Potřebuji nastavit limit předpony a počet tras, které Microsoft oznamuje?

**Určitého.** Společnost Microsoft oznamuje zhruba 280 předpony na internetu a může v budoucnu zvýšit 10-15%. Bezpečný limit 400-500 může být dobrý pro nastavení "maximální počet předpon".

**Č.** Bude Microsoft znovu inzerovat předpony partnerských uzlů na internetu?

**Určitého.** No.

**Č.** Platí se za tuto službu poplatek?

**Určitého.** Ne, ale u partnerského vztahu se očekává, že budou přenášet náklady mezi propojeními lokalit.

**Č.** Jaká je minimální rychlost připojení pro propojení?

**Určitého.** peering –.

**Č.** Je partnerský vztah vázaný na smlouvu SLA?

**Určitého.** Ano, jakmile využití dosáhne 40%, musí se začínat proces rozšíření PRODLEVy 45 60day.

**Č.** Jaká je výhoda této služby přes aktuální přímý partnerský vztah nebo Express Route?

**Určitého.** Bezplatné vyrovnávání a celá cesta jsou optimalizované pro hlasový provoz přes Microsoft WAN a konvergence se BFD za sekundu.

**Č.** Jak trvá dokončení procesu připojování?

**Určitého.** Čas bude proměnný v závislosti na počtu a umístění lokalit a Pokud partnerský uzel migruje stávající soukromé partnerské vztahy nebo vytváří nové kabely. Dopravce by měl naplánovat 3 + týdny.

**Č.** Můžou používat rozhraní API pro registraci?

**Určitého.** V současné době není podporována žádná podpora rozhraní API a konfigurace musí být provedena prostřednictvím webového portálu. 
