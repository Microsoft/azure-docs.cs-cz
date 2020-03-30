---
title: Správa administrativních jednotek (preview) – Azure AD | Dokumenty společnosti Microsoft
description: Použití jednotek pro správu pro podrobnější delegování oprávnění ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028417"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Správa administrativních jednotek ve službě Azure Active Directory (preview)

Tento článek popisuje jednotky pro správu ve službě Azure Active Directory (Azure AD). Jednotka pro správu je prostředek Azure AD, který může být kontejner pro jiné prostředky Azure AD. V této verzi preview mohou být tyto prostředky pouze uživatelé. Správce uživatelských účtů s rozsahem správy může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze v jejich administrativní jednotce.

Pomocí jednotek pro správu můžete delegovat oprávnění správce nad podmnožiny uživatelů a použít zásady na podmnožinu uživatelů. Pomocí jednotek pro správu můžete delegovat oprávnění na místní správce nebo nastavit zásady na podrobné úrovni.

## <a name="deployment-scenario"></a>Scénář nasazení

Administrativní jednotky mohou být užitečné v organizacích s nezávislými divizemi. Vezměme si příklad velké univerzity, která se skládá z mnoha autonomních škol (School of Business, School of Engineering, a tak dále), že každý má své vlastní IT administrátory, kteří řídí přístup, spravovat uživatele a nastavit zásady pro svou školu. Ústřední správce by mohl vytvořit administrativní jednotku pro obchodní školu a naplnit ji pouze studenty a zaměstnanci obchodní školy. Pak centrální správce můžete přidat obchodní školy IT pracovníky s rozsahem role, která uděluje oprávnění správce pouze pro uživatele Azure AD v jednotce pro správu obchodní školy.

## <a name="license-requirements"></a>Licenční požadavky

Použití jednotek pro správu vyžaduje licenci Azure Active Directory Premium pro každého správce jednotky správce. Další informace najdete [v tématu Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Správa administrativních jednotek

V této verzi preview je jediným způsobem, jak můžete vytvářet a spravovat jednotky pro správu, použití rutin Azure Active Directory Module pro windows powershellové rutiny, jak je popsáno v [části Práce s jednotkami pro správu.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Další informace o požadavcích na software a instalaci modulu Azure AD a odkazy na rutiny modulu Azure AD pro správu jednotek pro správu, včetně syntaxe, popisů parametrů a příkladů, najdete [v tématu Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Další kroky

[Edice Azure Active Directory](../fundamentals/active-directory-whatis.md)
