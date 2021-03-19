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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "72793129"
---
Zabezpečení infrastruktury Internet věcí (IoT) vyžaduje přísnou strategii zabezpečení v rámci hloubky. Tato strategie vyžaduje, abyste zabezpečili data v cloudu, chránili integritu dat během přenosu prostřednictvím veřejného Internetu a bezpečně zřídili zařízení. Každá vrstva sestaví větší bezpečnostní ujištění v celkové infrastruktuře.

## <a name="secure-an-iot-infrastructure"></a>Zabezpečení infrastruktury IoT

Tuto strategii zabezpečení je možné vyvíjet a provádět s aktivní účastí různých hráčů zapojených do výroby, vývoje a nasazení zařízení a infrastruktury IoT. Následuje popis těchto hráčů na nejvyšší úrovni.

* **Výrobce nebo integrátor hardwaru IoT**: obvykle jsou tito hráči výrobci hardwaru IoT, který je nasazený, integrátory napravují hardware od různých výrobců nebo dodavatelé, kteří poskytují hardware pro nasazení IoT vyrobené nebo integrované jinými dodavateli.

* **Vývojář řešení IoT**: vývoj řešení IoT obvykle provádí vývojář řešení. Tento vývojář může být součástí interního týmu nebo integrátoru systému (SI), který se specializuje s touto aktivitou. Vývojář řešení IoT může vyvíjet různé komponenty řešení IoT od začátku, integrovat různé komponenty na polici nebo Open Source nebo přijmout akcelerátory řešení s menším přizpůsobením.

* **Nasazení řešení IoT**: po vývoji řešení IoT je potřeba ho nasadit v poli. Tento proces zahrnuje nasazení hardwaru, propojení zařízení a nasazení řešení v hardwarových zařízeních nebo v cloudu.

* **Operátor řešení IoT**: po nasazení řešení IoT se vyžaduje dlouhodobá operace, monitorování, upgrady a údržba. Tyto úlohy může udělat interní tým, který zahrnuje specialisty na informační technologie, hardwarovou činnost a týmy údržby a specialisty na doménu, kteří sledují správné chování celkové infrastruktury IoT.

Následující části obsahují osvědčené postupy pro každého z těchto hráčů, které vám pomůžou vyvíjet, nasazovat a provozovat zabezpečenou infrastrukturu IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce nebo integrátor hardwaru IoT

Níže jsou uvedené osvědčené postupy pro výrobce hardwaru IoT a hardwarové integrátory.

* **Rozsah hardwaru na minimální požadavky**: návrh hardwaru by měl zahrnovat minimální funkce vyžadované pro provoz hardwaru a nic dalšího. Příkladem je zahrnutí portů USB, pokud je to nutné pro operaci zařízení. Tyto další funkce otevřou zařízení pro nechtěné způsoby útoku, které by se měly vyhnout.

* Ověření **manipulace s hardwarem**: Sestavte mechanismy pro detekci fyzické manipulace, jako je například otevření zařízení nebo jeho odebrání. Tyto neoprávněné signály můžou být součástí datového proudu nahraného do cloudu, který by se mohl vyvarovat od operátorů těchto událostí.

* **Sestavování s zabezpečeným hardwarem**: Pokud umožňují spotřebu na úrovni, Sestavujte bezpečnostní funkce, jako je zabezpečené a šifrované úložiště, nebo spouštěcí funkce založené na čipu TPM (Trusted Platform Module). Díky těmto funkcím je zařízení bezpečnější a lépe se chrání celková infrastruktura IoT.

* **Zabezpečte upgrady**: upgrady firmwaru během životnosti zařízení se nevyhnutelně provedou. Sestavování zařízení s bezpečnými cestami pro upgrady a kryptografická ujištění verzí firmwaru umožní, aby bylo zařízení zabezpečené během a po upgradech.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Níže jsou uvedené osvědčené postupy pro vývojáře řešení IoT:

* **Dodržujte zabezpečení metodologie vývoje softwaru**: vývoj zabezpečeného softwaru vyžaduje základní úvahu o zabezpečení, od vzniku projektu až po jeho implementaci, testování a nasazení. Tato metodologie má vliv na možnosti platforem, jazyků a nástrojů. Životní cyklus Microsoft Security Development poskytuje podrobný přístup k sestavování zabezpečeného softwaru.

* **Vyberte open source software s péčí**: Open source software nabízí příležitost k rychlému vývoji řešení. Při volbě open source softwaru zvažte úroveň aktivity komunity pro každou Open-Source komponentu. Aktivní komunita zajišťuje, že je software podporován a že problémy jsou zjišťovány a řešeny. Další možností je, že se nepodporují i neaktivní open source softwarový projekt a problémy se pravděpodobně neobjevují.

* **Integrace s péčí**: mnoho nedostatků zabezpečení softwaru existuje na hranici knihoven a rozhraní API. Funkce, které nemusí být potřeba pro aktuální nasazení, můžou být pořád dostupné přes vrstvu rozhraní API. Aby se zajistilo celkové zabezpečení, ujistěte se, že jste kontrolovali všechna rozhraní součástí, která jsou integrovaná pro chyby zabezpečení.

## <a name="iot-solution-deployer"></a>Nástroj pro nasazení řešení IoT

Níže jsou uvedené osvědčené postupy pro nástroje pro nasazení řešení IoT:

* **Bezpečné nasazení hardwaru**: nasazení IoT může vyžadovat nasazení hardwaru v nezabezpečených umístěních, například ve veřejných prostorech nebo v nekontrolovaných národních prostředích. V takových situacích se ujistěte, že je nasazení hardwaru v maximálním rozsahu. Pokud je na hardwaru k dispozici USB nebo jiné porty, ujistěte se, že jsou pokryté zabezpečeně. Mnoho vektorů útoku může tyto hodnoty použít jako vstupní body.

* **Udržujte ověřovací klíče v bezpečí**: během nasazování vyžaduje každé zařízení ID zařízení a přidružené ověřovací klíče vygenerované cloudovou službou. Tyto klíče si nechte fyzicky bezpečně i po nasazení. Jakékoli napadené klíče může použít škodlivé zařízení k maskování jako stávající zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Níže jsou uvedené osvědčené postupy pro operátory řešení IoT:

* **Udržujte systém v aktuálním stavu**: Ujistěte se, že operační systémy zařízení a všechny ovladače zařízení jsou upgradovány na nejnovější verze. Pokud zapnete automatické aktualizace ve Windows 10 (IoT nebo jiné SKU), společnost Microsoft je aktuální a poskytuje zabezpečený operační systém pro zařízení IoT. Udržování dalších operačních systémů (například Linux) pomáhá zajistit, aby byly chráněny i proti škodlivým útokům.

* **Ochrana před škodlivou aktivitou**: Pokud operační systém povoluje, nainstalujte nejnovější antivirové a antimalwarové funkce na každý operační systém zařízení. Tento postup může přispět k zmírnění většiny externích hrozeb. Většinu moderních operačních systémů můžete chránit před hrozbami, a to provedením příslušných kroků.

* **Nejčastější audit**: auditování infrastruktury IoT pro problémy související se zabezpečením je klíč při reakci na incidenty zabezpečení. Většina operačních systémů nabízí integrované protokolování událostí, které by se mělo často zkontrolovat, aby se zajistilo, že nedošlo k narušení zabezpečení. Informace o auditu je možné odeslat jako samostatný datový proud telemetrie do cloudové služby, kde se dá analyzovat.

* **Fyzicky chránit infrastrukturu IoT**: nejhorší bezpečnostní útoky proti infrastruktuře IoT se spouští pomocí fyzického přístupu k zařízením. Jedním z důležitých bezpečnostních postupů je chránit před škodlivým použitím portů USB a dalšího fyzického přístupu. Jedním z klíčových přístupů k odhalení, ke kterým mohlo dojít, je protokolování fyzického přístupu, jako je například použití portu USB. Znovu, Windows 10 (IoT a jiné SKU) umožňuje podrobné protokolování těchto událostí.

* **Chránit cloudová pověření**: přihlašovací údaje cloudového ověřování používané ke konfiguraci a provozování nasazení IoT jsou pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. Zabezpečte přihlašovací údaje změnou hesla často a nepoužívejte tyto přihlašovací údaje na veřejných počítačích.

Možnosti různých zařízení IoT se liší. Některá zařízení můžou být počítače, na kterých běží běžné desktopové operační systémy, a některá zařízení můžou používat velmi lehké operační systémy. Doporučené postupy zabezpečení, které byly popsané dříve, se můžou u těchto zařízení použít v různých stupních. V případě potřeby by se měly dodržovat Další doporučené postupy zabezpečení a nasazení od výrobců těchto zařízení.

Některá starší a omezená zařízení možná nebudou navržená speciálně pro nasazení IoT. Tato zařízení pravděpodobně nemají možnost šifrovat data, připojit se k Internetu nebo poskytovat pokročilé auditování. V těchto případech může moderní a zabezpečená brána polí agregovat data ze starších zařízení a zajistit zabezpečení potřebné pro připojení těchto zařízení přes Internet. Brány polí můžou poskytovat zabezpečené ověřování, vyjednávání šifrovaných relací, příjem příkazů z cloudu a spoustu dalších funkcí zabezpečení.