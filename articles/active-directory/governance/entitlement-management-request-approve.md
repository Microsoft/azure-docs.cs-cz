---
title: Schválení nebo zamítnutí žádostí o přístup – správa oprávnění Azure AD
description: Přečtěte si, jak pomocí portálu Můj přístup schvalovat nebo zamítat žádosti o přístupový balíček ve správě nároků služby Azure Active Directory.
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
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261720"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Schválení nebo odepření žádostí o přístup ve správě oprávnění Azure AD

Pomocí správy nároků Azure AD můžete nakonfigurovat zásady, které vyžadují schválení pro přístupové balíčky, a zvolit jednoho nebo více schvalovatelů. Tento článek popisuje, jak mohou určení schvalovatelé schvalovat nebo zamítat žádosti o přístupové balíčky.

## <a name="open-request"></a>Otevřít požadavek

Prvním krokem ke schválení nebo zamítnutí žádostí o přístup je vyhledání a otevření žádosti o přístup čekající na schválení. Existují dva způsoby, jak otevřít žádost o přístup.

**Předpokladová role:** Schvalovatel

1. Vyhledejte e-mail z Microsoft Azure, který vás požádá o schválení nebo zamítnutí žádosti. Zde je příklad e-mailu:

    ![Schválit žádost o přístup k e-mailu s balíčkem](./media/entitlement-management-shared/approver-request-email.png)

1. Kliknutím na odkaz **Schválit nebo odepřít požadavek** otevřete žádost o přístup.

1. Přihlaste se k portálu Můj přístup.

Pokud e-mail nemáte, můžete pomocí těchto kroků najít žádosti o přístup až do schválení.

1. Přihlaste se k [https://myaccess.microsoft.com](https://myaccess.microsoft.com)portálu Můj přístup na adrese .  (Pro vládu USA bude `myaccess.microsoft.us`doména v odkazu Portál můj přístup .)

1. V levé nabídce klikněte na **Schválení,** abyste zobceli seznam žádostí o přístup čekajících na schválení.

1. Na kartě **Čeká na vyřízení** najděte požadavek.

## <a name="approve-or-deny-request"></a>Schválit nebo zamítnout žádost

Po otevření žádosti o přístup až do schválení, můžete zobrazit podrobnosti, které vám pomohou provést schválení nebo zamítnutí rozhodnutí.

**Předpokladová role:** Schvalovatel

1. Kliknutím na odkaz **Zobrazení** otevřete podokno žádosti o přístup.

1. Kliknutím na **Podrobnosti** zobrazíte podrobnosti o žádosti o přístup.

    Podrobnosti zahrnují jméno uživatele, organizaci, datum zahájení a ukončení přístupu, pokud je k dispozici, obchodní odůvodnění, kdy byla žádost odeslána a kdy vyprší platnost žádosti.

1. Klepněte na **tlačítko Schválit** nebo **Odepřít**.

1. V případě potřeby zadejte důvod.

    ![Můj přístupový portál – žádost o přístup](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Chcete-li **odeslat** své rozhodnutí, klepněte na tlačítko Odeslat.

    Pokud je zásada nakonfigurována s více schvalovately, musí se rozhodnout pouze jeden schvalovatel o čekajícím schválení. Poté, co schvalovatel odeslal své rozhodnutí k žádosti o přístup, je žádost dokončena a není již k dispozici pro ostatní schvalovatele, aby žádost schválili nebo zamítli. Ostatní schvalovatelé mohou zobrazit rozhodnutí o žádosti a osoby s rozhodovací pravomocí na svém portálu Můj přístup. V současné době je podporováno pouze jednostupňové schválení.

    Pokud žádný z nakonfigurovaných schvalovatelů není schopen schválit nebo zamítnout žádost o přístup, vyprší platnost žádosti po nakonfigurované době trvání požadavku. Uživatel dostane upozorněni, že jeho žádost o přístup vypršela a že je třeba znovu odeslat žádost o přístup.

## <a name="next-steps"></a>Další kroky

- [Požádat o přístup k přístupovému balíčku](entitlement-management-request-access.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
