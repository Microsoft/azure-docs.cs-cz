---
title: Zkontrolovat přístup skupinám nebo aplikacím v kontrol přístupu Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak kontrolovat svůj vlastní přístup do skupiny nebo aplikace v Azure Active Directory kontroly přístupu.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f90a5aa3f201fa5d1578ac63526be26377aedf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731204"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Zkontrolovat přístup skupinám nebo aplikacím v kontrol přístupu Azure AD

Azure Active Directory (Azure AD) zjednodušuje, jak podniky spravovat přístup skupinám nebo aplikacím ve službě Azure AD a druhou Microsoft Online Services s funkcí s názvem kontrol přístupu Azure AD.

Tento článek popisuje, jak kontrolovat svůj vlastní přístup ke skupině nebo aplikaci.

## <a name="open-the-access-review"></a>Otevřete kontroly přístupu.

Prvním krokem k provádění kontroly přístupu je k vyhledání a otevření kontroly přístupu.

1. Vyhledejte e-mailu od Microsoftu, která vás vyzve k kontrolovat přístup. Tady je příklad e-mailu kontrolovat přístup ke skupině.

    ![Kontrola přístupu k e-mailu](./media/review-your-access/access-review-email.png)

1. Klikněte na tlačítko **kontrolovat přístup** odkaz k otevření kontroly přístupu.

Pokud nemáte k dispozici v e-mailu, zjistíte, že se čeká na přístup kontroly pomocí následujících kroků.

1. Přihlaste se k portálu MyApps na [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portálu MyApps](./media/review-your-access/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele, která zobrazuje název a výchozí organizace. Pokud je uveden více než jedné organizace, vyberte organizace, která požadované kontroly přístupu.

1. Na pravé straně stránky klikněte na tlačítko **kontrol přístupu** dlaždici zobrazíte seznam kontrol přístupu.

    Pokud není zobrazena na dlaždici, neexistují žádné kontroly přístupu k provedení pro danou organizaci a v tuto chvíli není potřeba žádná akce.

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
