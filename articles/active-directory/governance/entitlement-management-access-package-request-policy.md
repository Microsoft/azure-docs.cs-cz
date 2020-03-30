---
title: Změna nastavení žádosti a schválení pro balíček přístupu ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak změnit nastavení žádosti a schválení pro balíček přístupu ve správě nároků služby Azure Active Directory.
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
ms.openlocfilehash: 493ba6396a7ceb11b917fbda5dd6c37c070f2fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261928"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Změna nastavení žádosti a schválení pro balíček přístupu ve správě oprávnění Azure AD

Jako správce přístupových balíčků můžete kdykoli změnit uživatele, kteří mohou požádat o přístupový balíček, úpravou zásady nebo přidáním nové zásady. Můžete také změnit nastavení schválení.

Tento článek popisuje, jak změnit nastavení požadavku a schválení pro existující balíček přístupu.

## <a name="choose-between-one-or-multiple-polices"></a>Vyberte si mezi jednou nebo více funkcemi

Způsob, jakým určíte, kdo může požádat o přístupový balíček, je se zásadami. Při vytváření balíčku přístupu zadáte nastavení požadavku a schválení, které vytvoří zásadu. Většina přístupových balíčků bude mít jednu zásadu, ale jeden balíček přístupu může mít více zásad. Pokud chcete povolit přiřazení různých skupin uživatelů s různým nastavením požadavku a schválení, vytvořte pro přístupový balíček více zásad. Například jednu zásadu nelze použít k přiřazení interních a externích uživatelů ke stejnému balíčku přístupu. Můžete však vytvořit dvě zásady ve stejném balíčku přístupu – jeden pro interní uživatele a jeden pro externí uživatele. Pokud existuje více zásad, které se vztahují na uživatele, budou vyzváni v době jejich žádosti vybrat zásady, které chcete být přiřazeny. Následující diagram znázorňuje balíček přístupu se dvěma zásadami.

![Více zásad v balíčku přístupu](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scénář | Počet zásad |
| --- | --- |
| Chci, aby všichni uživatelé v mém adresáři měli stejné nastavení požadavků a schválení pro přístupový balíček | Jeden |
| Chci, aby všichni uživatelé v některých připojených organizacích mohli požádat o přístupový balíček | Jeden |
| Chci umožnit uživatelům v mém adresáři a také uživatelům mimo můj adresář požádat o přístupový balíček | Několik |
| Chci pro některé uživatele zadat různá nastavení schválení | Několik |
| Chci, aby platnost přiřazení balíčků pro některé uživatele vypršela, zatímco ostatní uživatelé mohou rozšířit svůj přístup | Několik |

Informace o logice priority, která se používá při použití více zásad, naleznete [v tématu Více zásad](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Otevření existující zásady nastavení požadavku a schválení

Chcete-li změnit nastavení požadavku a schválení pro přístupový balíček, musíte otevřít odpovídající zásady. Chcete-li otevřít nastavení požadavku a schválení pro přístupový balíček, postupujte takto.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Klikněte na **Zásady** a potom klikněte na zásady, které chcete upravit.

    Otevře se podokno podrobností zásad v dolní části stránky.

    ![Balíček aplikace Access – podokno podrobností o zásadách](./media/entitlement-management-shared/policy-details.png)

1. Kliknutím na **Upravit** upravte zásadu.

    ![Přístupový balíček – zásady úprav](./media/entitlement-management-shared/policy-edit.png)

1. Kliknutím na kartu **Požadavky** otevřete nastavení požadavku a schválení.

1. Proveďte kroky v jedné z následujících částí požadavku.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Přidání nové zásady nastavení požadavků a schválení

Pokud máte sadu uživatelů, kteří by měli mít jiné nastavení požadavků a schválení, budete pravděpodobně muset vytvořit novou zásadu. Chcete-li začít přidávat nové zásady do existujícího balíčku přístupu, postupujte takto.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Klikněte na **Zásady** a potom **na Přidat zásady**.

1. Zadejte název a popis zásady.

    ![Vytvoření zásady s názvem a popisem](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Kliknutím na **Další** otevřete kartu **Požadavky.**

1. Proveďte kroky v jedné z následujících částí požadavku.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Pokud upravujete zásadu, klepněte na **tlačítko Aktualizovat**. Pokud přidáváte novou zásadu, klepněte na **tlačítko Vytvořit**.

## <a name="next-steps"></a>Další kroky

- [Změna nastavení životního cyklu pro přístupový balíček](entitlement-management-access-package-lifecycle-policy.md)
- [Zobrazit žádosti o přístupový balíček](entitlement-management-access-package-requests.md)