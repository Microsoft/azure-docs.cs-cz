---
title: Změna nastavení životního cyklu pro balíček přístupu ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Přečtěte si, jak změnit nastavení životního cyklu pro balíček přístupu v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392342"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Změna nastavení životního cyklu pro balíček přístupu ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jako správce balíčků přístupu můžete kdykoli změnit nastavení životního cyklu pro balíček přístupu úpravou stávající zásady. Pokud změníte datum vypršení platnosti zásady, datum vypršení platnosti požadavků, které jsou již v nedokončeném schválení nebo schváleném stavu, se nezmění.

Tento článek popisuje, jak změnit nastavení životního cyklu pro existující balíček přístupu.

## <a name="open-lifecycle-settings"></a>Otevřít nastavení životního cyklu

Chcete-li změnit nastavení životního cyklu pro balíček přístupu, je nutné otevřít odpovídající zásadu. Pomocí těchto kroků otevřete nastavení životního cyklu pro balíček přístupu.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom klikněte na zásadu, která má nastavení životního cyklu, které chcete upravit.

    V dolní části stránky se otevře podokno Podrobnosti o zásadách.

    ![Přístupový balíček – podokno podrobností zásad](./media/entitlement-management-shared/policy-details.png)

1. Zásadu upravíte kliknutím na **Upravit** .

    ![Přístup k balíčku – upravit zásadu](./media/entitlement-management-shared/policy-edit.png)

1. Kliknutím na kartu **životní cyklus** otevřete nastavení životního cyklu.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Další kroky

- [Nastavení žádosti o změnu a schválení pro balíček pro přístup](entitlement-management-access-package-request-policy.md)