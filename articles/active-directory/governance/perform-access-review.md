---
title: Kontrola přístupu ke skupinám & aplikací v recenzích přístupu – Azure AD
description: Přečtěte si, jak zkontrolovat přístup ke členům skupiny nebo přístupu k aplikacím v Azure Active Directory kontroly přístupu.
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
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efab031ac2ad96524ce12e6d1c4a942f01ce2344
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299962"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Kontrola přístupu ke skupinám a aplikacím v prohlídekch Azure AD Access

Azure Active Directory (Azure AD) zjednodušuje způsob, jakým podniky spravují přístup k skupinám a aplikacím v Azure AD a dalších online službách Microsoftu s funkcí nazvanou kontroly přístupu Azure AD. V tomto článku se dozvíte, jak určený kontrolor provede kontrolu přístupu pro členy skupiny nebo uživatelů, kteří mají přístup k aplikaci. Pokud chcete zkontrolovat přístup ke čtení balíčku, přístup k balíčku pro [přístup ve správě nároků Azure AD](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Provádět kontrolu přístupu pomocí mých aplikací

Proces kontroly přístupu můžete spustit z oznamovacího e-mailu nebo přejít přímo na web.

- **E-mail**:

>[!IMPORTANT]
> Při přijímání e-mailu může dojít k prodlevám a v některých případech může trvat až 24 hodin. Přidejte azure-noreply@microsoft.com do seznamu bezpečných příjemců a ujistěte se, že přijímáte všechny e-maily.

1. Vyhledejte e-mail od Microsoftu, který žádá o kontrolu přístupu. Tady je příklad e-mailu pro kontrolu přístupu pro skupinu.

    ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/perform-access-review/access-review-email.png)

1. Kliknutím na odkaz **zahájit kontrolu** otevřete kontrolu přístupu.

- **Pokud e-maily**nemáte, můžete najít své nedokončené kontroly přístupu pomocí následujících kroků.

    1. Přihlaste se k portálu moje aplikace na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![Portál moje aplikace seznam aplikací, ke kterým máte oprávnění](./media/perform-access-review/myapps-access-panel.png)

    1. V pravém horním rohu stránky klikněte na uživatele vedle vašeho jména a výchozí organizace. Pokud se zobrazí více než jedna organizace, vyberte organizaci, která požádala o kontrolu přístupu.

    1. Kliknutím na dlaždici kontroly **přístupu** zobrazíte seznam nedokončených revizí přístupu.

        > [!NOTE]
        > Pokud dlaždice kontroly **přístupu** není viditelná, neexistují žádné kontroly přístupu, které by se daly v této organizaci provádět, a v tuto chvíli není potřeba žádná akce.

        ![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny](./media/perform-access-review/access-reviews-list.png)

    1. Klikněte na odkaz **zahájit kontrolu** pro kontrolu přístupu, kterou chcete provést.

Po otevření kontroly přístupu se zobrazí jména uživatelů, kteří potřebují zkontrolovat přístup.

Pokud se jedná o žádost o kontrolu vašeho vlastního přístupu, stránka bude vypadat jinak. Další informace najdete v tématu [Kontrola přístupu pro vlastní skupiny nebo aplikace](review-your-access.md).

![Otevřete kontrolu přístupu se seznamem uživatelů pro kontrolu.](./media/perform-access-review/perform-access-review.png)

Existují dva způsoby, jak můžete schválit nebo odepřít přístup:

- Můžete schválit nebo zamítnout přístup pro jednoho nebo více uživatelů ručně výběrem příslušné akce pro jednotlivé požadavky uživatele.
- Můžete přijmout doporučení systému.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Schválení nebo zamítnutí přístupu pro jednoho nebo více uživatelů

1. Projděte si seznam uživatelů a rozhodněte se, jestli chcete jejich trvalý přístup schválit nebo zamítnout.

    - Chcete-li schválit nebo odepřít přístup pro jednoho uživatele, kliknutím na řádek otevřete okno a určete akci, která má být provedena. 
    - Chcete-li schválit nebo odepřít přístup pro více uživatelů, přidejte zaškrtnutí vedle uživatelů a potom kliknutím na tlačítko **zkontrolovat X uživatele** otevřete okno, které určuje akci, která má být provedena.

1. Klikněte na **schválit** nebo **Odepřít**. 

    ![Okno akce, které zahrnuje schválení, zamítnutí a neznalost možností](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Pokud si nejste jisti, můžete kliknout na **neznát**. a uživatel získá přístup a vaše volba se zaznamená do protokolu auditu.

1. Správce kontroly přístupu může vyžadovat, abyste zadali důvod do pole **důvod** pro vaše rozhodnutí. I v případě, že není vyžadován důvod. K vašemu rozhodnutí stále můžete zadat nějaký důvod a informace, které zahrnete, budou k dispozici ostatním kontrolorům.

1. Až zadáte akci, která se má provést, klikněte na **Uložit**.

    >[!NOTE]
    > Odpověď můžete kdykoli změnit předtím, než dojde k ukončení kontroly přístupu. Pokud chcete změnit odpověď, vyberte řádek a aktualizujte odpověď. Můžete například schválit dříve zamítnutého uživatele nebo odepřít dříve schváleného uživatele.

    >[!IMPORTANT]
    > - Pokud má uživatel odepřený přístup, neodstraní se okamžitě. Po skončení období kontroly dojde k jejich odebrání nebo když správce zastaví kontrolu, pokud je povolená možnost [automaticky použít](complete-access-review.md#apply-the-changes) .
    > - Pokud existuje více revidujících, je zaznamenána poslední odeslaná odpověď. Vezměte v úvahu příklad, kdy správce určí dva revidující – Alice a Bob. Alice otevře nejdřív kontrolu přístupu a schválí žádost o přístup uživatele. Před koncem období recenze otevře Bob kontrolu přístupu a odepře přístup na stejném požadavku, který dřív schválila Alice. Poslední rozhodnutí o odepření přístupu je odpověď, která se zaznamená.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Schválit nebo odepřít přístup na základě doporučení

Abychom zajistili lepší a rychlejší kontroly přístupu, poskytujeme také doporučení, která můžete přijmout jediným kliknutím. Doporučení se generují na základě přihlašovací aktivity uživatele.

1. Na modrém panelu v dolní části stránky klikněte na **přijmout doporučení**.

    ![Otevřete seznam kontroly přístupu se zobrazením tlačítka přijmout doporučení.](./media/perform-access-review/accept-recommendations.png)

    Zobrazí se souhrn doporučených akcí.

    ![Okno, které zobrazuje souhrn doporučených akcí](./media/perform-access-review/accept-recommendations-summary.png)

1. Kliknutím na **OK** přijměte doporučení.

## <a name="perform-access-review-using-my-access-new"></a>Provést kontrolu přístupu pomocí přístupu (nové)

K novému uživatelskému rozhraní v možnosti vlastní pohled můžete získat přístup několika různými způsoby:

### <a name="my-apps-portal"></a>Portál moje aplikace

1. Přihlaste se k mým aplikacím na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portál moje aplikace seznam aplikací, ke kterým máte oprávnění](./media/perform-access-review/myapps-access-panel.png)

2. Kliknutím na dlaždici kontroly **přístupu** zobrazíte seznam nedokončených revizí přístupu.

    > [!NOTE]
    > Pokud dlaždice kontroly **přístupu** není viditelná, neexistují žádné kontroly přístupu, které by se daly v této organizaci provádět, a v tuto chvíli není potřeba žádná akce.

![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny s novinkami dostupnými pro nové prostředí, které se zobrazí během verze Preview](./media/perform-access-review/banner.png)

3. Klikněte na **vyzkoušet!** v nápisu v horní části stránky. Tím přejdete k novému prostředí pro přístup.
  
### <a name="email"></a>E-mail

  >[!IMPORTANT]
> Při přijímání e-mailu může dojít k prodlevám a v některých případech může trvat až 24 hodin. Přidejte azure-noreply@microsoft.com do seznamu bezpečných příjemců a ujistěte se, že přijímáte všechny e-maily.

   1. Vyhledejte e-mail od Microsoftu, který žádá o kontrolu přístupu. Níže vidíte příklad e-mailové zprávy:

   ![Příklad e-mailu od Microsoftu ke kontrole přístupu ke skupině](./media/perform-access-review/access-review-email-preview.png)

   2. Kliknutím na odkaz **zahájit kontrolu** otevřete kontrolu přístupu.

>[!NOTE]
>Pokud kliknete na možnost spustit revizi přejdete na **Moje aplikace** , postupujte podle kroků uvedených v části nahoře s názvem **Moje aplikace Portál**.

### <a name="navigate-to-my-access-directly"></a>Přejít přímo na moji přístup

Můžete také zobrazit své nedokončené kontroly přístupu pomocí prohlížeče a otevřít tak přístup.

1. Přihlaste se k mému přístupu na https://myaccess.microsoft.com/

2. V nabídce na levém bočním panelu vyberte kontroly **přístupu** , abyste viděli seznam nedokončených kontrol přístupu, které jsou vám přiřazeny.

   ![kontroly přístupu v nabídce](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Schválení nebo zamítnutí přístupu pro jednoho nebo více uživatelů

Po otevření okna přístup v části skupiny a aplikace můžete zobrazit:

- **Název** Název kontroly přístupu
- V **důsledku** Termín splnění revize. Po tomto datu je možné odebrat uživatele ze skupiny nebo aplikace, které se právě přezkoumávají.
- **Prostředek** Název prostředku, který se má zkontrolovat.
- **Průběh** Počet uživatelů zkontrolovaných v rámci této kontroly přístupu celkovým počtem uživatelů.

Začněte kliknutím na název kontroly přístupu.

![Seznam nevyřízených kontrol přístupu pro aplikace a skupiny](./media/perform-access-review/access-reviews-list-preview.png)

Po otevření se zobrazí seznam uživatelů v oboru pro kontrolu přístupu. Pokud se jedná o žádost o kontrolu vašeho vlastního přístupu, stránka bude vypadat jinak. Další informace najdete v tématu [Kontrola přístupu pro vlastní skupiny nebo aplikace](review-your-access.md).

Existují dva způsoby, jak můžete schválit nebo odepřít přístup:

- Pro jednoho nebo více uživatelů můžete ručně schválit nebo odepřít přístup.
- Můžete přijmout doporučení systému.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Ruční schválení nebo zamítnutí přístupu pro jednoho nebo více uživatelů

1. Projděte si seznam uživatelů a rozhodněte se, jestli chcete jejich trvalý přístup schválit nebo zamítnout.
2. Vyberte jednoho nebo více uživatelů kliknutím na kroužek vedle jejich názvů.
3. Na panelu výše vyberte **schválit** nebo **Odepřít** .
    - Pokud si nejste jisti, můžete kliknout na **neznát**. Uživatel získá přístup a vaše volba se zaznamená do protokolu auditu. Je důležité si uvědomit, že veškeré informace, které poskytnete, budou k dispozici ostatním kontrolorům. Můžou si komentáře přečíst a při kontrole žádosti je vzít v úvahu.

    ![Otevřete kontrolu přístupu a uveďte uživatele, kteří potřebují kontrolu.](./media/perform-access-review/user-list-preview.png)

4. Správce kontroly přístupu může vyžadovat, abyste zadali důvod do pole **důvod** pro vaše rozhodnutí. I v případě, že není vyžadován důvod. K vašemu rozhodnutí stále můžete zadat nějaký důvod a informace, které zahrnete, budou k dispozici ostatním schvalovatelům ke kontrole.

5. Klikněte na **Odeslat**.
    - Odpověď můžete kdykoli změnit, dokud se neukončí kontrola přístupu. Pokud chcete změnit odpověď, vyberte řádek a aktualizujte odpověď. Můžete například schválit dříve zamítnutého uživatele nebo odepřít dříve schváleného uživatele.

 >[!IMPORTANT]
 > - Pokud má uživatel odepřený přístup, neodstraní se okamžitě. Po skončení období kontroly dojde k jejich odebrání nebo když správce zastaví kontrolu. 
 > - Pokud existuje více revidujících, je zaznamenána poslední odeslaná odpověď. Vezměte v úvahu příklad, kdy správce určí dva revidující – Alice a Bob. Alice otevře nejdřív kontrolu přístupu a schválí žádost o přístup uživatele. Před koncem období recenze otevře Bob kontrolu přístupu a odepře přístup na stejném požadavku, který dřív schválila Alice. Poslední rozhodnutí o odepření přístupu je odpověď, která se zaznamená.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Schválit nebo odepřít přístup na základě doporučení

Abychom zajistili lepší a rychlejší kontroly přístupu, poskytujeme také doporučení, která můžete přijmout jediným kliknutím. Doporučení se generují na základě přihlašovací aktivity uživatele.

1. Vyberte jednoho nebo více uživatelů a pak klikněte na **přijmout doporučení**.

    ![Otevřete seznam kontroly přístupu se zobrazením tlačítka přijmout doporučení.](./media/perform-access-review/accept-recommendations-preview.png)

1. Doporučení přijměte kliknutím na **Odeslat** .

Pokud chcete přijmout doporučení pro všechny uživatele, ujistěte se, že není vybraná možnost nikdo, a klikněte na tlačítko **přijmout doporučení** na horním panelu.

>[!NOTE]
>Při přijetí doporučení se nebudou měnit předchozí rozhodnutí.

## <a name="next-steps"></a>Další kroky

- [Dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md)
