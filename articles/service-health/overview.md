---
title: Co je Azure Service Health?
description: Individuální informace o tom, jak jsou vaše aplikace Azure ovlivněny aktuálními a budoucími problémy a údržbou služeb Azure.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87499768"
---
# <a name="what-is-azure-service-health"></a>Co je Azure Service Health?

Azure nabízí sadu funkcí, které vám zajistí, abyste byli informováni o stavu vašich cloudových prostředků. Tyto informace zahrnují aktuální a nadcházející problémy, jako jsou například události ovlivňující službu, plánovaná údržba a další změny, které mohou mít vliv na dostupnost.

Azure Service Health je kombinací tří oddělených menších služeb.

[Stav Azure](azure-status-overview.md) informuje o výpadkech služby v Azure na **[stránce stavu Azure](https://status.azure.com)**. Stránka je globální pohled na stav všech služeb Azure napříč všemi oblastmi Azure. Stavová stránka je dobrým odkazem na incidenty s rozšířeným dopadem, ale důrazně doporučujeme, aby aktuální uživatelé Azure využili Azure Service Health, aby měli informace o incidentech a údržbě Azure.

[Služba Health Service](service-health-overview.md) nabízí přizpůsobené zobrazení stavu služeb a oblastí Azure, které používáte. Toto je nejlepší místo, kde můžete najít službu, která má vliv na komunikaci týkající se výpadků, plánovaných aktivit údržby a dalších poradců stavu, protože ověřené prostředí Service Health ví, které služby a prostředky aktuálně používáte. Nejlepší způsob využití služby Service Health je nastavit upozornění této služby, která vás budou prostřednictvím vašich upřednostňovaných komunikačních kanálů informovat o potížích se službami, plánované údržbě nebo jiných změnách, které by mohly ovlivnit oblasti nebo služby Azure, které využíváte.

[Resource Health](resource-health-overview.md) poskytuje informace o stavu vašich individuálních cloudových prostředků, jako je třeba konkrétní instance virtuálního počítače. Pomocí Azure Monitoru můžete také nastavit výstrahy, které vás upozorní na změny dostupnosti vašich cloudových prostředků. Resource Health spolu s Azure Monitormi oznámeními vám pomůžou udržet lepší informace o dostupnosti vašich prostředků po minutách a rychle posoudit, jestli problém souvisí s problémem na straně nebo s událostí platformy Azure.

Společně poskytují kompletní přehled o stavu Azure, a to s takovou úrovní podrobností, která je pro vás nejrelevantnější.

**Podívejte se na přehled stránky stavu Azure a služeb Azure Service Health a Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]