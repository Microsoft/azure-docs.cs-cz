---
title: Správa administrativních jednotek (Preview) – Azure Active Directory | Microsoft Docs
description: Použití jednotek pro správu k podrobnějšímu delegování oprávnění v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336923"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Správa administrativních jednotek v Azure Active Directory (Preview)

Tento článek popisuje jednotky pro správu v Azure Active Directory (Azure AD). Jednotka pro správu je prostředek služby Azure AD, který může být kontejnerem pro jiné prostředky Azure AD. V této verzi Preview můžou být tyto prostředky jenom uživatelé. Správce účtu uživatele s rozsahem správy může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze ve své jednotce pro správu.

Jednotky pro správu můžete použít k delegování oprávnění správce pro podmnožiny uživatelů a používání zásad pro podmnožinu uživatelů. Jednotky pro správu můžete použít k delegování oprávnění pro místní správce nebo k nastavení zásad na podrobné úrovni.

## <a name="deployment-scenario"></a>Scénář nasazení

Jednotky pro správu mohou být užitečné v organizacích s nezávislými divizemi. Vezměte v úvahu příklad velké školy, který se skládá z mnoha autonomních škol (školy firmy, školy a tak dále), že každý z nich má své vlastní správce IT, kteří řídí přístup, spravují uživatele a nastavují zásady pro své školy. Centrální správce může vytvořit jednotku pro správu pro školní firmu a naplnit ji jenom studentům a zaměstnancům společnosti Business School. Potom může centrální správce přidat pracovníky IT v podniku do vymezené role, která uděluje oprávnění správce jenom pro uživatele Azure AD v jednotce pro správu Business School.

## <a name="license-requirements"></a>Licenční požadavky

Chcete-li použít jednotky pro správu, vyžaduje licenci Azure Active Directory Premium pro každého správce jednotky pro správu. Další informace najdete v tématu [Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Správa jednotek pro správu

V této verzi Preview je jediným způsobem, jak můžete vytvářet a spravovat jednotky pro správu, použití modulu Azure Active Directory pro rutiny prostředí Windows PowerShell, jak je popsáno v tématu [práce s jednotkami pro správu](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) .

Další informace o požadavcích na software a o instalaci modulu Azure AD a referenční informace o rutinách modulu Azure AD pro správu jednotek pro správu, včetně syntaxe, popisů parametrů a příkladů, najdete v tématu [Azure Active. PowerShellový adresář](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

## <a name="next-steps"></a>Další kroky

[Edice Azure Active Directory](../fundamentals/active-directory-whatis.md)
