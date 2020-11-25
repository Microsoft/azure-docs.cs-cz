---
title: Jak nakonfigurovat přiřazení samoobslužné aplikace | Microsoft Docs
description: Povolit Samoobslužný přístup k aplikacím povolit uživatelům hledání vlastních aplikací
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfa7530617830de99d752edcf4545300525ddbe8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997456"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Postup konfigurace přiřazení samoobslužné aplikace

Předtím, než budou moct vaši uživatelé sami vyhledávat aplikace ze svých aplikací, je nutné povolit **Samoobslužný přístup** k aplikacím, u kterých chcete uživatelům umožnit, aby si mohli sami vyhledat a vyžádat přístup k aplikaci. Tato funkce je k dispozici pro aplikace, které byly přidány z [Galerie Azure AD](./add-application-portal.md), [Azure proxy aplikací služby AD](./application-proxy.md) nebo byly přidány prostřednictvím [souhlasu uživatele nebo správce](../develop/application-consent-experience.md). 

Tato funkce je skvělým způsobem, jak ušetřit čas a peníze jako skupinu IT a důrazně se doporučuje jako součást nasazení moderních aplikací s Azure Active Directory.

Tato funkce umožňuje:

-   Umožněte uživatelům automaticky zjišťovat aplikace z [mých aplikací](https://myapps.microsoft.com/) , aniž byste přestane skupinu IT.

-   Přidejte tyto uživatele do předem nakonfigurované skupiny, abyste viděli, kdo požadoval přístup, odebrali přístup a spravovali přiřazené role.

-   Volitelně umožňuje obchodnímu schvalovateli schválit žádosti o přístup k aplikaci, aby tato skupina IT nemusí.

-   Volitelně můžete nakonfigurovat až 10 jednotlivců, kteří můžou schvalovat přístup k této aplikaci.

-   Volitelně umožňuje obchodnímu schvalovateli nastavit hesla, která uživatelé můžou použít k přihlášení k aplikaci přímo z [mých aplikací](https://myapps.microsoft.com/)schvalovatele firmy.

-   Volitelně automaticky přiřazované uživatele samoobslužné služby přímo k roli aplikace.

> [!NOTE]
> Aby si uživatelé mohli požádat o připojení k samoobslužné aplikaci a vlastníkům schvalovat nebo odmítat žádosti, je potřeba mít licenci Azure Active Directory Premium (P1 nebo P2). Bez licence Azure Active Directory Premium uživatelé nemůžou přidávat samoobslužné aplikace.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolit Samoobslužný přístup k aplikacím povolit uživatelům hledání vlastních aplikací

Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům povolit samoobslužné zjišťování aplikací a volitelně povolit obchodním skupinám přístup k těmto aplikacím. U aplikací pro jednotné přihlašování pomocí hesla můžete také obchodní skupině dovolit spravovat přihlašovací údaje přiřazené těmto uživatelům ze svých panelů pro přístup k mým aplikacím.

Pokud chcete aplikaci povolit Samoobslužný přístup k aplikacím, postupujte podle následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce.

2. Vyberte **Azure Active Directory**. V navigační nabídce vlevo vyberte **podnikové aplikace**.

3. Vyberte aplikaci ze seznamu. Pokud aplikaci nevidíte, začněte psát její název do vyhledávacího pole. Nebo použijte ovládací prvky filtru k výběru typu aplikace, stavu nebo viditelnosti a pak vyberte **použít**.

4. V navigační nabídce vlevo vyberte **Samoobslužná služba**.

5. Chcete-li pro tuto aplikaci povolit Samoobslužný přístup k aplikacím, zapněte možnost **Povolit uživatelům žádat o přístup k této aplikaci?** přepněte na **Ano.**

6. Vedle **kterého skupiny se mají přidaní uživatelé přidat?** klikněte na **Vybrat skupinu**. Zvolte skupinu a potom klikněte na **Vybrat**. Když je žádost uživatele schválená, přidají se do této skupiny. Při zobrazení členství v této skupině budete moci zjistit, komu byl udělen přístup k aplikaci prostřednictvím samoobslužného přístupu.
  
    > [!NOTE]
    > Toto nastavení nepodporuje skupiny synchronizované z místního prostředí.

7. **Volitelné:** Chcete-li před povolením přístupu k této aplikaci vyžadovat schválení od uživatele, nastavte **před udělením přístupu k této aplikaci oprávnění vyžadovat schválení?** přepněte na **Ano**.

8. **Volitelné: u aplikací, které používají jenom jednotné přihlašování,** aby mohli obchodní schvalovatelé zadat hesla, která se do této aplikace odesílají pro schválené uživatele, nastavte možnost **udělit schvalovatelům, aby se pro tuto aplikaci nastavila hesla uživatele?** přepněte na **Ano**.

9. **Volitelné:** Chcete-li určit obchodní schvalovatele, kteří mají oprávnění schvalovat přístup k **této aplikaci,** klikněte na **Vybrat schvalovatelé** a potom vyberte až 10 individuálních obchodních schvalovatelů. Pak klikněte na **Vybrat**.

    >[!NOTE]
    >Skupiny se nepodporují. Můžete vybrat až 10 individuálních obchodních schvalovatelů. Pokud zadáte více schvalovatelů, může jeden schvalovatel žádost o přístup schválit.

10. **Volitelné:** **u aplikací, které zveřejňují role**, aby bylo možné přiřadit uživatele samoobslužné služby k roli, vedle možnosti, **které by se měly přiřazovat uživatelům v této aplikaci?** klikněte na **Vybrat roli** a pak zvolte roli, ke které se tito uživatelé mají přiřadit. Pak klikněte na **Vybrat**.

11. Kliknutím na tlačítko **Uložit** v horní části podokna dokončíte.

Jakmile dokončíte konfiguraci samoobslužné aplikace, uživatelé můžou přejít na své [aplikace](https://myapps.microsoft.com/) a kliknout na tlačítko **Přidat samoobslužné aplikace** a vyhledat aplikace, které jsou povolené pomocí samoobslužného přístupu. Obchodní schvalovatelé také uvidí oznámení ve svých [aplikacích](https://myapps.microsoft.com/). Můžete povolit e-mailem upozornění, když uživatel požaduje přístup k aplikaci, která vyžaduje schválení.

## <a name="next-steps"></a>Další kroky
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../enterprise-users/groups-self-service-management.md)