---
title: Nastavení žádosti o změnu a schválení pro balíček pro přístup ve správě nároků Azure AD – Azure Active Directory
description: Naučte se, jak změnit nastavení žádostí a schválení pro balíček přístupu v Azure Active Directory správě nároků.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376553"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Nastavení žádosti o změnu a schválení pro balíček přístupu v Azure AD – Správa nároků

Jako správce balíčků přístupu můžete kdykoli změnit uživatele, kteří můžou požádat o přístup k balíčku, úpravou zásady nebo přidáním nové zásady. Můžete také změnit nastavení schvalování.

Tento článek popisuje, jak změnit nastavení požadavku a schválení pro existující balíček přístupu.

## <a name="choose-between-one-or-multiple-polices"></a>Vyberte jednu nebo víc zásad.

Způsob, jakým určíte, kdo může žádat o přístup k balíčku, je zásadou. Když vytvoříte balíček pro přístup, zadáte nastavení žádosti a schválení, které vytvoří zásadu. Většina přístupových balíčků bude mít jednu zásadu, ale jeden balíček přístupu může mít víc zásad. Vytvořili jste několik zásad pro balíček pro přístup, pokud chcete, aby bylo možné udělit různým sadám uživatelů přiřazení s různými nastaveními požadavků a schvalování. Například jedna zásada se nedá použít k přiřazení interních a externích uživatelů ke stejnému balíčku přístupu. V jednom balíčku pro přístup se ale dají vytvořit dvě zásady – jeden pro interní uživatele a jeden pro externí uživatele. Pokud je k dispozici více zásad, které platí pro uživatele, budou v době žádosti vyzváni k výběru zásady, ke kterým se mají přiřadit. Následující diagram znázorňuje balíček přístupu se dvěma zásadami.

![Více zásad v balíčku pro přístup](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Scénář | Počet zásad |
| --- | --- |
| Chci, aby všichni uživatelé v adresáři měli stejné nastavení požadavků a schvalování pro balíček pro přístup | Hodinu |
| Chci, aby všichni uživatelé v některých připojených organizacích mohli žádat o přístup k balíčku | Hodinu |
| Chci uživatelům v adresáři a také uživatelům, kteří jsou mimo můj adresář, dovolit přístup k balíčku pro přístup | Několik |
| Chci pro některé uživatele zadat jiná nastavení schvalování | Několik |
| Chci, aby někteří uživatelé měli přístup k vypršení platnosti přiřazení balíčků, zatímco ostatní uživatelé můžou svůj přístup rozšíří. | Několik |

Informace o Logic prioritě, která se používá při použití více zásad, najdete v tématu [více zásad](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Otevře existující zásadu nastavení požadavků a schválení.

Chcete-li změnit nastavení žádosti a schválení pro balíček pro přístup, je nutné otevřít odpovídající zásadu. Pomocí těchto kroků otevřete nastavení žádosti a schválení pro balíček přístupu.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom klikněte na zásadu, kterou chcete upravit.

    V dolní části stránky se otevře podokno Podrobnosti o zásadách.

    ![Přístupový balíček – podokno podrobností zásad](./media/entitlement-management-shared/policy-details.png)

1. Zásadu upravíte kliknutím na **Upravit** .

    ![Přístup k balíčku – upravit zásadu](./media/entitlement-management-shared/policy-edit.png)

1. Kliknutím na kartu **žádosti** otevřete nastavení žádosti a schválení.

1. Proveďte kroky v jedné z následujících částí žádosti.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Přidat nové zásady nastavení požadavků a schválení

Pokud máte sadu uživatelů, kteří by měli mít různá nastavení požadavků a schvalování, budete pravděpodobně muset vytvořit novou zásadu. Pomocí těchto kroků můžete začít přidávat nové zásady do stávajícího balíčku přístupu.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom na **Přidat zásadu**.

1. Zadejte název a popis zásady.

    ![Vytvořit zásadu s názvem a popisem](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Kliknutím na **Další** otevřete kartu **žádosti** .

1. Proveďte kroky v jedné z následujících částí žádosti.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Pokud upravujete zásadu, klikněte na **aktualizovat**. Pokud přidáváte novou zásadu, klikněte na **vytvořit**.

## <a name="next-steps"></a>Další kroky

- [Změnit nastavení životního cyklu pro balíček pro přístup](entitlement-management-access-package-lifecycle-policy.md)
- [Zobrazit žádosti pro balíček pro přístup](entitlement-management-access-package-requests.md)