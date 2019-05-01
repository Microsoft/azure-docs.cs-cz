---
title: Schválit nebo odmítnout žádosti o přístup ve službě Azure AD oprávnění management (Preview) – Azure Active Directory
description: Další informace o použití portálu Můj přístup ke schvalování nebo zamítání žádostí pro balíček přístup ve službě Azure Active Directory management oprávnění (Preview).
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541522"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Schválit nebo odmítnout žádosti o přístup ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Správa oprávnění Azure AD můžete nakonfigurovat zásady, které vyžadují schválení pro přístup k balíčkům a zvolte jednu nebo více schvalovatelů. Tento článek popisuje, jak určených schvalovatelů mohli schválit nebo odmítnout žádosti o přístup k balíčkům.

## <a name="open-request"></a>Otevřít požadavek

Prvním krokem pro schválení nebo zamítnutí žádosti o přístup je najít a otevřít žádost o přístup čeká na schválení. Existují dva způsoby, jak otevřít žádost o přístup.

**Požadované role:** Schvalovatel

1. Vyhledejte e-mailu z Microsoft Azure, která vás vyzve k schválit nebo zamítnout žádost. Tady je příklad e-mailu:

    ![Schválit žádost o přístup k e-mailu balíčku](./media/entitlement-management-shared/email-approve-request.png)

1. Klikněte na tlačítko **schválit nebo zamítnout žádost o** odkaz k otevření žádosti o přístup.

1. Přihlaste se k portálu Můj přístup.

Pokud nemáte k dispozici v e-mailu, najdete pomocí následujících kroků žádosti o přístup čeká na vaše schválení.

1. Přihlaste se k portálu Můj přístup na adrese [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. V nabídce vlevo klikněte na tlačítko **schválení** chcete zobrazit seznam žádostí o přístup čeká na schválení.

1. Na **čekající** kartu, najít žádost.

## <a name="approve-or-deny-request"></a>Schválit nebo zamítnout žádost

Po otevření žádosti o přístup čeká na schválení, zobrazí se podrobnosti, které vám pomohou Ujistěte se, schválit nebo zamítnout rozhodnutí.

**Požadované role:** Schvalovatel

1. Klikněte na tlačítko **zobrazení** odkaz a otevřete tak podokno žádost o přístup.

1. Klikněte na tlačítko **podrobnosti** zobrazíte podrobnosti o žádosti o přístup.

    Podrobnosti zahrnují uživatelské jméno, organizaci, přístup k počáteční a koncové datum-li zadán, obchodní odůvodnění, kdy byl požadavek odeslán a kdy skončí platnost této žádosti.

1. Klikněte na tlačítko **schválit** nebo **Odepřít**.

1. V případě potřeby zadejte důvod.

    ![Moje přístup k portálu – žádost o přístup](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klikněte na tlačítko **odeslat** odeslat svoje rozhodnutí.

    Pokud zásada je konfigurována s několika schvalovateli, je potřeba jenom jeden schvalovatel rozhodnutí o čekajících na schválení. Po odeslání na žádost o přístup svého rozhodnutí schvalovatele žádosti se dokončí a již není k dispozici pro další schvalovatele o schválení nebo zamítnutí žádosti. Další schvalovatele můžete zobrazit žádost o rozhodnutí a rozhodovací pravomocí na portálu Moje přístup. V současné době se podporuje jenom jedné fáze schválení.

    Pokud žádné nakonfigurované schvalovatel schválí nebo zamítne žádost o přístup, žádost vyprší po doba trvání žádosti nakonfigurované. Uživatel získá informován, že vypršela platnost jejich žádost o přístup a je nutné znovu odeslat žádost o přístup.

## <a name="next-steps"></a>Další postup

- [Žádost o přístup přístupu k balíčku](entitlement-management-request-access.md)
- [Požádat o procesu a e-mailových oznámení](entitlement-management-process.md)
