---
title: Řízení přístupu k Azure Monitor sešitům
description: Zjednodušení složitých sestav s předem sestavenými a vlastními parametrizovanými sešity s řízením přístupu na základě rolí
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7d3bc13dc373cda510153099859cf4cd61b3dd69
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534530"
---
# <a name="access-control"></a>Řízení přístupu

Řízení přístupu v sešitech odkazuje na dvě věci:

* Přístup je vyžadován pro čtení dat v sešitu. Tento přístup se řídí standardními [rolemi Azure](../../role-based-access-control/overview.md) u prostředků použitých v sešitu. Sešity neurčují ani nekonfigurují přístup k těmto prostředkům. Uživatelé obvykle získají tento přístup k těmto prostředkům pomocí role [Čtenář monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) v těchto prostředcích.

* Vyžaduje se přístup k ukládání sešitů.

    - Ukládání privátních `("My")` sešitů nevyžaduje žádná další oprávnění. Všichni uživatelé můžou ukládat soukromé sešity a můžou tyto sešity zobrazit jenom.
    - Ukládání sdílených sešitů vyžaduje oprávnění k zápisu do skupiny prostředků pro uložení sešitu. Tato oprávnění jsou obvykle určena rolí [přispěvatele monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , ale lze je také nastavit prostřednictvím role *přispěvatele pracovní sešity* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standardní role s oprávněními souvisejícími s sešitem

[Čtečka monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) zahrnuje standardní/Read oprávnění, která by používaly nástroje pro monitorování (včetně sešitů) ke čtení dat z prostředků.

[Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) zahrnuje obecná `/write` oprávnění používaná různými nástroji pro monitorování pro ukládání položek (včetně `workbooks/write` oprávnění k ukládání sdílených sešitů).
"Přispěvatelé sešity" přidává do objektu oprávnění "sešity/zápis" do objektu pro ukládání sdílených sešitů.
Uživatelé nemůžou ukládat soukromé sešity, které uvidí jenom, a nemusí žádná zvláštní oprávnění.

Pro vlastní role:

Přidejte `microsoft.insights/workbooks/write` , chcete-li uložit sdílené sešity. Další podrobnosti najdete v tématu role [přispěvatele sešitu](../../role-based-access-control/built-in-roles.md#monitoring-contributor) .

## <a name="next-steps"></a>Další kroky

* [Začínáme](./workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.