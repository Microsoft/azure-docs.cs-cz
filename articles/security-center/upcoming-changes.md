---
title: Důležité změny přicházející do Azure Security Center
description: Nadcházející změny Azure Security Center, na které může být nutné vědět a pro které může být nutné naplánovat
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380159"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>"Aktualizace systému by se měly nainstalovat na vaše počítače" doporučení, jak podléhat doporučení

#### <a name="summary"></a>Shrnutí

| Aspekt | Podrobnosti |
|---------|---------|
|Datum oznámení | 9. listopadu 2020  |
|Datum pro tuto změnu  |  Střední-konec 2020. listopadu |
|Dopad     | Během přechodu od aktuální verze tohoto doporučení k jeho nahrazení se může změnit vaše zabezpečené skóre. |
|  |  |

Vydáváme rozšířenou verzi aktualizací systému, kterou je **třeba nainstalovat do doporučení vašich počítačů** . Nová verze *nahradí* aktuální verzi v ovládacím prvku zabezpečení použít aktualizace systému a přináší následující vylepšení:

- Dílčí doporučení pro každou chybějící aktualizaci
- Přepracované prostředí na Azure Security Center stránkách Azure Portal
- Obohacená data pro doporučení z Azure Resource graphu

#### <a name="transition-period"></a>Přechodné období

Bude se jednat o přechodné období od 36 hodiny (přibližně). Abychom minimalizovali případné přerušení, naplánovali jsme provedení aktualizace na víkend. Během přechodu můžou být ovlivněné vaše zabezpečené skóre.

#### <a name="redesigned-portal-experience"></a>Přepracování prostředí portálu

Stránka s podrobnostmi o doporučení pro **aktualizace systému by měla být nainstalovaná na vašich počítačích** obsahuje seznam zjištění, jak je uvedeno níže. Když vyberete jedno hledání, otevře se podokno podrobností s odkazem na informace o opravách a seznam ovlivněných prostředků.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Otevře se jedno z dílčích doporučení v prostředí portálu pro aktualizované doporučení.":::


#### <a name="richer-data-from-azure-resource-graph"></a>Rozsáhlejší data z Azure Resource graphu

Azure Resource Graph je služba v Azure, která je navržená tak, aby poskytovala efektivní průzkum prostředků. ARG můžete použít k dotazování škálování v rámci dané sady předplatných, abyste mohli efektivně řídit vaše prostředí. 

Pro Azure Security Center můžete použít ARG a [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) k dotazování široké škály dat stav zabezpečení.

Pokud se dotazuje na aktuální verzi **aktualizací systému, měla by být na počítačích nainstalovaná** jediná informace, která je k dispozici z arg, je to, že je potřeba na počítači opravit doporučení. Po vydání aktualizované verze bude následující dotaz vracet všechny chybějící aktualizace systému seskupené podle počítače.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).