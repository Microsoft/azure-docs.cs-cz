---
title: Co je Azure Service Health?
description: Individuální informace o tom, jak jsou vaše aplikace Azure ovlivněny aktuálními a budoucími problémy a údržbou služeb Azure.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654082"
---
# <a name="what-is-azure-service-health"></a>Co je Azure Service Health?

Azure nabízí sadu funkcí, které vám zajistí, abyste byli informováni o stavu vašich cloudových prostředků. Tyto informace zahrnují aktuální a nadcházející problémy, jako jsou například události ovlivňující službu, plánovaná údržba a další změny, které mohou mít vliv na dostupnost.

Azure Service Health je kombinací tří oddělených menších služeb.

[Stav Azure](azure-status-overview.md) informuje o výpadkech služby v Azure na **[stránce stavu Azure](https://status.azure.com)** . Stránka je globální pohled na stav všech služeb Azure napříč všemi oblastmi Azure. Stavová stránka je dobrým odkazem na incidenty s rozšířeným dopadem, ale důrazně doporučujeme, aby aktuální uživatelé Azure využili Azure Service Health, aby měli informace o incidentech a údržbě Azure.

[Azure Service Health](service-health-overview.md) poskytuje individuální přehled o stavu služeb a oblastí Azure, které používáte. Je to nejvhodnější místo pro hledání informací o výpadcích s dopadem na služby, aktivitách plánované údržby a dalších rad ohledně stavu, protože ověřené prostředí služby Azure Service Health ví, které služby a prostředky aktuálně používáte. Nejlepší způsob využití služby Service Health je nastavit upozornění této služby, která vás budou prostřednictvím vašich upřednostňovaných komunikačních kanálů informovat o potížích se službami, plánované údržbě nebo jiných změnách, které by mohly ovlivnit oblasti nebo služby Azure, které využíváte.

[Azure Resource Health](resource-health-overview.md) poskytuje informace o stavu vašich individuálních cloudových prostředků, jako je třeba konkrétní instance virtuálního počítače. Pomocí Azure Monitoru můžete také nastavit výstrahy, které vás upozorní na změny dostupnosti vašich cloudových prostředků. Azure Resource Health vám společně s oznámeními Azure Monitoru pomůže zajistit lepší informovanost o okamžité dostupnosti vašich prostředků a rychle vyhodnotit, jestli případné potíže jsou způsobené problémy na vaší straně, nebo jestli souvisejí s událostí platformy Azure.

Společně poskytují kompletní přehled o stavu Azure, a to s takovou úrovní podrobností, která je pro vás nejrelevantnější.

**Podívejte se na Přehled stránky stavu Azure, Azure Service Health a Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]