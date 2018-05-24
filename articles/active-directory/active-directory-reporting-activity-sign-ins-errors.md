---
title: Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory | Microsoft Docs
description: Referenční informace ke kódům chyb v sestavě aktivit přihlašování.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ebad9304c38333173cec66c6b5574a9b45b17cd1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory

Na základě informací poskytnutých sestavou přihlášení uživatelů najdete odpovědi na otázky tohoto typu:

- Kdo se přihlásil pomocí Azure Active Directory?
- Ke kterým aplikacím se někdo přihlásil?
- Která přihlášení selhala a proč?

Toto téma uvádí kódy chyb a související popisy. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak se dají zobrazit přihlášení, která selhala? 

Váš první vstupní bod pro všechna data aktivit přihlašování je **[Přihlášení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** v části **Aktivity** služby **Azure Active**.


![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Aktivita přihlašování")

V sestavě přihlášení můžete zobrazit všechna přihlášení, která selhala, tím, že jako **Stav přihlášení** vyberete **Selhání**.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Aktivita přihlašování")

Kliknutí na položku v zobrazeném seznamu otevře okno **Podrobnosti o aktivitě: Přihlášení**. Toto zobrazení poskytuje všechny podrobnosti, které Azure Active Directory o přihlášeních sleduje, včetně **kódu chyby přihlášení** a **důvodu selhání**.

![Aktivita přihlašování](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Aktivita přihlašování")


Jako alternativu k webu Azure Portal můžete pro přístup k datům přihlašování použít také [rozhraní API pro generování sestav](active-directory-reporting-api-getting-started-azure-portal.md).


Následující část poskytuje úplný přehled o všech možných chybách a související popisy. 

## <a name="error-codes"></a>Kódy chyb

|Chyba|Popis|
|---|---|
|50001|Objekt služby X nebyl nalezen v tenantovi Y. K tomu může dojít v případě, že aplikace nebyla nainstalována správcem tenanta. Nebo V adresáři nebyl nalezen objekt prostředku nebo je neplatný.|
|50008|V tokenu chybí nebo jsou špatně nakonfigurované kontrolní výrazy SAML.|
|50011|Adresa pro odpovědi chybí, je špatně nakonfigurována nebo neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci.|
|50012|Uživatel ohlásil podvod během vícefaktorového ověřování.|
|50053|Účet je uzamčen, protože uživatel se příliš mnohokrát pokusil přihlásit pomocí nesprávného ID uživatele nebo hesla.|
|50054|K ověřování se používá staré heslo.|
|50055|Neplatné heslo, zadáno prošlé heslo.|
|50057|Uživatelský účet je neaktivní.|
|50058|V zadaných přihlašovacích údajích nebyly nalezeny žádné informace o identitě uživatele nebo Uživatel nebyl nalezen v tenantovi nebo Byla odeslána žádost o přihlášení v tichém režimu, ale žádný uživatel není přihlášen nebo Službě se nepodařilo ověřit uživatele.|
|50072|Uživatelé se musí zaregistrovat k druhému faktoru ověřování (interaktivní).|
|50074|Uživatel neprošel ověřovacím testem MFA.|
|50076|Uživatel neprošel ověřovacím testem MFA (neinteraktivním).|
|50079|Uživatel se musí zaregistrovat k druhému faktoru ověřování.|
|50089|Ověření tokenu toku se nezdařilo, protože platnost tokenu vypršela.|
|50097|Zařízení není ověřeno.|
|50105|Přihlášený uživatel není přiřazen k roli pro tuto aplikaci.|
|50125|Přihlášení se přerušilo z důvodu resetování hesla nebo položky registrace hesla.|
|50126|Neplatné uživatelské jméno nebo heslo nebo Neplatné místní uživatelské jméno nebo heslo.|
|50127|Aby uživatel získal přístup k tomuto obsahu, musí si nainstalovat aplikaci zprostředkovatele.|
|50129|Zařízení není připojené k pracovišti. Připojení k pracovišti je nezbytné, aby se zařízení mohlo zaregistrovat.|
|50131|Používá se při různých chybách podmíněného přístupu. Např. špatný stav zařízení s Windows, zablokování požadavku z důvodu podezřelé aktivity nebo rozhodnutí zásad přístupu a zabezpečení.|
|50133|Relace je neplatná z důvodu vypršení platnosti nebo nedávné změny hesla.|
|50140|Uživateli se zobrazila výzva vyjádřit souhlas, aby mohl zůstat přihlášený k zařízení.|
|50144|Vypršela platnost hesla uživatele pro Active Directory.|
|53000|Zásady podmíněného přístupu vyžadují zařízení, které dodržuje předpisy, ale dané zařízení je nedodržuje.|
|53003|Přístup se zablokoval kvůli zásadám podmíněného přístupu.|
|65001|Aplikace X nemá oprávnění pro přístup k aplikaci Y nebo oprávnění bylo odvoláno. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete správci tenanta žádost o autorizaci k jednání jménem aplikace Y pro prostředek Z.|
|65005|Přístupový seznam požadovaných prostředků aplikace neobsahuje aplikace zjistitelné tímto prostředkem nebo Klientská aplikace požadovala přístup k prostředku, který nebyl zadán v jejím přístupovém seznamu požadovaných prostředků nebo Služba Graph vrátila chybnou žádost nebo prostředek nebyl nalezen.|
|70001|Aplikace X nebyla nalezena v tenantovi Y. K tomu může dojít v případě, že aplikace nebyla nainstalována správcem tenanta nebo nebyla odsouhlasena žádným uživatelem v tenantovi. Možná jste odeslali žádost o ověření do nesprávného tenanta.|
|80001|Ověřovací agent se nemohl připojit k Active Directory.|
|80002|Vypršel časový limit žádosti o ověření hesla ověřovacího agenta.|
|80003|Ověřovací agent přijal neplatnou odpověď.|
|80004|V žádosti o přihlášení byl použit nesprávný hlavní název uživatele (UPN).|
|80005|Ověřovací agent: Došlo k chybě.|
|80007|Ověřovací agent nebyl schopen ověřit heslo uživatele.|
|80010|Ověřovací agent nebyl schopen dešifrovat heslo.|
|80011|Ověřovací agent nebyl schopen získat dešifrovací klíč.|
|81001|Lístek Kerberos uživatele je příliš velký.|
|81002|Nepodařilo se ověřit lístek Kerberos uživatele.|
|81003|Nepodařilo se ověřit lístek Kerberos uživatele.|
|81004|Pokus o ověření protokolu Kerberos selhal.|
|81008|Nepodařilo se ověřit lístek Kerberos uživatele.|
|81009|Nepodařilo se ověřit lístek Kerberos uživatele.|
|81010|Bezproblémové jednotné přihlašování selhalo, protože vypršela platnost lístku Kerberos uživatele nebo je neplatný.|
|81011|Nepodařilo se najít objekt uživatele na základě informací v lístku Kerberos uživatele.|
|81012|Uživatel, který se pokouší přihlásit k Azure AD, je jiný než uživatel přihlášený na zařízení.|
|81013|Nepodařilo se najít objekt uživatele na základě informací v lístku Kerberos uživatele.|
|90014|Používá se v různých případech, kdy v přihlašovacích údajích není přítomné očekávané pole.|
|90093|Graph pro žádost vrátil kód chyby Zakázáno.|
|90094|Vyžaduje se souhlas správce.|
## <a name="next-steps"></a>Další kroky

Další podrobnosti najdete v tématu [Sestavy aktivit přihlašování na portálu Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
