---
title: Jednotky pro správu správy ve verzi preview ve službě Azure Active Directory
description: Použití jednotek pro správu pro podrobnější delegování oprávnění ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.component: users-groups-roles
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 0884726e59d9ab3f5a5cfe7bb0608f6b5a5da250
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449782"
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Správa administrativních jednotek v Azure AD – ve verzi public preview
Tento článek popisuje jednotky pro správu – nový kontejner služby Azure Active Directory prostředků, které lze použít pro delegování oprávnění pro správu přes podmnožiny uživatelů a použití zásady na určitou podskupinu uživatelů. Jednotky pro správu ve službě Azure Active Directory, povolte centrální správu delegovat oprávnění na místní správce nebo nastavit zásady na jemné úrovni.

To je užitečné v organizacích s nezávislé oddělení, třeba velký vysoké školy, která se skládá z mnoha autonomní školy (obchodní školy, inženýrství školy a tak dále), které jsou na sobě nezávislé. Takové divize mají své vlastní správce IT, kteří řízení přístupu, správu uživatelů a nastavit zásady speciálně pro jejich dělení. Centrální správci chcete mít možnost udělení těchto oddělení oprávnění správců přes uživatelům v jejich konkrétním oddělení. Přesněji řečeno v tomto příkladu centrální správce můžete, například vytvořit jednotce pro správu pro konkrétní školu (obchodní školní) a přidejte do ní pouze podnikoví uživatelé school. Jinými slovy, pak centrální správce můžete přidat obchodní školní pracovníci IT k roli s vymezeným oborem udělte pracovníků IT oprávnění správce školy firmy jenom přes organizační jednotky správce školy.

> [!IMPORTANT]
> Použití jednotek pro správu vyžaduje, aby správce s oborem jednotky pro správu mít licenci Azure Active Directory Premium a Azure Active Directory Basic licence pro všechny uživatele v jednotky pro správu. Další informace najdete v tématu [Začínáme se službou Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


Z centrální správce jednotce pro správu je objekt adresáře, který může být vytvořena a naplněna prostředky. **V této verzi preview těmito prostředky můžou být jenom uživatelé.** Po vytvoření a vyplnění jednotky pro správu může sloužit jako obor omezit udělená oprávnění pouze za prostředky obsažené v jednotky pro správu.

## <a name="managing-administrative-units"></a>Správa jednotek pro správu
V této verzi preview můžete vytvořit a spravovat pomocí rutin modulu Azure Active Directory pro Windows PowerShell jednotky pro správu. Další informace o tom, jak to udělat, najdete v článku [práce s jednotkami pro správu](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Další informace o požadavcích na software a instalace modulu Azure AD a informace o rutiny modulu Azure AD pro správu jednotky pro správu, včetně syntaxe, popisy parametrů a příklady naleznete v tématu [Azure Active Adresář Powershellu](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Další postup
[Edice Azure Active Directory](../fundamentals/active-directory-whatis.md)
