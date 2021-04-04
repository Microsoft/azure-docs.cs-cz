---
title: Změna nastavení životního cyklu pro balíček přístupu ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak změnit informace o žadateli & nastavení životního cyklu pro balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980122"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Změna nastavení životního cyklu pro balíček přístupu v Azure AD – Správa nároků

Jako správce balíčků přístupu můžete kdykoli změnit nastavení životního cyklu pro balíček přístupu úpravou stávající zásady. Pokud změníte datum vypršení platnosti zásady, datum vypršení platnosti požadavků, které jsou již v nedokončeném schválení nebo schváleném stavu, se nezmění.

Tento článek popisuje, jak změnit nastavení životního cyklu pro existující balíček přístupu.

## <a name="open-requestor-information"></a>Otevřít informace o žadateli
Pokud chcete zajistit, aby uživatelé měli správný přístup k balíčku přístupu, můžete si nakonfigurovat vlastní otázky, které požádají uživatele požadující přístup k určitým balíčkům přístupu. Mezi možnosti konfigurace patří: lokalizace, povinné/volitelné, a text/více formátů odpovědí volby. Žadatelům se zobrazí otázky, které požadují balíček, a odpovědi na otázky, aby jim pomohli své rozhodnutí. Pomocí následujících kroků můžete nakonfigurovat otázky v přístupovém balíčku:

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