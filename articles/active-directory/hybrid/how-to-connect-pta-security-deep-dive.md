---
title: Azure Active Directory předávací ověřování zabezpečení podrobně | Microsoft Docs
description: Tento článek popisuje, jak Azure Active Directory (Azure AD) předávací ověřování chrání vaše místní účty.
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280141"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory předávací ověřování zabezpečení s hloubkovým podrobně

Tento článek poskytuje podrobnější popis způsobu, jakým funguje předávací ověřování Azure Active Directory (Azure AD). Zaměřuje se na aspekty zabezpečení této funkce. Tento článek je určený pro zabezpečení a správce IT, dodržování předpisů a úředníků v oblasti zabezpečení a další odborníky na IT, kteří zodpovídají za zabezpečení IT a dodržování předpisů u malých a velkých podnikových organizací.

Mezi zahrnutá témata patří:
- Podrobné technické informace o tom, jak nainstalovat a zaregistrovat ověřovací agenty.
- Podrobné technické informace o šifrování hesel během přihlašování uživatelů
- Zabezpečení kanálů mezi místními agenty ověřování a službou Azure AD.
- Podrobné technické informace o tom, jak zajistit fungování ověřovacích agentů.
- Další témata související se zabezpečením.

## <a name="key-security-capabilities"></a>Klíčové funkce zabezpečení

Toto jsou klíčové aspekty zabezpečení této funkce:
- Je postavená na zabezpečené víceklientské architektuře, která poskytuje izolaci žádostí o přihlášení mezi klienty.
- Místní hesla se v jakémkoli formuláři nikdy neukládají v cloudu.
- Místní agenti ověřování, kteří naslouchají a reagují na požadavky na ověření hesla, budou mít pouze odchozí připojení z vaší sítě. Neexistuje žádný požadavek na instalaci těchto ověřovacích agentů v hraniční síti (DMZ). Osvědčeným postupem je považovat všechny servery, na kterých běží agenti ověřování, jako systémy vrstvy 0 (viz [referenční](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)informace).
- Pro odchozí komunikaci od agentů ověřování do Azure AD se používají jenom standardní porty (80 a 443). Nemusíte otevírat příchozí porty na bráně firewall. 
  - Port 443 se používá pro veškerou ověřenou odchozí komunikaci.
  - Port 80 se používá jenom ke stažení seznamů odvolaných certifikátů (CRL), aby se zajistilo, že žádný z certifikátů, které tato funkce nepoužila, nebyla odvolána.
  - Úplný seznam požadavků na síť najdete v tématu [Azure Active Directory předávacího ověřování: rychlý Start](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Hesla, která uživatelé poskytují během přihlašování, se šifrují v cloudu předtím, než je místní agent ověřování přijímají k ověřování proti službě Active Directory.
- Kanál HTTPS mezi Azure AD a místním ověřovacím agentem je zabezpečený pomocí vzájemného ověřování.
- Chrání vaše uživatelské účty pomocí [zásad podmíněného přístupu Azure AD](../conditional-access/overview.md), včetně Multi-Factor Authentication (MFA), [blokováním staršího ověřování](../conditional-access/concept-conditional-access-conditions.md) a [filtrováním útoků hrubou silou na hesla](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Zapojené součásti

Obecné informace o provozu, službě a zabezpečení dat v Azure AD najdete v [centru](https://azure.microsoft.com/support/trust-center/)zabezpečení. Při použití předávacího ověřování pro přihlášení uživatele jsou zapojeny následující komponenty:
- **Azure AD STS**: Bezstavová služba tokenů zabezpečení (STS), která zpracovává žádosti o přihlášení a vydává tokeny zabezpečení pro prohlížeče, klienty nebo služby podle potřeby.
- **Azure Service Bus**: poskytuje cloudovou komunikaci s podnikovým zasíláním zpráv a přenáší komunikaci, která pomáhá propojit místní řešení s cloudem.
- **Azure AD Connect ověřovací agent**: místní komponenta, která naslouchá a reaguje na žádosti o ověření hesla.
- **Azure SQL Database**: uchovává informace o agentech ověřování vašeho tenanta, včetně jejich metadat a šifrovacích klíčů.
- **Active Directory**: místní služba Active Directory, kde jsou uložené vaše uživatelské účty a hesla.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalace a registrace ověřovacích agentů

Agenti ověřování se nainstalují a zaregistrují ve službě Azure AD, když to bude:
   - [Povolit předávací ověřování prostřednictvím Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [Přidejte další ověřovací agenty, abyste zajistili vysokou dostupnost žádostí o přihlášení.](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
Získání ověřovacího agenta pro práci se skládá ze tří hlavních fází:

1. Instalace ověřovacího agenta
2. Registrace ověřovacího agenta
3. Inicializace ověřovacího agenta

Následující části podrobněji popisují tyto fáze.

### <a name="authentication-agent-installation"></a>Instalace ověřovacího agenta

Pouze globální Správci mohou nainstalovat ověřovacího agenta (pomocí Azure AD Connect nebo samostatné) na místním serveru. Instalace přidá dvě nové položky do **ovládacích panelů**  >  **programy** programy  >  **a seznam funkcí** :
- Aplikace ověřovacího agenta samotného. Tato aplikace se spouští s oprávněními [NetworkService](/windows/win32/services/networkservice-account) .
- Aplikace aktualizace, která se používá k automatické aktualizaci ověřovacího agenta. Tato aplikace běží s oprávněními [LocalSystem](/windows/win32/services/localsystem-account) .

>[!IMPORTANT]
>Z hlediska zabezpečení by správci měli zacházet se serverem, na kterém běží agent PTA, jako by šlo o řadič domény.  Servery agenta PTA by měly být posílené na stejném řádku, jak je uvedeno v části [zabezpečení řadičů domény před útoky](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack) .

### <a name="authentication-agent-registration"></a>Registrace ověřovacího agenta

Po instalaci agenta pro ověřování musí být tento agent zaregistrované ve službě Azure AD. Azure AD přiřadí každému agentovi ověřování jedinečný certifikát digitální identity, který může použít pro zabezpečenou komunikaci s Azure AD.

Postup registrace také váže agenta ověřování s vaším klientem. Tím se zajistí, že Azure AD ví, že tento konkrétní agent ověřování je jedinou jedinou autorizací pro zpracování žádostí o ověření hesla pro vašeho tenanta. Tento postup se opakuje u každého nového ověřovacího agenta, který zaregistrujete.

Ověřovací agenti používají následující postup k registraci ve službě Azure AD:

![Registrace agenta](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD nejdřív vyžaduje, aby se globální správce přihlásil k Azure AD pomocí svých přihlašovacích údajů. Během přihlašování agent ověřování získá přístupový token, který může používat jménem globálního správce.
2. Agent ověřování pak vygeneruje pár klíčů: veřejný klíč a privátní klíč.
    - Pár klíčů se generuje prostřednictvím standardu RSA 2048-bit Encryption.
    - Privátní klíč zůstane na místním serveru, kde se nachází agent ověřování.
3. Agent ověřování vytvoří žádost o registraci do služby Azure AD prostřednictvím protokolu HTTPS s následujícími součástmi, které jsou součástí žádosti:
    - Přístupový token získaný v kroku 1.
    - Veřejný klíč vygenerovaný v kroku 2.
    - Žádost o podepsání certifikátu (CSR nebo žádost o certifikát). Tento požadavek se vztahuje na certifikát digitální identity s Azure AD jako certifikační autorita (CA).
4. Azure AD ověří přístupový token v žádosti o registraci a ověří, že požadavek pochází od globálního správce.
5. Služba Azure AD pak podepíše a pošle certifikát digitální identity zpátky agentovi ověřování.
    - Pro podepsání certifikátu se používá kořenová certifikační autorita v Azure AD. 

      > [!NOTE]
      > Tato certifikační autorita _není v úložišti_ důvěryhodné kořenové certifikační autority Windows.
    - Certifikační autorita se používá jenom funkcí předávacího ověřování. Certifikační autorita se používá pouze k podepisování zástupců oddělení služeb při registraci ověřovacího agenta.
    -  Žádná z ostatních služeb Azure AD tuto certifikační autoritu nepoužívá.
    - Předmět certifikátu (rozlišující název nebo rozlišující název DN) je nastaven na ID tenanta. Tento rozlišující název je identifikátor GUID, který jedinečně identifikuje vašeho tenanta. Tento rozlišující obor je certifikát pro použití jenom s vaším klientem.
6. Azure AD ukládá veřejný klíč agenta ověřování v databázi v Azure SQL Database, ke které má přístup jenom Azure AD.
7. Certifikát (vydaný v kroku 5) je uložený na místním serveru v úložišti certifikátů Windows (konkrétně v umístění [CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) ). Používá ho agent ověřování i aktualizační aplikace.

### <a name="authentication-agent-initialization"></a>Inicializace ověřovacího agenta

Když se ověřovací Agent spustí buď poprvé po registraci, nebo po restartování serveru, potřebuje způsob, jak bezpečně komunikovat se službou Azure AD a zahájit přijímání žádostí o ověření hesla.

![Inicializace agenta](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Tady je postup, jak se inicializují agenti ověřování:

1. Ověřovací agent zpřístupňuje službě Azure AD odchozí požadavek na zavedení. 
    - Tato žádost se provádí přes port 443 a je přes vzájemně ověřený kanál HTTPS. Požadavek používá stejný certifikát, který byl vydán během registrace ověřovacího agenta.
2. Azure AD odpoví na požadavek poskytnutím přístupového klíče ke frontě Azure Service Bus, která je pro vašeho tenanta jedinečná a která je identifikovaná vaším ID tenanta.
3. Agent ověřování vytvoří trvalé odchozí připojení HTTPS (přes port 443) do fronty. 
    - Agent ověřování je nyní připraven k načtení a zpracování požadavků na ověření hesla.

Pokud máte ve vašem tenantovi zaregistrovaných víc agentů ověřování, pak Inicializační procedura zajistí, že se každá z nich připojí ke stejné Service Busové frontě. 

## <a name="process-sign-in-requests"></a>Zpracovat žádosti o přihlášení 

Následující diagram ukazuje, jak předávací ověřování zpracovává požadavky uživatelů na přihlášení.

![Zpracovat přihlášení](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Předávací ověřování zpracovává požadavek na přihlášení uživatele následujícím způsobem: 

1. Uživatel se pokusí o přístup k aplikaci, například k aplikaci [Outlook Web App](https://outlook.office365.com/owa).
2. Pokud uživatel ještě není přihlášený, aplikace přesměruje prohlížeč na přihlašovací stránku služby Azure AD.
3. Služba TOKENů Azure AD odpoví zpátky pomocí **přihlašovací stránky uživatele** .
4. Uživatel zadá své uživatelské jméno do **přihlašovací stránky uživatele** a pak vybere tlačítko **Další** .
5. Uživatel zadá heslo do **přihlašovací stránky uživatele** a pak vybere tlačítko pro **přihlášení** .
6. Uživatelské jméno a heslo se odešle do služby Azure AD STS v žádosti POST protokolu HTTPS.
7. Služba Azure AD STS načte veřejné klíče pro všechny ověřovací agenty zaregistrované ve vašem tenantovi z Azure SQL Database a zašifruje je pomocí těchto hesel.
    - Vytvoří "N" šifrované hodnoty hesel pro ověřovací agenty "N" zaregistrované ve vašem tenantovi.
8. Azure AD STS ukládá žádost o ověření hesla, která se skládá z uživatelského jména a šifrovaných hodnot hesla, do fronty Service Bus specifické pro vašeho tenanta.
9. Vzhledem k tomu, že inicializované agenti ověřování jsou trvale připojeni k frontě Service Bus, jeden z dostupných ověřovacích agentů načte požadavek na ověření hesla.
10. Agent ověřování vyhledává hodnotu šifrovaného hesla, která je specifická pro svůj veřejný klíč, pomocí identifikátoru a dešifruje ho pomocí jeho privátního klíče.
11. Ověřovací agent se pokusí ověřit uživatelské jméno a heslo proti místní službě Active Directory pomocí [rozhraní Win32 API LogonUser](/windows/win32/api/winbase/nf-winbase-logonusera) s parametrem **dwLogonType** nastaveným na **LOGON32_LOGON_NETWORK**. 
    - Toto rozhraní API je stejné rozhraní API, které používá Active Directory Federation Services (AD FS) (AD FS) k přihlašování uživatelů ve scénáři federovaného přihlášení.
    - Toto rozhraní API využívá ke zjištění řadiče domény standardní proces řešení ve Windows serveru.
12. Agent ověřování obdrží výsledek ze služby Active Directory, například úspěch, uživatelské jméno nebo heslo je nesprávné nebo vypršela platnost hesla.

   > [!NOTE]
   > Pokud agent ověřování během procesu přihlašování dojde k chybě, celý požadavek na přihlášení je vyřazený. Neexistují žádné ruční žádosti o přihlášení od jednoho ověřovacího agenta k místnímu ověřovacímu agentovi. Tito agenti komunikují jenom s cloudem, a ne mezi sebou.
   
13. Agent ověřování přepošle výsledek zpátky do služby Azure AD STS přes odchozí kanál protokolu HTTPS přes port 443. Vzájemné ověřování používá certifikát, který se dřív vystavil agentovi ověřování během registrace.
14. Služba Azure AD STS ověřuje, že se tento výsledek koreluje s konkrétním přihlašovacím požadavkem ve vašem tenantovi.
15. Služba tokenů zabezpečení Azure AD pokračuje s postupem přihlášení, jak je nakonfigurované. Pokud bylo například ověření hesla úspěšné, může být uživatel vyzván pro Multi-Factor Authentication nebo přesměrován zpět do aplikace.

## <a name="operational-security-of-the-authentication-agents"></a>Provozní zabezpečení agentů ověřování

Aby bylo zajištěno, že předávací ověřování zůstane provozně zabezpečené, služba Azure AD pravidelně obnovuje certifikáty agentů ověřování. Služba Azure AD vyvolá obnovení. Obnovení se neřídí samotnými ověřovacími agenty.

![Provozní zabezpečení](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Postup obnovení vztahu důvěryhodnosti ověřovacího agenta s Azure AD:

1. Ověřovací agent pravidelně odesílá výzvu k otestování Azure AD každých pár hodin, aby zkontroloval, jestli je čas obnovit svůj certifikát. Certifikát se obnoví 30 dní před jeho vypršením platnosti.
    - Tato kontrolu se provádí prostřednictvím vzájemně ověřeného kanálu HTTPS a používá stejný certifikát, který byl vydán během registrace.
2. Pokud služba indikuje, že je čas na obnovení, vygeneruje ověřovací agent nový pár klíčů: veřejný klíč a privátní klíč.
    - Tyto klíče jsou generovány prostřednictvím standardního šifrování RSA 2048.
    - Privátní klíč nikdy neopouští místní server.
3. Agent ověřování pak provede požadavek na obnovení certifikátu do služby Azure AD prostřednictvím protokolu HTTPS s následujícími součástmi, které jsou součástí žádosti:
    - Existující certifikát, který byl načten z umístění CERT_SYSTEM_STORE_LOCAL_MACHINE v úložišti certifikátů Windows. V tomto postupu není žádný globální správce, a proto neexistuje žádný přístupový token, který je potřeba pro uživatele globálního správce.
    - Veřejný klíč vygenerovaný v kroku 2.
    - Žádost o podepsání certifikátu (CSR nebo žádost o certifikát). Tento požadavek se vztahuje na nový certifikát digitální identity s Azure AD jako certifikační autorita.
4. Azure AD ověří existující certifikát v žádosti o obnovení certifikátu. Potom ověří, že požadavek pochází z ověřovacího agenta zaregistrovaného ve vašem tenantovi.
5. Pokud je stávající certifikát stále platný, služba Azure AD pak podepíše nový certifikát digitální identity a vydá nový certifikát agentovi ověřování. 
6. Pokud platnost existujícího certifikátu vyprší, Azure AD odstraní agenta ověřování ze seznamu registrovaných ověřovacích agentů vašeho tenanta. Globální správce pak musí ručně nainstalovat a zaregistrovat nového ověřovacího agenta.
    - K podepsání certifikátu použijte kořenovou certifikační autoritu služby Azure AD.
    - Nastavte předmět certifikátu (rozlišující název nebo DN) na ID tenanta, identifikátor GUID, který jedinečně identifikuje vašeho tenanta. Obory názvu jsou jenom certifikáty pro vašeho tenanta.
6. Azure AD ukládá nový veřejný klíč ověřovacího agenta do databáze v Azure SQL Database, ke které má přístup jenom. Také zruší platnost starého veřejného klíče přidruženého k agentovi ověřování.
7. Nový certifikát (vydaný v kroku 5) je pak uložený na serveru v úložišti certifikátů Windows (konkrétně v umístění [CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER) ).
    - Vzhledem k tomu, že se postup obnovení vztahu důvěryhodnosti neprovádí interaktivně (bez přítomnosti globálního správce), agent ověřování již nemá přístup k aktualizaci stávajícího certifikátu v umístění CERT_SYSTEM_STORE_LOCAL_MACHINE. 
    
   > [!NOTE]
   > Tento postup neodebere samotný certifikát z CERT_SYSTEM_STORE_LOCAL_MACHINEho umístění.
8. Nový certifikát se používá k ověření od tohoto okamžiku. Každé následné obnovení certifikátu nahrazuje certifikát v umístění CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Automatické aktualizace ověřovacích agentů

Aplikace aktualizace automaticky aktualizuje ověřovacího agenta při vydání nové verze (s opravami chyb nebo vylepšení výkonu). Aplikace aktualizačního programu nezpracovává žádné žádosti o ověření hesla pro vašeho tenanta.

Azure AD hostuje novou verzi softwaru jako podepsaný **balíček Instalační služba systému Windows (MSI)**. Soubor MSI je podepsaný pomocí [technologie Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) s SHA256 jako algoritmem Digest. 

![Automatické aktualizace](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Automatické aktualizace ověřovacího agenta:

1. Aplikace aktualizace otestuje každou hodinu službu Azure AD a zkontroluje, jestli je k dispozici nová verze ověřovacího agenta. 
    - Tato kontrolu se provádí prostřednictvím vzájemně ověřeného kanálu HTTPS pomocí stejného certifikátu, který byl vydán během registrace. Agent ověřování a aktualizační program sdílí certifikát uložený na serveru.
2. Pokud je k dispozici nová verze, Azure AD vrátí podepsané MSI zpátky do aktualizace.
3. Aktualizace ověří, že je soubor MSI podepsaný Microsoftem.
4. Nástroj pro aktualizace spustí soubor MSI. Tato akce zahrnuje následující kroky:

   > [!NOTE]
   > Aktualizační program se spouští s oprávněními [místního systému](/windows/win32/services/localsystem-account) .

    - Zastaví službu Agent ověřování.
    - Nainstaluje novou verzi ověřovacího agenta na server.
    - Restartuje službu ověřovacího agenta.

>[!NOTE]
>Pokud máte ve svém tenantovi zaregistrovaných víc agentů ověřování, Azure AD certifikáty neobnovuje ani je neaktualizuje ve stejnou dobu. Místo toho služba Azure AD naplní jednu po druhé, aby zajistila vysokou dostupnost žádostí o přihlášení.
>


## <a name="next-steps"></a>Další kroky
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány a které nejsou.
- [Rychlý Start](how-to-connect-pta-quick-start.md): zprovoznění při předávacím ověřování Azure AD.
- [Migrace z AD FS na předávací ověřování](https://aka.ms/adfstoptadpdownload) – podrobný průvodce migrací z AD FS (nebo jiných federačních technologií) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): umožňuje nakonfigurovat funkci inteligentního uzamknutí pro vašeho tenanta, aby se chránily uživatelské účty.
- [Jak to funguje](how-to-connect-pta-how-it-works.md): Naučte se základy fungování předávacího ověřování Azure AD.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Přečtěte si odpovědi na nejčastější dotazy.
- [Řešení potíží](tshoot-connect-pass-through-authentication.md): Naučte se řešit běžné problémy s funkcí předávacího ověřování.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md): Přečtěte si další informace o této doplňkové funkci.