---
title: Náhled správy administrativních jednotek v Azure Active Directory
description: Pomocí administrativních jednotek pro podrobnější delegování oprávnění v Azure Active Directory
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
ms.openlocfilehash: 373983413ea83e5924b52fbbcbc666aa1a188d80
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Správa administrativních jednotek ve službě Azure AD - verzi public preview
Tento článek popisuje administrativních jednotek – nový kontejner služby Active Directory Azure prostředků, které lze použít pro delegování oprávnění ke správě přes podmnožiny uživatelů a použití zásady na určitou podskupinu uživatelů. Administrativních jednotek v Azure Active Directory, povolte centrální správcům delegovat oprávnění pro místní správce nebo nastavení zásad na nejnižší úrovni.

To je užitečné v organizacích s nezávislé rozdělení, například velké školy, která se skládá z mnoha autonomního škol (Business školy, školní inženýrství atd.), které jsou na sobě nezávislé. Takové rozdělení mají své vlastní správce IT, kteří řízení přístupu, Správa uživatelů a nastavit zásady speciálně pro jejich dělení. Centrální správci chcete mít možnost udělit tyto oddělení oprávnění správci přes uživatele v jejich konkrétní oddělení. Přesněji řečeno v tomto příkladu, centrální správce můžete, například vytvořit správce jednotku pro konkrétní školní (Business školní) a jeho naplnění pouze podnikoví uživatelé školní. Jinými slovy, pak centrální správce můžete přidat školní obchodní pracovníky IT k roli vymezená udělte pracovníků IT firmy školní administrativní oprávnění jen přes školní správní organizační jednotky.

> [!IMPORTANT]
> Použití administrativních jednotek vyžaduje správce s oborem správy jednotky mít licenci Azure Active Directory Premium a Azure Active Directory Basic licencí pro všechny uživatele v jednotce pro správu. Další informace najdete v tématu [Začínáme s Azure AD Premium](active-directory-get-started-premium.md).
>


Z centrální správce administrativní jednotky je objekt adresáře, který může být vytvořeny a naplněny s prostředky. **V této verzi preview tyto prostředky mohou být pouze uživatelé.** Po vytvořeny a naplněny, lze jako obor pro správu jednotky omezit udělené oprávnění jen přes prostředky obsažené v administrativní jednotky.

## <a name="managing-administrative-units"></a>Správa administrativních jednotek
V této verzi preview můžete vytvořit a spravovat administrativních jednotek pomocí Azure Active Directory modulu pro rutiny prostředí Windows PowerShell. Další informace o tom, jak to udělat, najdete v části [práce s administrativních jednotek](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Další informace o požadavcích na software a instalace modulu Azure AD a informace o rutiny modulu Azure AD pro správu administrativních jednotek, včetně syntaxe, popisy parametrů a příklady naleznete v tématu [Azure Active Prostředí PowerShell Directory](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Další postup
[Edice služby Azure Active Directory](active-directory-editions.md)
