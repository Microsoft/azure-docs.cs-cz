---
title: Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory | Dokumentace Microsoftu
description: Referenční informace ke kódům chyb v sestavě aktivit přihlašování.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: dc01a775579455ae24c95ecc6f3858ce28149dea
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232293"
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory

Na základě informací poskytnutých sestavou přihlášení uživatelů najdete odpovědi na otázky tohoto typu:

- Kdo se přihlásil pomocí Azure Active Directory?
- Ke kterým aplikacím se někdo přihlásil?
- Která přihlášení selhala a proč?

Tento článek uvádí kódy chyb a související popisy. 

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
|20001|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|20012|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|20033|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40008|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40009|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40014|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|50000|Došlo k problému s naší přihlašovací službou. Pokud chcete tento problém vyřešit, [otevřete požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md).|
|50001|Hlavní název služby (SPN) nebyl v tomto tenantovi nalezen. Může k tomu dojít, když aplikaci nenainstaloval správce tenanta. Nebo V adresáři nebyl nalezen objekt prostředku nebo je neplatný.|
|50002|Během přihlašování došlo k chybě kvůli omezenému přístupu proxy v tenantovi. Pokud jde o vaši vlastní zásadu tenanta, můžete problém vyřešit změnou nastavení omezeného přístupu.|
|50003|Přihlášení se nezdařilo kvůli chybějícímu podpisovému klíči nebo certifikátu. Pravděpodobně v aplikaci není žádný klíč nakonfigurovaný. Podívejte se na řešení uvedená na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Pokud se vám přesto nedaří potíže vyřešit, kontaktujte vlastníka nebo správce aplikace.|
|50005|Uživatel se pokusil přihlásit k zařízení z platformy, kterou aktuálně zásady podmíněného přístupu nepodporují.|
|50006| Ověření podpisu se z důvodu neplatného podpisu nezdařilo. Podívejte se na řešení uvedené na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Pokud se vám přesto nedaří potíže vyřešit, kontaktujte vlastníka nebo správce aplikace.|
|50007|Šifrovací certifikát třetí strany nebyl pro tuto aplikaci nalezen. Pokud chcete tento problém vyřešit, [otevřete u Microsoftu požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md).|
|50008|V tokenu chybí nebo je špatně nakonfigurovaný kontrolní výraz SAML. Obraťte se na svého federačního zprostředkovatele.|
|50010|Ověření identifikátoru URI cílové skupiny pro aplikaci se nezdařilo, protože nejsou nakonfigurované cílové skupiny tokenu. Obraťte se na vlastníka aplikace.|
|50011|Adresa pro odpovědi chybí, je špatně nakonfigurována nebo neodpovídá adresám pro odpovědi, které jsou pro aplikaci nakonfigurované. Vyzkoušejte řešení uvedené na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Pokud se vám přesto nedaří potíže vyřešit, kontaktujte vlastníka nebo správce aplikace.|
|50013|Kontrolní výraz může být neplatný z různých důvodů: vydavatel tokenu se neshoduje s verzí rozhraní API v rámci jeho platného časového rozsahu; vypršela jeho platnost; je poškozený; nebo obnovovací token v kontrolním výrazu není primárním obnovovacím tokenem.|
|50017|Ověření certifikace se nezdařilo kvůli jednomu z následujících důvodů:<ul><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Nepodařilo se najít očekávaný CrlSegment.</li><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Distribuční bod rozdílového seznamu CRL je nakonfigurovaný bez odpovídajícího distribučního bodu CRL.</li><li>Z důvodu vypršení časového limitu se nepodařilo načíst platné segmenty CRL.</li><li>CRL nejde stáhnout.</li></ul>Obraťte se na správce tenanta.|
|50020|Uživatel nemá oprávnění – kvůli problému s verzí není možné vystavit tokeny – není zadané jméno vystavitele – problémy se jménem vystavitele (chybí nebo překračuje maximální délku). Obraťte se na vlastníka aplikace.|
|50027|Token JWT může být neplatný z následujících důvodů:<ul><li>Neobsahuje deklaraci identity hodnoty nonce nebo subjektu.</li><li>Identifikátor subjektu se neshoduje.</li><li>Deklarace identity idTokenu má duplicitní deklaraci identity.</li><li>Neočekávaný vystavitel</li><li>Neočekávaná cílová skupina</li><li>Nenachází se v platném časovém rozsahu. </li><li>Nesprávný formát tokenu</li><li>U externího ID tokenu od vystavitele se nezdařilo ověření podpisu.</li></ul>Obraťte se na vlastníka aplikace.|
|50029|Neplatný identifikátor URI – název domény obsahuje neplatné znaky. Obraťte se na správce tenanta.|
|50034|Uživatel v adresáři neexistuje. Obraťte se na správce tenanta.|
|50042|Chybí náhodný řetězec vyžadovaný ke generování párovaného identifikátoru. Obraťte se na správce tenanta.|
|50048|Subjekt se v klientském kontrolním výrazu neshoduje s deklarací identity vystavitele. Obraťte se na správce tenanta.|
|50050|Požadavek je poškozený. Obraťte se na vlastníka aplikace.|
|50053|Účet je uzamčen, protože uživatel se příliš mnohokrát pokusil přihlásit pomocí nesprávného ID uživatele nebo hesla.|
|50055|Neplatné heslo, zadáno prošlé heslo.|
|50056|Neplatné nebo chybějící heslo – heslo pro tohoto uživatele se v úložišti nenachází.|
|50057|Uživatelský účet je neaktivní. Správce tento účet zakázal.|
|50058|Aplikace se pokusila přihlásit v tichém režimu, ale uživatele se v tichém režimu přihlásit nepodařilo. Aplikace potřebuje spustit interaktivní tok, který uživatelům poskytne možnost přihlásit se. Obraťte se na vlastníka aplikace.|
|50059|Uživatel v adresáři neexistuje. Obraťte se na správce tenanta.|
|50061|Požadavek o odhlášení je neplatný. Obraťte se na vlastníka aplikace.|
|50072|Uživatelé se musí zaregistrovat k druhému faktoru ověřování (interaktivní).|
|50074|Uživatel neprošel ověřovacím testem MFA.|
|50076|Uživatel neprošel ověřovacím testem MFA (neinteraktivním).|
|50079|Uživatelé se musí zaregistrovat k druhému faktoru ověřování (neinteraktivní přihlášení).|
|50085|Obnovovací token vyžaduje přihlášení prostřednictvím distribučního bodu vydávání (IDP) sociální sítě. Požádejte uživatele, aby se zkusil pomocí uživatelského jména a hesla přihlásit znovu.|
|50089|Platnost tokenu toku vypršela – ověření se nezdařilo. Požádejte uživatele, aby se zkusil pomocí uživatelského jména a hesla přihlásit znovu.|
|50097|Vyžaduje se ověření zařízení – ID zařízení (deviceId) – deklarace identity DeviceAltSecId nejsou vyplněné nebo k identifikátoru zařízení neexistuje odpovídající zařízení.|
|50099|Podpis JWT je neplatný. Obraťte se na vlastníka aplikace.|
|50105|Přihlášený uživatel nemá přiřazenou roli k aktuálně přihlášené aplikaci. Přiřaďte uživatele k aplikaci. Další informace najdete na adrese: [https://docs.microsoft.com/en-us/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/en-us/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role).|
|50107|Požadovaný objekt sféry federace neexistuje. Obraťte se na správce tenanta.|
|50120|Problém s hlavičkou JWT. Obraťte se na správce tenanta.|
|50124|Transformace deklarací identity obsahuje neplatný vstupní parametr. Obraťte se na správce klienta, aby aktualizovat zásady.|
|50125|Přihlášení se přerušilo z důvodu resetování hesla nebo položky registrace hesla.|
|50126|Neplatné uživatelské jméno nebo heslo nebo Neplatné místní uživatelské jméno nebo heslo.|
|50127|Aby uživatel získal přístup k tomuto obsahu, musí si nainstalovat aplikaci zprostředkovatele.|
|50128|Neplatný název domény – identifikující informace o tenantovi se nenachází ani v požadavku a ani je není možné odvodit z poskytnutých přihlašovacích údajů.|
|50129|Zařízení není připojené k pracovišti. Připojení k pracovišti je nezbytné, aby se zařízení mohlo zaregistrovat.|
|50130|Hodnotu deklarace identity nelze interpretovat jako známou metodu ověřování.|
|50131|Používá se při různých chybách podmíněného přístupu. Například špatný stav zařízení s Windows, zablokování požadavku z důvodu podezřelé aktivity nebo rozhodnutí zásad přístupu a zabezpečení.|
|50132|Přihlašovací údaje byly odvolány z následujících důvodů:<ul><li>Artefakt jednotného přihlašování je neplatný nebo mu platnost vypršela.</li><li>Relace není pro aplikaci dostatečně nová.</li><li>Požadavek na přihlášení v tichému režimu byl odeslán, ale uživatelova relace s Azure AD je neplatná nebo skončila její platnost.</li></ul>|
|50133|Relace je neplatná z důvodu vypršení platnosti nebo nedávné změny hesla.|
|50135|Účet je ohrožený, vyžaduje se změna hesla.|
|50136|Přesměrování relace účtu spravované služby do aplikace – došlo k rozpoznání jediné relace účtu spravované služby. |
|50140|K této chybě došlo kvůli přerušení operace „Zůstat přihlášeni“ během přihlašování. Více informací získáte, když [otevřete požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
|50143|Neshoda relace – relace je neplatná, protože se tenant uživatele kvůli jinému prostředku neshoduje s nápovědou domény. Více informací získáte, když [otevřete požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby.|
|50144|Vypršela platnost hesla uživatele pro Active Directory. Vygenerujte uživateli nové heslo nebo koncového uživatele požádejte, ať se přihlásí pomocí nástroje pro samoobslužné resetování hesla.|
|50146|Tato aplikace se musí nakonfigurovat pomocí podpisového klíče, který je specifický pro danou aplikaci. Buď není pomocí tohoto klíče nakonfigurovaná nebo klíči vypršela platnost nebo ještě klíč v platnost nevstoupil. Obraťte se na vlastníka aplikace.|
|50148|Zadaný code_verifier neodpovídá přidruženému code_challenge, který byl dodán v žádosti o autorizaci pro PKCE. Obraťte se na vývojáře aplikace. |
|50155|Ověření zařízení se u tohoto uživatele nezdařilo.|
|50158|Externí bezpečnostní kontrola nebyla splněná.|
|50161|Deklarace identity odeslané externím zprostředkovatelem nejsou dostatečné nebo od externího zprostředkovatele chybí vyžádaná deklarace identity.|
|50166|Odeslání požadavku zprostředkovateli deklarací se nezdařilo.|
|50169|Tato sféra není nakonfigurovanou sférou aktuálního oboru názvů.|
|50172|Externí zprostředkovatel deklarací není schválený. Obraťte se na správce tenanta.|
|50173|Vyžaduje se nový token zabezpečení. Požádejte uživatele, ať se znovu přihlásí pomocí nových přihlašovacích údajů.|
|50177|Externí kontrola nepodporuje průchozí uživatele.|
|50178|Kontrola relace nepodporuje průchozí uživatele.|
|50180|Vyžaduje se integrované ověřování systému Windows. Povolte tenantovi bezproblémové jednotné přihlašování.|
|51001|Nápověda domény není u místního identifikátoru zabezpečení k dispozici – Místní hlavní název uživatele (UPN).|
|51004|Uživatelský účet v adresáři neexistuje.|
|51006|Vyžaduje se integrované ověřování systému Windows. Uživatel se přihlásil pomocí tokenu relace, kterému chybí integrované ověřování Windows (WIA) deklarace identity. Požádejte uživatele, aby se přihlásil znovu.|
|52004|Uživatel neposkytl souhlas pro přístup k prostředkům LinkedIn. |
|53000|Zásady podmíněného přístupu vyžadují zařízení, které dodržuje předpisy, ale dané zařízení je nedodržuje. Požádejte uživatele, aby své zařízení zaregistroval prostřednictvím schváleného zprostředkovatele správy mobilních zařízení, jako je Intune.|
|53001|Zásady podmíněného přístupu vyžadují zařízení, které je připojené k doméně, a toto zařízení k doméně připojené není. Požádejte uživatele, aby použil zařízení připojené k doméně.|
|53002|Použitá aplikace není schválenou aplikací pro podmíněný přístup. Pokud chce uživatel získat přístup, musí použít jednu z aplikací na seznamu schválených aplikací.|
|53003|Přístup se zablokoval kvůli zásadám podmíněného přístupu.|
|53004|Uživatel musí dokončit registraci pro vícefaktorové ověřování, než získá přístup k tomuto obsahu. Uživatel by se měl zaregistrovat k vícefaktorovému ověřování.|
|65001|Aplikace X nemá oprávnění pro přístup k aplikaci Y nebo oprávnění bylo odvoláno. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete správci tenanta žádost o autorizaci k jednání jménem aplikace Y pro prostředek Z.|
|65004|Uživatel odmítl souhlas s udělením přístupu aplikace. Požádejte uživatele, aby se zkusil znovu přihlásit a udělil aplikaci souhlas.|
|65005|Přístupový seznam požadovaných prostředků aplikace neobsahuje aplikace zjistitelné tímto prostředkem; klientská aplikace požadovala přístup k prostředku, který nebyl zadán v jejím přístupovém seznamu požadovaných prostředků; služba Graph vrátila chybnou žádost; nebo prostředek nebyl nalezen. Pokud aplikace podporuje SAML, pravděpodobně jste ji nakonfigurovali pomocí špatného identifikátoru (entity). Vyzkoušejte řešení uvedené pro SAML v následujícím odkazu: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|Udělení oprávnění může být neplatné z následujících důvodů:<ul><li>Požadovaný kontrolní výraz SAML 2.0 má neplatnou metodu potvrzení subjektu.</li><li>Tok OnBehalfOf aplikace se u V2 nepodporuje.</li><li>Primární obnovovací token není podepsaný klíčem relace.</li><li>Externí obnovovací token je neplatný.</li><li>Udělení oprávnění bylo získáno pro jiného tenanta.</li></ul>|
|70001|Aplikace X nebyla nalezena v tenantovi Y. K tomu může dojít v případě, že aplikace s identifikátorem X nebyla nainstalována správcem tenanta nebo nebyla odsouhlasena žádným uživatelem v tenantovi. Pravděpodobně jste špatně nakonfigurovali hodnotu identifikátoru aplikace nebo jste odeslali požadavek na ověření špatnému tenantovi.|
|70002|Aplikace vrátila neplatné přihlašovací údaje klienta. Obraťte se na vlastníka aplikace.|
|70003|Aplikace vrátila nepodporovaný typ udělení oprávnění. Obraťte se na vlastníka aplikace.|
|70004|Aplikace vrátila neplatný identifikátor URI pro přesměrování. Adresa přesměrování specifikovaná klientem neodpovídá žádné nakonfigurované adrese ani žádné adrese na seznamu schválených adres OIDC. Obraťte se na vlastníka aplikace.|
|70005|Aplikace vrátila nepodporovaný typ odpovědi z následujících důvodů:<ul><li>Typ odpovědi „token“ není v aplikaci povolený.</li><li>Typ odpovědi „id_token“ vyžaduje rozsah „OpenID“ – v parametru wctx obsahuje nepodporovanou hodnotu parametru OAuth.</li></ul>Obraťte se na vlastníka aplikace.|
|70007|Aplikace vrátila při žádosti o token nepodporovanou hodnotu „response_mode“. Obraťte se na vlastníka aplikace.|
|70008|Poskytnutému autorizačnímu kódu nebo obnovovacímu tokenu vypršela platnost – odvoláno. Požádejte uživatele, aby se zkusil přihlásit znovu.|
|70011|Rozsah požadovaný aplikací je neplatný. Obraťte se na vlastníka aplikace.|
|70012|Při ověřování uživatele účtu spravované služby (příjemce) došlo k chybě serveru. Zkuste to prosím znovu. Pokud k chybě dochází opakovaně, [otevřete požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md). |
|70018|Neplatný ověřovací kód z důvodu zadání špatného uživatelského kódu pro tok kódu zařízení. Autorizace se neschválila.|
|70019|Platnost ověřovacího kódu vypršela. Požádejte uživatele, aby se zkusil přihlásit znovu.|
|70037|Odpověď na výzvu nebyla správná. Vzdálený přístup k relaci byl odepřen.|
|75001|Během vytváření vazby zprávy SAML došlo k chybě.|
|75003|Aplikace vrátila chybu související s nepodporovanou vazbou (odpověď protokolu SAML nelze odeslat prostřednictvím jiné vazby než HTTP POST). Obraťte se na vlastníka aplikace.|
|75005|Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Obraťte se na vlastníka aplikace.|
|75008|Požadavek z aplikace byl odepřen, protože požadavek SAML měl neočekávaný cíl. Obraťte se na vlastníka aplikace.|
|75011|Metoda ověřování, kterou uživatel provedl ověření se službou, se neshoduje s požadovanou metodou ověřování. Obraťte se na vlastníka aplikace.|
|75016|Požadavek na ověření SAML2 má neplatný formát NameIdPolicy. Obraťte se na vlastníka aplikace.|
|80001|Ověřovací agent se nemohl připojit k Active Directory. Přesvědčte se, že je ověřovací agent nainstalovaný na počítači s připojením k doméně a že počítač má na dohled datové centrum, které může vyřídit požadavek na přihlášení uživatele.|
|80002|Vnitřní chyba. Vypršel časový limit žádosti o ověření hesla. Požadavek na ověření se nám nepodařilo interní hybridní službě identit odeslat. Pokud chcete získat o této chybě více informací, [otevřete požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md).|
|80003|Ověřovací agent přijal neplatnou odpověď. Při pokusu o ověření pomocí místní služby Active Directory došlo k neznámé chybě. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](fundamentals/active-directory-troubleshooting-support-howto.md).|
|80005|Ověřovací agent: během zpracování odpovědi od ověřovacího agenta došlo k neznámé chybě. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](fundamentals/active-directory-troubleshooting-support-howto.md).|
|80007|Ověřovací agent nebyl schopen ověřit heslo uživatele.|
|80010|Ověřovací agent nebyl schopen dešifrovat heslo. |
|80011|Ověřovacímu agentovi se nepodařilo získat šifrovací klíč.|
|80012|Uživatelé se pokusili přihlásit mimo povolenou dobu (je určená v AD).|
|80013|Pokus o ověření nebylo možné dokončit kvůli časovému posunu mezi počítačem, který má spuštěného ověřovacího agenta, a počítačem, který má spuštěnou službu AD. Opravte problémy se synchronizací času.|
|80014|Ověřovacímu agentovi vypršel časový limit. Více informací o této chybě získáte, když [otevřete požadavek na podporu](fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete kód chyby, ID korelace a datum a čas.|
|81001|Lístek Kerberos uživatele je příliš velký. K tomu může dojít, když je uživatel členem příliš mnoha skupin a lístek Kerberos obsahuje příliš velký počet členství. Snižte počet členství uživatele ve skupinách a zkuste to znovu.|
|81005|Ověřovací balíček není podporovaný.|
|81007|Tenant není povolený pro bezproblémové jednotné přihlašování.|


## <a name="next-steps"></a>Další postup

Další informace najdete v tématu o [sestavách aktivit přihlašování na portálu Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
