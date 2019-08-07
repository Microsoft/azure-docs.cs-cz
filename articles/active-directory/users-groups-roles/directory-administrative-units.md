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
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736785"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Správa administrativních jednotek v Azure Active Directory (Public Preview)

Tento článek popisuje jednotky pro správu v Azure Active Directory (Azure AD), což je kontejner prostředků, které se dají použít k delegování oprávnění správce pro podmnožiny uživatelů a používání zásad pro podmnožinu uživatelů. Ve službě Azure AD umožňují centrálním správcům delegovat oprávnění pro místní správce nebo nastavit zásady na podrobné úrovni.

To je užitečné v organizacích s nezávislými divizemi, například na velkou vysokou univerzita, která se skládá z mnoha autonomních škol (obchodní škola, inženýrské školy atd.), které jsou vzájemně nezávislé. Tyto divize mají vlastní správce IT, kteří řídí přístup, spravují uživatele a nastavují zásady konkrétně pro jejich oddělení. Centrální správci chtějí dát těmto správcům oprávnění k těmto uživatelům v jejich konkrétní divizi. Konkrétně pomocí tohoto příkladu může centrální správce vytvořit například jednotku pro správu pro konkrétní školu (obchodní školu) a naplnit ji jenom s uživateli Business School. Potom může centrální správce přidat pracovníky IT v podniku do vymezené role, jinými slovy, udělit pracovníkům oddělení IT práva pro správu firemní školy jenom přes správní jednotku Business School.

> [!IMPORTANT]
> Pokud chcete použít jednotky pro správu, musí mít správce administrativní jednotky licenci Azure Active Directory Premium. Další informace najdete v tématu [Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>

Z pohledu centrálního správce je jednotka pro správu objekt adresáře, který lze vytvořit a naplnit pomocí prostředků. **V této verzi Preview můžou být tyto prostředky jenom uživatelé.** Po vytvoření a naplnění se dá jednotka pro správu použít jako obor k omezení uděleného oprávnění jenom na prostředky obsažené v jednotce pro správu.

## <a name="managing-administrative-units"></a>Správa jednotek pro správu

V této verzi Preview můžete vytvářet a spravovat jednotky pro správu pomocí modulu Azure Active Directory pro rutiny prostředí Windows PowerShell. Další informace o tom, jak to udělat, najdete v tématu [práce s jednotkami pro správu](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) .

Další informace o požadavcích na software a o instalaci modulu Azure AD a informace o rutinách modulu Azure AD pro správu jednotek pro správu, včetně syntaxe, popisů parametrů a příkladů, najdete v tématu [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Další postup

[Edice Azure Active Directory](../fundamentals/active-directory-whatis.md)
