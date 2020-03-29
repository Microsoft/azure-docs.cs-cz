---
title: Konfigurace pracovního postupu souhlasu správce – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat způsob, jak mohou koncoví uživatelé požádat o přístup k aplikacím, které vyžadují souhlas správce.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430199"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurace pracovního postupu souhlasu správce (preview)

Tento článek popisuje, jak povolit funkci pracovního postupu souhlasu správce (preview), která koncovým uživatelům umožňuje požádat o přístup k aplikacím, které vyžadují souhlas správce.

Bez pracovního postupu souhlasu správce bude uživatel v tenantovi, kde je zakázán souhlas uživatele, zablokován při pokusu o přístup k libovolné aplikaci, která vyžaduje oprávnění k přístupu k datům organizace. Uživateli se zobrazí obecná chybová zpráva, že nemá oprávnění k přístupu k aplikaci, a měl by požádat o pomoc svého správce. Ale často, uživatel neví, na koho se obrátit, tak se buď vzdát, nebo vytvořit nový místní účet v aplikaci. I když je správce upozorněn, není vždy zjednodušený proces, který by správci pomohl udělit přístup a upozornit jejich uživatele.
 
Pracovní postup souhlasu správce poskytuje správcům bezpečný způsob, jak udělit přístup k aplikacím, které vyžadují schválení správce. Pokud se uživatel pokusí o přístup k aplikaci, ale nemůže poskytnout souhlas, může odeslat žádost o schválení správce. Žádost je odeslána e-mailem správcům, kteří byli označeni jako recenzenti. Recenzent provede akci na žádost a uživatel je upozorněn na akci.

Chcete-li schválit žádosti, musí být recenzent globální správce, správce cloudových aplikací nebo správce aplikací. Recenzent již musí mít přiřazenou jednu z těchto rolí správců. jednoduše je označíte jako recenzenta, což nezvýší jejich oprávnění.

## <a name="enable-the-admin-consent-workflow"></a>Povolení pracovního postupu souhlasu správce

Povolení pracovního postupu souhlasu správce maže a zvolte recenzenty:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. V horní části levé navigační nabídky klikněte na **Všechny služby.** Otevře se **rozšíření Azure Active Directory.**
3. Do vyhledávacího pole filtru zadejte "**Azure Active Directory**" a vyberte **položku Služby Azure Active Directory.**
4. V navigační nabídce klepněte na **položku Podnikové aplikace**. 
5. V části **Manage**vyberte **Uživatelská nastavení**.
6. V části **Žádosti o souhlas správce (Preview)** mohou **uživatelé požádat o souhlas správce s aplikacemi, se kterými nemohou souhlasit.** **Yes**

   ![Konfigurace nastavení pracovního postupu souhlasu správce](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Nakonfigurujte tahle nastavení:

   * **Vyberte uživatele, kteří mají zkontrolovat žádosti o souhlas správce**. Vyberte recenzenty pro tento pracovní postup ze sady uživatelů, kteří mají role globálního správce, správce cloudových aplikací a správce aplikací.
   * **Vybraní uživatelé obdrží e-mailová oznámení o žádostech**. Povolit nebo zakázat e-mailová oznámení recenzentům při požadavku.  
   * **Vybraní uživatelé obdrží připomenutí vypršení platnosti žádosti**. Povolte nebo zakažte e-mailová oznámení připomenutí recenzentům, když platnost žádosti vyprší.  
   * **Platnost žádosti o souhlas vyprší po (dnech).** Určete, jak dlouho zůstávají požadavky platné.

7. Vyberte **Uložit**. Může trvat až hodinu, než se funkce aktivuje.

> [!NOTE]
> Recenzenti tohoto pracovního postupu můžete přidat nebo odebrat úpravou seznamu **Recenzenti select admin consent.** Všimněte si, že aktuální omezení této funkce je, že recenzenti mohou zachovat možnost kontrolovat požadavky, které byly provedeny, zatímco oni byli určeni jako recenzent.

## <a name="how-users-request-admin-consent"></a>Jak uživatelé požadují souhlas správce

Po povolení pracovního postupu souhlasu správce mohou uživatelé požádat o schválení správce pro aplikaci, se kterou nemají oprávnění k udělení souhlasu. Následující kroky popisují uživatelské zkušenosti při žádosti o schválení. 

1. Uživatel se pokusí přihlásit k aplikaci.

2. Zobrazí se zpráva **požadované schválení.** Uživatel zadá odůvodnění, že potřebuje přístup k aplikaci, a pak vybere **možnost Požádat o schválení**.

   ![Žádost a odůvodnění uživatele souhlasu správce](media/configure-admin-consent-workflow/end-user-justification.png)

3. **Zpráva odeslaná požadavkem** potvrzuje, že žádost byla odeslána správci. Pokud uživatel odešle několik požadavků, pouze první požadavek je odeslána na správce.

   ![Žádost a odůvodnění uživatele souhlasu správce](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Uživatel obdrží e-mailové oznámení, když je jeho žádost schválena, zamítnuta nebo zablokována. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Kontrola a přijetí opatření v žádostech o souhlas správce

Kontrola žádostí o souhlas správce a přijetí opatření:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako jeden z registrovaných recenzentů pracovního postupu souhlasu správce.
2. V horní části levé navigační nabídky vyberte **Všechny služby.** Otevře se **rozšíření Azure Active Directory.**
3. Do vyhledávacího pole filtru zadejte "**Azure Active Directory**" a vyberte položku **Služby Azure Active Directory.**
4. V navigační nabídce klepněte na **položku Podnikové aplikace**.
5. V části **Aktivita**vyberte **Požadavky na souhlas správce (náhled).**

   > [!NOTE]
   > Recenzenti uvidí pouze požadavky správce, které byly vytvořeny poté, co byly označeny jako recenzent.

1. Vyberte požadovanou aplikaci.
2. Zkontrolujte podrobnosti o žádosti:  

   * Chcete-li zjistit, kdo žádá o přístup a proč, vyberte kartu **Požadováno podle.**
   * Chcete-li zjistit, jaká oprávnění aplikace požaduje, vyberte **možnost Zkontrolovat oprávnění a souhlas**.

8. Vyhodnoťte žádost a přijměte příslušnou akci:

   * **Schválení žádosti**. Chcete-li žádost schválit, udělte jí souhlas správce. Jakmile je žádost schválena, všichni žadatelé jsou upozorněni, že jim byl udělen přístup.  
   * **Odepřít požadavek**. Chcete-li žádost zamítnout, musíte poskytnout odůvodnění, které bude poskytnuto všem žádostem. Jakmile je žádost zamítnuta, všichni žadatelé jsou upozorněni, že jim byl odepřen přístup k aplikaci. Zamítnutí žádosti nezabrání uživatelům v tom, aby v budoucnu znovu požádali o souhlas správce s aplikací.  
   * **Blokovat požadavek**. Chcete-li požadavek zablokovat, musíte zadat odůvodnění, které bude poskytnuto všem žádostem. Jakmile je požadavek zablokován, všichni žadatelé jsou upozorněni, že jim byl odepřen přístup k aplikaci. Blokování požadavku vytvoří objekt instančního objektu pro aplikaci ve vašem tenantovi v zakázaném stavu. Uživatelé nebudou moci v budoucnu požádat o souhlas správce s aplikací.
 
## <a name="email-notifications"></a>E-mailová oznámení
 
Pokud je nakonfigurováno, všichni recenzenti obdrží e-mailová oznámení, když:

* Byl vytvořen nový požadavek.
* Platnost žádosti vypršela.
* Žádost se blíží datu vypršení platnosti  
 
Žadatelé obdrží e-mailová oznámení, když:

* Podají novou žádost o přístup
* Jejich žádost vypršela.
* Jejich žádost byla zamítnuta nebo zablokována.
* Jejich žádost byla schválena
 
## <a name="audit-logs"></a>Protokoly auditu 
 
Níže uvedená tabulka popisuje scénáře a hodnoty auditu, které jsou k dispozici pro pracovní postup souhlasu správce. 

> [!NOTE]
> Uživatelský kontext objektu actor auditu aktuálně chybí ve všech scénářích. Toto je známé omezení ve verzi preview.


|Scénář  |Auditní služba  |Kategorie auditu  |Auditní činnost  |Auditní aktér  |Omezení protokolu auditu  |
|---------|---------|---------|---------|---------|---------|
|Správce, který povoluje pracovní postup žádosti o souhlas        |Kontroly přístupu           |Správa uživatelů           |Vytvořit šablonu zásad zásad správného řízení          |Kontext aplikace            |V současné době nelze najít kontext uživatele            |
|Správce zanese pracovní postup žádosti o souhlas       |Kontroly přístupu           |Správa uživatelů           |Odstranit šablonu zásad zásad správného řízení          |Kontext aplikace            |V současné době nelze najít kontext uživatele           |
|Správce aktualizuje konfigurace pracovního postupu souhlasu        |Kontroly přístupu           |Správa uživatelů           |Aktualizovat šablonu zásad zásad správného řízení          |Kontext aplikace            |V současné době nelze najít kontext uživatele           |
|Koncový uživatel, který pro aplikaci vytvořil žádost o souhlas správce       |Kontroly přístupu           |Zásada         |Vytvořit požadavek           |Kontext aplikace            |V současné době nelze najít kontext uživatele           |
|Recenzenti, kteří schvalují žádost o souhlas správce       |Kontroly přístupu           |Správa uživatelů           |Schválit všechny požadavky v obchodním toku          |Kontext aplikace            |V současné době nemůžete najít kontext uživatele nebo ID aplikace, které bylo uděleno souhlas správce.           |
|Recenzenti, kteří odmítli žádost o souhlas správce       |Kontroly přístupu           |Správa uživatelů           |Schválit všechny požadavky v obchodním toku          |Kontext aplikace            | V současné době nelze najít uživatelský kontext objektu actor, který odmítl žádost o souhlas správce          |

## <a name="faq"></a>Nejčastější dotazy 

**Po zapnutí tohoto pracovního postupu, ale proč se při testování funkcí nezobrazí nová výzva "Vyžadováno schválení", která mi umožňuje požádat o přístup?**

Po zapnutí funkce může trvat až 60 minut, než koncoví uživatelé aktualizaci uvidí. Můžete ověřit, že konfigurace správně se projevila zobrazením hodnoty `https://graph.microsoft.com/beta/settings` **EnableAdminConsentRequests** v rozhraní API.

**Proč se jako recenzent nezobrazují všechny nevyřízené žádosti?**

Recenzenti mohou zobrazit pouze požadavky správce, které byly vytvořeny poté, co byly označeny jako recenzent. Pokud jste tedy byli nedávno přidáni jako recenzent, neuvidíte žádné požadavky, které byly vytvořeny před přiřazením.

**Proč se jako recenzent zobrazí více žádostí o stejnou aplikaci?**
  
Pokud vývojář aplikace nakonfiguroval svou aplikaci tak, aby používala statický a dynamický souhlas k žádosti o přístup k datům svého koncového uživatele, zobrazí se dvě žádosti o souhlas správce. Jeden požadavek představuje statická oprávnění a druhý představuje dynamická oprávnění.

**Mohu jako žadatel zkontrolovat stav své žádosti?**  

Ne, prozatím mohou žadatelé dostávat aktualizace pouze prostřednictvím e-mailových oznámení.

**Jako recenzent, je možné schválit žádost, ale ne pro každého?**
 
Pokud máte obavy o udělení souhlasu správce a povolení všem uživatelům v tenantovi používat aplikaci, doporučujeme odmítnout žádost. Potom ručně udělte souhlas správce omezením přístupu k aplikaci vyžadováním přiřazení uživatele a přiřazením uživatelů nebo skupin k aplikaci. Další informace naleznete [v tématu Metody přiřazování uživatelů a skupin](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Další kroky

Další informace o souhlasu s aplikacemi najdete v [tématu Rozhraní pro souhlas služby Azure Active Directory](../develop/consent-framework.md).

[Konfigurace způsobu, jakým koncoví uživatelé uzaměňují souhlas s aplikacemi](configure-user-consent.md)

[Udělení souhlasu správce pro celý klient s aplikací](grant-admin-consent.md)

[Oprávnění a souhlas v platformě microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD na StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
