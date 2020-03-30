---
title: Řízení přístupu k sešitům Azure Monitor
description: Zjednodušení složitých sestav pomocí předem vytvořených a vlastních parametrizovaných sešitů s řízením přístupu na základě rolí
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658417"
---
# <a name="access-control"></a>Řízení přístupu

Řízení přístupu v sešitech odkazuje na dvě věci:

* Ke čtení dat v sešitu je nutný přístup. Tento přístup je řízen [standardnírole Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) na prostředky používané v sešitu. Sešity neurčují ani nekonfigurují přístup k těmto prostředkům. Uživatelé by obvykle získat tento přístup k těmto prostředkům pomocí [role sledování čtečky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) na tyto prostředky.

* K uložení sešitů je nutný přístup

    - Uložení `("My")` soukromých sešitů nevyžaduje žádná další oprávnění. Všichni uživatelé mohou ukládat soukromé sešity a pouze oni mohou tyto sešity zobrazit.
    - Uložení sdílených sešitů vyžaduje k uložení sešitu oprávnění k zápisu do skupiny prostředků. Tato oprávnění jsou obvykle určena rolí [Přispěvatel monitorování,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ale lze je také nastavit prostřednictvím role *Přispěvatel sešitů.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standardní role s oprávněními souvisejícími se sešitem

[Sledování Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) obsahuje standardní /read oprávnění, která by byla použita pomocí nástrojů pro sledování (včetně sešitů) ke čtení dat z prostředků.

[Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) `/write` zahrnuje obecná oprávnění používaná různými nástroji `workbooks/write` pro monitorování pro ukládání položek (včetně oprávnění k ukládání sdílených sešitů).
"Přispěvatel sešitů" přidá k objektu oprávnění "sešity/zápisy" pro uložení sdílených sešitů.
Pro ukládání soukromých sešitů, které mohou zobrazit pouze oni, nejsou vyžadována žádná zvláštní oprávnění.

Pro vlastní řízení přístupu na základě rolí:

Přidáním `microsoft.insights/workbooks/write` uložíte sdílené sešity. Další podrobnosti najdete v roli [Přispěvatel sešitu.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
