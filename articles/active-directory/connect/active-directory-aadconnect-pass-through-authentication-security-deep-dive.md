---
title: Azure Active Directory předávací ověřování podrobné informace o zabezpečení | Dokumentace Microsoftu
description: Tento článek popisuje, jak předávací ověřování služby Azure Active Directory (Azure AD) chrání vaše místní účty
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f220e0b6dd5abb596128ba84af89d0e725f66117
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521961"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory předávací ověřování podrobné informace o zabezpečení

Tento článek poskytuje podrobnější popis toho, jak funguje předávací ověřování služby Azure Active Directory (Azure AD). Zaměřuje se na aspekty zabezpečení funkce. Tento článek je určený pro zabezpečení a IT správce, hlavní dodržování předpisů a zabezpečením a dalším IT profesionálům, kteří jsou zodpovědní za zabezpečení IT a dodržování předpisů na malé až střední velikosti organizace nebo velké podniky.

Zákazníky a vyřešené témata:
- Podrobné technické informace o tom, jak nainstalovat a zaregistrovat agentů ověřování.
- Podrobné technické informace o šifrování hesla při přihlášení uživatele.
- Zabezpečení těchto kanálů mezi místní ověřování agentů a Azure AD.
- Podrobné technické informace o tom, jak provozně zabezpečit agentů ověřování.
- Další témata související se zabezpečením.

## <a name="key-security-capabilities"></a>Klíčových bezpečnostních schopností

Jde o aspektech zabezpečení klíče této funkce:
- Orchard je založen na zabezpečenou architekturu s více klienty, která zajišťuje izolaci žádostí o přihlášení mezi tenanty.
- Místních hesel se nikdy neukládají v cloudu v libovolné formě.
- Místní ověřování agenty, které naslouchají hrozeb a reakce na, odchozí připojení z v rámci vaší sítě vytvořit pouze žádostí o ověření hesla. Není nutné k instalaci těchto agentů ověřování v hraniční síti (DMZ). Jako osvědčený postup považovat všechny servery vrstvy 0 systémy spuštěná agentů ověřování (viz [odkaz](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Pro odchozí komunikaci z agentů ověřování do služby Azure AD se používají pouze standardní porty (80 a 443). Není nutné otevřít příchozí porty na bráně firewall. 
  - Pro všechny ověřené odchozí komunikace se používá port 443.
  - Port 80 slouží pouze pro stahování seznamy odvolaných certifikátů (CRL) k zajištění, že žádný z certifikátů používaných touto funkcí byl odvolán.
  - Úplný seznam požadavků na síť, naleznete v tématu [předávacího ověřování Azure Active Directory: rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Hesla, které uživatelé poskytnout během přihlášení jsou šifrované v cloudu, předtím, než je agentů ověřování místní přijmout pro ověření na základě služby Active Directory.
- Kanál protokolu HTTPS mezi službami Azure AD a místní ověřovací Agent se šifrují pomocí vzájemného ověřování.
- Chrání vaše uživatelské účty tím, že funguje bez problémů s [zásady podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md), včetně služby Multi-Factor Authentication (MFA), [blokování starší verze ověřování](../conditional-access/conditions.md) a [ filtrování útoky na hesla hrubou silou](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Součástí

Obecné podrobnosti o zabezpečení dat a provozní, služba Azure AD, najdete v článku [centrum](https://azure.microsoft.com/support/trust-center/). Při používání předávacího ověřování pro přihlášení uživatele se podílejí následující komponenty:
- **Služby tokenů zabezpečení Azure AD**: bezstavové služby tokenů zabezpečení (STS), která zpracovává požadavky na přihlášení a vydává tokeny zabezpečení pro prohlížeče, klienty nebo služby podle potřeby uživatelů.
- **Azure Service Bus**: poskytuje cloudovou komunikaci s podnikovým zasíláním zpráv a propojení komunikace, která pomáhá propojit místní řešení s cloudem.
- **Agent Azure AD Connect ověřování**: komponentu v místním, která přijímá a reaguje na požadavky ověřování hesla.
- **Azure SQL Database**: obsahuje informace o vašem tenantovi agentů ověřování, včetně jejich metadata, šifrovacích klíčů.
- **Služby Active Directory**: místní služby Active Directory, kde jsou uloženy vaše uživatelské účty a hesla.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalace a registrace agentů ověřování

Agentů ověřování jsou instalaci a registraci do služby Azure AD při můžete buď:
   - [Povolit předávací ověřování prostřednictvím služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Přidejte další agenty ověřování k zajištění vysoké dostupnosti žádostí o přihlášení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Začínáme pracovní ověřovacího agenta služby zahrnuje tři hlavní fáze:

1. Ověření instalace agenta
2. Ověření registrace agenta
3. Inicializace ověřování agenta

Následující části popisují tyto fáze podrobně.

### <a name="authentication-agent-installation"></a>Ověření instalace agenta

Jenom globální správci můžete nainstalovat ověřovacího agenta (pomocí služby Azure AD Connect nebo samostatné) na místním serveru. Přidá dva nové položky pro instalaci **ovládací panely** > **programy** > **programy a funkce** seznamu:
- Samotná aplikace ověřovacího agenta. Tato aplikace funguje s [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) oprávnění.
- Aktualizátor aplikace, který se používá k automatické aktualizaci ověřovacího agenta. Tato aplikace funguje s [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) oprávnění.

### <a name="authentication-agent-registration"></a>Ověření registrace agenta

Po instalaci ověřovací Agent potřebuje k registraci v Azure AD. Azure AD přiřadí každé ověřovací Agent jedinečné, digitální identity certifikát, který můžete použít pro zabezpečenou komunikaci s Azure AD.

Postup registrace sváže ověřovacího agenta v tenantu. Tím se zajistí, že Azure AD ví, že tato konkrétní ověřovací Agent je pouze jeden oprávnění pro zpracování žádostí o ověření hesla pro vašeho tenanta. Tento postup se opakuje pro každý nový ověřovací Agent zaregistrovat.

Agentů ověřování použijte následující postup zaregistrovat se službou Azure AD:

![Registrace agenta](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD poprvé požádá, že se globální správce přihlásit ke službě Azure AD pomocí svých přihlašovacích údajů. Při přihlašování ověřování agenta získá přístupový token, který můžete použít jménem globálního správce.
2. Ověřovací Agent poté vygeneruje pár klíče: veřejný klíč a soukromý klíč.
    - Pár klíčů je generována pomocí standardního šifrování RSA 2048 bitů.
    - Privátní klíč zůstane na na místním serveru, ve které se nachází Agent ověřování.
3. Ověřovací Agent odešle "registrace" požadavek do služby Azure AD prostřednictvím protokolu HTTPS, s následujícími součástmi zahrnutý v požadavku:
    - Přístupový token získali v kroku 1.
    - Veřejný klíč vygenerovaný v kroku 2.
    - Žádost o podepsání certifikátu (CSR nebo žádost o certifikát). Tento požadavek platí i pro certifikát digitálních identit s Azure AD jako jeho certifikační autority (CA).
4. Azure AD ověří přístupový token v žádosti o registraci a ověřuje, že žádost pochází ze globálního správce.
5. Azure AD pak přihlásí a odešle certifikát digitální identity ověřovacího agenta.
    - Kořenové certifikační Autority ve službě Azure AD se používá k podepsání certifikátu. 

     >[!NOTE]
     > Tato certifikační Autorita je _není_ Windows důvěryhodné kořenové certifikační autority úložiště.
    - Certifikační Autorita používá pouze funkce předávací ověřování. Certifikační Autority slouží pouze k přihlášení během registrace ověřovacího agenta služby zákazníkům.
    -  Žádné další služby Azure AD pomocí této certifikační Autority.
    - Subjekt certifikátu (rozlišující název nebo název domény) je nastavena na ID vašeho tenanta. Tento název domény je identifikátor GUID, který jednoznačně identifikuje vašeho tenanta. Tento název domény obory certifikátu pro použití pouze v tenantu.
6. Veřejný klíč ověřovacího agenta služby Azure AD ukládá v databázi Azure SQL, která jenom služby Azure AD má přístup k.
7. Certifikát (vydané v kroku 5) je uložen na místním serveru do úložiště certifikátů Windows (konkrétně v [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) umístění). Používá se ověřovací Agent i aplikace Updater.

### <a name="authentication-agent-initialization"></a>Inicializace ověřování agenta

Při spuštění ověřovacího agenta, poprvé po registraci nebo po server restartovat, potřebuje způsob, jak bezpečně komunikovat se službou Azure AD a začněte přijímat žádosti o ověření hesla.

![Inicializace agenta](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Zde je, jak jsou inicializovány agentů ověřování:

1. Ověřovací Agent zašle odchozí bootstrap požadavek do služby Azure AD. 
    - Tento požadavek se provádí přes port 443 a je nad vzájemně ověřené kanálu protokolu HTTPS. Požadavek používá stejný certifikát, který byl vydán během registrace agenta ověřování.
2. Azure AD odpoví na požadavek tím, že poskytuje přístupový klíč do fronty služby Azure Service Bus, která je pro vašeho klienta jedinečný a, který je identifikován podle ID vašeho tenanta.
3. Ověřovací Agent díky trvalé odchozí připojení HTTPS (přes port 443) fronty. 
    - Ověřovací Agent je nyní připraven k načtení a zpracování požadavků na ověření hesla.

Pokud máte více agentů ověřování zaregistrovaný ve svém tenantovi, pak inicializační proceduru zajistí, že každý z nich připojí ke stejné fronty služby Service Bus. 

## <a name="process-sign-in-requests"></a>Zpracování žádostí o přihlášení 

Následující diagram znázorňuje, jak předávací ověřování zpracovává požadavky přihlášení uživatele.

![Proces přihlášení](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Předávací ověřování zpracování požadavku na přihlášení uživatele následujícím způsobem: 

1. Uživatel se pokusí o přístup k aplikaci, například [Outlook Web App](https://outlook.office365.com/owa).
2. Pokud ještě není přihlášení uživatele, aplikace prohlížeč přesměruje na přihlašovací stránce služby Azure AD.
3. Odpoví služba tokenů zabezpečení Azure AD zpátky s **přihlášení uživatele** stránky.
4. Uživatel zadá své uživatelské jméno do **přihlášení uživatele** stránce a potom vybere **Další** tlačítko.
5. Uživatel zadá své heslo do **přihlášení uživatele** stránce a potom vybere **přihlášení** tlačítko.
6. Uživatelské jméno a heslo jsou odeslány do Azure AD Služba tokenů zabezpečení ve požadavek POST protokolu HTTPS.
7. Azure AD STS načte veřejných klíčů pro všechny agenty ověřování zaregistrovaný ve svém tenantovi ze služby Azure SQL database a heslo zašifruje pomocí nich. 
    - Vyvolá hodnoty "N" šifrované heslo pro ověření "N" agenti zaregistrovaní ve svém tenantovi.
8. Azure AD STS umístí žádosti o ověření hesla se skládá z uživatelského jména a hodnoty šifrované heslo, do fronty služby Service Bus, který je určený výhradně pro vašeho tenanta.
9. Protože inicializované agentů ověřování trvalé připojení do fronty služby Service Bus, jeden z dostupných agentů ověřování načte žádosti o ověření hesla.
10. Ověřovací Agent vyhledá šifrované heslo hodnotu, která je specifická pro svůj veřejný klíč, s použitím identifikátoru a dešifruje ji pomocí jeho privátní klíč.
11. Ověřovací Agent se pokusí ověřit uživatelské jméno a heslo pro místní služby Active Directory s použitím [rozhraní API systému Win32 LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) s **dwLogonType** parametr nastaven na **LOGON32_LOGON_NETWORK**. 
    - Toto rozhraní API je stejného rozhraní API, která používá služby Active Directory Federation Services (AD FS) k přihlašování uživatelů ve scénáři federované přihlášení.
    - Toto rozhraní API se spoléhá na standardním rozlišením procesu ve Windows serveru k vyhledání řadiče domény.
12. Ověřovací Agent přijímá výsledek ze služby Active Directory, jako je například úspěch, uživatelské jméno nebo heslo není správné nebo platnost hesla vypršela.
13. Ověřovací Agent výsledek předá zpět služby tokenů zabezpečení Azure AD přes odchozí vzájemně ověřené kanál HTTPS přes port 443. Vzájemné ověřování používá certifikát k ověřování agenta dříve vydané během registrace.
14. Služba tokenů zabezpečení Azure AD ověří, že tento výsledek koreluje s konkrétní přihlášení žádost ve svém tenantovi.
15. Azure AD STS pokračuje s postupem přihlášení podle konfigurace. Například pokud ověření hesla byla úspěšná, uživatel může být postiženy omezeními pro ověřování službou Multi-Factor Authentication nebo přesměrován zpět do aplikace.

## <a name="operational-security-of-the-authentication-agents"></a>Provozní zabezpečení agentů ověřování

Aby bylo zajištěno, že předávací ověřování zůstává provozně zabezpečené, pravidelně Azure AD se tato možnost obnoví agentů ověřování certifikátů. Azure AD se aktivuje prodloužení. Prodloužení se neřídí agentů ověřování sami.

![Provozní zabezpečení](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Obnovit ověřovací Agent vztah důvěryhodnosti s Azure AD:

1. Ověřovací Agent příkaz ping pravidelně Azure AD každých pár hodin ke kontrole, pokud je čas na obnovení certifikátu. Certifikát se obnovuje 30 dnů před vypršením jeho platnosti.
    - Tato kontrola se provádí přes vzájemně ověřené kanálu protokolu HTTPS a používá stejný certifikát, který byl vydán během registrace.
2. Pokud službu označuje, že je čas k obnovení, ověřovací Agent vygeneruje nový pár klíčů: veřejný klíč a soukromý klíč.
    - Tyto klíče jsou vygenerované pomocí standardního šifrování RSA 2048 bitů.
    - Privátní klíč se nikdy neopustí na místním serveru.
3. Ověřovací Agent pak požádá "obnovení certifikátu" do služby Azure AD prostřednictvím protokolu HTTPS, s následujícími součástmi zahrnutý v požadavku:
    - Existující certifikát, který je načten z CERT_SYSTEM_STORE_LOCAL_MACHINE umístění v úložišti certifikátů Windows. Zahrnuté v tomto postupu, neexistuje žádný globální správce, takže neexistuje žádný token přístupu potřeby jménem globálního správce.
    - Veřejný klíč vygenerovaný v kroku 2.
    - Žádost o podepsání certifikátu (CSR nebo žádost o certifikát). Tento požadavek platí i pro nový certifikát digitálních identit s Azure AD jako jeho certifikační autority.
4. Azure AD ověří existujícího certifikátu v žádost o obnovení certifikátu. Poté ověří, že žádost pochází z ověřování agenta registrován ve svém tenantovi.
5. Pokud je stále platný stávající certifikát, Azure AD pak zaregistruje nový certifikát digitální identity a vydá nový certifikát zpět do agenta ověřování. 
6. Pokud existujícího certifikátu vypršela platnost, Azure AD z vašeho tenanta seznamu registrovaných agentů ověřování odstraní ověřovacího agenta. Pak musí ručně nainstalovat a zaregistrovat nové ověřovací Agent globální správce.
    - Azure AD kořenovou certifikační Autoritu použijte k podepsání certifikátu.
    - Nastavte předmět certifikátu (rozlišující název nebo název domény) na ID vašeho tenanta, identifikátor GUID, který jednoznačně identifikuje vašeho tenanta. Název domény obory certifikát, který chcete pouze vašeho tenanta.
6. Nový veřejný klíč ověřovacího agenta služby Azure AD uloží ve službě Azure SQL database, která pouze má přístup k. Také zruší platnost starých veřejný klíč přidružený k ověřování agenta.
7. Nový certifikát (vydané v kroku 5) jsou pak uloženy na serveru do úložiště certifikátů Windows (konkrétně v [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) umístění).
    - Protože postup obnovení vztah důvěryhodnosti se stane neinteraktivně (bez přítomnosti globální správce), ověřovací Agent již nemá přístup k aktualizaci existujícího certifikátu v CERT_SYSTEM_STORE_LOCAL_MACHINE umístění. 
    
   > [!NOTE]
   > Tento postup neodebere z umístění CERT_SYSTEM_STORE_LOCAL_MACHINE vlastní certifikát.
8. Nový certifikát se používá k ověřování z tohoto bodu na. Každé následné obnovení certifikátu nahradí certifikát v CERT_SYSTEM_STORE_LOCAL_MACHINE umístění.

## <a name="auto-update-of-the-authentication-agents"></a>Automatické aktualizace agentů ověřování

Aktualizátor aplikaci automaticky aktualizuje ověřovací Agent po vydání nové verze. Aplikace nezpracovává veškerých žádostí o ověření hesla pro vašeho tenanta. 

Azure AD je hostitelem nová verze softwaru podepsané **balíček Instalační služby systému Windows (MSI)**. Soubor MSI je podepsaná pomocí [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) s SHA256 jako algoritmus digest. 

![Automatická aktualizace](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

K automatické aktualizaci ověřovací Agent:

1. Příkazy ping aplikace Aktualizátoru Azure AD každou hodinu kvůli kontrole, jestli je k dispozici nová verze ověřovacího agenta služby. 
    - Tato kontrola se provádí přes kanál protokolu HTTPS vzájemně ověřené pomocí stejného certifikátu, který byl vydán během registrace. Ověřovací Agent a Aktualizátoru sdílet certifikát uložený na serveru.
2. Pokud je dostupná nová verze, Azure AD vrátí zpět do Aktualizátoru podepsaný soubor MSI.
3. Aktualizátoru ověřuje, že soubor MSI je podepsán společností Microsoft.
4. Spuštění aktualizační soubor MSI. Tato akce zahrnuje následující kroky:

 > [!NOTE]
 > Se systémem Aktualizátoru [místní systém](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) oprávnění.

    - Zastaví službu ověřování agenta
    - Nainstaluje novou verzi ověřovacího agenta na server
    - Restartuje službu ověřování agenta

>[!NOTE]
>Pokud máte více agentů ověřování zaregistrovaný ve svém tenantovi Azure AD nepodporuje obnovit své certifikáty ani aktualizovat ve stejnou dobu. Místo toho Azure AD se proto postupně k zajištění vysoké dostupnosti žádostí o přihlášení.
>


## <a name="next-steps"></a>Další postup
- [Aktuální omezení](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Zjistěte, jaké postupy se podporují, a ty, které nejsou.
- [Rychlý start](active-directory-aadconnect-pass-through-authentication-quick-start.md): uvedení do provozu na předávacího ověřování Azure AD.
- [Migrace ze služby AD FS na předávací ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) – podrobné pokyny k migraci ze služby AD FS (nebo jiné technologie federation) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Nakonfigurujte schopnosti inteligentním uzamčením ve svém tenantovi k ochraně uživatelské účty.
- [Jak to funguje](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Seznamte se se základy toho, jak funguje předávacího ověřování Azure AD.
- [Nejčastější dotazy k](active-directory-aadconnect-pass-through-authentication-faq.md): Najděte odpovědi na nejčastější dotazy.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Zjistěte, jak řešit běžné problémy s funkcí předávací ověřování.
- [Azure AD bezproblémového jednotného přihlašování k](active-directory-aadconnect-sso.md): Další informace o této doplňkové funkce.
