---
title: Defender pro často kladené otázky pro IoT
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Defenderu pro funkce a službu IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 5e7eabd44ea8c56fbb102f9e48812745a31de62a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089192"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Nejčastější dotazy k Azure Defenderu pro IoT

Tento článek obsahuje seznam nejčastějších dotazů a odpovědí k programu Defender pro IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Co je u jedinečného umístění Azure pro zabezpečení IoT k dispozici?

Defender pro IoT umožňuje podnikům rozšiřování stávajícího zobrazení zabezpečení na Internetu do celého řešení IoT. Azure poskytuje ucelený pohled na vaše obchodní řešení, což vám umožní vzít v úvahu akce a rozhodnutí související s podnikáním na základě stav podnikového zabezpečení a shromážděných dat. Kombinované zabezpečení pomocí Azure IoT, Azure IoT Edge a Azure Security Center vám umožní vytvořit požadované řešení s potřebným zabezpečením.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Naše organizace používá proprietární nestandardní průmyslové protokoly. Jsou podporované? 

Azure Defender pro IoT poskytuje komplexní podporu protokolů. Kromě podpory vloženého protokolu můžete zabezpečit zařízení IoT a OT s vlastními a vlastními protokoly nebo protokoly, které se odchylují od jakéhokoli standardu. Pomocí sady SDK pro vývoj v horizontu můžete vytvářet neodvětví modulů plug-in, které dekódují síťový provoz na základě definovaných protokolů. Provoz se analyzuje podle služeb, aby poskytoval kompletní monitorování, upozorňování a vytváření sestav. Použít horizont:
- Rozbalení viditelnosti a řízení bez nutnosti upgradu na nové verze
- Zabezpečte proprietární informace vývojem na pracovišti jako externí modul plug-in. 
- Lokalizace textu pro výstrahy, události a parametry protokolu.

Toto jedinečné řešení pro vývoj protokolů jako modulů plug-in nevyžaduje pro podporu nového protokolu žádné vyhrazené vývojářské týmy ani verze vydání. Vývojáři, partneři a zákazníci můžou bezpečně vyvíjet protokoly a sdílet poznatky a znalosti pomocí horizontu. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Musím si koupit hardwarová zařízení od partnerů Microsoftu?
Azure Defender pro IoT snímač se spouští na konkrétních specifikacích hardwaru, jak je popsáno v [Průvodci specifikacemi hardwaru](https://aka.ms/AzureDefenderforIoTBareMetalAppliance), zákazníci si můžou koupit certifikovaný hardware od partnerů Microsoftu nebo používat poskytnuté kusovníky (BOM) a koupit si ho sami. 

Certifikovaný hardware byl testován v našich laboratořích pro stabilitu ovladačů, pokles paketů a velikost sítě.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>Nařízení nám neumožňuje připojit náš systém k Internetu. Můžeme i nadále používat Defender pro IoT?

Ano, můžete to! Místní řešení Azure Defender pro IoT Platform je nasazené jako fyzické nebo virtuální zařízení snímače, které prochází síťový provoz (prostřednictvím SPAN, RSPAN nebo klepnutí) a umožňuje tak analyzovat, zjišťovat a průběžně monitorovat, objevovat a nepřetržitě monitorovat sítě pro IT, a IoT. U větších podniků může více senzorů agregovat svá data do místní konzoly pro správu.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Kde se v síti mám připojit porty monitorování?

Služba Azure Defender pro IoT snímač se připojí k portu SPAN nebo k síti KLEPNE a hned začne shromažďovat síťový provoz ICS prostřednictvím pasivního monitorování (bez agentů). Nemá žádný vliv na sítě, protože není umístěný v cestě k datům a aktivně nekontroluje zařízení.

Například:
- Jedno zařízení (Virtual of fyzický) může být v DMZ vrstvě dílenského řízení a všechny přenosy dat z dílenského řízení jsou směrovány do této vrstvy.
- Alternativně můžete najít malé senzory v každé aplikaci dílny pomocí cloudu nebo místní správy, která se nachází v DMZ vrstvě dílenského řízení. Jiné zařízení (virtuální nebo fyzické) může monitorovat provoz v dílenském DMZ vrstvě (pro SCADA, historian nebo instatusování).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Jak program Defender pro IoT porovnává soutěž s konkurencí?

Zatímco jiná řešení poskytují sadu funkcí, které zákazníkům umožňují vytvářet vlastní řešení, Defender pro IoT poskytuje jedinečné komplexní řešení zabezpečení IoT, které poskytuje nejrůznější přehled o zabezpečení všech souvisejících prostředků Azure. Azure umožňuje rychlé nasazení a úplnou integraci s IoT Hubmi moduly, které umožňují snadnou integraci s existujícími nástroji pro správu zařízení.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Musím být zákazníkem Azure IoT?

Yes. V případě nasazení připojených k cloudu Azure Defender pro IoT spoléhá na konektivitu a infrastrukturu Azure IoT.
## <a name="can-i-create-my-own-alerts"></a>Můžu vytvořit vlastní výstrahy?

Yes. Přizpůsobenou výstrahu můžete nastavit na předem stanovenou sadu chování, jako je například IP adresa a otevřené porty. Další informace o vlastních výstrahách a o tom, jak je udělat, najdete v tématu [Vytvoření vlastních výstrah](quickstart-create-custom-alerts.md) .

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Kde můžu zobrazit protokoly? Můžu přizpůsobovat protokoly?

- Pomocí vašeho připojeného pracovního prostoru Log Analytics zobrazovat výstrahy a doporučení. Nakonfigurujte velikost úložiště a dobu trvání v pracovním prostoru.

- Nezpracovaná data z vašeho agenta zabezpečení taky můžete ukládat do svého účtu Log Analytics. Před změnou konfigurace této možnosti zvažte možnost změnit velikost, dobu trvání, požadavky na úložiště a související náklady.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co se stane, když připojení k Internetu přestane fungovat?

Senzory a agenti pokračují v běhu a ukládají data, pokud je zařízení spuštěné. Data se ukládají do mezipaměti zpráv zabezpečení podle konfigurace velikosti. Když zařízení znovu získá připojení, zprávy o zabezpečení budou pokračovat v posílání.





## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s Defenderem pro IoT, najdete v následujících článcích:

- Přečtěte si [Přehled](overview.md) programu Defender for IoT.
- Ověření [požadovaných součástí služby](service-prerequisites.md)
- Další informace o [tom, jak začít](getting-started.md)
- Vysvětlení [výstrah zabezpečení pro program Defender pro IoT](concept-security-alerts.md)
