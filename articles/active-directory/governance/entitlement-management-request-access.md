---
title: Požádat o přístup k balíčku přístup ve službě Azure AD oprávnění management (Preview) – Azure Active Directory
description: Další informace o použití portálu Můj přístup požádáte o přístup k balíčku přístup ve službě Azure Active Directory management oprávnění (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541552"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Požádat o přístup k balíčku přístup ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Přihlaste se k portálu Moje aplikace Access

Prvním krokem je pro přihlášení k portálu tento přístup, kde můžete požádat o přístup k balíčku přístup.

**Požadované role:** Žadatel

1. Hledejte e-mailu nebo zprávu ze správce projektu nebo firmy, které pracujete. E-mailu by měla obsahovat odkaz pro přístup k balíčku, který bude potřebovat přístup k. Bude začínat na odkaz:

    `https://myaccess.microsoft.com`

1. Otevřete odkaz.

1. Přihlaste se k portálu Můj přístup.

    Ujistěte se, že pomocí účtu organizace. Pokud jste si jistí, obraťte se projekt nebo obchodní správce.

## <a name="request-an-access-package"></a>Žádost přístup k balíčku

Jakmile najdete balíček přístupu na portálu Moje přístup, může odeslat žádost.

**Požadované role:** Žadatel

1. Kliknutím na značku zaškrtnutí k vybrání balíčku přístup.

    ![Moje portál přístup – přístup k balíčkům](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klikněte na tlačítko **žádat o přístup** a otevřete tak podokno žádost o přístup.

1. Pokud **obchodní odůvodnění** pole se zobrazí, zadejte odůvodnění, by potřebovali mít přístup.

1. Pokud **vyžádat pro konkrétní období?** je povolen, vyberte **Ano** nebo **ne**.

1. V případě potřeby zadejte datum zahájení a koncové datum.

    ![Moje přístup k portálu – žádost o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Až budete hotovi, klikněte na tlačítko **odeslat** svou žádost odešlete.

1. Klikněte na tlačítko **historie žádostí** zobrazíte seznam požadavků a stav.

    Pokud balíček přístup vyžaduje schválení, žádost je nyní ve stavu čekající na schválení.

## <a name="cancel-a-request"></a>Zrušit žádost

Je-li odeslat požadavek na přístup a požadavek je stále v **čekající na schválení** stavu, můžete je zrušit žádost.

**Požadované role:** Žadatel

1. Na portálu Moje přístup na levé straně, klikněte na tlačítko **historie žádostí** zobrazíte seznam požadavků a stav.

1. Klikněte na tlačítko **zobrazení** odkaz pro požadavek, chcete akci zrušit.

1. Pokud je stále v požadavku **čekající na schválení** stavu, můžete kliknout na **požadavek na zrušení** pro zrušení požadavku.

    ![Moje přístup k portálu – požadavek na zrušení.](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klikněte na tlačítko **historie žádostí** potvrďte požadavek byl zrušen.

## <a name="select-a-policy"></a>Výběr zásady

Pokud se požaduje přístup k balíčku přístup, který má více zásad, které se vztahují, může být vyzváni k výběru zásady. Například může správce přístupu k balíčku nakonfigurovat balíček přístup s dvě zásady pro dvě skupiny interní zaměstnanci. První zásada může povolit přístup po dobu 60 dnů a vyžadují schválení. Druhá zásada může povolit přístup pro 2 dny a nevyžaduje schválení. Pokud narazíte na tento scénář, je nutné vybrat zásady, kterou chcete použít.

**Požadované role:** Žadatel

## <a name="next-steps"></a>Další postup

- [Schválit nebo odmítnout žádosti o přístup](entitlement-management-request-approve.md)
- [Požádat o procesu a e-mailových oznámení](entitlement-management-process.md)
