---
title: Předdefinované role – virtuální plocha Windows – Azure
description: Přehled předdefinovaných rolí pro virtuální plochu Windows k dispozici pro Azure RBAC.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a1094004bc523a59c3fcf7eb77c6f11bc4554080
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445852"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Předdefinované role pro virtuální počítače s Windows

Virtuální plocha Windows používá řízení přístupu na základě role (RBAC) v Azure k přiřazení rolí uživatelům a správcům. Tyto role poskytují správcům oprávnění k provádění určitých úloh. Další informace o předdefinovaných rolích pro službu Azure RBAC najdete v tématu [předdefinované role Azure](../role-based-access-control/built-in-roles.md).

Standardní předdefinované role pro Azure jsou vlastník, přispěvatel a čtenář. Virtuální počítač s Windows ale má další role, které vám umožňují oddělit role správy pro fondy hostitelů, skupiny aplikací a pracovní prostory. Toto oddělení vám umožní podrobnější kontrolu nad úlohami správy. Tyto role jsou pojmenovány v souladu se standardními rolemi Azure a metodologií s nejnižšími oprávněními.

Virtuální počítač s Windows nemá konkrétní roli vlastníka. Pro objekty služby však můžete použít standardní roli vlastníka.

## <a name="desktop-virtualization-contributor"></a>Přispěvatel virtualizace plochy

Role Přispěvatel virtualizace plochy umožňuje spravovat všechny aspekty nasazení. Neuděluje vám ale přístup k výpočetním prostředkům. K publikování skupin aplikací pro uživatele nebo skupiny uživatelů budete také potřebovat roli správce přístupu uživatelů.


- Microsoft. DesktopVirtualization/\* 
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="desktop-virtualization-reader"></a>Čtečka virtualizace plochy

Role čtenář virtualizace plochy vám umožní zobrazit vše v nasazení, ale neumožní vám provádět žádné změny.

- Microsoft. DesktopVirtualization/ \* /Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/nasazení/čtení
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-contributor"></a>Přispěvatel fondu hostitelů

Role Přispěvatel fondu hostitelů umožňuje spravovat všechny aspekty fondů hostitelů, včetně přístupu k prostředkům. K vytvoření virtuálních počítačů budete potřebovat roli dalšího přispěvatele, přispěvatel virtuálních počítačů. K vytvoření fondu hostitelů na portálu budete potřebovat role přispěvatele AppGroup a pracovní prostor, nebo můžete použít roli Přispěvatel virtualizace plochy.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="host-pool-reader"></a>Čtečka fondu hostitelů

Role čtečky fondu hostitelů umožňuje zobrazit vše ve fondu hostitelů, ale neumožní vám provádět žádné změny.

- Microsoft. DesktopVirtualization/hostpools/ \* /Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/nasazení/čtení
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-contributor"></a>Přispěvatel skupiny aplikací

Role Přispěvatel skupiny aplikací umožňuje spravovat všechny aspekty skupin aplikací. Pokud chcete publikovat skupiny aplikací pro uživatele nebo skupiny uživatelů, budete potřebovat roli správce přístupu uživatelů.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="application-group-reader"></a>Čtečka skupin aplikací

Role čtenář skupiny aplikací umožňuje zobrazit vše ve skupině aplikací a nebude moci provádět žádné změny.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/nasazení/čtení
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-contributor"></a>Přispěvatel pracovního prostoru

Role Přispěvatel v pracovním prostoru umožňuje spravovat všechny aspekty pracovních prostorů. Pokud chcete získat informace o aplikacích přidaných do skupin aplikací, budete také muset přiřadit roli Čtenář skupiny aplikací.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/pracovní prostory/\*
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/Deployments/\*
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="workspace-reader"></a>Čtenář pracovního prostoru

Role čtenáře pracovního prostoru umožňuje zobrazit vše v pracovním prostoru, ale neumožní vám provádět žádné změny.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/pracovní prostory/číst
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/nasazení/čtení
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="user-session-operator"></a>Operátor uživatelské relace

Role operátora uživatelské relace umožňuje odeslat zprávy, odpojit relace a použít funkci odhlásit se k podepisování relací mimo hostitele relace. Tato role ale neumožňuje provádět správu hostitele relace, jako je odebrání hostitele relace, změna režimu vyprazdňování a tak dále. Tato role uvidí přiřazení, ale nemůže měnit správce. Tuto roli doporučujeme přiřadit ke konkrétním fondům hostitelů. Pokud toto oprávnění udělíte na úrovni skupiny prostředků, bude mít správce oprávnění ke čtení všech fondů hostitelů v rámci skupiny prostředků.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/nasazení/čtení
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*

## <a name="session-host-operator"></a>Operátor hostitele relace

Role operátor hostitele relace umožňuje zobrazit a odebrat hostitele relací a také změnit režim vyprázdnění. Nemůžou přidávat hostitele relací pomocí Azure Portal, protože nemají oprávnění k zápisu pro objekty fondu hostitelů. Pokud je registrační token platný (vygenerovaný a neprošlý), můžete tuto roli použít k přidání hostitelů relací do fondu hostitelů mimo Azure Portal, pokud má správce výpočetní oprávnění prostřednictvím role Přispěvatel virtuálních počítačů.

Následující seznam popisuje oprávnění, ke kterým má tato role přístup:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft. Resources/Subscriptions/resourceGroups/Read
- Microsoft. Resources/nasazení/čtení
- Microsoft. Authorization/ \* /Read
- Microsoft. Insights/alertRules/\*
- Microsoft. support/\*
