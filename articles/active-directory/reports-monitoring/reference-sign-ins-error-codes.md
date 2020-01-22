---
title: Kódy chyb na portálu Azure Active Directory | Microsoft Docs
description: Referenční informace ke kódům chyb v sestavě aktivit přihlašování.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7a025835275169b260dfd1f91b65341b5ba02ff
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294095"
---
# <a name="sign-in-activity-report-error-codes"></a>Kódy chyb sestav aktivit přihlašování 

Na základě informací poskytnutých [sestavou přihlášení uživatelů](concept-sign-ins.md)najdete odpovědi na otázky, jako například:

- Kdo se přihlásil k aplikaci?
- Které aplikace se přihlásily k?
- Která přihlášení se nezdařila a proč?

Pokud se přihlášení nezdaří, zobrazí se kód chyby odpovídající selhání. V tomto článku jsou uvedeny kódy chyb a jejich popisy spolu s navrhovaným postupem akce. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak se dají zobrazit přihlášení, která selhala? 

V nabídce [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**nebo vyhledejte a vyberte **Azure Active Directory** na libovolné stránce.

![Vyberte Azure Active Directory](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

V části **monitorování**vyberte **přihlášení** a otevřete [sestavu přihlášení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Přihlašovací aktivita](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "Aktivita přihlášení")

Filtrováním sestavy zobrazíte všechna neúspěšná přihlášení výběrem možnosti **selhání** v rozevíracím seznamu **stav přihlášení** .

![Přihlašovací aktivita](./media/reference-sign-ins-error-codes/06.png "Aktivita přihlášení")

Když vyberete položku z filtrovaného seznamu, otevře se okno **Podrobnosti aktivity: přihlášení** . Toto zobrazení obsahuje další informace o neúspěšném přihlašovacím událostech, včetně **kódu chyby přihlášení** a **důvodu selhání**.

![Přihlašovací aktivita](./media/reference-sign-ins-error-codes/05.png "Aktivita přihlášení")

Pomocí [rozhraní API pro vytváření sestav](concept-reporting-api.md)můžete také programově získat přístup k přihlašovacím údajům.

## <a name="error-codes"></a>Kódy chyb


|Chyba|Popis|
|---|---|
|16000|Toto je podrobný popis interní implementace, nikoli chybový stav. Tento odkaz můžete bezpečně ignorovat.|
|20001|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|20012|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|20033|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40008|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40009|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40014|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|50000|Došlo k problému s naší přihlašovací službou. Pokud chcete tento problém vyřešit, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|50001|Hlavní název služby (SPN) nebyl v tomto tenantovi nalezen. K tomu může dojít, pokud aplikace nebyla nainstalována správcem tenanta, nebo pokud objekt zabezpečení prostředku nebyl v adresáři nalezen nebo je neplatný.|
|50002|Během přihlašování došlo k chybě kvůli omezenému přístupu proxy v tenantovi. Pokud se jedná o vaše vlastní zásady tenanta, můžete tento problém vyřešit změnou nastavení klienta s omezeným přístupem.|
|50003|Přihlášení se nezdařilo kvůli chybějícímu podpisovému klíči nebo certifikátu. Pravděpodobně v aplikaci není žádný klíč nakonfigurovaný. Podívejte se na řešení uvedená na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Pokud potíže potrvají, obraťte se na vlastníka aplikace nebo správce aplikace.|
|50005|Uživatel se pokusil přihlásit k zařízení z platformy, která je aktuálně Nepodporovaná pomocí zásad podmíněného přístupu.|
|50006| Ověření podpisu se z důvodu neplatného podpisu nezdařilo. Podívejte se na řešení uvedené na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Pokud potíže potrvají, obraťte se na vlastníka aplikace nebo správce aplikace.|
|50007|Šifrovací certifikát třetí strany nebyl pro tuto aplikaci nalezen. [Otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s Microsoftem, abyste mohli tento problém vyřešit.|
|50008|V tokenu chybí nebo je špatně nakonfigurovaný kontrolní výraz SAML. Obraťte se na svého federačního zprostředkovatele.|
|50010|Ověření identifikátoru URI cílové skupiny pro aplikaci se nezdařilo, protože nejsou nakonfigurované cílové skupiny tokenu. Obraťte se na vlastníka aplikace a požádejte ho o vyřešení.|
|50011|Adresa pro odpovědi chybí, je špatně nakonfigurována nebo neodpovídá adresám pro odpovědi, které jsou pro aplikaci nakonfigurované. Zkuste řešení uvedené na [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Pokud potíže potrvají, obraťte se na vlastníka aplikace nebo správce aplikace.|
|50012| Toto je obecná chybová zpráva, která indikuje, že ověření se nezdařilo. K tomu může dojít z důvodů, například chybějících nebo neplatných přihlašovacích údajů nebo deklarací identity v žádosti. Zajistěte, aby byl požadavek odeslán se správnými přihlašovacími údaji a deklaracemi. |
|50013|Kontrolní výraz je neplatný z důvodu různých důvodů. Vystavitel tokenu se například neshoduje s verzí rozhraní API v rámci platného časového rozsahu, token vypršel nebo je poškozen nebo obnovovací token v kontrolním výrazu není primární obnovovací token.|
|50017|Ověření certifikace se nezdařilo kvůli jednomu z následujících důvodů:<ul><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Nepodařilo se najít očekávaný CrlSegment.</li><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Distribuční bod rozdílového seznamu CRL je nakonfigurovaný bez odpovídajícího distribučního bodu CRL.</li><li>Z důvodu vypršení časového limitu se nepodařilo načíst platné segmenty CRL.</li><li>CRL nejde stáhnout.</li></ul>Obraťte se na správce klienta.|
|50020|Uživatel je neautorizovaný z některého z následujících důvodů.<ul><li>Uživatel se pokouší přihlásit pomocí účtu MSA s koncovým bodem V1.</li><li>Uživatel v tenantovi neexistuje.</li></ul> Obraťte se na vlastníka aplikace.|
|50027|Token JWT může být neplatný z následujících důvodů:<ul><li>Neobsahuje deklaraci identity hodnoty nonce nebo subjektu.</li><li>Identifikátor subjektu se neshoduje.</li><li>Deklarace identity idTokenu má duplicitní deklaraci identity.</li><li>Neočekávaný vystavitel</li><li>Neočekávaná cílová skupina</li><li>Nenachází se v platném časovém rozsahu. </li><li>Nesprávný formát tokenu</li><li>U externího ID tokenu od vystavitele se nezdařilo ověření podpisu.</li></ul>Obraťte se na vlastníka aplikace.|
|50029|Neplatný identifikátor URI – název domény obsahuje neplatné znaky. Obraťte se na správce klienta.|
|50034|Uživatel v adresáři neexistuje. Obraťte se na správce tenanta.|
|50042|V zásadě chybí hodnota Salt požadovaná pro generování identifikátoru ID. Obraťte se na správce klienta.|
|50048|Subjekt se v klientském kontrolním výrazu neshoduje s deklarací identity vystavitele. Obraťte se na správce klienta.|
|50050|Požadavek je poškozený. Obraťte se na vlastníka aplikace.|
|50053|Účet je zamčený, protože uživatel se pokusil o přihlášení příliš mnohokrát pomocí nesprávného ID uživatele nebo hesla.|
|50055|Neplatné heslo, zadáno prošlé heslo.|
|50056|Heslo pro tohoto uživatele v úložišti neexistuje nebo je neplatné nebo má hodnotu null.|
|50057|Uživatelský účet je neaktivní. Správce tento účet zakázal.|
|50058|Aplikace se pokusila přihlásit v tichém režimu, ale uživatele se v tichém režimu přihlásit nepodařilo. Aplikace potřebuje spustit interaktivní tok, který uživatelům umožní přihlásit se k možnosti přihlášení. Obraťte se na vlastníka aplikace.|
|50059|Uživatel v adresáři neexistuje. Obraťte se na správce tenanta.|
|50061|Požadavek o odhlášení je neplatný. Obraťte se na vlastníka aplikace.|
|50072|Uživatel se musí zaregistrovat pro dvojúrovňové ověřování (interaktivní).|
|50074|Uživatel neprošel ověřovacím testem MFA.|
|50076|Uživatel nevyhověl Challenge MFA (neinteraktivní).|
|50078|Předložení služby Multi-Factor Authentication vypršelo, je nutné aktualizovat službu Multi-Factor Authentication pro přístup.|
|50079|Uživatel se musí zaregistrovat pro dvojúrovňové ověřování (neinteraktivní přihlášení).|
|50085|Obnovovací token vyžaduje přihlášení prostřednictvím distribučního bodu vydávání (IDP) sociální sítě. Přihlaste se, aby se uživatel pokusil znovu přihlásit pomocí svého uživatelského jména a hesla.|
|50089|Vypršela platnost tokenu toku – ověření se nezdařilo. Zkuste se znovu přihlásit pomocí uživatelského jména a hesla.|
|50097|Vyžaduje se ověřování zařízení. K tomu může dojít, protože deklarace DeviceId nebo DeviceAltSecId jsou **null**nebo pokud neexistuje žádné zařízení odpovídající identifikátoru zařízení.|
|50099|Podpis JWT je neplatný. Obraťte se na vlastníka aplikace.|
|50105|Přihlášený uživatel nemá přiřazenou roli k aktuálně přihlášené aplikaci. Přiřaďte uživatele k aplikaci. Další informace najdete na adrese: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role).|
|50107|Požadovaný objekt sféry federace neexistuje. Obraťte se na správce klienta.|
|50120|Problém s hlavičkou JWT. Obraťte se na správce klienta.|
|50124|Transformace deklarací identity obsahuje neplatný vstupní parametr. Obraťte se na správce klienta, aby zásady aktualizoval.|
|50125|Přihlášení bylo přerušeno z důvodu resetování hesla nebo zápisu hesla.|
|50126|Neplatné uživatelské jméno nebo heslo nebo neplatné místní uživatelské jméno nebo heslo.|
|50127|Aby mohl uživatel získat přístup k tomuto obsahu, musí nainstalovat aplikaci zprostředkovatele.|
|50128|Neplatný název domény – nenašly se žádné informace zjištěné v žádosti ani žádné informace, které se nevedly v zadaných přihlašovacích údajích.|
|50129|Zařízení není připojené k pracovišti – k registraci zařízení je potřeba **připojení k síti na pracovišti** .|
|50130|Hodnotu deklarace identity nejde interpretovat jako známou metodu ověřování.|
|50131|Používá se v různých chybách podmíněného přístupu. Například špatný stav zařízení s Windows, zablokování požadavku z důvodu podezřelé aktivity nebo rozhodnutí zásad přístupu a zabezpečení.|
|50132|Přihlašovací údaje byly odvolány z následujících důvodů:<ul><li>Artefakt jednotného přihlašování je neplatný nebo mu platnost vypršela.</li><li>Relace není pro aplikaci dostatečně nová.</li><li>Požadavek na přihlášení v tichému režimu byl odeslán, ale uživatelova relace s Azure AD je neplatná nebo skončila její platnost.</li></ul>|
|50133|Relace je neplatná z důvodu vypršení platnosti nebo nedávné změny hesla.|
|50135|Změna hesla se vyžaduje kvůli riziku účtu.|
|50136|Byla zjištěna relace MSA do relace MSA pro jednotlivé aplikace. |
|50140|K této chybě došlo kvůli přerušení operace „Zůstat přihlášeni“ během přihlašování. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
|50143|Neshoda relací – relace je neplatná, protože klient uživatele se neshoduje s pomocným parametrem domény z důvodu jiného prostředku. Pokud chcete získat další podrobnosti,  [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s ID korelace, ID požadavku a kódem chyby.|
|50144|Vypršela platnost hesla uživatele pro Active Directory. Vygenerujte nové heslo pro uživatele nebo nechte koncového uživatele pomocí nástroje pro samoobslužné obnovení.|
|50146|Tato aplikace se musí nakonfigurovat pomocí podpisového klíče, který je specifický pro danou aplikaci. Buď není pomocí tohoto klíče nakonfigurovaná nebo klíči vypršela platnost nebo ještě klíč v platnost nevstoupil. Obraťte se na vlastníka aplikace.|
|50148|Zadaný code_verifier neodpovídá přidruženému code_challenge, který byl dodán v žádosti o autorizaci pro PKCE. Obraťte se na vývojáře aplikace. |
|50155|Pro tohoto uživatele se nepovedlo ověřování zařízení.|
|50158|Nebylo splněno externí bezpečnostní výzvy.|
|50161|Deklarace identity odeslané externím poskytovatelem nejsou dostatečné nebo chybí deklarace identity vyžadovaná externím zprostředkovatelem.|
|50166|Nepovedlo se odeslat požadavek zprostředkovateli deklarací.|
|50169|Tato sféra není nakonfigurovanou sférou aktuálního oboru názvů.|
|50172|Externí zprostředkovatel deklarací není schválený. Kontaktujte správce klienta.|
|50173|Vyžaduje se nový token zabezpečení. Přihlaste se znovu pomocí nových přihlašovacích údajů.|
|50177|Pro průchozí uživatele není externí výzva podporována.|
|50178|Řízení relace není pro průchozí uživatele podporováno.|
|50180|Vyžaduje se integrované ověřování systému Windows. Povolte tenantovi bezproblémové jednotné přihlašování.|
|50181|Chyba související s jednorázovým heslem při přihlašování |
|50201|Toto přerušení výzvy zprávy se uživateli zobrazí během přihlašování, pokud je třeba poskytnout další informace uživateli.|
|51001|Pomocný parametr domény není k dispozici u místního identifikátoru zabezpečení místního hlavního názvu uživatele (UPN).|
|51004|Uživatelský účet v adresáři neexistuje.|
|51006|Vyžaduje se integrované ověřování systému Windows. Uživatel přihlášený pomocí tokenu relace, který chybí prostřednictvím deklarace identity Požádejte uživatele, aby se přihlásil znovu.|
|52004|Uživatel neposkytl souhlas pro přístup k prostředkům LinkedIn. |
|53000|Zásady podmíněného přístupu vyžadují zařízení, které dodržuje předpisy, ale dané zařízení je nedodržuje. Zaregistrujte si uživatele zařízení pomocí schváleného poskytovatele MDM, jako je Intune.|
|53001|Zásady podmíněného přístupu vyžadují zařízení, které je připojené k doméně, a toto zařízení k doméně připojené není. Uživatel musí používat zařízení připojené k doméně.|
|53002|Použitá aplikace není schválenou aplikací pro podmíněný přístup. Pokud chce uživatel získat přístup, musí použít jednu z aplikací na seznamu schválených aplikací.|
|53003|Přístup byl zablokován kvůli zásadám podmíněného přístupu.|
|53004|Uživatel musí dokončit registraci pro vícefaktorové ověřování, než získá přístup k tomuto obsahu. Uživatel by se měl zaregistrovat k vícefaktorovému ověřování.|
|65001|Aplikace X nemá oprávnění pro přístup k aplikaci Y nebo oprávnění bylo odvoláno. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete správci tenanta žádost o autorizaci k jednání jménem aplikace Y pro prostředek Z.|
|65004|Uživatel odmítl souhlas s udělením přístupu aplikace. Požádejte uživatele, aby se zkusil znovu přihlásit a udělil aplikaci souhlas.|
|65005|Přístupový seznam požadovaných prostředků aplikace neobsahuje aplikace zjistitelné tímto prostředkem; klientská aplikace požadovala přístup k prostředku, který nebyl zadán v jejím přístupovém seznamu požadovaných prostředků; služba Graph vrátila chybnou žádost; nebo prostředek nebyl nalezen. Pokud aplikace podporuje SAML, pravděpodobně jste ji nakonfigurovali pomocí špatného identifikátoru (entity). Vyzkoušejte řešení uvedené pro SAML v následujícím odkazu: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Udělení oprávnění může být neplatné z následujících důvodů:<ul><li>Požadovaný kontrolní výraz SAML 2.0 má neplatnou metodu potvrzení subjektu.</li><li>Tok OnBehalfOf aplikace se u V2 nepodporuje.</li><li>Primární obnovovací token není podepsaný klíčem relace.</li><li>Externí obnovovací token je neplatný.</li><li>Udělení oprávnění bylo získáno pro jiného tenanta.</li></ul>|
|70001|Aplikace X nebyla nalezena v tenantovi Y. K tomu může dojít v případě, že aplikace s identifikátorem X nebyla nainstalována správcem tenanta nebo nebyla odsouhlasena žádným uživatelem v tenantovi. Možná jste nakonfigurovali hodnotu identifikátoru pro aplikaci nebo odeslali žádost o ověření na špatného tenanta.|
|70002|Aplikace vrátila neplatné přihlašovací údaje klienta. Obraťte se na vlastníka aplikace.|
|70003|Aplikace vrátila nepodporovaný typ udělení oprávnění. Obraťte se na vlastníka aplikace.|
|70004|Aplikace vrátila neplatný identifikátor URI pro přesměrování. Adresa přesměrování specifikovaná klientem neodpovídá žádné nakonfigurované adrese ani žádné adrese na seznamu schválených adres OIDC. Obraťte se na vlastníka aplikace.|
|70005|Aplikace vrátila nepodporovaný typ odpovědi z následujících důvodů:<ul><li>Typ odpovědi „token“ není v aplikaci povolený.</li><li>Typ odpovědi „id_token“ vyžaduje rozsah „OpenID“ – v parametru wctx obsahuje nepodporovanou hodnotu parametru OAuth.</li></ul>Obraťte se na vlastníka aplikace.|
|70007|Aplikace vrátila při žádosti o token nepodporovanou hodnotu „response_mode“. Obraťte se na vlastníka aplikace.|
|70008|Poskytnutý autorizační kód nebo obnovovací token vypršel nebo byl odvolán. Přihlaste se znovu, aby se uživatel znovu přihlašuje.|
|70011|Rozsah požadovaný aplikací je neplatný. Obraťte se na vlastníka aplikace.|
|70012|Při ověřování uživatele účtu spravované služby (příjemce) došlo k chybě serveru. Opakujte přihlášení a Pokud potíže potrvají, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Neplatný ověřovací kód z důvodu zadání špatného uživatelského kódu pro tok kódu zařízení. Autorizace není schválená.|
|70019|Platnost ověřovacího kódu vypršela. Přihlaste se znovu, aby se uživatel znovu přihlásil.|
|70037|Odpověď na výzvu nebyla správná. Vzdálený přístup k relaci byl odepřen.|
|70043|Správa relací podmíněného přístupu Azure vynutí vypršení platnosti relace.|
|70044|Správa relací podmíněného přístupu Azure vynutí vypršení platnosti relace.|
|75001|Během vytváření vazby zprávy SAML došlo k chybě.|
|75003|Aplikace vrátila chybu související s nepodporovanou vazbou (odpověď protokolu SAML nelze odeslat prostřednictvím jiné vazby než HTTP POST). Obraťte se na vlastníka aplikace.|
|75005|Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Obraťte se na vlastníka aplikace.|
|75008|Požadavek z aplikace byl odepřen, protože požadavek SAML měl neočekávaný cíl. Obraťte se na vlastníka aplikace.|
|75011|Metoda ověřování, kterou uživatel provedl ověření se službou, se neshoduje s požadovanou metodou ověřování. Obraťte se na vlastníka aplikace.|
|75016|Požadavek na ověření SAML2 má neplatný formát NameIdPolicy. Obraťte se na vlastníka aplikace.|
|80001|Ověřovací agent se nemohl připojit k Active Directory. Přesvědčte se, že je ověřovací agent nainstalovaný na počítači s připojením k doméně a že počítač má na dohled datové centrum, které může vyřídit požadavek na přihlášení uživatele.|
|80002|Vnitřní chyba. Vypršel časový limit žádosti o ověření hesla. Nepovedlo se nám buď Odeslat žádost o ověření do interní služby hybridní identity. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80003|Ověřovací agent přijal neplatnou odpověď. Při pokusu o ověření pomocí místní služby Active Directory došlo k neznámé chybě. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80005|Ověřovací agent: během zpracování odpovědi od ověřovacího agenta došlo k neznámé chybě. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80007|Ověřovací agent nebyl schopen ověřit heslo uživatele.|
|80010|Ověřovací agent nebyl schopen dešifrovat heslo. |
|80011|Ověřovacímu agentovi se nepodařilo získat šifrovací klíč.|
|80012|Uživatelé se pokusili o přihlášení mimo povolenou dobu (Tato možnost je určena ve službě AD).|
|80013|Pokus o ověření nebylo možné dokončit kvůli časovému posunu mezi počítačem, který má spuštěného ověřovacího agenta, a počítačem, který má spuštěnou službu AD. Opravte problémy se synchronizací času.|
|80014|Vypršel časový limit ověřovacího agenta. [Otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s kódem chyby, ID korelace a DateTime a získejte další podrobnosti o této chybě.|
|81001|Lístek Kerberos uživatele je příliš velký. K tomu může dojít, když je uživatel členem příliš mnoha skupin a lístek Kerberos obsahuje příliš velký počet členství. Snižte počet členství uživatele ve skupinách a zkuste to znovu.|
|81005|Ověřovací balíček se nepodporuje.|
|81007|Tenant není povolený pro bezproblémové přihlašování.|
|81012|Toto není chybový stav. Indikuje, že se uživatel, který se pokouší přihlásit ke službě Azure AD, liší od uživatele, který se přihlásil k zařízení. Tento kód můžete v protokolech bezpečně ignorovat.|
|90010|Požadavek není z různých důvodů podporován. Požadavek se například provede pomocí nepodporované metody Request (podporuje se jenom metoda POST) nebo se nepodporuje podpisový algoritmus tokenu, který se požaduje. Obraťte se na vývojáře aplikace.|
|90014| Chybí povinné pole pro zprávu protokolu. Obraťte se na vlastníka aplikace. Pokud jste vlastníkem aplikace, ujistěte se, že máte všechny potřebné parametry pro žádost o přihlášení. |
|90051| Neplatný token delegování Je zadané neplatné národní ID cloudu ({cloudId}).|
|90072| Účet musí být nejprve v tenantovi přidán jako externí uživatel. Odhlaste se a znovu se přihlaste s jiným účtem služby Azure AD.|
|90094| Aplikace požádala o oprávnění, která přihlášenému uživateli není oprávněná k souhlasu, a uživatel byl zablokován. |
|90095| Aplikace požádala o oprávnění, která přihlášenému uživateli není oprávněná k souhlasu, a uživatel byl zobrazen ve formuláři [žádosti o souhlas správce](../manage-apps/configure-admin-consent-workflow.md) . |
|500011| Objekt zabezpečení prostředku s názvem <site address> nebyl nalezen v tenantovi s názvem <tenant ID>. K tomu může dojít, pokud aplikace nebyla nainstalována správcem tenanta nebo odsouhlasena žádným uživatelem v tenantovi. Možná jste odeslali žádost o ověření do nesprávného tenanta.|
|500021| Tenant je omezený proxy společností. Odepřete přístup k prostředkům.|
|500121| Během žádosti o silný požadavek na ověření došlo k chybě ověřování.|
|500133| Kontrolní výraz není v rámci platného časového rozsahu. Před použitím tohoto kontrolního výrazu pro uživatele Ověřte, zda nevypršela platnost přístupového tokenu, nebo požádejte o nový token.|
|530021|Aplikace nesplňuje požadavky na aplikace schválené pro podmíněný přístup.|
|530032|Blokováno zásadami zabezpečení.| 
|700016|Aplikace s identifikátorem {appIdentifier} se nenašla v adresáři {tenant}. K tomu může dojít, pokud aplikace nebyla nainstalována správcem tenanta nebo odsouhlasena žádným uživatelem v tenantovi. Je možné, že jste odeslali žádost o ověření na špatného tenanta.|
|900432|Důvěrného klienta není v žádosti mezi cloudy podporován.|
|5000811|Nepovedlo se ověřit signaturu tokenu SAML. Identifikátor podpisového klíče neodpovídá žádným platným registrovaným klíčům.|
|7000215|Byl zadán neplatný tajný klíč klienta.|
|7000218|Tělo žádosti musí obsahovat následující parametr: client_assertion nebo client_secret.|


## <a name="next-steps"></a>Další kroky

* [Přehled sestav přihlášení](concept-sign-ins.md)
* [Programový přístup k sestavám Azure AD](concept-reporting-api.md)
