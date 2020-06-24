---
title: Jak používat Samoobslužný přístup k aplikacím | Microsoft Docs
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1c5a003eb113289bcb798423ff649b944dcf186
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761064"
---
# <a name="how-to-use-self-service-application-access"></a>Jak používat Samoobslužný přístup k aplikacím

Než budou moct vaši uživatelé sami zjišťovat aplikace z přístupového panelu, musíte povolit **Samoobslužný přístup** k aplikacím, u kterých chcete uživatelům umožnit, aby si mohli sami vyhledat a požádat o přístup.

Tato funkce je skvělým způsobem, jak ušetřit čas a peníze jako skupinu IT a důrazně se doporučuje jako součást nasazení moderních aplikací s Azure Active Directory.

Tato funkce umožňuje:

-   Umožněte uživatelům automaticky zjišťovat aplikace z [přístupového panelu aplikace](https://myapps.microsoft.com/) bez přestane skupiny IT.

-   Přidejte tyto uživatele do předem nakonfigurované skupiny, abyste viděli, kdo požadoval přístup, odebrali přístup a spravovali přiřazené role.

-   Volitelně umožňuje obchodnímu schvalovateli schválit žádosti o přístup k aplikaci, aby tato skupina IT nemusí.

-   Volitelně můžete nakonfigurovat až 10 jednotlivců, kteří můžou schvalovat přístup k této aplikaci.

-   Volitelně umožníte obchodnímu schvalovateli nastavit hesla, která uživatelé můžou použít k přihlášení k aplikaci přímo z [přístupového panelu aplikace](https://myapps.microsoft.com/)obchodního schvalovatele.

-   Volitelně automaticky přiřazované uživatele samoobslužné služby přímo k roli aplikace.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolit Samoobslužný přístup k aplikacím povolit uživatelům hledání vlastních aplikací

Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům povolit samoobslužné zjišťování aplikací. volitelně jim umožní obchodní skupině schvalovat přístup k těmto aplikacím. Obchodní skupině můžete dovolit, aby uživatelé mohli spravovat přihlašovací údaje přiřazená těmto uživatelům pro samoobslužné přihlašování pomocí hesel přímo ze svých přístupových panelů.

Pokud chcete aplikaci povolit Samoobslužný přístup k aplikacím, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní nabídky navigace na levé straně.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, ke které chcete povolit Samoobslužný přístup ze seznamu.

7. Po načtení aplikace klikněte v navigační nabídce aplikace na **Samoobslužná služba** .

8. Chcete-li pro tuto aplikaci povolit Samoobslužný přístup k aplikacím, zapněte možnost **Povolit uživatelům žádat o přístup k této aplikaci?** přepněte na **Ano.**

9. Pokud chcete vybrat skupinu, do které chcete přidat uživatele, kteří požadují přístup k této aplikaci, klikněte na selektor vedle popisku, **ke kterému by se měla přiřadit skupina uživatelů?** a vyberte skupinu.

10. **Volitelné:** Pokud chcete před povolením přístupu k této aplikaci vyžadovat obchodní schválení, nastavte **před udělením přístupu k této aplikaci oprávnění vyžadovat schválení?** přepněte na **Ano**.

11. **Volitelné: u aplikací, které používají jenom jednotné přihlašování,** Pokud chcete těmto obchodním schvalovatelům udělit hesla, která se do této aplikace odesílají pro schválené uživatele, nastavte pro **tuto aplikaci** hesla, která chcete u této aplikace odeslat? přepněte na **Ano**.

12. **Volitelné:** Pokud chcete určit obchodní schvalovatele, kteří mají povolený přístup k této aplikaci, klikněte na selektor vedle popisku **, který má povolený souhlas s přístupem k této aplikaci?** Pokud chcete vybrat až 10 individuálních obchodních schvalovatelů.

    * Skupiny se nepodporují.

13. **Volitelné:** **u aplikací, které zveřejňují role**. Pokud chcete roli přiřadit schválené uživatele samoobslužné služby, klikněte na selektor vedle **role, které by se měly přiřazovat uživatelům v této aplikaci. Pokud** chcete vybrat roli, ke které se tito uživatelé mají přiřadit.

14. Kliknutím na tlačítko **Uložit** v horní části okna dokončete.

Jakmile dokončíte konfiguraci samoobslužné aplikace, uživatelé můžou přejít na svůj [přístupový panel aplikace](https://myapps.microsoft.com/) a kliknout na tlačítko **+ Přidat** a najít tak aplikace, na které jste povolili Samoobslužný přístup. Obchodní schvalovatelé také uvidí oznámení na svém [přístupovém panelu aplikací](https://myapps.microsoft.com/). Můžete povolit e-mailem upozornění, když uživatel požaduje přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují pouze pracovní postupy s jedním schválením, což znamená, že pokud zadáte více schvalovatelů, může každý jednotlivý schvalovatel schválit přístup k aplikaci.

## <a name="next-steps"></a>Další kroky
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../users-groups-roles/groups-self-service-management.md)
