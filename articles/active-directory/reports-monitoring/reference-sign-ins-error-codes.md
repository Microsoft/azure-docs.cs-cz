---
title: Kódy chyb v sestavě aktivit přihlašování na portálu Azure Active Directory | Dokumentace Microsoftu
description: Referenční informace ke kódům chyb v sestavě aktivit přihlašování.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 54d2c08a0055e580105ba3b1da66c53617dfcb7d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151886"
---
# <a name="sign-in-activity-report-error-codes"></a>Kódy chyb sestavě aktivit přihlašování 

Informace poskytnuté [sestavou přihlášení uživatelů](concept-sign-ins.md), najdete odpovědi na otázky tohoto typu:

- Kdo se přihlásil k aplikaci?
- Které aplikace byly podepsány k?
- Která přihlášení selhala důvod, proč?

Když u přihlášení se nezdaří, zobrazí se odpovídající selhání kód chyby. Tento článek uvádí kódy chyb a jejich popisy, spolu s doporučený postup, kde je to možné. 

## <a name="how-can-i-display-failed-sign-ins"></a>Jak se dají zobrazit přihlášení, která selhala? 

Přejděte [sestavy přihlášení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) v [webu Azure portal](https://portal.azure.com).

![Aktivita přihlašování](./media/reference-sign-ins-error-codes/61.png "Aktivita přihlašování")

Filtrovat sestavu pro zobrazení všech neúspěšných přihlášení tak, že vyberete **selhání** z **stav přihlášení** rozevíracího seznamu.

![Aktivita přihlašování](./media/reference-sign-ins-error-codes/06.png "Aktivita přihlašování")

Výběrem položky z filtrovaný seznam otevře **podrobnosti o aktivitě: Přihlášení** okno. Toto zobrazení vám poskytne další informace o neúspěšných události přihlášení, včetně **kódu chyby přihlášení** a **důvod selhání**.

![Aktivita přihlašování](./media/reference-sign-ins-error-codes/05.png "Aktivita přihlašování")

Můžete také programově přistupovat pomocí data přihlášení [API pro vytváření sestav](concept-reporting-api.md).

## <a name="error-codes"></a>Kódy chyb


|Chyba|Popis|
|---|---|
|16000|Toto je interní implementaci a ne chybovou podmínku. Tento odkaz můžete bezpečně ignorovat.|
|20001|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|20012|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|20033|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40008|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40009|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|40014|Vyskytl se problém s federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP).|
|50000|Došlo k problému s naší přihlašovací službou. Pokud chcete tento problém vyřešit, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|50001|Hlavní název služby (SPN) nebyl v tomto tenantovi nalezen. Tomu může dojít, pokud aplikace nebyla nainstalována správcem tenanta, nebo pokud prostředek instančního objektu v adresáři nebyl nalezen nebo je neplatný.|
|50002|Během přihlašování došlo k chybě kvůli omezenému přístupu proxy v tenantovi. Pokud se jedná vlastní zásady tenanta, můžete změnit nastavení tenanta s omezeným přístupem k vyřešení problému.|
|50003|Přihlášení se nezdařilo kvůli chybějícímu podpisovému klíči nebo certifikátu. Pravděpodobně v aplikaci není žádný klíč nakonfigurovaný. Podívejte se na řešení uvedená na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Pokud se problém nevyřeší, obraťte se na vlastníka aplikace nebo administrátora aplikace.|
|50005|Uživatel se pokusil přihlásit k zařízení z platformy, která není v současnosti podporován pomocí zásad podmíněného přístupu.|
|50006| Ověření podpisu se z důvodu neplatného podpisu nezdařilo. Podívejte se na řešení uvedené na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Pokud se problém nevyřeší, obraťte se na vlastníka aplikace nebo administrátora aplikace.|
|50007|Šifrovací certifikát třetí strany nebyl pro tuto aplikaci nalezen. [Vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s Microsoftem získat tyto pevné.|
|50008|V tokenu chybí nebo je špatně nakonfigurovaný kontrolní výraz SAML. Obraťte se na svého federačního zprostředkovatele.|
|50010|Ověření identifikátoru URI cílové skupiny pro aplikaci se nezdařilo, protože nejsou nakonfigurované cílové skupiny tokenu. Řešení, obraťte se na vlastníka aplikace.|
|50011|Adresa pro odpovědi chybí, je špatně nakonfigurována nebo neodpovídá adresám pro odpovědi, které jsou pro aplikaci nakonfigurované. Zkuste řešení uvedené na [ https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application ](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Pokud se problém nevyřeší, obraťte se na vlastníka aplikace nebo administrátora aplikace.|
|50012| Toto je obecné chybové zprávy, která označuje, že ověřování se nezdařilo. To může nastat z důvodu například chybí nebo není platný přihlašovací údaje nebo deklarací identity v požadavku. Ujistěte se, že žádost se poslala se správnými přihlašovacími údaji a deklarace identity. |
|50013|Kontrolní výraz je neplatný z různých důvodů. Například vydavatel tokenu neodpovídá verzi rozhraní api v rámci jeho platný časový rozsah, je token, jejichž platnost vypršela nebo je poškozený nebo obnovovací token v kontrolního výrazu není primární obnovovací token.|
|50017|Ověření certifikace se nezdařilo kvůli jednomu z následujících důvodů:<ul><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Nepodařilo se najít očekávaný CrlSegment.</li><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Distribuční bod rozdílového seznamu CRL je nakonfigurovaný bez odpovídajícího distribučního bodu CRL.</li><li>Z důvodu vypršení časového limitu se nepodařilo načíst platné segmenty CRL.</li><li>CRL nejde stáhnout.</li></ul>Obraťte se na správce tenanta.|
|50020|Uživatel nemá pro jednu z následujících důvodů.<ul><li>Uživatel se pokouší přihlásit pomocí účtu MSA ke koncovému bodu v1</li><li>Uživatel neexistuje v tenantovi.</li></ul> Obraťte se na vlastníka aplikace.|
|50027|Token JWT může být neplatný z následujících důvodů:<ul><li>Neobsahuje deklaraci identity hodnoty nonce nebo subjektu.</li><li>Identifikátor subjektu se neshoduje.</li><li>Deklarace identity idTokenu má duplicitní deklaraci identity.</li><li>Neočekávaný vystavitel</li><li>Neočekávaná cílová skupina</li><li>Nenachází se v platném časovém rozsahu. </li><li>Nesprávný formát tokenu</li><li>U externího ID tokenu od vystavitele se nezdařilo ověření podpisu.</li></ul>Obraťte se na vlastníka aplikace.|
|50029|Neplatný identifikátor URI – název domény obsahuje neplatné znaky. Obraťte se na správce tenanta.|
|50034|Uživatel v adresáři neexistuje. Obraťte se na svého správce klienta.|
|50042|Chybí náhodný řetězec vyžadovaný ke generování párovaného identifikátoru. Obraťte se na správce tenanta.|
|50048|Subjekt se v klientském kontrolním výrazu neshoduje s deklarací identity vystavitele. Obraťte se na správce tenanta.|
|50050|Požadavek je poškozený. Obraťte se na vlastníka aplikace.|
|50053|Účet je uzamčený, protože uživatel se pokusil přihlásit příliš mnohokrát pomocí nesprávného ID uživatele nebo hesla.|
|50055|Neplatné heslo, zadáno prošlé heslo.|
|50056|Neplatný nebo null hesel – hesla v úložišti pro tohoto uživatele neexistuje.|
|50057|Uživatelský účet je neaktivní. Správce tento účet zakázal.|
|50058|Aplikace se pokusila přihlásit v tichém režimu, ale uživatele se v tichém režimu přihlásit nepodařilo. Aplikace je potřeba spustit interaktivní tok, který dává uživatelům možnost přihlášení. Obraťte se na vlastníka aplikace.|
|50059|Uživatel v adresáři neexistuje. Obraťte se na svého správce klienta.|
|50061|Požadavek o odhlášení je neplatný. Obraťte se na vlastníka aplikace.|
|50072|Uživatel musí zaregistrovat k dvojúrovňového ověřování (interaktivní).|
|50074|Uživatel neprošel ověřovacím testem MFA.|
|50076|Uživatel neprošel ověřovacím testem MFA (neinteraktivního).|
|50079|Uživatel musí zaregistrovat k dvoufaktorové ověřování (neinteraktivní přihlášení).|
|50085|Obnovovací token vyžaduje přihlášení prostřednictvím distribučního bodu vydávání (IDP) sociální sítě. Máte uživatele. Zkuste použít přihlášení znovu pomocí uživatelského jména a hesla.|
|50089|Vypršela platnost tokenu toku – ověření se nezdařilo. Vyzkoušejte přihlášení znovu pomocí uživatelského jména a hesla uživatele|
|50097|Je vyžadováno ověření zařízení. Tato situace může nastat, protože jsou deklarace identity ID zařízení nebo DeviceAltSecId **null**, nebo pokud neexistuje žádné zařízení odpovídající identifikátor zařízení.|
|50099|Podpis JWT je neplatný. Obraťte se na vlastníka aplikace.|
|50105|Přihlášený uživatel nemá přiřazenou roli k aktuálně přihlášené aplikaci. Přiřadíte uživatele k aplikaci. Další informace najdete na adrese: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role).|
|50107|Požadovaný objekt sféry federace neexistuje. Obraťte se na správce tenanta.|
|50120|Problém s hlavičkou JWT. Obraťte se na správce tenanta.|
|50124|Transformace deklarací identity obsahuje neplatný vstupní parametr. Obraťte se na správce tenanta se aktualizovat zásady.|
|50125|Přihlášení bylo přerušeno z důvodu obnovení hesla nebo položka registrace hesla.|
|50126|Neplatné uživatelské jméno nebo heslo, nebo neplatné místní uživatelské jméno nebo heslo.|
|50127|Uživatel musí nainstalovat broker aplikaci získat přístup k tomuto obsahu.|
|50128|Neplatný název domény – žádné informace o identifikaci tenanta najít v jednom požadavku nebo odvozené od všech zadaných přihlašovacích údajů.|
|50129|Zařízení není místu - **Workplace join** požadovaných k registraci zařízení.|
|50130|Hodnota deklarace identity se nedá interpretovat jako známé ověřovací metoda.|
|50131|Používá se při různých chybách podmíněného přístupu. Například špatný stav zařízení s Windows, zablokování požadavku z důvodu podezřelé aktivity nebo rozhodnutí zásad přístupu a zabezpečení.|
|50132|Přihlašovací údaje byly odvolány z následujících důvodů:<ul><li>Artefakt jednotného přihlašování je neplatný nebo mu platnost vypršela.</li><li>Relace není pro aplikaci dostatečně nová.</li><li>Požadavek na přihlášení v tichému režimu byl odeslán, ale uživatelova relace s Azure AD je neplatná nebo skončila její platnost.</li></ul>|
|50133|Relace je neplatná z důvodu vypršení platnosti nebo nedávné změny hesla.|
|50135|Změna hesla se vyžaduje kvůli účet rizika.|
|50136|Přesměrujte MSA relace aplikace – zjištěná MSA jedné relace. |
|50140|K této chybě došlo kvůli přerušení operace „Zůstat přihlášeni“ během přihlašování. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
|50143|Neshoda relace - relace je neplatný, protože uživatel tenanta se neshoduje s nápovědu domény kvůli jinému prostředku.  [Vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s ID korelace ID požadavku a chyba kódu zobrazíte další podrobnosti.|
|50144|Vypršela platnost hesla uživatele pro Active Directory. Generovat nové heslo pro uživatele nebo mít pomocí nástroje samoobslužného obnovení koncového uživatele.|
|50146|Tato aplikace se musí nakonfigurovat pomocí podpisového klíče, který je specifický pro danou aplikaci. Buď není pomocí tohoto klíče nakonfigurovaná nebo klíči vypršela platnost nebo ještě klíč v platnost nevstoupil. Obraťte se na vlastníka aplikace.|
|50148|Zadaný code_verifier neodpovídá přidruženému code_challenge, který byl dodán v žádosti o autorizaci pro PKCE. Obraťte se na vývojáře aplikace. |
|50155|Pro tohoto uživatele se nezdařilo ověřování zařízení.|
|50158|Externí bezpečnostní kontroly nebyla splněná.|
|50161|Odešle externím poskytovatelem není dostatečné, nebo chybí deklarace identity požádal o externího poskytovatele.|
|50166|Nepovedlo se odeslat požadavek na zprostředkovatele deklarací identity.|
|50169|Tato sféra není nakonfigurovanou sférou aktuálního oboru názvů.|
|50172|Externí zprostředkovatel deklarací není schválený. Obraťte se na správce tenanta|
|50173|Vyžaduje se nový token zabezpečení. Máte přihlášení uživatele znovu pomocí nové přihlašovací údaje.|
|50177|Externí challenge není podporováno pro průchozí uživatele.|
|50178|Řízení relace není podporována pro průchozí uživatele.|
|50180|Vyžaduje se integrované ověřování systému Windows. Povolte tenantovi bezproblémové jednotné přihlašování.|
|51001|Nápověda domény není k dispozici s identifikátorem zabezpečení místní – místní hlavní název uživatele.|
|51004|Uživatelský účet v adresáři neexistuje.|
|51006|Vyžaduje se integrované ověřování systému Windows. Uživatel se přihlásil pomocí tokenu relace, kterému chybí integrované ověřování Windows (WIA) deklarace identity. Požádejte uživatele, aby se přihlásil znovu.|
|52004|Uživatel neposkytl souhlas pro přístup k prostředkům LinkedIn. |
|53000|Zásady podmíněného přístupu vyžadují zařízení, které dodržuje předpisy, ale dané zařízení je nedodržuje. Požádejte uživatele zaregistrovat své zařízení s poskytovatelem služby schválené MDM, jako je Intune.|
|53001|Zásady podmíněného přístupu vyžadují zařízení, které je připojené k doméně, a toto zařízení k doméně připojené není. Použití uživatele domény se připojili k zařízení.|
|53002|Použitá aplikace není schválenou aplikací pro podmíněný přístup. Pokud chce uživatel získat přístup, musí použít jednu z aplikací na seznamu schválených aplikací.|
|53003|Přístup se zablokoval kvůli zásadám podmíněného přístupu.|
|53004|Uživatel musí dokončit registraci pro vícefaktorové ověřování, než získá přístup k tomuto obsahu. Uživatel by se měl zaregistrovat k vícefaktorovému ověřování.|
|65001|Aplikace X nemá oprávnění pro přístup k aplikaci Y nebo oprávnění bylo odvoláno. Nebo Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek. Nebo uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. odešlete správci tenanta žádost o autorizaci k jednání jménem aplikace: Y pro prostředek: Z.|
|65004|Uživatel odmítl souhlas s udělením přístupu aplikace. Požádejte uživatele, aby se zkusil znovu přihlásit a udělil aplikaci souhlas.|
|65005|Přístupový seznam požadovaných prostředků aplikace neobsahuje aplikace zjistitelné tímto prostředkem; klientská aplikace požadovala přístup k prostředku, který nebyl zadán v jejím přístupovém seznamu požadovaných prostředků; služba Graph vrátila chybnou žádost; nebo prostředek nebyl nalezen. Pokud aplikace podporuje SAML, pravděpodobně jste ji nakonfigurovali pomocí špatného identifikátoru (entity). Vyzkoušejte řešení uvedené pro SAML v následujícím odkazu: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|Udělení oprávnění může být neplatné z následujících důvodů:<ul><li>Požadovaný kontrolní výraz SAML 2.0 má neplatnou metodu potvrzení subjektu.</li><li>Tok OnBehalfOf aplikace se u V2 nepodporuje.</li><li>Primární obnovovací token není podepsaný klíčem relace.</li><li>Externí obnovovací token je neplatný.</li><li>Udělení oprávnění bylo získáno pro jiného tenanta.</li></ul>|
|70001|Aplikace X nebyla nalezena v tenantovi Y. K tomu může dojít v případě, že aplikace s identifikátorem X nebyla nainstalována správcem tenanta nebo nebyla odsouhlasena žádným uživatelem v tenantovi. Možná máte správně nakonfigurovaný. hodnota identifikátoru pro aplikaci nebo odeslání žádosti o ověření do nesprávného tenanta.|
|70002|Aplikace vrátila neplatné přihlašovací údaje klienta. Obraťte se na vlastníka aplikace.|
|70003|Aplikace vrátila nepodporovaný typ udělení oprávnění. Obraťte se na vlastníka aplikace.|
|70004|Aplikace vrátila neplatný identifikátor URI pro přesměrování. Adresa přesměrování specifikovaná klientem neodpovídá žádné nakonfigurované adrese ani žádné adrese na seznamu schválených adres OIDC. Obraťte se na vlastníka aplikace.|
|70005|Aplikace vrátila nepodporovaný typ odpovědi z následujících důvodů:<ul><li>Typ odpovědi „token“ není v aplikaci povolený.</li><li>Typ odpovědi „id_token“ vyžaduje rozsah „OpenID“ – v parametru wctx obsahuje nepodporovanou hodnotu parametru OAuth.</li></ul>Obraťte se na vlastníka aplikace.|
|70007|Aplikace vrátila při žádosti o token nepodporovanou hodnotu „response_mode“. Obraťte se na vlastníka aplikace.|
|70008|Zadaný autorizační kód nebo obnovovací token vypršela nebo byl odvolán. Jste nový pokus uživatele přihlášení.|
|70011|Rozsah požadovaný aplikací je neplatný. Obraťte se na vlastníka aplikace.|
|70012|Při ověřování uživatele účtu spravované služby (příjemce) došlo k chybě serveru. Opakujte přihlášení, a pokud se problém nevyřeší, [vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Neplatný ověřovací kód z důvodu zadání špatného uživatelského kódu pro tok kódu zařízení. Autorizace není schválený.|
|70019|Platnost ověřovacího kódu vypršela. Požádejte uživatele, zkuste přihlásit.|
|70037|Odpověď na výzvu nebyla správná. Vzdálený přístup k relaci byl odepřen.|
|75001|Během vytváření vazby zprávy SAML došlo k chybě.|
|75003|Aplikace vrátila chybu související s nepodporovanou vazbou (odpověď protokolu SAML nelze odeslat prostřednictvím jiné vazby než HTTP POST). Obraťte se na vlastníka aplikace.|
|75005|Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Obraťte se na vlastníka aplikace.|
|75008|Požadavek z aplikace byl odepřen, protože požadavek SAML měl neočekávaný cíl. Obraťte se na vlastníka aplikace.|
|75011|Metoda ověřování, kterou uživatel provedl ověření se službou, se neshoduje s požadovanou metodou ověřování. Obraťte se na vlastníka aplikace.|
|75016|Požadavek na ověření SAML2 má neplatný formát NameIdPolicy. Obraťte se na vlastníka aplikace.|
|80001|Ověřovací agent se nemohl připojit k Active Directory. Přesvědčte se, že je ověřovací agent nainstalovaný na počítači s připojením k doméně a že počítač má na dohled datové centrum, které může vyřídit požadavek na přihlášení uživatele.|
|80002|Vnitřní chyba. Vypršel časový limit žádosti o ověření hesla. Požadavek na ověření se nám nepodařilo interní hybridní službě identit odeslat. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80003|Ověřovací agent přijal neplatnou odpověď. Při pokusu o ověření pomocí místní služby Active Directory došlo k neznámé chybě. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80005|Ověřovací Agent: Při zpracování odpovědi z ověřování agenta došlo k neznámé chybě. Pokud chcete získat o této chybě více informací, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
|80007|Ověřovací agent nebyl schopen ověřit heslo uživatele.|
|80010|Ověřovací agent nebyl schopen dešifrovat heslo. |
|80011|Ověřovacímu agentovi se nepodařilo získat šifrovací klíč.|
|80012|Uživatelé se pokusily o připojení mimo povolených hodin (to je určeno ve službě AD).|
|80013|Pokus o ověření nebylo možné dokončit kvůli časovému posunu mezi počítačem, který má spuštěného ověřovacího agenta, a počítačem, který má spuštěnou službu AD. Opravte problémy se synchronizací času.|
|80014|Ověřovacímu agentovi vypršel časový limit. [Vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s kódem chyby, ID korelace a datum a čas, abyste získali více podrobností o této chybě.|
|81001|Lístek Kerberos uživatele je příliš velký. K tomu může dojít, když je uživatel členem příliš mnoha skupin a lístek Kerberos obsahuje příliš velký počet členství. Snižte počet členství uživatele ve skupinách a zkuste to znovu.|
|81005|Ověřovací balíček není podporován.|
|81007|Tenant není povolená pro bezproblémové jednotné přihlašování.|
|81012|Toto není chybovou podmínku. Označuje, že tento uživatel pokoušel se přihlásit ke službě Azure AD se liší od uživatele přihlášené k zařízení. Tento kód v protokolech můžete bezpečně ignorovat.|
|90010|Požadavek není podporován z různých důvodů. Například požadavku pomocí metody Nepodporovaná žádost (podporuje se jenom metody POST) nebo token, který byl vyžádán podpisový algoritmus se nepodporuje. Obraťte se na vývojáře aplikace.|
|90014| Chybí povinné pole. pro zprávy protokolu, obraťte se na vlastníka aplikace. Pokud jste vlastníkem aplikace, ujistěte se, že máte všechny potřebné parametry pro žádost o přihlášení. |
|90072| Účet musí být nejprve přidán jako externí uživatel v tenantovi. Odhlášení a přihlaste se znovu pomocí jiné služby Azure AD účtu.|
|90094| Udělení vyžaduje oprávnění správce. Požádejte správce tenanta k poskytnutí souhlasu pro tuto aplikaci.|
|500133| Výraz se nenachází v jeho platný časový rozsah. Ujistěte se, že jej před použitím pro uživatele kontrolní výraz nevyprší platnost přístupového tokenu nebo požádat o nový token.|

## <a name="next-steps"></a>Další postup

* [Přehled sestav přihlášení](concept-sign-ins.md)
* [Programový přístup k sestavám Azure AD](concept-reporting-api.md)
