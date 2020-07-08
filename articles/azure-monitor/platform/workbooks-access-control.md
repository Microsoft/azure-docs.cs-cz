---
title: Řízení přístupu k Azure Monitor sešitům
description: Zjednodušení složitých sestav s předem sestavenými a vlastními parametrizovanými sešity s řízením přístupu na základě rolí
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658417"
---
# <a name="access-control"></a>Řízení přístupu

Řízení přístupu v sešitech odkazuje na dvě věci:

* Přístup je vyžadován pro čtení dat v sešitu. Tento přístup se řídí standardními [rolemi Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) u prostředků použitých v sešitu. Sešity neurčují ani nekonfigurují přístup k těmto prostředkům. Uživatelé obvykle získají tento přístup k těmto prostředkům pomocí role [Čtenář monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) v těchto prostředcích.

* Vyžaduje se přístup k ukládání sešitů.

    - Ukládání privátních `("My")` sešitů nevyžaduje žádná další oprávnění. Všichni uživatelé můžou ukládat soukromé sešity a můžou tyto sešity zobrazit jenom.
    - Ukládání sdílených sešitů vyžaduje oprávnění k zápisu do skupiny prostředků pro uložení sešitu. Tato oprávnění jsou obvykle určena rolí [přispěvatele monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , ale lze je také nastavit prostřednictvím role *přispěvatele pracovní sešity* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standardní role s oprávněními souvisejícími s sešitem

[Čtečka monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) zahrnuje standardní/Read oprávnění, která by používaly nástroje pro monitorování (včetně sešitů) ke čtení dat z prostředků.

[Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) zahrnuje obecná `/write` oprávnění používaná různými nástroji pro monitorování pro ukládání položek (včetně `workbooks/write` oprávnění k ukládání sdílených sešitů).
"Přispěvatelé sešity" přidává do objektu oprávnění "sešity/zápis" do objektu pro ukládání sdílených sešitů.
Uživatelé nemůžou ukládat soukromé sešity, které uvidí jenom, a nemusí žádná zvláštní oprávnění.

Pro vlastní řízení přístupu na základě rolí:

Přidejte `microsoft.insights/workbooks/write` , chcete-li uložit sdílené sešity. Další podrobnosti najdete v tématu role [přispěvatele sešitu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) .

## <a name="next-steps"></a>Další kroky

* [Začínáme](workbooks-visualizations.md) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
