---
title: Úvod ke srovnávacím testům zabezpečení Azure
description: Úvod do zabezpečení srovnávacích testů
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369187"
---
# <a name="azure-security-benchmark-introduction"></a>Úvod do Azure Security srovnávacích testů

Nové služby a funkce jsou vydávány denně v Azure, vývojáři rychle publikují nové cloudové aplikace založené na těchto službách a útočníci vždy hledají nové způsoby zneužití nesprávně nakonfigurovaných prostředků. Migrace do cloudu je rychlá, vývojáři se rychle pohybují a útočníci se vždycky na přesunu. Jak zachováte a zajistěte, aby vaše cloudová nasazení byla zabezpečená? Jak se liší postupy zabezpečení pro cloudové systémy, které se liší od místních systémů? Jak monitorovat konzistenci napříč mnoha nezávislými vývojovými týmy?

Microsoft zjistil, že používání *srovnávacích testů zabezpečení* vám může pomoci rychle zabezpečit cloudová nasazení. Doporučení srovnávacích testů od poskytovatele cloudových služeb poskytují výchozí bod pro výběr konkrétního nastavení konfigurace zabezpečení ve vašem prostředí a umožňují vám rychle snížit rizika pro vaši organizaci.

Srovnávací test zabezpečení Azure zahrnuje kolekci vysoce ovlivňujících doporučení zabezpečení, která můžete použít k zajištění služeb, které používáte v Azure:

- **Kontrolní mechanismy zabezpečení**: Tato doporučení se obecně vztahují na vaše klienty Azure a služby Azure. Každé doporučení identifikuje seznam zúčastněných stran, které jsou obvykle součástí plánování, schválení nebo implementace srovnávacího testu. 
- **Směrné plány služeb**: tyto ovládací prvky se vztahují na jednotlivé služby Azure a poskytují doporučení k této konfiguraci zabezpečení této služby.

## <a name="implement-the-azure-security-benchmark"></a>Implementace srovnávacího testu zabezpečení Azure
- **Plánování** implementace srovnávacích testů zabezpečení Azure najdete v [dokumentaci](overview.md) pro podnikové ovládací prvky a směrné plány specifické pro danou službu k naplánování řídicí architektury a způsobu, jakým se MAPUJE na pokyny jako CIS (Controls v 7.1) a NIST (SP 800-53) Framework.
- **Monitorujte** dodržování předpisů pomocí služby Azure Security test status (a dalších sad ovládacích prvků) pomocí [řídicího panelu pro dodržování předpisů Azure Security Center předpisy](../../security-center/security-center-compliance-dashboard.md).
- **Navažte guardrails** k automatizaci zabezpečených konfigurací a prosazování dodržování předpisů Azure Security test (a dalších požadavků ve vaší organizaci) pomocí Azure modrotisky a Azure Policy.
 
Počítejte s tím, že Azure Security srovnávacích testů je v souladu s [osvědčenými postupy zabezpečení Microsoftu](/security/compass/microsoft-security-compass-introduction) (dříve Azure Security kompas), aby srovnávací test zabezpečení Azure poskytoval jediné konsolidované zobrazení doporučení zabezpečení Azure od Microsoftu.

## <a name="common-use-cases"></a>Běžné případy použití

Srovnávací test zabezpečení Azure se často používá k řešení těchto běžných výzev pro zákazníky nebo partnery služeb, kteří jsou:
- Novinkou v Azure a hledáte osvědčené postupy zabezpečení, které zajišťují zabezpečené nasazení.
- Zlepšení zabezpečení stav stávajících nasazení v Azure, aby se mohla upřednostnit nejdůležitější rizika a rizika.
- Schvalování služeb Azure pro použití technologií a podnikem pro splnění konkrétních pokynů pro zabezpečení.
- Splnění zákonných požadavků pro zákazníky, kteří pocházejí z státní správy nebo vysoce regulovaných odvětví, jako je finance a zdravotnictví (nebo dodavatelé služeb, kteří potřebují vytvářet systémy pro tyto zákazníky). Tito zákazníci musí zajistit, aby konfigurace Azure splňovala možnosti zabezpečení určené v oborovém rozhraní, jako je SNS, NIST nebo PCI. Srovnávací test zabezpečení Azure poskytuje účinný přístup k ovládacím prvkům, které už jsou předem namapované na tyto srovnávací testy.

## <a name="terminology"></a>Terminologie

Výrazy "Control", "srovnávací" a "základní" jsou často používány v dokumentaci srovnávacích testů zabezpečení Azure a je důležité porozumět tomu, jak Azure tyto výrazy používá.


| Období | Popis | Příklad |
|--|--|--|
| Řízení | Ovládací prvek je podrobný popis funkce nebo aktivity, která se musí řešit a která není specifická pro technologii nebo implementaci. | Ochrana dat je jedním z ovládacích prvků zabezpečení. Tento ovládací prvek obsahuje konkrétní akce, které je třeba řešit, aby bylo zajištěno, že dojde k ochraně dat. |
| Srovnávací test | Srovnávací test obsahuje doporučení pro zabezpečení konkrétní technologie, jako je Azure. Doporučení jsou zařazená do kategorie podle ovládacího prvku, ke kterému patří. | Srovnávací test zabezpečení Azure zahrnuje doporučení týkající se zabezpečení, která jsou specifická pro platformu Azure. |
| Standardní hodnoty | Základní hodnotou je implementace srovnávacího testu pro jednotlivé služby Azure. V rámci rozsahu implementace Azure je každá organizace rozhodla o srovnávacím doporučení a odpovídajících konfiguracích. | Společnost Contoso se podívá na povolení funkcí zabezpečení Azure SQL podle konfigurace doporučené v základní úrovni zabezpečení Azure SQL.

Uvítáme vaše názory na srovnávací testy zabezpečení Azure. V níže uvedené oblasti zpětné vazby doporučujeme zadat komentáře. Pokud dáváte přednost sdílení vstupu s týmem Azure Security test, budete připraveni vyplnit formulář na stránce https://aka.ms/AzSecBenchmark
