---
title: Kontrolovat Resource Health události, které mají vliv na váš cluster AKS (Preview)
description: Pomocí Azure Resource Health ověřte stav clusteru AKS.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: f830debafb1a9b6b303ee6af987c7cd4fbf34aea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358249"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Kontrolovat Resource Health události, které mají vliv na váš cluster AKS (Preview)


Při spouštění vašich kontejnerových úloh na AKS chcete zajistit, aby bylo možné řešit problémy a opravovat problémy, jakmile vznikne, aby se minimalizoval dopad na dostupnost vašich úloh. [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview) poskytují přehled o různých událostech stavu, které můžou způsobit, že váš cluster AKS nebude k dispozici.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Otevřít Resource Health

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Pro přístup k Resource Health clusteru AKS:

- V [Azure Portal](https://portal.azure.com)přejděte na svůj cluster AKS.
- V levém navigačním panelu vyberte **Resource Health** .

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Přístup k Resource Health pro všechny clustery v rámci vašeho předplatného:

- Vyhledejte **Service Health** v [Azure Portal](https://portal.azure.com) a přejděte na ni.
- V levém navigačním panelu vyberte **stav prostředku** .
- Vyberte své předplatné a nastavte typ prostředku na službu Azure Kubernetes Service (AKS).

![Snímek obrazovky ukazuje stav prostředků pro clustery A K S.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Kontrolovat stav

Azure Resource Health vám pomůže s diagnostikou a získáním podpory pro problémy se službami, které ovlivňují vaše prostředky Azure. Resource Health sestavuje aktuální a minulý stav vašich prostředků a pomůže vám zjistit, jestli je problém způsobený uživatelem zahájenou akcí nebo událostí platformy.

Resource Health přijímá signály pro spravovaný cluster ke zjištění stavu clusteru. Kontroluje stav vašeho clusteru AKS a oznamuje akce vyžadované pro každý signál stavu. Tyto signály jsou v rozsahu od automatického řešení potíží, plánovaných aktualizací, neplánovaných událostí stavu a nedostupností způsobené uživatelem inicializovanými akcemi. Tyto signály jsou klasifikovány pomocí stavu Azure Resource Health: *k dispozici*, *nedostupné*, *neznámé*a *omezené*.

- **K dispozici**: Pokud neexistují žádné známé problémy, které by ovlivnily stav vašeho clusteru, Resource Health nahlásí cluster jako *dostupný*.

- **Nedostupné**: když existuje událost platformy nebo jiné platformy, která má vliv na stav vašeho clusteru, Resource Health hlásí cluster jako *nedostupný*.

- **Neznámé**: když dojde ke ztrátě dočasných připojení ke metrikám stavu clusteru, Resource Health hlásí cluster jako *Neznámý*.

- **Snížené**: když dojde k problému se stavem, který vyžaduje vaši akci, Resource Health hlásí cluster jako *snížený*.

Další podrobnosti o tom, co každý stav uvádí, najdete v článku [přehled Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview#health-status).

### <a name="view-historical-data"></a>Zobrazit historická data

Můžete si také prohlédnout posledních 30 dní historických Resource Health informací v části **historie stavu** .

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit potíže s clustery pomocí [diagnostiky AKS](https://docs.microsoft.com/azure/aks/concepts-diagnostics), spusťte kontroly clusteru.
