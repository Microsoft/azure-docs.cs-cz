---
title: Volba mezi Azure Cost Managementem a Cloudynem
description: Tento článek vám pomůže určit, jestli je pro vaše potřeby správy nákladů nejvhodnější Azure Cost Management nebo Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 9b17cdfbae7fccae146f01654fb755f23f00563c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80083206"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Volba mezi Azure Cost Managementem a Cloudynem

Cloudyn vyřazuje z provozu do konce roku 2020. Stávající funkce Cloudynu se tam, kde je to možné, integrují přímo do webu Azure Portal. S výjimkou zákazníků CSP se v tuto chvíli nemohou onboardovat žádní noví zákazníci. Podpora pro stávající produkt zůstane zachovaná, dokud se úplně nevyřadí z provozu.

Společnost Microsoft získala Cloudyn a migruje jeho funkce pro správu nákladů z portálu Cloudyn nativně do Azure. Pokud chcete používat nové funkce, přihlaste se k webu Azure Portal a v seznamu služeb Azure přejděte na [Cost Management a fakturace](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview). V porovnání se službou Cloudyn nabízí nativní prostředí vylepšený výkon a nižší latenci dat přibližně osm hodin.

Migrace klíčových funkcí pro smlouvu Enterprise, průběžné platby a kategorie nabídek MSDN pro Azure Cost Management je dokončená. Předplatná CSP jsou aktuálně migrovaná do Azure Cost Management.

Pokud máte kategorii nabídek, která ještě nebyla migrovaná, měli byste nadále používat portál Cloudyn. Kdokoli jiný může použít Azure Cost Management.

## <a name="recommended-services-by-offer"></a>Doporučené služby podle nabídky

| Nabídky Microsoft Azure | Doporučená služba pro správu nákladů |
| --- | --- |
| Smlouva Azure Enterprise | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (průběžné platby/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Smlouva se zákazníkem Microsoftu | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Smlouva se zákazníkem Microsoftu podporovaná partnery | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Dostupné funkce Cost Managementu

Některé z následujících funkcí jsou k dispozici v Cloudynu, ale všechny jsou nyní k dispozici v Azure Cost Management.

- Rozhraní API
- Doporučení pro optimalizaci nákladů Azure, mimo jiné:
    - Doporučení pro vypnutí a určení správné velikosti instancí Azure
    - Doporučení pro rezervace Azure
- Rozpočty
- Analýza nákladů
- Export dat do účtu služby Azure Storage
- Nižší latence
- Aplikace šablony Power BI
- Podpora značek prostředků
- Podpora analýzy nákladů mezi cloudy pro AWS

## <a name="next-steps"></a>Další kroky
- Další informace o službě [Azure Cost Management](../cost-management-billing-overview.md)