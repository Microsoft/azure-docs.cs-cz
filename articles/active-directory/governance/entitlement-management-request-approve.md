---
title: Schválení nebo zamítnutí žádostí o přístup ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Naučte se používat portál pro přístup ke schválení nebo zamítnutí žádostí o přístup k balíčku přístupu v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78243f02cbbe3d06b94ee52d6193865dbfa27121
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489004"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Schválení nebo zamítnutí žádostí o přístup ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Díky správě nároků ve službě Azure AD můžete nakonfigurovat zásady, které vyžadují schválení balíčků přístupu, a vybrat jednoho nebo více schvalovatelů. Tento článek popisuje, jak můžou určení schvalovatelé schvalovat nebo odmítat žádosti o přístup k balíčkům.

## <a name="open-request"></a>Otevřít požadavek

Prvním krokem ke schválení nebo zamítnutí žádostí o přístup je vyhledání a otevření žádosti o přístup, která čeká na schválení. Existují dva způsoby, jak otevřít žádost o přístup.

**Požadovaná role:** Schvalovatel

1. Vyhledejte e-mail od Microsoft Azure, který vás vyzve ke schválení nebo zamítnutí žádosti. Tady je příklad e-mailu:

    ![Schválit žádost o přístup k e-mailu balíčku](./media/entitlement-management-shared/email-approve-request.png)

1. Kliknutím na odkaz **schválit nebo odmítnout** otevřete žádost o přístup.

1. Přihlaste se k portálu přístupu.

Pokud nemáte e-mail, můžete podle následujících kroků najít žádosti o přístup, které čekají na schválení.

1. Přihlaste se na portál My Access [https://myaccess.microsoft.com](https://myaccess.microsoft.com)na adrese.

1. V nabídce vlevo klikněte na **schválení** . zobrazí se seznam žádostí o přístup, které čekají na schválení.

1. Na kartě **čeká** na vyřízení žádost.

## <a name="approve-or-deny-request"></a>Schválit nebo zamítnout žádost

Po otevření žádosti o přístup se zobrazí podrobnosti, které vám pomohou učinit rozhodnutí o schválení nebo zamítnutí.

**Požadovaná role:** Schvalovatel

1. Kliknutím na odkaz **Zobrazit** otevřete podokno žádost o přístup.

1. Kliknutím na **Podrobnosti** zobrazíte podrobnosti o žádosti o přístup.

    Podrobnosti zahrnují jméno uživatele, organizaci, počáteční a koncové datum přístupu, pokud jsou k dispozici, obchodní odůvodnění, čas odeslání žádosti a čas vypršení platnosti žádosti.

1. Klikněte na **schválit** nebo **Odepřít**.

1. V případě potřeby zadejte důvod.

    ![Můj portál pro přístup – žádost o přístup](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kliknutím na **Odeslat** odešlete rozhodnutí.

    Pokud je zásada nakonfigurovaná s více schvalovateli, je potřeba, abyste měli rozhodnutí o nedokončeném schválení jenom jeden schvalovatel. Poté, co schvalovatel odešle své rozhodnutí na žádost o přístup, je žádost dokončena a již není k dispozici ostatním schvalovatelům ke schválení nebo zamítnutí žádosti. Ostatní schvalovatelé můžou na svém portálu pro přístup zobrazit rozhodnutí o žádosti a tvůrce rozhodnutí. V tuto chvíli je podporována pouze schválení s jednou fází.

    Pokud žádný z konfigurovaných schvalovatelů nemůže schválit nebo zamítnout žádost o přístup, platnost žádosti vyprší po nakonfigurované době trvání žádosti. Uživateli se zobrazí oznámení o vypršení platnosti žádosti o přístup a o tom, že musí žádost o přístup znovu odeslat.

## <a name="next-steps"></a>Další kroky

- [Vyžádat přístup k balíčku přístupu](entitlement-management-request-access.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
