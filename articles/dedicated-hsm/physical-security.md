---
title: Fyzické zabezpečení HSM – vyhrazené modulu hardwarového zabezpečení Azure | Dokumentace Microsoftu
description: Informace o vyhrazených modulu hardwarového zabezpečení Azure zařízení fyzického zabezpečení v datových centrech
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 662800d2b9db7d7a19861431354e55b3897e020a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080040"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Fyzické zabezpečení v Azure vyhrazená HSM

Azure vyhrazené HSM pomáhá splnit požadavky na Pokročilé zabezpečení pro úložiště klíčů. Je spravované následující postupy přísné zásady zabezpečení v průběhu životního cyklu úplné podle potřeb zákazníků.

## <a name="security-through-procurement"></a>Zabezpečení prostřednictvím zajišťování

Microsoft se řídí zabezpečené platformě pro zajištění procesu. Jsme Správa dodavatelského řetězce a ujistěte se, že konkrétní zařízení seřazené a odeslání zařízení přicházejících u našich datových centrech. Zařízení jsou v plastů zpětných proti události. Ukládají se v oblasti zabezpečené úložiště, dokud stává v galerii data datového centra.  Jako jsou stojany obsahující zařízení, do modulu hardwarového zabezpečení jsou považovány za vysoký obchodní impact(HBI). Zařízení jsou zamknuté a video dozoru neustále přední a zadní.

## <a name="security-through-deployment"></a>Zabezpečení až po nasazení

Moduly hardwarového zabezpečení jsou nainstalovány v stojany spolu s přidružené síťové součásti. Po instalaci, musíte je nakonfigurovat dřív, než budou k dispozici jako součást vyhrazené HSM služby Azure. Tato aktivita konfigurace se provádí pomocí zaměstnanci Microsoftu, které prošly kontrolu na pozadí. "Just In Time" (JIT) správy se používá k omezení přístupu jenom zaměstnancům, pravé a pouze v čase, který je nutný přístup k. Postupy a systémy, ujistěte se také, že se protokoluje všechny aktivity související se zařízením hardwarového zabezpečení.

## <a name="security-in-operations"></a>Zabezpečení v operacích

Moduly hardwarového zabezpečení jsou hardwarovými zařízeními (skutečné HSM se karta PCI v rámci zařízení) tak, aby byl možný, mohou se vyskytnout problémy na úrovni součásti. Potenciální problémy patří, ale nejsou omezeny na selhání napájení ventilátoru a power. Tento typ události bude vyžadovat údržbu nebo přerušení nebo opravy aktivity nahradit všechny komponenty vyměnitelné za chodu.

### <a name="component-replacement"></a>Komponenta nahrazení

Po zřízení zařízení a v části Správa zákazníků, je za běhu připojitelných napájení pouze komponenty, které bylo nahrazeno. Tato součást je mimo hranice zabezpečení a nezpůsobí proti události. Systém lístků podpory se používá k autorizaci pro přístup k zadní-of-rack HBI technický pracovník Microsoftu. Při zpracování-the-ticket, objeví se fyzické dočasný klíč. Tento klíč poskytuje inženýr přístup k zařízení a umožňuje jejich Prohodit ovlivněné komponenty. Jiný přístup (to znamená, manipulovat událost způsobí) by se dělalo, pokud zařízení není přidělená zákazník tedy minimalizovat rizika zabezpečení a dostupnost.  

### <a name="device-replacement"></a>Nahrazení zařízení

V případě selhání celkový počet zařízení následuje podobný identifikátoru použitému při selhání součásti procesu. Pokud zákazník není schopen zeroize zařízení nebo zařízení je v neznámém stavu, dat opatřené zařízení budou odebrány a umístí do přihrádky odstranění v racku. Zařízení, které jsou umístěny v přihrádce zničí řízené a zabezpečeným způsobem. Žádná data opatřené zařízení ze HBI rack ponechá datacentru společnosti Microsoft.

### <a name="other-rack-access-activities"></a>Další aktivity přístupu do racku

Technický pracovník Microsoftu, musíte získat přístup k rack používaný zařízením hardwarového zabezpečení (například údržba síťových zařízení), standardní bezpečnostní postupy se použije k získání přístupu k HBI zabezpečené stojanu. Veškerý přístup bude dozoru videa. Zařízením hardwarového zabezpečení jsou ověřené na [FIPS 140-2 úrovně 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) tak, aby všechny neoprávněného přístupu k zařízení HSM se signál, aby zákazníka a data budou zeroized.

## <a name="logical-level-security-considerations"></a>Zabezpečení na úrovni logické aspekty

Moduly hardwarového zabezpečení připravené k virtuální síti vytvořené zákazníka. Toto je privátní IUP adresní prostor zákazníka.  Tato konfigurace poskytuje úroveň izolace cenné logické sítě a zajišťuje přístup pouze zákazník. Z toho vyplývá, že všechny kontrolní mechanismy zabezpečení na úrovni logické se odpovědnost zákazník.

## <a name="next-steps"></a>Další postup

Doporučuje se, že všechny klíčové koncepty služby, jako například vysokou dostupnost, zabezpečení a možnosti podpory například jsou dobře pochopitelné i před zřizování zařízení, návrh aplikace nebo nasazení.

* [Vysoká dostupnost](high-availability.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
* [Architektura nasazení](deployment-architecture.md)