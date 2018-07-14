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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38945471"
---
# <a name="internet-of-things-security-best-practices"></a>Osvědčené postupy zabezpečení IOT

Zabezpečení infrastruktury Internetu věcí (IoT) vyžaduje přísné strategii zabezpečení do hloubky. Tato strategie vyžaduje, abyste k zabezpečení dat v cloudu, chránit integritu dat při přenosu přes veřejný internet a bezpečně zřizování zařízení. Každá vrstva sestavení větší zajištění zabezpečení, v celkové infrastruktury.

## <a name="secure-an-iot-infrastructure"></a>Zabezpečení infrastruktury IoT

Tato strategie zabezpečení in-depth lze vyvinuli a spustit aktivní účasti různé přehrávače, které jsou součástí výroby, vývoj a nasazení infrastruktury a zařízení IoT. Tady je stručný popis těchto přehrávače.

* **Výrobce hardwaru IoT/integrátor**: tyto přehrávače jsou obvykle výrobci hardwaru IoT nasazuje integrátorům sestavování hardwarem od různých výrobců a dodavatelů poskytuje hardware pro nasazení IoT vyrobenými nebo integrované podle jiných dodavatelů.
* **Pro vývojáře IoT řešení**: vývoj řešení IoT se obvykle provádí vývojář řešení. Tento vývojář může část interního týmu nebo systémový integrátor (SI), která se specializuje na tuto aktivitu. Pro vývojáře IoT řešení můžete vyvíjet různé součásti řešení IoT od začátku, integrovat různých předem připravená nebo open source komponenty nebo přijmout akcelerátory řešení s menší úpravy.
* **Nástroje pro nasazení řešení IoT**: po IoT řešení je napsán v jazyce, je potřeba nasadit v poli. Tento proces zahrnuje nasazení hardwaru, propojení zařízení a nasazení řešení v hardwarových zařízeních nebo v cloudu.
* **Operátor řešení IoT**: nasazené řešení po IoT, vyžaduje dlouhodobé provoz, sledování, upgradu a údržby. Tyto úlohy provést interně týmem, který obsahuje informace o technologii specialisty, hardwarových operací a údržby týmy a domény specialistů, kteří monitorovat správné chování celkovou infrastrukturu IoT.

Následující části poskytují osvědčené postupy pro každou z těchto hráčům pomáhají vyvíjet, nasazovat a provozovat zabezpečené infrastruktury IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Výrobce hardwaru IoT/integrátor

Níže jsou osvědčené postupy pro výrobce hardwaru IoT a integrátory hardwaru.

* **Určení oboru, minimální požadavky na hardware**: návrh hardwaru by měl obsahovat minimální požadované pro fungování hardwaru a žádné další funkce. Příkladem je pro zahrnutí portů USB pouze v případě, že je nezbytné pro operace zařízení. Tyto další funkce otevřít zařízení pro nežádoucí útoky, které by se jim vyhnout.
* **Ujistěte se, hardware manipulovat testování**: sestavení mechanismy pro detekci fyzické úmyslné poškozování, jako je například otevírání krytí zařízení nebo odebrání součástí zařízení. Tyto manipulovat signály mohou být součástí datového proudu nahráli do cloudu, což může upozornit operátory tyto události.
* **Sestavení kolem zabezpečený hardware**: Pokud COGS povoluje, vytvářet funkce zabezpečení, jako je zabezpečené a šifrované úložiště nebo spuštění funkce založené na čipu Trusted Platform Module (TPM). Tyto funkce musíte zařízení více zabezpečit a chránit celkovou infrastrukturu IoT.
* **Zabezpečit inovace**: upgrady firmwaru během životního cyklu zařízení jsou nevyhnutelné. Vytváření zařízení s cestami k zabezpečení pro inovace a kryptografické záruky verzí firmwaru vám umožní aby zařízení bylo zabezpečené, během a po upgradu.

## <a name="iot-solution-developer"></a>Řešení IoT pro vývojáře

Tady jsou osvědčené postupy pro vývojáře řešení IoT:

* **Postupujte podle metodologie vývoje zabezpečeného softwaru**: vývoj zabezpečeného softwaru vyžaduje základů přemýšlení o zabezpečení, od vzniku projektu až po jeho implementaci, testování a nasazení. Volby platforem, jazyků a nástrojů jsou ovlivněny pomocí této metody. Microsoft Security Development Lifecycle poskytuje podrobné přístup k vytváření zabezpečených softwaru.
* **Zvolte možnost open source softwaru pečlivě**: Open source softwaru poskytuje možnost rychle vyvíjet řešení. Abyste dokázali vybrat open source softwaru, vezměte v úvahu úrovně aktivity pro každou komponentu open sourcové komunity. Aktivní komunitě zajistí, že software je podporovaný a zjišťování a řešit problémy. Můžete také nemusí být podporován projekt open source softwaru skrytého a neaktivní a pravděpodobně nejsou být zjištěny problémy.
* **Integrace s opatrností**: existuje mnoho nedostatky v zabezpečení softwaru na hranici knihovny a rozhraní API. Funkce, které nemusí být nezbytný pro aktuální nasazení může být stále k dispozici prostřednictvím vrstvu rozhraní API. Aby bylo zajištěno celkové zabezpečení, nezapomeňte zaškrtnout všechna rozhraní komponenty integrace probíhá pro chyby zabezpečení.

## <a name="iot-solution-deployer"></a>Nástroje pro nasazení řešení IoT

Tady jsou osvědčené postupy pro pracovníky řešení IoT:

* **Bezpečné nasazení hardwaru**: nasazení IoT může vyžadovat hardware pro nasazení v nezabezpečených umístění, jako je například veřejný mezery nebo bez dohledu národní prostředí. V takových situacích, ujistěte se, že hardware nasazení odolného proti maximálním možném rozsahu. Pokud USB nebo jiné porty jsou k dispozici na hardwaru, ujistěte se, pokrývá bezpečně. Mnoho způsobů útoku je použít jako vstupní body.
* **Bezpečnost ověřovací klíče**: během nasazování, každé zařízení vyžaduje ID zařízení a související ověřovací klíče generované cloudovou službu. Bezpečnost tyto klíče fyzicky po nasazení. Žádné ohrožený klíč umožňuje škodlivý zařízení maskovat jako ze stávajících zařízení.

## <a name="iot-solution-operator"></a>Operátor řešení IoT

Tady jsou osvědčené postupy pro operátory řešení IoT:

* **Aktualizovat systém**: Ujistěte se, že jsou operační systémy zařízení a všechny ovladače zařízení upgradují na nejnovější verze. Je-li zapnout automatické aktualizace Windows 10 (IoT nebo ostatní SKU), Microsoft udržuje je aktuální, poskytuje zabezpečení operačního systému pro zařízení IoT. Udržování jinými operačními systémy (jako je Linux) aktuální pomáhá tak zajistit, že jsou rovněž chráněni před útoky se zlými úmysly.
* **Ochrana před škodlivými aktivitami**: Pokud operačního systému povolí, nainstalujte nejnovější funkce dané antivirového a antimalwarového na každý operační systém zařízení. Tento postup může pomoci zmírnit Většina externích hrozeb. Většina moderních operačních systémů před hrozbami můžete chránit pomocí odpovídající kroků.
* **Auditovat často**: auditování IoT infrastruktury pro souvisejících se zabezpečením, je klíč při reakci na incidenty zabezpečení. Většina operačních systémů poskytuje integrované události protokolování, které byste měli zkontrolovat často zajistit, aby že nedošlo k porušení zabezpečení došlo k chybě. Auditu mohou být odesílány informace jako datový proud samostatné telemetrická data do cloudové služby ve kterém se dají analyzovat.
* **Fyzicky ochrana infrastruktury IoT**: nejhorší zabezpečení útoky na infrastrukturu IoT spustily pomocí fyzický přístup k zařízením. Jedné postupem důležité zabezpečení je pro ochranu proti zneužití portů USB a dalších fyzický přístup. Fyzický přístup, jako je například použití portu USB je protokolování jednoho klíče na po porušení, které mohly nastat. Znovu Windows 10 (Internet věcí a ostatní SKU) zapne podrobné protokolování z těchto událostí.
* **Ochrana přihlašovacích údajů cloudu**: cloudové ověřování pověření používaná pro konfiguraci a provozu při nasazení IoT se pravděpodobně nejjednodušší způsob, jak získat přístup a ohrozit systém IoT. Ochrana přihlašovacích údajů často změnou hesla a nepoužívejte tyto přihlašovací údaje na veřejné počítače.

Funkce různých zařízeních IoT se liší. Některá zařízení může být počítače se systémem běžné operační systémy a některá zařízení může být velmi nenáročné operačním systémem. Osvědčené postupy zabezpečení popsané dříve může být pro tato zařízení v různých úrovních. Pokud je zadán, byste měli dodržet, další nasazení osvědčené postupy pro zabezpečení a od výrobců tato zařízení.

Některá zařízení starší verzi a omezením nemusí mít určený speciálně pro nasazení IoT. Tato zařízení nemusí mít možnost šifrování dat, připojení k Internetu nebo poskytování pokročilých auditování. V těchto případech můžete brány moderní a zabezpečené pole agregovat data ze starší verze zařízení a zabezpečení potřebné pro tato zařízení připojují přes Internet. Vyjednávání šifrovaných relací zabezpečeného ověřování, obdržení příkazů z cloudu a řadu dalších funkcí zabezpečení může poskytnout bran v terénu.