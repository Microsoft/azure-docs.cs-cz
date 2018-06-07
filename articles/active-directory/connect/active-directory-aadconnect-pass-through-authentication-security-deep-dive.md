---
title: Azure Active Directory předávací ověřování zabezpečení podrobné informace | Microsoft Docs
description: Tento článek popisuje, jak předávací ověřování Azure Active Directory (Azure AD) chrání vaše místní účty
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cb8382a9801c3570a190259416d846fe518cc6ea
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595032"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory předávací ověřování zabezpečení podrobné informace

Tento článek poskytuje podrobnější popis toho, jak funguje Azure Active Directory (Azure AD) předávací ověřování. Zaměřuje se na aspekty zabezpečení funkce. Tento článek slouží k zabezpečení a správci IT, ředitel dodržování předpisů a zabezpečení osob a dalším IT profesionálům, kteří jsou zodpovědní za IT zabezpečení a dodržování předpisů na malé a střední velikosti organizace nebo velké podniky.

Témata řešit:
- Podrobné technické informace o tom, jak nainstalovat a zaregistrovat ověřování agentů.
- Podrobné technické informace o šifrování hesla při přihlášení uživatele.
- Zabezpečení kanálů mezi místní ověřování agentů a Azure AD.
- Podrobné technické informace o tom, jak agenty ověřování provozně zabezpečit.
- Další témata související se zabezpečením.

## <a name="key-security-capabilities"></a>Klíčové funkce zabezpečení

Tyto aspekty zabezpečení klíčů této funkce jsou:
- Je založen na zabezpečené nevyužívá dělené tabulky více architekturu, která zajišťuje izolaci žádostí o přihlášení mezi klienty.
- Místních hesel nikdy ukládají v cloudu v žádný formulář.
- Místní ověřování agentů, který naslouchat a reagovat na, žádosti o ověření hesla provést pouze odchozí připojení z ve vaší síti. Není nutné k instalaci těchto agentů ověřování v hraniční síti (DMZ).
- Pro odchozí komunikaci od agentů ověřování do služby Azure AD se používají pouze standardní porty (80 a 443). Nemusíte otevřete příchozí porty v bráně firewall. 
  - Port 443 se používá pro všechny ověřené odchozí komunikaci.
  - Port 80 se používá pouze pro stahování seznamy odvolaných certifikátů (CRL) pro zajištění, že žádný z certifikátů používat tuto funkci byl odvolán.
  - Úplný seznam požadavků sítě najdete v tématu [ověřování Azure Active Directory průchozí: rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Hesla, které uživatelé zadali při přihlašování se šifrují v cloudu, než místní ověřování agentů nepřijmete pro ověření na základě služby Active Directory.
- Kanál HTTPS mezi službou Azure AD a místní ověřování agenta je zabezpečená pomocí vzájemného ověřování.
- Tato funkce se bezproblémově integruje s funkce Ochrana cloudu Azure AD, například zásady podmíněného přístupu (včetně ověřování Azure Multi-Factor Authentication), ochrany identit a inteligentní uzamčení.

## <a name="components-involved"></a>Součásti související se situací

Obecné podrobnosti o provozu, služba Azure AD a zabezpečení dat najdete v tématu [Centrum zabezpečení](https://azure.microsoft.com/support/trust-center/). Při použití předávaného ověřování pro přihlášení uživatele, se podílejí následující komponenty:
- **Azure tokenů zabezpečení AD**: bezstavové služby tokenů zabezpečení (STS), která zpracovává požadavky na přihlášení a vydává tokeny zabezpečení pro prohlížeče, klienty nebo služby podle potřeby uživatelů.
- **Azure Service Bus**: poskytuje povolenou podporu cloudu komunikace s podnikových způsobů zasílání zpráv a komunikaci předávací službu, která umožňuje připojit místní řešení s cloudem.
- **Agent Azure AD Connect ověřování**: komponentu místní, která naslouchá a reaguje na požadavky na ověření hesla.
- **Azure SQL Database**: obsahuje informace o ověřování agentů vašeho klienta, včetně jejich metadat, šifrovacích klíčů.
- **Služby Active Directory**: místní služby Active Directory, kde jsou uloženy vaše uživatelské účty a hesla.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalace a registrace ověřování agentů

Ověřování agenti jsou nainstalovaného a zaregistrovaného na Azure AD při můžete buď:
   - [Povolit předávací ověřování prostřednictvím služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Přidat další ověřování agentů k zajištění vysoké dostupnosti žádostí o přihlášení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Získávání k ověřování agenta pracovní zahrnuje tři hlavní fáze:

1. Instalace agenta ověřování
2. Registrace agenta ověřování
3. Inicializace agenta ověřování

Následující části popisují tyto fáze podrobně.

### <a name="authentication-agent-installation"></a>Instalace agenta ověřování

Jenom globální správci můžete nainstalovat agenta ověřování (pomocí Azure AD Connect nebo samostatné) na místní server. Přidá dvě nové položky k instalaci **ovládací panely** > **programy** > **programy a funkce** seznamu:
- Vlastní aplikace Agent ověřování. Tato aplikace funguje s [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) oprávnění.
- Aktualizační metoda aplikace, která se používá k automatické aktualizace agenta ověřování. Tato aplikace funguje s [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) oprávnění.

### <a name="authentication-agent-registration"></a>Registrace agenta ověřování

Po instalaci agenta ověřování, musí se k registraci v Azure AD. Azure AD přiřadí každé ověřování agenta jedinečný, identity digitální certifikát, který můžete použít pro zabezpečenou komunikaci se službou Azure AD.

Postup registrace také váže Agent ověřování pomocí vašeho klienta. Tím se zajistí, že Azure AD ví, že tato konkrétní Agent ověřování je pouze jeden oprávnění pro zpracování žádosti o ověření hesla pro vašeho klienta. Tento postup se opakuje pro každý nový Agent ověřování zaregistrovat.

Ověřování agenty pomocí následujících kroků zaregistrovat se službou Azure AD:

![Registrace agenta](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD poprvé požádá, že globální správce přihlásit do služby Azure AD pomocí svých přihlašovacích údajů. Agent ověřování při přihlašování, získá přístupový token, který můžete použít jménem globální správce.
2. Agent ověřování potom vytvoří pár klíčů: veřejný klíč a soukromý klíč.
    - Pár klíčů je generována pomocí standardní šifrování RSA 2048 bitů.
    - Privátní klíč zůstane na místním serveru, kde se nachází Agent ověřování.
3. Agent ověřování požádá ", registrace" do služby Azure AD prostřednictvím protokolu HTTPS, s následujícími součástmi zahrnuté v žádosti:
    - Přístupový token získali v kroku 1.
    - Veřejný klíč vygenerovaný v kroku 2.
    - Certifikát podepisování požadavku (CSR nebo žádost o certifikát). Tento požadavek platí pro certifikát digitální identity s Azure AD jako jeho certifikační autoritu (CA).
4. Azure AD ověří token přístupu v žádosti o registraci a ověřuje, zda požadavek pochází globálního správce.
5. Azure AD poté podepíše a odešle certifikát digitální identitu zpět do agenta ověřování.
    - Kořenové certifikační Autority ve službě Azure AD se používá k podepsání certifikátu. 

     >[!NOTE]
     > Tato certifikační Autorita je _není_ v systému Windows důvěryhodné kořenové certifikační autority úložišti.
    - Certifikační Autority se používá pouze ve funkci předávací ověřování. Certifikační Autority slouží pouze k podepsání zástupci oddělení služeb zákazníkům během registrace agenta ověřování.
    -  Žádné další služby Azure AD pomocí této certifikační Autority.
    - Subjekt certifikátu (rozlišující název nebo název domény) je nastaven na vaše ID klienta. Tento název domény je identifikátor GUID, který jednoznačně identifikuje vašeho klienta. Tento název domény rozsahy certifikátu pro použití pouze u vašeho klienta.
6. Azure AD ukládá veřejný klíč agenta ověřování v Azure SQL database, která pouze Azure AD má přístup k.
7. Certifikát (vystavený v kroku 5) je uložený na místním serveru v úložišti certifikátů systému Windows (konkrétně v [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) umístění). Používá se Agent ověřování i aktualizační aplikace.

### <a name="authentication-agent-initialization"></a>Inicializace agenta ověřování

Při spuštění ověřování agenta, poprvé po registraci nebo po server restartovat, musí být pro bezpečnou komunikaci se službou Azure AD a začít přijímat žádosti o ověření hesla.

![Inicializace agenta](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Zde je, jak jsou inicializovány agenty ověřování:

1. Agent ověřování zašle odchozí bootstrap požadavek do služby Azure AD. 
    - Tento požadavek se provádí přes port 443 a je kanálem vzájemně ověřené HTTPS. Požadavek používá stejný certifikát, který byl vydán během registrace agenta ověřování.
2. Azure AD odpoví na žádost tím, že poskytuje přístupový klíč pro frontu Azure Service Bus, který je jedinečný pro vašeho klienta a která je identifikovaná vaše ID klienta.
3. Agent ověřování vytvoří trvalé odchozí připojení HTTPS (přes port 443) fronty. 
    - Agent ověřování je nyní připravena k načtení a zpracování požadavků na ověření hesla.

Pokud máte více ověřování agenty registrovaný na klientovi a potom inicializace postup zajistí, že každé z nich připojuje ke stejné fronty Service Bus. 

## <a name="process-sign-in-requests"></a>Zpracování žádostí o přihlášení 

Následující diagram znázorňuje, jak předávací ověřování zpracovává požadavky přihlášení uživatele.

![Proces přihlášení](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Předávací ověřování zpracovává požadavek přihlášení uživatele takto: 

1. Uživatel se pokusí o přístup k aplikaci, například [Outlook Web App](https://outlook.office365.com/owa).
2. Pokud již není přihlášený uživatel, aplikace prohlížeč přesměruje na přihlašovací stránku služby Azure AD.
3. Odpoví služby tokenů zabezpečení Azure AD zpátky s **přihlášení uživatele** stránky.
4. Uživatel zadá své uživatelské jméno a heslo do **přihlášení uživatele** stránce a potom vybere **přihlášení** tlačítko.
5. Uživatelské jméno a heslo se odešlou do Azure AD Služba tokenů zabezpečení ve požadavek POST protokolu HTTPS.
6. Azure AD STS načte veřejných klíčů pro všechny agenty ověřování registrovaný na vašeho klienta z databáze Azure SQL a zašifruje heslo pomocí nich. 
    - Vyvolá hodnoty "N" zašifrované heslo pro agenty "N" ověřování registrovaný na klientovi.
7. Azure AD STS umístí žádost o ověření hesla se skládá z uživatelské jméno a heslo šifrované hodnoty, do fronty Service Bus, který je určený výhradně pro vašeho klienta.
8. Protože inicializovaného agenty ověřování trvalé připojení k fronty Service Bus, jednu z dostupných agentů ověřování načte žádost o ověření hesla.
9. Agent ověřování vyhledá zašifrované heslo hodnotu, která je specifická pro svůj veřejný klíč, pomocí identifikátor a dešifruje ji pomocí jeho privátní klíč.
10. Agent ověřování pokusí se ověřit uživatelské jméno a heslo pro místní službu Active Directory pomocí [rozhraní API Win32 LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) s **dwLogonType** parametr nastaven na **LOGON32_LOGON_NETWORK**. 
    - Toto rozhraní API je stejné rozhraní API, které se používá ve službě Active Directory Federation Services (AD FS) pro přihlášení uživatelů ve scénáři federované přihlašování.
    - Toto rozhraní API závisí na procesu standardní překladu, který v systému Windows Server na řadič domény najít.
11. Agent ověřování obdrží výsledek ze služby Active Directory, jako je například úspěch, uživatelské jméno nebo nesprávné heslo nebo platnost hesla.
12. Agent ověřování výsledek předá zpět tokenů zabezpečení Azure AD přes odchozí vzájemně ověřené kanál HTTPS přes port 443. Vzájemné ověřování pomocí certifikátu dříve vydaného Agent ověřování během registrace.
13. Služby tokenů zabezpečení služby Azure AD ověřuje, že tento výsledek koreluje s konkrétní přihlášení žádost na klientovi.
14. Azure AD STS pokračuje s postupem přihlášení podle konfigurace. Například pokud ověření hesla bylo úspěšné, uživatel může být postiženy pro službu Multi-Factor Authentication nebo přesměrován zpět do aplikace.

## <a name="operational-security-of-the-authentication-agents"></a>Provozní zabezpečení ověřování agentů

Zajistit, aby zůstala provozně zabezpečené předávací ověřování, Azure AD pravidelně obnovuje ověřování agentů certifikáty. Azure AD se aktivuje obnovení. Obnovení nejsou řídí agenty ověřování sami.

![Provozní zabezpečení](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Obnovení důvěryhodnosti Agent ověřování s Azure AD:

1. Agent ověřování pravidelně příkaz ping Azure AD každých několik hodin a zkontrolovat, pokud je čas na obnovení certifikátu. 
    - Tato kontrola se provádí přes kanál vzájemně ověřené HTTPS a používá stejný certifikát, který byl vydán během registrace.
2. Pokud službu naznačuje, že je čas k obnovení, Agent ověřování generuje nový pár klíčů: veřejný klíč a soukromý klíč.
    - Tyto klíče se generují pomocí standardní šifrování RSA 2048 bitů.
    - Privátní klíč se nikdy neopustí na místním serveru.
3. Agent ověřování pak požádá "obnovení certifikátu" do služby Azure AD prostřednictvím protokolu HTTPS, s následujícími součástmi zahrnuté v žádosti:
    - Existující certifikát, který je načten z umístění CERT_SYSTEM_STORE_LOCAL_MACHINE v úložišti certifikátů systému Windows. Účastnící se tento postup, neexistuje žádný globální správce, takže neexistuje žádný přístupový token potřeby jménem globální správce.
    - Veřejný klíč vygenerovaný v kroku 2.
    - Certifikát podepisování požadavku (CSR nebo žádost o certifikát). Tento požadavek platí pro nový certifikát digitální identitu, s Azure AD jako jeho certifikační autority.
4. Azure AD ověří existující certifikát v požadavek na obnovení certifikátu. Poté ověří, zda požadavek pochází ověřování agenta registrován na vašeho klienta.
5. Pokud je stále platný stávající certifikát, Azure AD poté podepíše nový certifikát digitální identity a vydá nový certifikát zpět do agenta ověřování. 
6. Pokud vypršela platnost stávající certifikát, Azure AD Agent ověřování odstraní z vašeho klienta seznam registrovaných ověřování agentů. Globální správce musí potom ručně nainstalujte a zaregistrujte nový Agent ověřování.
    - Pomocí Azure AD kořenová certifikační Autorita k podepsání certifikátu.
    - Nastavte předmětu certifikátu (rozlišující název nebo název domény) na vaše ID klienta, identifikátor GUID, který jednoznačně identifikuje vašeho klienta. Certifikát klienta pouze rozsahy, název domény.
6. Azure AD ukládá v databázi Azure SQL, kterou pouze má přístup k nový veřejný klíč agenta ověřování. Také zruší platnost původního veřejný klíč přiřazeného k agentu ověřování.
7. Nový certifikát (vydané v kroku 5) je pak uloženy na serveru v úložišti certifikátů systému Windows (konkrétně v [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) umístění).
    - Protože postup obnovení vztah důvěryhodnosti se stane interaktivně (bez přítomnosti globální správce), Agent ověřování již nemá přístup k aktualizaci existující certifikát v CERT_SYSTEM_STORE_LOCAL_MACHINE umístění. 
    
   > [!NOTE]
   > Tento postup neodebere samotný certifikát z umístění CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Nový certifikát se používá pro ověřování z tohoto bodu na. Všechny následné obnovení certifikátu nahrazuje certifikátu v umístění CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Automatická aktualizace agentů ověřování

Aktualizační aplikace automaticky aktualizuje agenta ověřování při vydání nové verze. Aplikace zpracovává všechny žádosti o ověření hesla pro vašeho klienta. 

Azure AD hostitelem novou verzi softwaru jako přihlášeni **balíček služby Windows Installer (MSI)**. Soubor MSI je podepsaná pomocí [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) s SHA256 jako algoritmus digest. 

![Automatická aktualizace](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Pro automatickou aktualizaci agenta ověřování:

1. Příkazy ping aktualizační aplikace Azure AD každou hodinu ke kontrole, pokud je k dispozici nová verze agenta ověřování. 
    - Tato kontrola se provádí přes kanál vzájemně ověřené HTTPS pomocí stejný certifikát, který byl vydán během registrace. Agent ověřování a aktualizační sdílet certifikát uložený na serveru.
2. Pokud je dostupná nová verze, Azure AD podepsaný soubor MSI vrátí zpět do aktualizačního programu.
3. Aktualizační ověřuje, že soubor MSI je podepsán společností Microsoft.
4. Aktualizační metoda spustí soubor MSI. Tato akce zahrnuje následující kroky:

 > [!NOTE]
 > Aktualizační běží s [místní systém](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) oprávnění.

    - Zastaví službu Agent ověřování
    - Nainstaluje novou verzi agenta ověřování na serveru
    - Restartuje službu Agent ověřování

>[!NOTE]
>Pokud máte více ověřování agenty registrovaný na vašeho klienta Azure AD není obnovit své certifikáty nebo je aktualizovat ve stejnou dobu. Místo toho Azure AD se proto postupně na vysokou dostupnost žádostí o přihlášení.
>


## <a name="next-steps"></a>Další postup
- [Aktuální omezení](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Zjistěte, jaké postupy se podporují, a ty, které nejsou.
- [Rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md): zprovoznění na Azure AD předávací ověřování.
- [Inteligentní uzamčení](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): nakonfigurovat možnosti inteligentního uzamčení na vašeho klienta k ochraně uživatelské účty.
- [Jak to funguje](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Získejte základní informace o tom, jak funguje Azure AD předávací ověřování.
- [Nejčastější dotazy](active-directory-aadconnect-pass-through-authentication-faq.md): Vyhledejte odpovědi na nejčastější dotazy.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Přečtěte si o řešení běžných problémů s funkcí předávací ověřování.
- [Azure AD bezproblémové SSO](active-directory-aadconnect-sso.md): Další informace o této funkci vzájemně doplňují.
