---
title: Žádosti o schválení nebo zamítnutí přístupu – Správa nároků Azure AD
description: Naučte se, jak pomocí portálu pro přístup schvalovat nebo odmítat žádosti na balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfbd5e6f190e0600c31aa17e9b1a75f08e213df4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537658"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Schválení nebo zamítnutí žádostí o přístup v Azure AD – Správa nároků

Díky správě nároků ve službě Azure AD můžete nakonfigurovat zásady, které vyžadují schválení balíčků přístupu, a vybrat jednoho nebo více schvalovatelů. Tento článek popisuje, jak můžou určení schvalovatelé schvalovat nebo odmítat žádosti o přístup k balíčkům.

## <a name="open-request"></a>Otevřít požadavek

Prvním krokem ke schválení nebo zamítnutí žádostí o přístup je vyhledání a otevření žádosti o přístup, která čeká na schválení. Existují dva způsoby, jak otevřít žádost o přístup.

**Požadovaná role:** Uživatelem

1. Vyhledejte e-mail od Microsoft Azure, který vás vyzve ke schválení nebo zamítnutí žádosti. Tady je příklad e-mailu:

    ![Schválit žádost o přístup k e-mailu balíčku](./media/entitlement-management-shared/approver-request-email.png)

1. Kliknutím na odkaz **schválit nebo odmítnout** otevřete žádost o přístup.

1. Přihlaste se k portálu přístupu.

Pokud nemáte e-mail, můžete podle následujících kroků najít žádosti o přístup, které čekají na schválení.

1. Přihlaste se na portál My Access na adrese [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (Pro státní správu USA bude doména na portálu pro správu přístupu `myaccess.microsoft.us` .)

1. V nabídce vlevo klikněte na **schválení** . zobrazí se seznam žádostí o přístup, které čekají na schválení.

1. Na kartě **čeká na vyřízení** žádost.

## <a name="view-requestors-answers-to-questions-preview"></a>Zobrazit odpovědi žadatele na otázky (Preview)

1. V části přístup přejděte na kartu **schválení** .

1. Přejděte na žádost, kterou chcete schválit, a klikněte na **Podrobnosti**. Pokud jste připraveni udělat rozhodnutí, můžete kliknout na **schválit** nebo **odmítnout** .

1. Klikněte na **Podrobnosti žádosti**.

     ![Můj portál přístupu – žádost o přístup – klikněte na Podrobnosti žádosti.](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. Informace poskytnuté žadatelem budou v dolní části panelu.

     ![Snímek obrazovky se zobrazenými podrobnostmi o žádosti](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Na základě informací poskytnutých žadatelem můžete žádost schválit nebo zamítnout. Pokyny najdete v postupu schválení nebo zamítnutí žádosti.

## <a name="approve-or-deny-request"></a>Schválit nebo zamítnout žádost

Po otevření žádosti o přístup se zobrazí podrobnosti, které vám pomohou učinit rozhodnutí o schválení nebo zamítnutí.

**Požadovaná role:** Uživatelem

1. Kliknutím na odkaz **Zobrazit** otevřete podokno žádost o přístup.

1. Kliknutím na **Podrobnosti** zobrazíte podrobnosti o žádosti o přístup.

    Podrobnosti zahrnují jméno uživatele, organizaci, počáteční a koncové datum přístupu, pokud jsou k dispozici, obchodní odůvodnění, čas odeslání žádosti a čas vypršení platnosti žádosti.

1. Klikněte na **schválit** nebo **Odepřít**.

1. V případě potřeby zadejte důvod.

    ![Snímek obrazovky ukazuje stránku, na kterou přijímáte nebo odepřete žádost.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Kliknutím na **Odeslat** odešlete rozhodnutí.

    Pokud je zásada nakonfigurovaná s více schvalovateli, je potřeba, abyste měli rozhodnutí o nedokončeném schválení jenom jeden schvalovatel. Poté, co schvalovatel odešle své rozhodnutí na žádost o přístup, je žádost dokončena a již není k dispozici ostatním schvalovatelům ke schválení nebo zamítnutí žádosti. Ostatní schvalovatelé můžou na svém portálu pro přístup zobrazit rozhodnutí o žádosti a tvůrce rozhodnutí. V tuto chvíli je podporována pouze schválení s jednou fází.

    Pokud žádný z konfigurovaných schvalovatelů nemůže schválit nebo zamítnout žádost o přístup, platnost žádosti vyprší po nakonfigurované době trvání žádosti. Uživateli se zobrazí oznámení o vypršení platnosti žádosti o přístup a o tom, že musí žádost o přístup znovu odeslat.

## <a name="next-steps"></a>Další kroky

- [Vyžádat přístup k balíčku přístupu](entitlement-management-request-access.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
