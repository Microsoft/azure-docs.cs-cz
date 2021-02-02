---
title: Jak používat Samoobslužný přístup k aplikacím v Azure AD
description: Povolit samoobslužné služby, aby uživatelé mohli najít aplikace v Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: 13f91fdd9e2d9501fba426bd6facbf9824a39285
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257013"
---
# <a name="how-to-use-self-service-application-access"></a>Jak používat Samoobslužný přístup k aplikacím

Předtím, než budou moct vaši uživatelé sami zjišťovat aplikace ze své stránky Moje aplikace, je potřeba povolit **Samoobslužný přístup** k aplikacím, u kterých chcete uživatelům umožnit, aby si mohli sami zjistit a vyžádat přístup k aplikaci.

Tato funkce je skvělým způsobem, jak ušetřit čas a peníze jako skupinu IT a důrazně se doporučuje jako součást nasazení moderních aplikací s Azure Active Directory.

Další informace o používání mých aplikací z perspektivy koncových uživatelů najdete v [nápovědě k portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

Tato funkce umožňuje:

-   Umožněte uživatelům automaticky zjišťovat aplikace z [mých aplikací](https://myapps.microsoft.com/) , aniž byste přestane skupinu IT.
-   Přidejte tyto uživatele do předem nakonfigurované skupiny, abyste viděli, kdo požadoval přístup, odebrali přístup a spravovali přiřazené role.
-   Volitelně můžete povolit, aby někdo schválil žádosti o přístup k aplikacím, aby tato skupina IT nemusela.
-   Volitelně můžete nakonfigurovat až 10 jednotlivců, kteří můžou schvalovat přístup k této aplikaci.
-   Volitelně můžete někomu dovolit, aby si nastavili hesla, která uživatelé můžou použít k přihlášení k aplikaci.
-   Volitelně automaticky přiřazované uživatele samoobslužné služby přímo k roli aplikace.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolit Samoobslužný přístup k aplikacím povolit uživatelům hledání vlastních aplikací

Samoobslužný přístup k aplikacím je skvělý způsob, jak uživatelům povolit samoobslužné zjišťování aplikací. volitelně jim umožní obchodní skupině schvalovat přístup k těmto aplikacím. Obchodní skupině můžete dovolit, aby uživatelé spravovali přihlašovací údaje přiřazené k heslům Single-Sign v aplikacích přímo ze své stránky Moje aplikace.

Pokud chcete aplikaci povolit Samoobslužný přístup k aplikacím, postupujte podle následujících kroků:
1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**
2. Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace**.
5. Vyberte **Všechny aplikace**. Zobrazí se seznam všech vašich aplikací.
   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**
6. Vyberte aplikaci, ke které chcete povolit Samoobslužný přístup ze seznamu.
7. Po načtení aplikace vyberte **Samoobslužná služba** z nabídky navigace na levé straně aplikace.
8. Chcete-li pro tuto aplikaci povolit Samoobslužný přístup k aplikacím, zapněte možnost **Povolit uživatelům žádat o přístup k této aplikaci?** přepněte na **Ano.**
9. Pokud chcete vybrat skupinu, do které se mají přidat uživatelé, kteří požadují přístup k této aplikaci, vyberte selektor vedle popisku, **ke kterému se má přiřadit skupina uživatelů** a vyberte skupinu.
10. **Volitelné:** Pokud chcete před povolením přístupu k této aplikaci vyžadovat obchodní schválení, nastavte **před udělením přístupu k této aplikaci oprávnění vyžadovat schválení?** přepněte na **Ano**.
11. **Volitelné: u aplikací, které používají jenom jednotné přihlašování,** Pokud chcete těmto obchodním schvalovatelům udělit hesla, která se do této aplikace odesílají pro schválené uživatele, nastavte pro **tuto aplikaci** hesla, která chcete u této aplikace odeslat? přepněte na **Ano**.
12. **Volitelné:** Určete, kteří obchodní schvalovatelé mají oprávnění schvalovat přístup k této aplikaci. Vyberte **, kdo smí schvalovat přístup k této aplikaci?** Pak vyberte až 10 individuálních obchodních schvalovatelů.
    * Skupiny se nepodporují.
13. **Volitelné:** **u aplikací, které zveřejňují role**, pokud chcete roli přiřadit schválené uživatele samoobslužné služby, vyberte selektor vedle **role, které by se měly přiřazovat uživatelům v této aplikaci? Pokud** chcete vybrat roli, ke které se tito uživatelé mají přiřadit.
14. Kliknutím na tlačítko **Uložit** v horní části dokončíte.

Jakmile dokončíte konfiguraci samoobslužné aplikace, uživatelé můžou přejít na [Moje aplikace](https://myapps.microsoft.com/) a vybrat tlačítko **+ Přidat** a najít tak aplikace, na které jste povolili Samoobslužný přístup. Obchodní schvalovatelé také uvidí oznámení na stránce [Moje aplikace](https://myapps.microsoft.com/) . Můžete povolit e-mailem upozornění, když uživatel požaduje přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují pouze pracovní postupy s jedním schválením, což znamená, že pokud zadáte více schvalovatelů, může každý jednotlivý schvalovatel schválit přístup k aplikaci.

## <a name="things-to-check-if-self-service-isnt-working"></a>Věci pro kontrolu, jestli nefunguje Samoobslužná služba
-   Ujistěte se, že uživatel nebo skupina mají povolený požadavek samoobslužného přístupu k aplikacím.
-   Ujistěte se, že uživatel navštívil správné místo pro Samoobslužný přístup k aplikacím. Uživatelé můžou přejít na stránku [Moje aplikace](https://myapps.microsoft.com/) a vybrat tlačítko **+ Přidat** a najít aplikace, na které jste povolili Samoobslužný přístup.
-   Pokud byl nedávno nakonfigurovaný Samoobslužný přístup k aplikacím, zkuste se znovu přihlásit a znovu přihlašovat do mých aplikací uživatele po několika minutách, abyste zjistili, jestli se projevily změny samoobslužného přístupu.

## <a name="next-steps"></a>Další kroky
[Nastavení služby Azure Active Directory pro samoobslužnou správu skupin](../enterprise-users/groups-self-service-management.md)
