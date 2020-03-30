---
title: Supportability a retirement policy guide for Azure Guest OS | Dokumenty společnosti Microsoft
description: Obsahuje informace o tom, co bude Microsoft podporovat, pokud jde o hostovaný operační systém Azure používaný cloudovými službami.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68945457"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Zásady podpory a zásady vyřazení hostovaného operačního systému Azure
Informace na této stránce se týkají operačního systému Azure Guest[(Guest OS](cloud-services-guestos-update-matrix.md)) pro pracovníky cloudových služeb a webové role (PaaS). Nevztahuje se na virtuální počítače (IaaS).

Společnost Microsoft má publikované [zásady podpory pro hostovaného operačního systému](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Stránka, kterou čtete, nyní popisuje, jak je zásada implementována.

Tato politika je

1. Společnost Microsoft bude podporovat **alespoň nejnovější dvě rodiny hostovaného operačního systému**. Když je rodina v důchodu, mají zákazníci 12 měsíců od oficiálního data odchodu do důchodu, aby se aktualizovali na novější podporovanou rodinu hostovaného operačního systému.
2. Společnost Microsoft bude podporovat **alespoň nejnovější dvě verze podporovaných rodin hostovaného operačního systému**.
3. Microsoft bude podporovat **alespoň nejnovější dvě verze sady Azure SDK**. Když je verze sady SDK vyřazena, zákazníci budou mít 12 měsíců od oficiálního data odchodu do důchodu, aby se aktualizovali na novější verzi.

Někdy může být podporováno více než dvě rodiny nebo verze. Oficiální informace o podpoře hostovaného operačního systému se zobrazí ve [verzích Azure Guest OS Releases a SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Když je verze hostovaného operačního systému vyřazena
Nové verze hostovaného operačního systému jsou zavedeny přibližně každý měsíc, aby byly **začleněny** nejnovější aktualizace MSRC. Z důvodu pravidelných měsíčních aktualizací je verze hostovaného operačního systému obvykle zakázána přibližně 60 dní po jeho vydání. Tato aktivita udržuje alespoň dvě verze hostovaného operačního systému pro každou rodinu k dispozici pro použití.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces během odchodu rodiny hostovaného operačního systému do důchodu
Jakmile je odchod do důchodu oznámil, zákazníci mají 12 měsíců "přechod" období před starší rodina je oficiálně odstraněn z provozu. Tato doba přechodu může být prodloužena podle uvážení společnosti Microsoft. Aktualizace budou zveřejněny na [vydáních hostovaného operačního systému Azure a matici kompatibility sad SDK](cloud-services-guestos-update-matrix.md).

Postupný proces odchodu do důchodu začne šest (6) měsíců do přechodného období. Během této doby:

1. Společnost Microsoft informuje zákazníky o odchodu do důchodu.
2. Novější verze sady Azure SDK nebude podporovat vyřazené řady hostovaného operačního systému.
3. Nová nasazení a přesuny cloudových služeb nebudou v rodině důchodců povoleny.

Společnost Microsoft bude i nadále zavádět novou verzi hostovaného operačního systému zahrnující nejnovější aktualizace MSRC až do posledního dne přechodného období, známého jako "datum vypršení platnosti". K datu vypršení platnosti cloudové služby, které jsou stále spuštěné, nebudou v rámci azure sla podporovány. Společnost Microsoft má možnost vynutit upgrade, odstranění nebo zastavení těchto služeb po tomto datu.

### <a name="process-during-a-guest-os-version-retirement"></a>Proces během vyřazení verze hostovaného operačního systému
Pokud zákazníci nastavili svůj hostovaný operační systém tak, aby se automaticky aktualizoval, nikdy se nebudou muset starat o práci s verzemi hostovaného operačního systému. Budou vždy používat nejnovější verzi hostovaného operačního systému.

Verze hostovaného operačního systému jsou vydávány každý měsíc. Vzhledem k rychlosti pravidelných verzí má každá verze pevnou životnost.

Po 60 dnech životnosti je verze "*zakázána*". "Zakázáno" znamená, že verze je odebrána z portálu. Verzi již nelze nastavit z konfiguračního souboru CSCFG. Existující nasazení jsou ponechána spuštěná. Ale nová nasazení a kód a aktualizace konfigurace pro stávající nasazení nebudou povoleny.

Někdy poté, co se stal "zakázán", hostovaného operačního systému verze "vyprší" a všechny instalace stále běží, že vypršela verze jsou vystaveny problémům se zabezpečením a zranitelnosti. Obecně platí, že vypršení platnosti se provádí v dávkách, takže období od vypnutí do vypršení platnosti se může lišit.

Zákazníci, kteří nakonfigurují své služby tak, aby aktualizovali hostovaný operační systém ručně, by měli zajistit, aby jejich služby byly spuštěny v podporovaném hostovaném os. Pokud je služba nakonfigurována k automatické aktualizaci hostovaného operačního systému, základní platforma zajistí dodržování předpisů a upgraduje na nejnovější hostovaný operační systém.

Tato období mohou být podle uvážení společnosti Microsoft delší, aby se usnadnilpřechod zákazníků. Všechny změny budou sděleny na [azure guest os verze a SDK kompatibility matice](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Oznámení během odchodu do důchodu
* **Rodinný odchod do důchodu** <br>Společnost Microsoft bude používat blogové příspěvky a oznámení portálu. Zákazníci, kteří stále používají vyřazenou rodinu hostovaného operačního systému, budou prostřednictvím přímé komunikace (e-mail, portálové zprávy, telefonní hovor) upozorněni na přiřazené správce služeb. Všechny změny budou zaúčtovány do [verzí azure guest os a matice kompatibility sady SDK](cloud-services-guestos-update-matrix.md).
* **Verze odchodu do důchodu** <br>Všechny změny a data, ke kterým dojde, budou zaúčtovány do [verzí azure guest os a matice kompatibility sady SDK](cloud-services-guestos-update-matrix.md), včetně vydání, zakázáno a vypršení platnosti. Správci služeb budou dostávat e-maily, pokud mají nasazení spuštěná v zakázané verzi nebo rodině hostovaného operačního systému. Načasování těchto e-mailů se může lišit. Obecně jsou nejméně měsíc před invaliditou, i když toto načasování není oficiální SLA.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Jak mohu zmírnit dopady migrace?**

Pro návrh cloudových služeb doporučujeme používat nejnovější rodinu hostovaného operačního.

1. Začněte plánovat migraci do novější rodiny brzy.
2. Nastavte dočasná testovací nasazení pro testování cloudové služby spuštěné v nové rodině.
3. Nastavte verzi hostovaného operačního systému na **automatickou** (osVersion=* v souboru [.cscfg),](cloud-services-model-and-package.md#cscfg) aby k migraci na nové verze hostovaného operačního systému došlo automaticky.

**Co když moje webová aplikace vyžaduje hlubší integraci s osem?**

Pokud architektura webových aplikací závisí na základních funkcích operačního systému, použijte funkce podporované platformou, jako jsou [úlohy při spuštění](cloud-services-startup-tasks.md) nebo jiné mechanismy rozšiřitelnosti. Případně můžete také použít [virtuální počítače Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktura jako služba), kde jste zodpovědní za údržbu základního operačního systému.

## <a name="next-steps"></a>Další kroky
Projděte si nejnovější [verze hostovaného operačního systému](cloud-services-guestos-update-matrix.md).
