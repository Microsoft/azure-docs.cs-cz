---
title: Kontrola přístupu ke skupinám & aplikací v recenzích přístupu – Azure AD
description: Naučte se kontrolovat vlastní přístup k skupinám nebo aplikacím v Azure Active Directory kontroly přístupu.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746775"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Kontrola přístupu pro vlastní skupiny nebo aplikace v prohlídekch služby Azure AD

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým podniky spravují přístup ke skupinám nebo aplikacím v Azure AD a dalších online službách Microsoftu s funkcí nazvanou kontroly přístupu Azure AD.

Tento článek popisuje, jak zkontrolovat vlastní přístup ke skupině nebo aplikaci.

## <a name="review-your-access-using-my-apps"></a>Kontrola přístupu pomocí mých aplikací

Prvním krokem k provedení kontroly přístupu je vyhledání a otevření kontroly přístupu.

>[!IMPORTANT]
> Při přijímání e-mailu může dojít k prodlevám a v některých případech může trvat až 24 hodin. Přidejte azure-noreply@microsoft.com do seznamu bezpečných příjemců a ujistěte se, že přijímáte všechny e-maily.

1. Vyhledejte e-mail od Microsoftu, který vás vyzve ke kontrole přístupu. Tady je příklad e-mailu pro kontrolu přístupu ke skupině.

    ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/review-your-access/access-review-email.png)

1. Kliknutím na odkaz **zkontrolovat přístup** otevřete kontrolu přístupu.

Pokud e-maily nemáte, můžete najít své nedokončené kontroly přístupu pomocí následujících kroků.

1. Přihlaste se k portálu moje aplikace na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portál moje aplikace seznam aplikací, ke kterým máte oprávnění](./media/review-your-access/myapps-access-panel.png)

1. V pravém horním rohu stránky klikněte na symbol uživatele. Zobrazí se vaše jméno a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

1. Na pravé straně stránky klikněte na dlaždici kontroly **přístupu** , abyste viděli seznam nedokončených kontrol přístupu.

    Pokud se dlaždice nezobrazí, pro danou organizaci není potřeba provést žádné kontroly přístupu a v tuto chvíli se nevyžaduje žádná akce.

    ![Seznam nevyřízených kontrol přístupu pro vaše aplikace a skupiny](./media/review-your-access/access-reviews-list.png)

1. Klikněte na odkaz **zahájit kontrolu** pro kontrolu přístupu, kterou chcete provést.

### <a name="perform-the-access-review"></a>Provést kontrolu přístupu

Po otevření kontroly přístupu uvidíte svůj přístup.

1. Zkontrolujte svůj přístup a rozhodněte se, jestli stále potřebujete přístup.

    Pokud je žádost o kontrolu přístupu pro ostatní, stránka bude vypadat jinak. Další informace najdete v tématu [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md).

    ![Snímek obrazovky, který zobrazuje otevřenou kontrolu přístupu s dotazem, jestli stále potřebujete přístup ke skupině.](./media/review-your-access/perform-access-review.png)

1. Kliknutím na **Ano** zachováte přístup, nebo kliknutím na **ne** odeberte přístup.

1. Pokud kliknete na **Ano**, možná budete muset zadat odůvodnění v poli **důvod** .

    ![Snímek obrazovky zobrazující dokončenou kontrolu přístupu, která se zeptá, jestli stále potřebujete přístup ke skupině, a vyberte Ano.](./media/review-your-access/perform-access-review-submit.png)

1. Klikněte na **Odeslat**.

    Váš výběr se odešle a vrátíte se na portál moje aplikace.

    Pokud chcete změnit odpověď, otevřete znovu stránku kontroly přístupu a aktualizujte svou odpověď. Odpověď můžete kdykoli změnit, dokud se neukončí kontrola přístupu.

    > [!NOTE]
    > Pokud jste označili, že už nepotřebujete přístup, neodebere se okamžitě. Po ukončení recenze dojde k odebrání nebo když správce zastaví kontrolu.

## <a name="review-your-own-access-using-my-access-new"></a>Kontrola vlastního přístupu pomocí přístupu (nové)

Můžete vyzkoušet nové prostředí s aktualizovaným uživatelským rozhraním v části přístup několika různými způsoby:

### <a name="my-apps-portal"></a>Portál moje aplikace

1. Přihlaste se k portálu moje aplikace na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portál moje aplikace seznam aplikací, ke kterým máte oprávnění](./media/review-your-access/myapps-access-panel.png)

2. Kliknutím na dlaždici kontroly **přístupu** zobrazíte seznam nedokončených revizí přístupu.

    > [!NOTE]
    > Pokud dlaždice kontroly **přístupu** není viditelná, neexistují žádné kontroly přístupu, které by se daly v této organizaci provádět, a v tuto chvíli není potřeba žádná akce.

3. Klikněte na **vyzkoušet!** v nápisu v horní části stránky přejdete na nové prostředí pro přístup.

    ![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny s novinkami dostupnými pro nové prostředí, které se zobrazí během verze Preview](./media/review-your-access/banner-your-access.png)

4. Pokračovat v části **provede kontrolu přístupu** .

### <a name="email"></a>E-mail

>[!IMPORTANT]
> Při přijímání e-mailu může dojít k prodlevám a v některých případech může trvat až 24 hodin. Přidejte azure-noreply@microsoft.com do seznamu bezpečných příjemců a ujistěte se, že přijímáte všechny e-maily.

1. Vyhledejte e-mail od Microsoftu, který žádá o kontrolu přístupu. Níže vidíte příklad e-mailové zprávy:

 ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/review-your-access/access-review-email-preview.png)

2. Kliknutím na odkaz **zkontrolovat přístup** otevřete kontrolu přístupu.

3. Pokračovat v části **provede kontrolu přístupu** .

>[!NOTE]
>Pokud kliknete na možnost spustit revizi přejdete na **Moje aplikace** , postupujte podle kroků uvedených v části nahoře s názvem **Moje aplikace Portál**.

### <a name="directly-at-my-access"></a>Přímo v mém přístupu

Můžete také zobrazit své nedokončené kontroly přístupu pomocí prohlížeče a otevřít tak přístup.

1. Přihlaste se k mému přístupu na https://myaccess.microsoft.com/

2. V nabídce na levém bočním panelu vyberte kontroly **přístupu** , abyste viděli seznam nedokončených kontrol přístupu, které jsou vám přiřazeny.

   ![kontroly přístupu v nabídce](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Provést kontrolu přístupu

1. V části skupiny a aplikace můžete vidět:
    
    - **Název** Název kontroly přístupu
    - V **důsledku** Termín splnění revize. Po tomto datu je možné odebrat uživatele ze skupiny nebo aplikace, které se právě přezkoumávají.
    - **Prostředek** Název prostředku, který se má zkontrolovat.
    - **Průběh** Počet uživatelů zkontrolovaných v rámci této kontroly přístupu celkovým počtem uživatelů.
    
2. Začněte kliknutím na název kontroly přístupu.

   ![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny](./media/review-your-access/access-reviews-list-preview.png)

3. Zkontrolujte svůj přístup a rozhodněte se, jestli stále potřebujete přístup.

    Pokud je žádost o kontrolu přístupu pro ostatní, stránka bude vypadat jinak. Další informace najdete v tématu [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md).

    ![Otevřete kontrolu přístupu s dotazem, jestli stále potřebujete přístup ke skupině.](./media/review-your-access/review-access-preview.png)

1. Vyberte **Ano** , pokud chcete zachovat přístup, nebo vyberte **ne** pro odebrání přístupu.

1. Pokud kliknete na **Ano**, možná budete muset zadat odůvodnění v poli **důvod** .

    ![Dokončila se kontrola přístupu s dotazem, jestli stále potřebujete přístup ke skupině.](./media/review-your-access/review-access-yes-preview.png)

1. Klikněte na **Odeslat**.

    Váš výběr se odešle a vrátíte se na stránku můj přístup.

    Pokud chcete změnit odpověď, otevřete znovu stránku kontroly přístupu a aktualizujte svou odpověď. Odpověď můžete kdykoli změnit, dokud se neukončí kontrola přístupu.

    > [!NOTE]
    > Pokud jste označili, že už nepotřebujete přístup, neodebere se okamžitě. Po ukončení recenze dojde k odebrání nebo když správce zastaví kontrolu.

## <a name="next-steps"></a>Další kroky

- [Dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md)
