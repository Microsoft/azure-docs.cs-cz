---
title: Změna nastavení životního cyklu pro balíček přístupu ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak změnit nastavení životního cyklu pro balíček přístupu ve správě nároků služby Azure Active Directory.
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
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261980"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Změna nastavení životního cyklu pro balíček přístupu ve správě nároků Azure AD

Jako správce přístupových balíčků můžete kdykoli změnit nastavení životního cyklu pro přístupový balíček úpravou existující zásady. Pokud změníte datum vypršení platnosti zásady, datum vypršení platnosti pro požadavky, které jsou již v čekajícím schválení nebo schváleném stavu, se nezmění.

Tento článek popisuje, jak změnit nastavení životního cyklu pro existující balíček přístupu.

## <a name="open-lifecycle-settings"></a>Otevření nastavení životního cyklu

Chcete-li změnit nastavení životního cyklu pro přístupový balíček, musíte otevřít odpovídající zásady. Podle těchto kroků otevřete nastavení životního cyklu pro přístupový balíček.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Klikněte na **Zásady** a potom klikněte na zásadu, která má nastavení životního cyklu, které chcete upravit.

    Otevře se podokno podrobností zásad v dolní části stránky.

    ![Balíček aplikace Access – podokno podrobností o zásadách](./media/entitlement-management-shared/policy-details.png)

1. Kliknutím na **Upravit** upravte zásadu.

    ![Přístupový balíček – zásady úprav](./media/entitlement-management-shared/policy-edit.png)

1. Kliknutím na kartu **Životní cyklus** otevřete nastavení životního cyklu.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Další kroky

- [Změna nastavení žádosti a schválení pro přístupový balíček](entitlement-management-access-package-request-policy.md)