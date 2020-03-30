---
title: zahrnout soubor
description: zahrnout soubor
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793129"
---
Zabezpečení infrastruktury Internetu věcí (IoT) vyžaduje přísnou strategii hloubkového zabezpečení. Tato strategie vyžaduje zabezpečení dat v cloudu, ochranu integrity dat při přenosu přes veřejný internet a bezpečné zřizování zařízení. Každá vrstva vytváří větší zabezpečení zabezpečení v celkové infrastruktuře.

## <a name="secure-an-iot-infrastructure"></a>Zabezpečení infrastruktury IoT

Tuto strategii hloubkové zabezpečení lze vyvinout a provést za aktivní účasti různých aktérů zapojených do výroby, vývoje a nasazení zařízení a infrastruktury IoT. Následuje vysoce postavený popis těchto hráčů.

* **Výrobce/integrátor hardwaru IoT**: Obvykle jsou tito hráči výrobci naváděného hardwaru IoT, integrátoři, kteří sestavují hardware od různých výrobců, nebo dodavatelé, kteří poskytují hardware pro nasazení IoT vyrobené nebo integrované jinými dodavateli.

* **Vývojář řešení IoT**: Vývoj řešení IoT obvykle provádí vývojář řešení. Tento vývojář může být součástí interního týmu nebo systémového integrátora (SI) specializujícího se na tuto činnost. Vývojář řešení IoT může vyvíjet různé součásti řešení IoT od nuly, integrovat různé komponenty off-the-shelf nebo open source nebo přijmout akcelerátory řešení s menšími úpravami.

* **Nasazovač řešení IoT**: Po vyvinutí řešení IoT je potřeba nasadit v terénu. Tento proces zahrnuje nasazení hardwaru, propojení zařízení a nasazení řešení v hardwarových zařízeních nebo v cloudu.

* **Operátor řešení IoT**: Po nasazení řešení IoT vyžaduje dlouhodobé operace, monitorování, upgrady a údržbu. Tyto úkoly může provádět interní tým, který zahrnuje specialisty na informační technologie, týmy pro operace hardwaru a údržbu a specialisty na domény, kteří monitorují správné chování celkové infrastruktury IoT.

Následující části poskytují osvědčené postupy pro každého z těchto hráčů, které pomáhají vyvíjet, nasazovat a provozovat zabezpečenou infrastrukturu IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce/integrátor hardwaru IoT

Níže jsou uvedeny osvědčené postupy pro výrobce hardwaru IoT a integrátory hardwaru.

* **Rozsah hardwaru na minimální požadavky**: Návrh hardwaru by měl obsahovat minimální funkce potřebné pro provoz hardwaru a nic víc. Příkladem je zahrnout porty USB pouze v případě, že je to nezbytné pro provoz zařízení. Tyto další funkce otevírají zařízení pro nežádoucí útočné vektory, kterým je třeba se vyhnout.

* **Vytvoření hardwaru odolného proti manipulaci**: Zabudovává se do mechanismů pro detekci fyzických manipulací, jako je otevření krytu zařízení nebo odebrání části zařízení. Tyto tamper signály mohou být součástí datového proudu nahraného do cloudu, což by mohlo upozornit operátory na tyto události.

* **Sestavení kolem zabezpečeného hardwaru**: Pokud to COGS dovolí, vytvářejte funkce zabezpečení, jako je zabezpečené a šifrované úložiště, nebo spouštěcí funkce založené na modulu TPM (Trusted Platform Module). Díky těmto funkcím jsou zařízení bezpečnější a pomáhají chránit celkovou infrastrukturu IoT.

* **Zabezpečte upgrady:** Aktualizace firmwaru během životnosti zařízení jsou nevyhnutelné. Vytváření zařízení se zabezpečenými cestami pro upgrady a kryptografickou zárukou verzí firmwaru umožní, aby zařízení bylo během upgradu a po něm zabezpečeno.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Následující jsou doporučené postupy pro vývojáře řešení IoT:

* **Dodržujte bezpečnou metodiku vývoje softwaru**: Vývoj zabezpečeného softwaru vyžaduje základní myšlení o bezpečnosti, od vzniku projektu až po jeho implementaci, testování a nasazení. Tato metodika ovlivňuje výběr platforem, jazyků a nástrojů. Životní cyklus vývoje zabezpečení společnosti Microsoft poskytuje podrobný přístup k vytváření zabezpečeného softwaru.

* **Vyberte si open-source software s péčí**: Open-source software poskytuje příležitost k rychlému vývoji řešení. Při výběru softwaru s otevřeným zdrojovým kódem zvažte úroveň aktivity komunity pro každou komponentu s otevřeným zdrojovým kódem. Aktivní komunita zajišťuje, že software je podporován a že problémy jsou zjištěny a řešeny. Alternativně nemusí být podporován obskurní a neaktivní softwarový projekt s otevřeným zdrojovým kódem a problémy pravděpodobně nebudou zjištěny.

* **Integrace s péčí**: Mnoho chyb zabezpečení softwaru existují na hranici knihoven a API. Funkce, které nemusí být vyžadovány pro aktuální nasazení může být stále k dispozici prostřednictvím vrstvy rozhraní API. Chcete-li zajistit celkové zabezpečení, zkontrolujte všechna rozhraní součástí, které jsou integrovány z bezpečnostních chyb.

## <a name="iot-solution-deployer"></a>Nasazovač řešení IoT

Následující jsou osvědčené postupy pro nasazení řešení IoT:

* **Nasazení hardwaru bezpečně**: Nasazení IoT může vyžadovat nasazení hardwaru v nezabezpečených umístěních, například ve veřejných prostorách nebo v národních prostředích bez dohledu. V takových situacích zajistěte, aby nasazení hardwaru bylo maximálně odolné proti manipulaci. Pokud jsou na hardwaru k dispozici porty USB nebo jiné porty, ujistěte se, že jsou bezpečně zakryty. Mnoho útočných vektorů je může použít jako vstupní body.

* **Udržujte ověřovací klíče v bezpečí:** Během nasazení vyžaduje každé zařízení ID zařízení a přidružené ověřovací klíče generované cloudovou službou. Udržujte tyto klíče fyzicky bezpečné i po nasazení. Jakýkoli kompromitovaný klíč může být použit škodlivým zařízením k maskování jako existující zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Následující jsou osvědčené postupy pro operátory řešení IoT:

* **Udržujte systém v aktuálním stavu**: Ujistěte se, že operační systémy zařízení a všechny ovladače zařízení jsou upgradovány na nejnovější verze. Pokud zapnete automatické aktualizace ve Windows 10 (IoT nebo jiných skum), Microsoft udržuje aktuální a poskytuje zabezpečený operační systém pro zařízení IoT. Udržování aktuálních operačních systémů (například Linux) pomáhá zajistit, že jsou také chráněny před škodlivými útoky.

* **Ochrana před škodlivými aktivitami**: Pokud to operační systém umožňuje, nainstalujte do každého operačního systému zařízení nejnovější antivirové a antimalwarové funkce. Tento postup může pomoci zmírnit většinu externích hrozeb. Většinu moderních operačních systémů můžete chránit před hrozbami přijetím vhodných kroků.

* **Časté auditování**: Při reakci na bezpečnostní incidenty je klíčová auditování infrastruktury IoT pro problémy související se zabezpečením. Většina operačních systémů poskytuje integrované protokolování událostí, které by mělo být často kontrolováno, aby se ujistil, že nedošlo k porušení zabezpečení. Informace o auditu lze odeslat jako samostatný telemetrický datový proud do cloudové služby, kde je lze analyzovat.

* **Fyzicky chránit infrastrukturu IoT**: Nejhorší útoky zabezpečení proti infrastruktuře IoT se spouštějí pomocí fyzického přístupu k zařízením. Jednou z důležitých bezpečnostních postupů je ochrana před škodlivým použitím portů USB a jiným fyzickým přístupem. Jedním z klíčů k odhalení porušení, ke kterým mohlo dojít, je protokolování fyzického přístupu, například použití portu USB. Systém Windows 10 (IoT a další sku) opět umožňuje podrobné protokolování těchto událostí.

* **Ochrana přihlašovacích údajů cloudu**: Přihlašovací údaje pro cloudové ověřování používané pro konfiguraci a provoz nasazení IoT jsou pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. Chraňte pověření často měnit heslo a zdržet se používání těchto pověření na veřejných počítačích.

Možnosti různých zařízení IoT se liší. Některá zařízení mohou být počítače s běžnými operačními systémy pro stolní počítače a některá zařízení mohou být se systémem velmi lehkých operačních systémů. Doporučené postupy zabezpečení popsané dříve mohou být použitelné pro tato zařízení v různé míře. Pokud je k dispozici, je třeba dodržovat další osvědčené postupy zabezpečení a nasazení od výrobců těchto zařízení.

Některá starší a omezená zařízení nemusí být navržena speciálně pro nasazení IoT. Tato zařízení mohou postrádat možnost šifrovat data, připojit se k Internetu nebo poskytovat pokročilé auditování. V těchto případech může moderní a zabezpečená brána polí agregovat data ze starších zařízení a zajistit zabezpečení potřebné pro připojení těchto zařízení přes Internet. Brány v terénu mohou poskytovat zabezpečené ověřování, vyjednávání šifrovaných relací, příjem příkazů z cloudu a mnoho dalších funkcí zabezpečení.