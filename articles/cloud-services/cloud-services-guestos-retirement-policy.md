---
title: Příručka k podpoře a zásady vyřazení z provozu pro hostovaný operační systém Azure | Dokumentace Microsoftu
description: Poskytuje informace o tom, co Microsoft se podporují jde do hostovaného operačního systému Azure používá Služba Cloud Services.
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
ms.openlocfilehash: 6068f054a2ce695a889351b1f959319c64eb73fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235594"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Služba Azure policy podpoře a vyřazení hostovaného operačního systému
Informace na této stránce se týká operačního systému hosta Azure ([hostovaného operačního systému](cloud-services-guestos-update-matrix.md)) pro cloudové služby worker a webová role (PaaS). Nevztahuje se na virtuálních počítačích (IaaS).

Microsoft má publikování [zásady podpory pro hostovaný operační systém](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Na stránce, které právě čtete nyní popisuje, jak implementovat zásady.

Tato zásada je

1. Microsoft bude podporovat **alespoň nejnovější dvěma rodinami hostovaný operační systém**. Když je rodina byl vyřazen z provozu, zákazníci mají 12 měsíců od data oficiální vyřazení aktualizovat na novější podporované řady hostovaného operačního systému.
2. Microsoft bude podporovat **alespoň dvou nejnovějších verzí podporované řady hostovaného operačního systému**.
3. Microsoft bude podporovat **alespoň dvě nejnovější verze sady Azure SDK**. Když verzi sady SDK byl vyřazen z provozu, budou mít zákazníci 12 měsíců od data oficiální vyřazení aktualizovat na novější verzi.

V některých případech mohou být podporovány více než dvěma rodinami nebo vydání. Informace o podpoře oficiální hostovaného operačního systému se objeví na [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Když je vyřazený verze hostovaného operačního systému
Nové hostovaného operačního systému **verze** zavedení o začlenit nejnovější aktualizace MSRC Každý měsíc. Z důvodu pravidelné měsíční aktualizace verze hostovaného operačního systému je běžně zakázané přibližně 60 dnů po jeho uvolnění. Tato aktivita udržuje alespoň dvě verze hostovaného operačního systému pro každou skupinu, která je k dispozici pro použití.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces při vyřazení řady hostovaného operačního systému
Jakmile je jsme oznámili vyřazení z provozu, zákazníci mají 12 měsíců "přechodu" období před opakovaným starší řady se oficiálně vyřadí z provozu. Tento čas přechodu může být rozšířena na základě vlastního uvážení Microsoftu. Aktualizace se publikuje na [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md).

Proces postupného vyřazení se začne šest (6) měsíců do přechodného období. Během této doby:

1. Microsoft upozorní zákazníky vyřazení z provozu.
2. Novější verzi sady Azure SDK nebude podporovat vyřazení hostovaného operačního systému řady.
3. Nová nasazení a opětovných nasazení cloudových služeb nebudou povolena, vyřazení řady

Microsoft nadále zavedení nové verze hostovaného operačního systému s nejnovějšími aktualizacemi MSRC do posledního dne přechodného období, známé jako "datum vypršení platnosti". Na datum vypršení platnosti se není podporován cloudové služby spuštěné v rámci smlouvy SLA pro Azure. Microsoft má na základě vlastního uvážení vynutit upgradu, odstraňte nebo zastavte tyto služby po tomto datu.

### <a name="process-during-a-guest-os-version-retirement"></a>Proces při vyřazení verze operačního systému hosta
Pokud zákazníci nastavit automatickou aktualizaci jejich hostovaného operačního systému, že nikdy nemusíte dělat starosti o práci s verzí hostovaného operačního systému. Budou vždy používat nejnovější verzi operačního systému hosta.

Verze operačního systému hosta, jsou vydávány každý měsíc. Z důvodu míra běžných verzí má každá verze pevné životnost.

60 dní do životnost, vyžaduje se verze "*zakázané*". "Zakázáno" znamená, že je verze odebrána z portálu. Už může být verze nastavená z konfiguračního souboru CSCFG. Stávající nasazení jsou spuštěny. Ale nebudou povolena nová nasazení a existující nasazení aktualizace kódu a konfigurace.

Nějakou dobu po stávají "zakázáno", verze operačního systému hosta "*vyprší platnost*" a všechny instalace pořád běží tuto verzi mají být nuceně upgradovat a nastavit na automaticky hostovaný operační systém v budoucnu aktualizovat. Vypršení platnosti se provádí v dávkách, takže časovém intervalu od postižení vypršení platnosti se může lišit.

Tyto tečky se dají vytvořit delší uvážení společnosti Microsoft pro usnadnění přechody zákazníků. Všechny změny oznámíme na [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Oznámení během vyřazení z provozu
* **Vyřazení řady** <br>Microsoft použije blogové příspěvky a portálu oznámení. Zákazníci, kteří stále používají vyřazení hostovaného operačního systému řady budete informováni prostřednictvím přímé komunikace (e-mailu, portálu zprávy, telefonního hovoru) pro správce služby přiřazené. Všechny změny budou odeslány do [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md).
* **Verze vyřazení z provozu** <br>Všechny změny a data se objeví se zveřejní [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](cloud-services-guestos-update-matrix.md), včetně verze, zakázána a vypršení platnosti. Správci služeb budou dostávat e-maily, pokud mají nasazení spuštěné na zakázané verze hostovaného operačního systému nebo řady. Načasování těchto e-mailů se může lišit. Obecně jsou alespoň měsíc předcházející postižení, i když tato časování není oficiální smlouvu SLA.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Jak lze zmírnit dopady migrace?**

Doporučujeme používat nejnovější hostovaného operačního systému řady pro návrh vašich cloudových služeb.

1. Pusťte se do plánování migrace na novější řady již v rané fázi.
2. Nastavení nasazení dočasných testů pro testování vaší cloudové služby, které běží na nové rodiny.
3. Nastavení vaší verze hostovaného operačního systému **automatické** (osVersion = * v [.cscfg](cloud-services-model-and-package.md#cscfg) souboru), automaticky dojde k migraci na nové verze hostovaného operačního systému.

**Co když Moje webová aplikace vyžaduje hlubší integraci s operačním systémem?**

Pokud vaše architektura pro webové aplikace závisí na základní funkce operačního systému, použijte funkce podporované platformy, jako [úlohy po spuštění](cloud-services-startup-tasks.md) nebo jiných mechanismů rozšiřitelnosti. Alternativně můžete také použít [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infrastruktura jako služba), kde jsou odpovědná za správu základního operačního systému.

## <a name="next-steps"></a>Další postup
Přečtěte si nejnovější [verzí hostovaných operačních systémů](cloud-services-guestos-update-matrix.md).
