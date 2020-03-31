---
title: Fyzické zabezpečení modulu hardwarového zabezpečení modulu hardwarového zabezpečení modulu hardwarového zabezpečení služby Azure | Dokumenty společnosti Microsoft
description: Informace o fyzickém zabezpečení zařízení Azure Dedicated HSM v datových centrech
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881025"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Fyzické zabezpečení vyhrazeného modulu hardwarového zabezpečení Azure

Vyhrazený modul hardwarového zabezpečení Azure vám pomůže splnit pokročilé požadavky na zabezpečení pro úložiště klíčů. Je spravován a řídí se přísnými postupy zabezpečení po celou dobu svého životního cyklu, aby uspokojil potřeby zákazníků.

## <a name="security-through-procurement"></a>Bezpečnost prostřednictvím zadávání veřejných zakázek

Společnost Microsoft sleduje proces bezpečného zásobování. Řídíme řetězec úschovy a zajišťujeme, aby konkrétní objednané a odeslané zařízení bylo zařízením přicházejícím do našich datových center. Zařízení jsou v plastových zádech. Jsou uloženy v zabezpečeném úložišti, dokud nejsou uvedeny do provozu v datové galerii datového centra.  Regály obsahující zařízení HSM jsou považovány za vysoce obchodní dopad (HBI). Zařízení jsou uzamčena a pod kamerovým dohledem po celou dobu přední a zadní.

## <a name="security-through-deployment"></a>Zabezpečení prostřednictvím nasazení

Soubory hesel jsou instalovány v rackech společně s přidruženými síťovými součástmi. Po instalaci musí být nakonfigurovány před jejich zpřístupnění jako součást služby Azure Dedicated HSM. Tuto konfigurační aktivitu provádějí zaměstnanci společnosti Microsoft, kteří prošli ověřením spolehlivosti. Správa "Just In Time" (JIT) se používá k omezení přístupu pouze správným zaměstnancům a pouze po dobu, po kterou je přístup potřebný. Použité postupy a systémy také zajišťují, že jsou protokolovány všechny aktivity související se zařízením s hsm.

## <a name="security-in-operations"></a>Bezpečnost v provozu

Moduly hardwarového systému jsou hardwarová zařízení (skutečný modul hardwarového systému je karta PCI v rámci zařízení), takže je možné, že mohou vzniknout problémy na úrovni komponent. Potenciální problémy zahrnují, ale nejsou omezeny na poruchy ventilátoru a napájení. Tento typ události bude vyžadovat údržbu nebo přerušení/opravu aktivity nahradit všechny vyměnitelné součásti.

### <a name="component-replacement"></a>Výměna součásti

Po zřízení zařízení a pod správou zákazníka je napájení vyměnitelné za provozu jedinými součástmi, které by byly vyměněny. Tato součást je mimo hranice zabezpečení a nezpůsobí událost tamper. Systém prodeje jízdenek se používá k autorizaci inženýra společnosti Microsoft pro přístup k zadní části racku HBI. Při zpracování lístku je vydán dočasný fyzický klíč. Tento klíč poskytuje inženýrovi přístup k zařízení a umožňuje mu vyměnit postiženou součást. Jakýkoli jiný přístup (to znamená, že způsobuje nefalšující událost) by byl proveden, pokud zařízení není přiděleno zákazníkovi, čímž se minimalizuje riziko zabezpečení a dostupnosti.  

### <a name="device-replacement"></a>Výměna zařízení

V případě úplného selhání zařízení je dodržen proces podobný procesu použitému při selhání součásti. Pokud zákazník není schopen zařízení vynulovat nebo je zařízení v neznámém stavu, budou datová ložisková zařízení odebrána a umístěna do kontejneru pro zničení v racku. Zařízení umístěná v koši budou řízena a bezpečně zničena. Žádná data ložiska zařízení z racku HBI opustí datové centrum společnosti Microsoft.

### <a name="other-rack-access-activities"></a>Další aktivity přístupu do racku

Pokud musí technik společnosti Microsoft přistupovat k racku používanému zařízeními hsm (například údržbou síťových zařízení), budou k získání přístupu k zabezpečenému racku HBI použity standardní postupy zabezpečení. Veškerý přístup bude pod kamerovým dohledem. Zařízení HSM jsou ověřena na [FIPS 140-2 Level 3,](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) takže jakýkoli neoprávněný přístup k zařízením HSM bude signalizován zákazníkovi a data budou vynulována.

## <a name="logical-level-security-considerations"></a>Důležité informace o zabezpečení na logické úrovni

Soubory hestely jsou zřízeny do virtuální sítě vytvořené zákazníkem. Toto je soukromý adresní prostor IUP zákazníka.  Tato konfigurace poskytuje cennou logickou izolaci na úrovni sítě a zajišťuje přístup pouze zákazníkem. To znamená, že všechny ovládací prvky zabezpečení logické úrovně jsou odpovědností zákazníka.

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení a supportability například dobře chápat před zřizování zařízení, návrh aplikace nebo nasazení.

* [Vysoká dostupnost](high-availability.md)
* [Síťové služby](networking.md)
* [Možnosti podpory](supportability.md)
* [Sledování](monitoring.md)
* [Architektura nasazení](deployment-architecture.md)