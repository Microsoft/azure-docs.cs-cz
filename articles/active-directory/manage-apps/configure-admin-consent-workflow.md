---
title: Nakonfigurovat pracovní postup souhlasu správce – Azure Active Directory | Microsoft Docs
description: Naučte se konfigurovat způsob, jak koncovým uživatelům požádat o přístup k aplikacím, které vyžadují souhlas správce.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d89ea0cbc7d1e0379a9cbfce40f11d4f8ac93f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643758"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurace pracovního postupu pro vyjádření souhlasu správce (Preview)

Tento článek popisuje, jak povolit funkci pracovní postup pro vyjádření souhlasu správce (Preview), která koncovým uživatelům dává možnost požádat o přístup k aplikacím, které vyžadují souhlas správce.

Bez pracovního postupu souhlasu správce se při pokusu o přístup k aplikaci, která vyžaduje oprávnění k přístupu k datům organizace, zablokuje uživatel v tenantovi, kde je zakázaný souhlas uživatele. Uživateli se zobrazí obecná chybová zpráva s upozorněním, že nemají oprávnění k přístupu k aplikaci a že by si měli požádat o pomoc správce. Uživatel ale často neví, kdo má kontaktovat, takže buď v aplikaci vytvoří nový místní účet, nebo ho v něm vytvoří. I když je správce upozorněn, není vždy jednodušší proces, který správcům umožňuje udělit přístup a upozorňovat jejich uživatele.
 
Pracovní postup pro vyjádření souhlasu správce poskytuje správcům zabezpečený způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem. Když se uživatel pokusí o přístup k aplikaci, ale není schopen vyjádřit souhlas, může odeslat žádost o schválení správcem. Požadavek se pošle e-mailem správcům, kteří byli určeni jako kontroloři. Kontrolor provede v žádosti akci a uživateli se zobrazí oznámení o akci.

Aby bylo možné schválit žádosti, kontrolor musí být globální správce, správce cloudové aplikace nebo správce aplikace. Kontrolor již musí mít přiřazenou jednu z těchto rolí správce. pouhým označením jako kontrolor nezvyšují svá oprávnění.

## <a name="enable-the-admin-consent-workflow"></a>Povolení pracovního postupu pro vyjádření souhlasu správce

Pokud chcete povolit pracovní postup souhlasu správce a zvolit kontrolory:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. V horní části levé navigační nabídky klikněte na **všechny služby** . Otevře se **rozšíření Azure Active Directory** .
3. Do vyhledávacího pole filtru zadejte "**Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. V navigační nabídce klikněte na **podnikové aplikace**. 
5. V části **Spravovat** vyberte **uživatelská nastavení**.
6. V části **žádosti o souhlas správce (Preview)** **můžou uživatelé požádat o souhlas správce na aplikace** , které nemůžou udělit souhlas s **Ano**.

   ![Konfigurovat nastavení pracovního postupu pro vyjádření souhlasu správce](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Nakonfigurujte tahle nastavení:

   * **Vyberte možnost Uživatelé a zkontrolujte požadavky na souhlas správce**. Vyberte kontrolory pro tento pracovní postup ze skupiny uživatelů, kteří mají role Globální správce, správce cloudové aplikace a Správce aplikací.
   * **Vybraní uživatelé budou dostávat e-mailová oznámení pro žádosti**. Povolit nebo zakázat e-mailová oznámení kontrolorům, když je žádost vytvořena.  
   * **Vybraní uživatelé budou dostávat připomenutí vypršení platnosti žádosti**. Umožňuje povolit nebo zakázat e-mailová oznámení připomenutí kontrolorům, když žádost brzy vyprší.  
   * **Platnost žádosti o souhlas vyprší po (dny)**. Zadejte, jak dlouho zůstanou požadavky platné.

7. Vyberte **Uložit**. Může trvat až hodinu, než se funkce aktivuje.

> [!NOTE]
> Revidující pro tento pracovní postup můžete přidat nebo odebrat tak, že upravíte seznam **kontroloři žádosti o souhlas správce** . Všimněte si, že aktuální omezení této funkce je, že Recenzenti mohou zachovat možnost kontrolovat žádosti, které byly provedeny v době, kdy byly označeny jako kontrolor.

## <a name="how-users-request-admin-consent"></a>Jak si uživatelé žádají o souhlas správce

Po povolení pracovního postupu pro vyjádření souhlasu správce můžou uživatelé požádat o schválení správce pro aplikaci, které nemají oprávnění k souhlasu. Následující kroky popisují uživatelské prostředí při žádosti o schválení. 

1. Uživatel se pokusí přihlásit k aplikaci.

2. Zobrazí se zpráva **požadovaná při schválení** . Uživatel zadá odůvodnění pro potřebu přístupu k aplikaci a pak vybere **schválení žádosti**.

   ![Snímek obrazovky se zobrazí dialogové okno požadováno schválení, kde můžete požádat o schválení.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Zpráva **Odeslaná žádost** potvrzuje, že žádost byla odeslána správci. Pokud uživatel odešle několik požadavků, pošle se do správce jenom první požadavek.

   ![Snímek obrazovky se žádostí o potvrzení se odeslala.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Uživatel dostane e-mailové oznámení, když je jejich žádost schválená, zamítnutá nebo blokovaná. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Kontrola a provedení požadavků na žádosti o souhlas správce

Chcete-li zkontrolovat žádosti o souhlas správce a provést akci:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako jeden z registrovaných kontrolorů pracovního postupu správce souhlasu.
2. V horní části levé navigační nabídky vyberte **všechny služby** . Otevře se **rozšíření Azure Active Directory** .
3. Do vyhledávacího pole filtru zadejte "**Azure Active Directory**" a vyberte položku **Azure Active Directory** .
4. V navigační nabídce klikněte na **podnikové aplikace**.
5. V části **aktivita** vyberte **správce žádosti o souhlas (Preview)**.

   > [!NOTE]
   > Revidující uvidí pouze požadavky správce, které byly vytvořeny poté, co byli určeni jako kontrolor.

1. Vyberte aplikaci, která se požaduje.
2. Zkontrolujte podrobnosti o žádosti:  

   * Chcete-li zjistit, kdo požaduje přístup a proč, vyberte kartu **požadováno uživatelem** .
   * Chcete-li zjistit, jaká oprávnění aplikace požaduje, vyberte možnost **zkontrolovat oprávnění a souhlas**.

8. Vyhodnoťte požadavek a proveďte příslušnou akci:

   * **Schvalte žádost**. Pokud chcete žádost schválit, udělte aplikaci souhlas správce. Po schválení žádosti se všem žadatelům oznámí, že jim byl udělen přístup.  
   * **Zamítnout žádost** Pokud chcete žádost odmítnout, musíte zadat odůvodnění, které se poskytne všem žadatelům. Po zamítnutí žádosti budou všichni žadatelé upozorněni, že jim byl odepřen přístup k aplikaci. Zamítnutí žádosti nezabrání uživatelům v budoucnu požádat o souhlas správce do aplikace znovu.  
   * **Zablokuje požadavek**. Chcete-li zablokovat požadavek, je nutné zadat odůvodnění, které budou poskytnuty všem žadatelům. Po zablokování žádosti budou všichni žadatelé upozorněni, že jim byl odepřen přístup k aplikaci. Blokování žádosti vytvoří instanční objekt služby pro aplikaci ve vašem tenantovi v zakázaném stavu. Uživatelé nebudou moct aplikaci v budoucnu požádat o souhlas správce.
 
## <a name="email-notifications"></a>E-mailová oznámení
 
V případě nakonfigurovaného budou všichni kontroloři dostávat e-mailová oznámení v těchto případech:

* Vytvořila se nová žádost.
* Platnost žádosti vypršela.
* Žádost se blíží datu vypršení platnosti.  
 
Žadatelům budou doručena e-mailová oznámení v těchto případech:

* Odesílají novou žádost o přístup.
* Platnost jejich žádosti vypršela.
* Jejich žádost byla zamítnuta nebo zablokována.
* Jejich žádost byla schválena.
 
## <a name="audit-logs"></a>Protokoly auditu 
 
Následující tabulka popisuje scénáře a hodnoty auditu, které jsou k dispozici pro pracovní postup pro vyjádření souhlasu správce. 

> [!NOTE]
> Uživatelský kontext objektu actor v současnosti chybí ve všech scénářích. Toto je známé omezení verze Preview.


|Scenario  |Služba Audit  |Kategorie auditu  |Aktivita auditu  |Objekt actor auditu  |Omezení protokolu auditu  |
|---------|---------|---------|---------|---------|---------|
|Správce, který povoluje pracovní postup žádosti o souhlas        |Kontroly přístupu           |UserManagement           |Vytvořit šablonu zásad správného řízení          |Kontext aplikace            |Aktuálně nemůžete najít kontext uživatele.            |
|Správce zakázal pracovní postup žádosti o souhlas.       |Kontroly přístupu           |UserManagement           |Odstranit šablonu zásad správného řízení          |Kontext aplikace            |Aktuálně nemůžete najít kontext uživatele.           |
|Správce aktualizuje Konfigurace pracovního postupu souhlasu.        |Kontroly přístupu           |UserManagement           |Aktualizovat šablonu zásad správného řízení          |Kontext aplikace            |Aktuálně nemůžete najít kontext uživatele.           |
|Koncový uživatel, který vytváří žádost o souhlas správce pro aplikaci       |Kontroly přístupu           |Zásady         |Vytvořit žádost           |Kontext aplikace            |Aktuálně nemůžete najít kontext uživatele.           |
|Kontroloři, kteří schvalují žádost o souhlas správce       |Kontroly přístupu           |UserManagement           |Schválit všechny požadavky v podnikovém toku          |Kontext aplikace            |V současné době nemůžete najít kontext uživatele nebo ID aplikace, kterým byl udělen souhlas správce.           |
|Kontroloři odmítající žádost o souhlas správce       |Kontroly přístupu           |UserManagement           |Schválit všechny požadavky v podnikovém toku          |Kontext aplikace            | V současné době nemůžete najít kontext uživatele objektu actor, který zamítl žádost o souhlas správce.          |

## <a name="faq"></a>Časté otázky 

**Mám zapnutý tento pracovní postup, ale když testujete jeho funkčnost, nemůžete mi zobrazit, že se výzva k schválení vyžaduje?**

Po zapnutí funkce může koncovým uživatelům trvat až 60 minut, než se aktualizace zobrazí. To, jestli se konfigurace projeví správně, můžete ověřit zobrazením hodnoty **EnableAdminConsentRequests** v `https://graph.microsoft.com/beta/settings` rozhraní API.

**Proč se mi nezobrazují všechny nevyřízené žádosti?**

Recenzenti mohou zobrazit pouze požadavky správce, které byly vytvořeny poté, co byli určeni jako kontrolor. Takže pokud jste byli nedávno přidáni jako kontrolor, neuvidíte žádné požadavky, které byly vytvořeny před přiřazením.

**Proč se jako kontrolor zobrazuje více požadavků na stejnou aplikaci?**
  
Pokud vývojář aplikace nakonfiguroval svoji aplikaci tak, aby používala statický a dynamický souhlas s žádostí o přístup k datům koncového uživatele, zobrazí se dvě žádosti o souhlas správce. Jedna žádost představuje statická oprávnění a druhá představuje dynamická oprávnění.

**Je možné, že jako žadatel jsem kontroluje stav mého požadavku?**  

Ne, žadatelům nyní mohou získávat aktualizace pouze prostřednictvím e-mailových oznámení.

**Jako kontrolor je možné aplikaci schválit, ale ne pro všechny uživatele?**
 
Pokud máte obavy o udělení souhlasu správce a povolení používání aplikace všemi uživateli v tenantovi, doporučujeme, abyste žádost zamítli. Pak ručně udělte souhlas správce tím, že omezíte přístup k aplikaci tím, že vyžádáte přiřazení uživatele a do aplikace přiřadíte uživatele nebo skupiny. Další informace najdete v tématu [metody přiřazení uživatelů a skupin](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak se předávají aplikacím, najdete v tématu [Azure Active Directory souhlasu Framework](../develop/consent-framework.md).

[Konfigurace způsobu vyjadřování souhlasu koncových uživatelů s aplikacemi](configure-user-consent.md)

[Udělení souhlasu správce v rámci celého tenanta aplikaci](grant-admin-consent.md)

[Oprávnění a souhlas na platformě Microsoft identity](../develop/v2-permissions-and-consent.md)

[Azure AD v Microsoft Q&A](/answers/topics/azure-active-directory.html)