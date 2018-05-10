---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2c6f5cf2d89da0c2418ac58ca5d47a8aa05e732f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="internet-of-things-security-best-practices"></a>Osvědčené postupy pro zabezpečení Internetu věcí

Zabezpečení infrastruktury Internetu věcí (IoT) vyžaduje přísných strategii zabezpečení do hloubky. Tato strategie vyžaduje, abyste k zabezpečení dat v cloudu, chránit integritu dat v průběhu přenosu prostřednictvím veřejného Internetu a bezpečně zřizovat zařízení. Jednotlivé úrovně sestavení větší zajištění zabezpečení v celé infrastruktury.

## <a name="secure-an-iot-infrastructure"></a>Zabezpečení infrastruktury IoT

Tato strategie zabezpečení do hloubky můžete vyvinuté a provést s aktivní účast různé přehrávače spojené s výrobní, vývoj a nasazení zařízení IoT a infrastruktury. Následuje stručný popis těchto přehrávače.

* **Výrobce hardwaru IoT/integrátor**: tyto přehrávače jsou obvykle výrobců hardwaru IoT nasazuje integrátorem ty se hardware od různých výrobců a dodavatelů poskytuje hardwaru pro nasazení služby IoT vytvořené nebo integrované jinými dodavateli.
* **Vývojář řešení IoT**: vývoj řešení IoT se obvykle provádí vývojář řešení. Tento vývojář může součástí interní týmu nebo systémový integrátor (SI) specializované této aktivity. Vývojář řešení IoT můžete vyvíjet různé součásti řešení IoT od začátku, integrovat různé součásti dodávaných nebo open-source nebo přijmout akcelerátorů řešení s menšími přizpůsobení.
* **Nástroje pro nasazení řešení IoT**: vyvinutý řešení po IoT, musí být nasazen v poli. Tento proces zahrnuje nasazení hardwaru, propojení zařízení a nasazování řešení v hardwarových zařízeních nebo v cloudu.
* **Operátor řešení IoT**: po IoT řešení nasazeno, vyžaduje dlouhodobé provoz, sledování, upgradu a údržby. Tyto úlohy lze provést interní tým, který obsahuje informace o technologii odborníky, hardwarových operací a údržby týmů a odborníky domény, kteří monitorování správné chování celé infrastruktury IoT.

Části obsahují osvědčené postupy pro každou z těchto přehrávače usnadňuje vývoj, nasadit a provozovat zabezpečené infrastruktury IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce hardwaru IoT/integrátor

Následují osvědčené postupy pro IoT výrobcům hardwaru a integrátorem hardwaru.

* **Obor hardware a požadavky na minimální**: návrh hardwaru by měla obsahovat minimální požadované pro operaci hardwaru a nic další funkce. Příkladem je zahrnout portů USB jenom v případě, že je nezbytná pro fungování zařízení. Tyto další funkce otevřít zařízení pro nežádoucí útoky, které by se mělo zabránit.
* **Ujistěte se, hardwaru manipulovat ověření**: sestavení v mechanismy pro zjistit fyzické případnou manipulaci, jako je například otevírání krytí zařízení nebo odebrání součástí zařízení. Tyto manipulovat signálů může být součástí datového proudu nahrán do cloudu, což může výstrahy operátory tyto události.
* **Sestavení kolem zabezpečený hardware**: Pokud spotřebu povoluje, sestavení funkce zabezpečení, jako je zabezpečená a šifrovaná úložiště nebo spouštěcí funkce založené na čipu Trusted Platform Module (TPM). Tyto funkce musíte zařízení informace zabezpečení a ochraně celé infrastruktury IoT.
* **Zabezpečit upgrady**: upgrade firmwaru po dobu životnosti zařízení je nevyhnutelné. Vytváření zařízení s zabezpečené cesty pro upgrade a kryptografické záruku verzí firmwaru vám umožní zařízení, které bude zabezpečené během a po upgradu.

## <a name="iot-solution-developer"></a>Vývojář řešení IoT

Následují osvědčené postupy pro vývojáře řešení IoT:

* **Postupujte podle zabezpečené softwaru vývoj metodika**: vývoj zabezpečené softwaru vyžaduje základů přemýšlení o zabezpečení, od zahájení projektu zcela k jeho implementaci, testování a nasazení. Volby platforem, jazyků a nástroje jsou vliv pomocí této metody. Microsoft Security Development Lifecycle poskytuje podrobné přístup k sestavení zabezpečení softwaru.
* **Zvolte open-source softwaru pečlivě**: Open-source softwaru vám dává příležitost k rychlému vývoji řešení. Pokud zvolíte open-source softwaru, zvažte úrovni aktivity komunity pro každou součást open source. Aktivní komunitě zajišťuje, že software podporuje a zjišťování a řešit problémy. Případně nemusí být podporován projektu skrytého a neaktivní open-source softwaru a pravděpodobně nejsou být zjištěny problémy.
* **Integrovat dát pozor**: řadu nedostatků zabezpečení softwaru existovat hranice knihovny a rozhraní API. Funkce, které nemusí být potřebné pro aktuální nasazení může být k dispozici prostřednictvím vrstvu rozhraní API. Aby celkové zabezpečení, nezapomeňte zaškrtnout všechna rozhraní součásti, kterou je prováděna integrace pro chyby zabezpečení.

## <a name="iot-solution-deployer"></a>Nástroje pro nasazení řešení IoT

Následují osvědčené postupy pro IoT řešení moduly pro nasazení softwaru:

* **Nasazení hardwaru bezpečně**: IoT nasazení může vyžadovat hardwaru, který má být nasazený v nezabezpečená umístění, například veřejné mezery nebo bez dohledu národní prostředí. V takových situacích, zajistěte, aby nasazení hardwaru manipulací v maximálním možném rozsahu. Pokud USB nebo jiné porty jsou dostupné na hardware, ujistěte se, zahrnutých bezpečně. Mnoho vektory útoku můžete použít jako vstupní body.
* **Chránit ověřovací klíče**: během nasazování každé zařízení vyžaduje ID zařízení a související ověřovací klíče generované cloudové služby. Chránit tyto klíče fyzicky i po jeho nasazení. Všechny ohrožené klíč lze škodlivý zařízení jako ze stávajících zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Následují osvědčené postupy pro operátory řešení IoT:

* **Zachování aktualizovaného stavu systému**: Zajistěte, aby operační systémy zařízení a všechny ovladače zařízení upgradovali na nejnovější verze. Pokud zapnete automatické aktualizace v systému Windows 10 (IoT nebo jiných SKU), Microsoft udržuje je aktuální, zajištění zabezpečení operačního systému pro zařízení IoT. Zachování jinými operačními systémy (například Linux) aktuální pomáhá zajistit, že jsou taky chráněná před útoky se zlými úmysly.
* **Ochrana proti škodlivé aktivity**: Pokud operačního systému povolí, nainstalujte nejnovější funkce antivirového a antimalwarového na každý operační systém zařízení. Tento postup může pomoci zmírnit hrozby nejvíce externí. Většina moderních operačních systémů proti hrozbám můžete chránit pomocí příslušné kroky.
* **Audit často**: auditování IoT infrastruktury pro problémy související se zabezpečením je klíč při odpovědi na bezpečnostní incidenty v oblasti. Většina operačních systémů zadejte protokolování integrované událostí, který by měl být zkontrolovány často a ujistěte se, zda nedošlo k žádné porušení zabezpečení. Informace o auditování nelze odesílat jako datový proud telemetrie samostatné ke cloudové službě kde lze analyzovat.
* **Fyzicky ochrana infrastruktury IoT**: nejhorší zabezpečení útoky na infrastrukturu IoT spustily pomocí fyzický přístup k zařízením. Jeden postupem důležité zabezpečení je pro ochranu proti zneužití portů USB a dalších fyzický přístup. Jeden klíč k odhalení průnikům, jež mohly nastat je protokolování fyzický přístup, jako je například použití portu USB. Windows 10 (IoT a další identifikátory SKU) znovu, zapne podrobné protokolování těchto událostí.
* **Ochranu přihlašovacích údajů cloudu**: cloudové ověřování pověření použitá pro konfiguraci a provozní nasazení služby IoT jsou pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. Chraňte pověření často změnou hesla a nepoužívejte tyto přihlašovací údaje na veřejné počítače.

Možnosti různých zařízení IoT se liší. Některá zařízení může být počítače se systémem běžné operační systémy a některá zařízení může být velmi šedé operačním systémem. Osvědčené postupy zabezpečení popsané dříve může být pro tato zařízení v různých stupňů. Pokud je zadán, musí pak další nasazení osvědčené postupy pro zabezpečení a od výrobců těchto zařízení.

Některá zařízení starší verze a omezené nemusí byl určený speciálně pro IoT nasazení. Tato zařízení nemusí mít možnost šifrování dat, připojení k Internetu nebo zadejte pokročilé auditování. Brána pole moderní a zabezpečení v těchto případech můžete shromažďovat data ze zařízení se starší verzí a zabezpečení, vyžaduje se pro tato zařízení připojující se přes Internet. Pole brány může poskytovat vyjednávání šifrovaných relací zabezpečeného ověřování, obdržení příkazy z cloudu a řadu dalších funkcí zabezpečení.