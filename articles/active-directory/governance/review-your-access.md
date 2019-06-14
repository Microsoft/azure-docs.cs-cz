---
title: Zkontrolovat přístup skupinám nebo aplikacím v kontroly přístupu – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak kontrolovat svůj vlastní přístup skupinám nebo aplikacím v kontrol přístupu Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b38422b22885ad96c6681abe58b897155809521
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66113187"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Zkontrolovat přístup ke skupinám nebo aplikacím ve službě Azure AD kontroly přístupu

Azure Active Directory (Azure AD) zjednodušuje podnikům správu přístupu k skupiny a aplikace ve službě Azure AD a zkontroluje ostatní Microsoft Online Services s funkci s názvem přístup služby Azure AD.

Tento článek popisuje, jak kontrolovat svůj vlastní přístup ke skupině nebo aplikaci.

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2

Další informace najdete v tématu [kteří uživatelé musí mít licence?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Otevřete kontroly přístupu.

Prvním krokem k provádění kontroly přístupu je k vyhledání a otevření kontroly přístupu.

1. Vyhledejte e-mailu od Microsoftu, která vás vyzve k kontrolovat přístup. Tady je příklad e-mailu kontrolovat přístup ke skupině.

    ![Kontrola přístupu k e-mailu](./media/review-your-access/access-review-email.png)

1. Klikněte na tlačítko **kontrolovat přístup** odkaz k otevření kontroly přístupu.

Pokud nemáte k dispozici v e-mailu, zjistíte, že se čeká na přístup kontroly pomocí následujících kroků.

1. Přihlaste se k portálu MyApps na [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portálu MyApps](./media/review-your-access/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele. Zobrazí se vaše jméno a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

1. Na pravé straně stránky klikněte na tlačítko **kontrol přístupu** dlaždici zobrazíte seznam kontrol přístupu.

    Pokud se dlaždice nezobrazí, pro danou organizaci není potřeba provést žádné kontroly přístupu a v tuto chvíli se nevyžaduje žádná akce.

    ![Seznam kontrol přístupu](./media/review-your-access/access-reviews-list.png)

1. Klikněte na tlačítko **začít kontrolu** odkaz pro kontroly přístupu, kterou chcete provést.

## <a name="perform-the-access-review"></a>Provádění kontroly přístupu

Po spuštění kontroly přístupu, zobrazí se váš přístup.

1. Kontrolovat přístup a rozhodněte se, jestli stále potřebují přístup.

    Pokud žádost ke kontrole přístupu pro ostatní uživatele, bude stránka vypadat jiný. Další informace najdete v tématu [kontrolovat přístup skupinám nebo aplikacím](perform-access-review.md).

    ![Provádění kontroly přístupu](./media/review-your-access/perform-access-review.png)

1. Klikněte na tlačítko **Ano** chcete zachovat přístup, nebo klikněte na tlačítko **ne** aby odebral váš přístup.

1. Vyberete-li **Ano**, možná budete muset zadat odůvodnění v **důvod** pole.

    ![Provádění kontroly přístupu](./media/review-your-access/perform-access-review-submit.png)

1. Klikněte na **Submit** (Odeslat).

    Odeslání svůj výběr a vrátíte se k portálu MyApps.

    Pokud chcete změnit vaše odpověď, znovu otevřete stránku kontrol přístupu a aktualizujte vaše odpověď. Vaše odpověď můžete změnit kdykoli až do ukončení kontroly přístupu.

    > [!NOTE]
    > Pokud jste určili, které už nepotřebujete přístup, se neodeberou okamžitě. Se odeberou, když kontrola skončila, nebo při zastavení revize správce.

## <a name="next-steps"></a>Další postup

- [Dokončení kontroly přístupu skupiny nebo aplikace](complete-access-review.md)
