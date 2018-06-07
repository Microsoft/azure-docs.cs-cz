---
title: Možnosti podpory a zásady vyřazení Průvodce pro Azure hostovaného operačního systému | Microsoft Docs
description: Poskytuje informace o co podpory společnosti Microsoft se s ohledem na Azure hostovaného operačního systému používá cloudové služby.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: dfa3bac95b9827789950b4931e3198237de4a1fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608558"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure zásad možnosti podpory a vyřazení hostovaného operačního systému
Informace na této stránce má vztah k Azure hostovaného operačního systému ([hostovaného operačního systému](cloud-services-guestos-update-matrix.md)) pro cloudové služby worker a webová role (PaaS). Nevztahuje se na virtuálních počítačích (IaaS).

Společnost Microsoft nemá publikovaný [zásady podpory pro hostovaného operačního systému](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Stránka, kterou právě čtete teď popisuje, jak implementovat zásady.

Tato zásada je

1. Microsoft bude podporovat **alespoň nejnovější dvě řady operačního systému hosta**. Při vyřazení rodinu, mají zákazníci dobu 12 měsíců od data oficiální vyřazení aktualizovat na novější podporované rodiny hostovaného operačního systému.
2. Microsoft bude podporovat **alespoň dvě nejnovější verze podporovaných rodin OS hosta**.
3. Microsoft bude podporovat **alespoň dva nejnovější verze sady Azure SDK**. Při vyřazení verzi sady SDK, bude mít zákazníci dobu 12 měsíců od data oficiální vyřazení aktualizovat na novější verzi.

V některých případech může být podporována více než dvě řady nebo verze. Informace o podpoře oficiální hostovaného operačního systému se zobrazí na [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Když je vyřazeno verze hostovaného operačního systému
Nové hostovaného operačního systému **verze** vydávají o každý měsíc umožňují začlenění nejnovějších aktualizací střediska MSRC. Z důvodu pravidelných měsíčních aktualizací obvykle verze hostovaného operačního systému je zakázána přibližně 60 dnů po jeho vydání. Tato aktivita udržuje alespoň dvě verze hostovaného operačního systému pro každou skupinu, která je k dispozici pro použití.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces při vyřazení rodiny hostovaného operačního systému
Jakmile oznamují vyřazení zákazníci mají 12 měsíce "přechodu" období před starší rodiny se oficiálně vyřadí z provozu. Tento čas přechodu může prodloužit uvážení společnosti Microsoft. Aktualizace budou odeslány na [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).

Proces postupného vyřazení zahájíte měsíců půl (6) do období přechodu. Během této doby:

1. Microsoft oznámí zákazníkům vyřazení.
2. Novější verzi sady Azure SDK nebude podporovat vyřazeno rodiny hostovaného operačního systému.
3. Nová nasazení a redeployments cloudové služby nebudou povolena, v vyřazeno řady

Microsoft bude zavádět nové verze hostovaného operačního systému zařadit nejnovější aktualizace střediska MSRC do posledního dne období přechodu, označuje jako "datum vypršení platnosti". Na datum vypršení platnosti bude se Nepodporovaná cloudové služby, které jsou pořád spuštěné v rámci smlouvy SLA pro Azure. Společnost Microsoft nemá na uvážení vynutit upgrade, odstraňte nebo zastavte tyto služby po tomto datu.

### <a name="process-during-a-guest-os-version-retirement"></a>Proces při vyřazení verze operačního systému hosta
Pokud zákazníci nastavit jejich hostovaného operačního systému k automatické aktualizaci, mají nikdy starat o práci s verzí hostovaného operačního systému. Se budou vždy používat nejnovější verzi hostovaného operačního systému.

Verze operačního systému hosta vydávají každý měsíc. Z důvodu rychlost regulární verzí každá verze má pevnou životnost.

Na 60 dnů do životnost, verze je "*zakázáno*". "Zakázáno" znamená, že verze odebrána z portálu. Už může být verze nastavená z konfiguračního souboru CSCFG. Existující nasazení jsou spuštěny. Ale nových nasazení a existující nasazení kódu a konfigurace aktualizace nebudou povolena.

Delší dobu, po tom, aby "Zakázat", verze operačního systému hosta "*vyprší platnost*" a všechny instalace této verze jsou force upgradovat a nastavte k automatické aktualizaci hostovaného operačního systému v budoucnu. Vypršení platnosti se provádí v dávkách tak doba z postižení vypršení platnosti se může lišit.

Tyto doby může provést delší uvážení společnosti Microsoft k usnadnění přechody zákazníka. Všechny změny budou oznamovat na [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Oznámení při vyřazení
* **Rodiny vyřazení** <br>Microsoft použije příspěvcích na blogu a portálu oznámení. Zákazníci, kteří stále používají vyřazeno hostovaného operačního systému rodiny informováni prostřednictvím přímé komunikaci (e-mailu, portálu zprávy, telefonního hovoru) pro správce přiřazené služby. Všechny změny budou odeslány do [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md).
* **Vyřazení verze** <br>Všechny změny a kalendářní data k nim dojde, budou odeslány do [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](cloud-services-guestos-update-matrix.md), včetně verze, zakázán nebo vypršení platnosti. Správci služby bude dostávat e-maily, pokud mají nasazení spuštění na zakázáno verze operačního systému hosta nebo rodiny. Načasování těchto e-mailů se může lišit. Obecně jsou alespoň měsíc předcházející postižení, i když tento časování není oficiální SLA.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Jak zmírnit dopad migrace**

Doporučujeme vám, že používáte nejnovější rodiny hostovaného operačního systému pro navrhování cloudové služby.

1. Zahájit plánování migrace na řadu novější již v rané fázi.
2. Nastavte dočasné testovací nasazení pro testování cloudové služby spuštěné v nové rodiny.
3. Nastavit vaší verzí hostovaného operačního systému na **automatické** (osVersion = * v [.cscfg](cloud-services-model-and-package.md#cscfg) souboru) tak, že migrace na nové verze hostovaného operačního systému probíhá automaticky.

**Co když Moje webová aplikace vyžaduje hlubší integrace s operačním systémem?**

Pokud architektura vaší webové aplikace, závisí na základní funkce operačního systému, použijte možnosti podporované platformy, jako [spuštění úlohy](cloud-services-startup-tasks.md) nebo jiných mechanismů rozšíření. Alternativně můžete také použít [virtuální počítače Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktura jako služba), kde je zodpovědná za údržbu příslušný operační systém.

## <a name="next-steps"></a>Další postup
Přečtěte si nejnovější [uvolní hostovaného operačního systému](cloud-services-guestos-update-matrix.md).
