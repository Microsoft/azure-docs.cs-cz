---
title: Podrobné informace o zabezpečení předávacího ověřování služby Azure Active Directory| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak předávací ověřování služby Azure Active Directory (Azure AD) chrání vaše místní účty.
services: active-directory
keywords: Předávací ověřování Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185562"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Podrobný přehled zabezpečení předávacího ověřování služby Azure Active Directory

Tento článek obsahuje podrobnější popis fungování předávacího ověřování služby Azure Active Directory (Azure AD). Zaměřuje se na aspekty zabezpečení funkce. Tento článek je určen pro správce zabezpečení a IT, vedoucí oddělení pro dodržování předpisů a zabezpečení a další odborníky v oblasti IT, kteří jsou zodpovědní za zabezpečení IT a dodržování předpisů v malých a středních organizacích nebo velkých podnicích.

Mezi určená témata patří:
- Podrobné technické informace o instalaci a registraci agentů ověřování.
- Podrobné technické informace o šifrování hesel během přihlášení uživatele.
- Zabezpečení kanálů mezi místními agenty ověřování a službou Azure AD.
- Podrobné technické informace o tom, jak udržovat agenty ověřování provozní mise.
- Další témata související se zabezpečením.

## <a name="key-security-capabilities"></a>Klíčové možnosti zabezpečení

Toto jsou klíčové aspekty zabezpečení této funkce:
- Je postaven na zabezpečené víceklientské architektuře, která poskytuje izolaci požadavků na přihlášení mezi klienty.
- Místní hesla se nikdy neukládají v cloudu v žádné formě.
- Místní agenti ověřování, kteří poslouchají požadavky na ověření hesla a reagují na ně, pouze odchozí připojení z vaší sítě. Neexistuje žádný požadavek na instalaci těchto agentů ověřování v hraniční síti (DMZ). Jako osvědčený postup považovat všechny servery se systémem Autentizační agenti jako tier 0 systémy (viz [odkaz).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)
- Pouze standardní porty (80 a 443) se používají pro odchozí komunikaci z agenty ověřování do Azure AD. Není nutné otevírat příchozí porty na bráně firewall. 
  - Port 443 se používá pro veškerou ověřenou odchozí komunikaci.
  - Port 80 se používá pouze ke stažení seznamů odvolaných certifikátů (CRL), aby bylo zajištěno, že žádný z certifikátů používaných touto funkcí nebyl odvolán.
  - Úplný seznam síťových požadavků najdete v [tématu Předávací ověřování služby Azure Active Directory: Úvodní příručka](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Hesla, která uživatelé poskytují během přihlášení, jsou v cloudu zašifrována dříve, než je místní agenti ověřování přijmou k ověření ve službě Active Directory.
- Kanál HTTPS mezi službou Azure AD a místním agentem ověřování je zabezpečený pomocí vzájemného ověřování.
- Chrání vaše uživatelské účty bezproblémovou spoluprací se [zásadami podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md), včetně vícefaktorového ověřování (MFA), [blokování starších ověřování](../conditional-access/concept-conditional-access-conditions.md) a filtrování útoků [hrubou silou hesla](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Komponenty zapojené

Obecné podrobnosti o provozním, servisním a datovém zabezpečení Azure AD najdete v [Centru zabezpečení](https://azure.microsoft.com/support/trust-center/). Při použití předávacího ověřování pro přihlášení uživatele se jedná o následující součásti:
- **Azure AD STS**: Bezstavové služby tokenů zabezpečení (STS), která zpracovává požadavky na přihlášení a vydává tokeny zabezpečení pro prohlížeče, klienty nebo služby uživatelů podle potřeby.
- **Azure Service Bus**: Poskytuje komunikaci s podporou cloudu s podnikovou komunikací pro zasílání zpráv a přenosy, která vám pomůže připojit místní řešení s cloudem.
- **Agent ověřování Azure AD Connect:** Místní součást, která naslouchá a reaguje na požadavky na ověření hesla.
- **Azure SQL Database**: Obsahuje informace o agentech ověřování vašeho tenanta, včetně jejich metadat a šifrovacích klíčů.
- **Active Directory**: Místní služba Active Directory, kde jsou uloženy uživatelské účty a jejich hesla.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Instalace a registrace autentizačních agentů

Agenti ověřování jsou nainstalované a registrované ve službě Azure AD, když:
   - [Povolení předávacího ověřování prostřednictvím služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Přidání dalších agentů ověřování, abyste zajistili vysokou dostupnost žádostí o přihlášení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Získání agenta ověřování práce zahrnuje tři hlavní fáze:

1. Instalace agenta ověřování
2. Registrace agenta ověřování
3. Inicializace agenta ověřování

Následující části podrobně popisují tyto fáze.

### <a name="authentication-agent-installation"></a>Instalace agenta ověřování

Pouze globální správci můžete nainstalovat agenta ověřování (pomocí Azure AD Connect nebo samostatně) na místním serveru. Instalace přidá do seznamu Programy**Programs** > **a funkce** **Ovládacích panelů** > dvě nové položky:
- Samotná aplikace agenta ověřování. Tato aplikace je spuštěna s oprávněními [NetworkService.](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)
- Aplikace Updater, která se používá k automatické aktualizaci agenta ověřování. Tato aplikace je spuštěna s oprávněními [LocalSystem.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

### <a name="authentication-agent-registration"></a>Registrace agenta ověřování

Po instalaci agenta ověřování, musí se zaregistrovat sám s Azure AD. Azure AD přiřadí každému agentovi ověřování jedinečný certifikát digitální identity, který může použít pro zabezpečenou komunikaci se službou Azure AD.

Postup registrace také váže agenta ověřování s tenantem. Tím zajistíte, že Azure AD ví, že tento konkrétní agent ověřování je jediný oprávněný ke zpracování požadavků na ověření hesla pro vašeho tenanta. Tento postup se opakuje pro každého nového agenta ověřování, který zaregistrujete.

Agenti ověřování používají následující kroky k registraci ve službě Azure AD:

![Registrace agenta](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD nejprve požaduje, aby se globální správce přihlásil ke službě Azure AD pomocí svých přihlašovacích údajů. Během přihlašování agent ověřování získá přístupový token, který může použít jménem globálního správce.
2. Agent ověřování pak generuje dvojici klíčů: veřejný klíč a soukromý klíč.
    - Dvojice klíčů je generována pomocí standardního rsa 2048bitového šifrování.
    - Soukromý klíč zůstane na místním serveru, kde je umístěn agent ověřování.
3. Agent ověřování provede požadavek na registraci do služby Azure AD přes protokol HTTPS s následujícími součástmi zahrnutými v požadavku:
    - Přístupový token získaný v kroku 1.
    - Veřejný klíč generovaný v kroku 2.
    - Žádost o podpis certifikátu (CSR nebo žádost o certifikát). Tento požadavek se vztahuje na certifikát digitální identity, s Azure AD jako jeho certifikační autority (CA).
4. Azure AD ověří přístupový token v žádosti o registraci a ověří, že požadavek pochází od globálního správce.
5. Azure AD pak podepíše a odešle certifikát digitální identity zpět agenta ověřování.
    - Kořenová certifikační autorita ve službě Azure AD se používá k podepsání certifikátu. 

      > [!NOTE]
      > Tento certifikační úřad _není_ v úložišti důvěryhodných kořenových certifikátů systému Windows.
    - Certifikační autorita je používána pouze funkcí předávacího ověřování. Certifikační autorita se používá pouze k podepisování služeb ní při registraci agenta ověřování.
    -  Žádná z ostatních služeb Azure AD používat tento certifikační autoritu.
    - Předmět certifikátu (Rozlišující název nebo DN) je nastaven na ID vašeho klienta. Tento dn je identifikátor GUID, který jednoznačně identifikuje vašeho klienta. Tento DN obory certifikát pro použití pouze s tenantem.
6. Azure AD ukládá veřejný klíč agenta ověřování v databázi Azure SQL, ke které má přístup jenom Azure AD.
7. Certifikát (vydaný v kroku 5) je uložen na místním serveru v úložišti certifikátů systému Windows (konkrétně v [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) umístění). Používá se agenta ověřování a aktualizační aplikace.

### <a name="authentication-agent-initialization"></a>Inicializace agenta ověřování

Při spuštění agenta ověřování, buď poprvé po registraci nebo po restartování serveru, potřebuje způsob, jak bezpečně komunikovat se službou Azure AD a začít přijímat požadavky na ověření hesla.

![Inicializace agenta](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Zde je způsob, jakým jsou agenti ověřování inicializováni:

1. Agent ověřování provede požadavek odchozí zaváděcí pasti do služby Azure AD. 
    - Tento požadavek se provádí přes port 443 a je přes vzájemně ověřený kanál HTTPS. Požadavek používá stejný certifikát, který byl vydán během registrace agenta ověřování.
2. Azure AD reaguje na požadavek poskytnutím přístupového klíče do fronty Azure Service Bus, která je jedinečná pro vašeho tenanta a která je identifikována vaším ID klienta.
3. Agent ověřování vytvoří trvalé odchozí připojení HTTPS (přes port 443) do fronty. 
    - Agent ověřování je nyní připraven k načtení a zpracování požadavků na ověření hesla.

Pokud máte více agentů ověřování registrovaných na vašem tenantovi, pak postup inicializace zajišťuje, že každý z nich se připojí ke stejné frontě Service Bus. 

## <a name="process-sign-in-requests"></a>Zpracovat žádosti o přihlášení 

Následující diagram znázorňuje, jak předávací ověřování zpracovává požadavky na přihlášení uživatelů.

![Zpracovat přihlášení](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Předávací ověřování zpracovává požadavek na přihlášení uživatele následujícím způsobem: 

1. Uživatel se pokusí získat přístup k aplikaci, například [Outlook Web App](https://outlook.office365.com/owa).
2. Pokud uživatel ještě není přihlášen, aplikace přesměruje prohlížeč na přihlašovací stránku Azure AD.
3. Služba Azure AD STS odpoví zpět s **přihlašovací stránku uživatele.**
4. Uživatel zadá své uživatelské jméno na přihlašovací stránku **uživatele** a potom vybere tlačítko **Další.**
5. Uživatel zadá své heslo na přihlašovací stránku **uživatele** a potom vybere tlačítko **Přihlásit** se.
6. Uživatelské jméno a heslo se zasílá do služby Azure AD STS v požadavku HTTPS POST.
7. Azure AD STS načte veřejné klíče pro všechny agenty ověřování registrovaných ve vašem tenantovi z databáze Azure SQL a šifruje heslo pomocí nich.
    - Vytváří "N" šifrované hodnoty hesla pro "N" ověřovací agenti registrovaní ve vašem tenantovi.
8. Azure AD STS umístí požadavek ověření hesla, který se skládá z uživatelského jména a šifrované hodnoty hesla, do fronty Service Bus specifické pro vašeho tenanta.
9. Vzhledem k tomu, že inicializovaní agenti ověřování jsou trvale připojeni k frontě služby Service Bus, jeden z dostupných agentů ověřování načte požadavek na ověření hesla.
10. Agent ověřování vyhledá hodnotu šifrovaného hesla, která je specifická pro jeho veřejný klíč, pomocí identifikátoru a dešifruje ji pomocí jeho soukromého klíče.
11. Agent ověřování se pokusí ověřit uživatelské jméno a heslo proti místní službě Active Directory pomocí rozhraní API Aplikace Pro přihlášení k systému [Souborů Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) s parametrem **dwLogonType** nastaveným na **LOGON32_LOGON_NETWORK**. 
    - Toto rozhraní API je stejné rozhraní API, které používá služba AD FS (AD FS) k přihlášení uživatelů ve federovaném scénáři přihlášení.
    - Toto rozhraní API při vyhledání řadiče domény závisí na procesu standardního rozlišení v systému Windows Server.
12. Agent ověřování obdrží výsledek ze služby Active Directory, například úspěch, uživatelské jméno nebo nesprávné heslo nebo vypršela platnost hesla.

   > [!NOTE]
   > Pokud agent ověřování selže během procesu přihlášení, je zrušen celý požadavek na přihlášení. Neexistuje žádné předání žádostí o přihlášení z jednoho agenta ověřování do jiného agenta ověřování v místním prostředí. Tito agenti komunikují pouze s cloudem, a ne mezi sebou.
   
13. Agent ověřování předává výsledek zpět do služby Azure AD STS přes odchozí vzájemně ověřený kanál HTTPS přes port 443. Vzájemné ověřování používá certifikát dříve vydaný agentovi ověřování během registrace.
14. Azure AD STS ověří, že tento výsledek koreluje s konkrétní požadavek na přihlášení na vašem tenantovi.
15. Azure AD STS pokračuje s postupem přihlášení, jak je nakonfigurován. Například pokud ověření hesla byla úspěšná, uživatel může být napadena pro vícefaktorové ověřování nebo přesměrovánzpět do aplikace.

## <a name="operational-security-of-the-authentication-agents"></a>Provozní bezpečnost autentizačních agentů

Chcete-li zajistit, že předávací ověřování zůstane provozně zabezpečené, Azure AD pravidelně obnovuje certifikáty agentů ověřování. Azure AD aktivuje obnovení. Obnovení se neřídí samotnými agenty ověřování.

![Provozní zabezpečení](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Obnovení důvěryhodnosti agenta ověřování pomocí služby Azure AD:

1. Agent ověřování pravidelně ping Azure AD každých několik hodin ke kontrole, zda je čas obnovit svůj certifikát. Certifikát je obnoven 30 dní před vypršením jeho platnosti.
    - Tato kontrola se provádí přes vzájemně ověřený kanál HTTPS a používá stejný certifikát, který byl vydán při registraci.
2. Pokud služba označuje, že je čas na obnovení, agent ověřování generuje nový pár klíčů: veřejný klíč a soukromý klíč.
    - Tyto klíče jsou generovány pomocí standardního rsa 2048bitového šifrování.
    - Soukromý klíč nikdy neopustí místní server.
3. Agent ověřování pak provede požadavek na obnovení certifikátu pro Azure AD přes PROTOKOL HTTPS s následujícími součástmi zahrnutými v požadavku:
    - Existující certifikát, který je načten z umístění CERT_SYSTEM_STORE_LOCAL_MACHINE v úložišti certifikátů systému Windows. Do tohoto postupu není zapojen žádný globální správce, takže není potřeba žádný přístupový token jménem globálního správce.
    - Veřejný klíč generovaný v kroku 2.
    - Žádost o podpis certifikátu (CSR nebo žádost o certifikát). Tento požadavek se vztahuje na nový certifikát digitální identity, s Azure AD jako jeho certifikační autority.
4. Azure AD ověří existující certifikát v žádosti o obnovení certifikátu. Pak ověří, že požadavek pochází z agenta ověřování registrovaného ve vašem tenantovi.
5. Pokud je existující certifikát stále platný, Azure AD pak podepíše nový certifikát digitální identity a vydá nový certifikát zpět agentovi ověřování. 
6. Pokud platnost existujícího certifikátu vypršela, Azure AD odstraní agenta ověřování ze seznamu registrovaných agentů ověřování vašeho tenanta. Globální správce pak musí ručně nainstalovat a zaregistrovat nového agenta ověřování.
    - K podepsání certifikátu použijte kořenovou certifikační autoritu Azure AD.
    - Nastavte předmět certifikátu (Rozlišující název nebo DN) na id klienta, identifikátor GUID, který jednoznačně identifikuje vašeho klienta. DN obory certifikát u vašeho klienta pouze.
6. Azure AD ukládá nový veřejný klíč agenta ověřování v databázi Azure SQL, ke které má přístup jenom k němu. Také zruší platnost starého veřejného klíče přidruženého k agentovi ověřování.
7. Nový certifikát (vydaný v kroku 5) je pak uložen na serveru v úložišti certifikátů systému Windows (konkrétně v [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) umístění).
    - Vzhledem k tomu, že postup obnovení důvěryhodnosti probíhá neinteraktivně (bez přítomnosti globálního správce), agent ověřování již nemá přístup k aktualizaci existujícího certifikátu v CERT_SYSTEM_STORE_LOCAL_MACHINE umístění. 
    
   > [!NOTE]
   > Tento postup neodebere samotný certifikát z umístění CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Nový certifikát se používá pro ověřování od tohoto okamžiku. Každé následné obnovení certifikátu nahrazuje certifikát v CERT_SYSTEM_STORE_LOCAL_MACHINE místě.

## <a name="auto-update-of-the-authentication-agents"></a>Automatická aktualizace agentů ověřování

Aplikace Updater automaticky aktualizuje agenta ověřování při vydání nové verze (s opravami chyb nebo vylepšeními výkonu). Aplikace Updater nezpracovává žádné požadavky na ověření hesla pro vašeho klienta.

Azure AD hostuje novou verzi softwaru jako podepsaný **balíček Instalační služby systému Windows (MSI).** MSI je podepsána pomocí [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) s SHA256 jako algoritmus digest. 

![Automatická aktualizace](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Automatická aktualizace agenta ověřování:

1. Aplikace Updater ping Azure AD každou hodinu ke kontrole, pokud je k dispozici nová verze agenta ověřování. 
    - Tato kontrola se provádí přes vzájemně ověřený kanál HTTPS pomocí stejného certifikátu, který byl vydán během registrace. Agent ověřování a Updater sdílejí certifikát uložený na serveru.
2. Pokud je k dispozici nová verze, Azure AD vrátí podepsané MSI zpět updater.
3. Updater ověří, zda je MSI podepsánspolečností Microsoft.
4. Updater spustí MSI. Tato akce zahrnuje následující kroky:

   > [!NOTE]
   > Updater běží s oprávněními [místního systému.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

    - Zastaví službu Agent ověřování.
    - Nainstaluje na server novou verzi agenta ověřování.
    - Restartuje službu Agent ověřování.

>[!NOTE]
>Pokud máte více agentů ověřování registrovaných ve vašem tenantovi, Azure AD neobnoví jejich certifikáty nebo aktualizovat je ve stejnou dobu. Místo toho Azure AD tak činí jeden po druhém k zajištění vysoké dostupnosti žádostí o přihlášení.
>


## <a name="next-steps"></a>Další kroky
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, které scénáře jsou podporovány a které ne.
- [Úvodní příručka:](how-to-connect-pta-quick-start.md)Zprovoznění předávacího ověřování Azure AD.
- [Migrace ze služby AD FS do předávacího ověřování](https://aka.ms/adfstoptadpdownload) – podrobný průvodce migrací ze služby AD FS (nebo jiných federačních technologií) do předávacího ověřování.
- [Inteligentní uzamčení:](../authentication/howto-password-smart-lockout.md)Nakonfigurujte funkci inteligentního uzamčení ve vašem tenantovi k ochraně uživatelských účtů.
- [Jak to funguje](how-to-connect-pta-how-it-works.md): Naučte se základy fungování předávacího ověřování Azure AD.
- [Nejčastější dotazy](how-to-connect-pta-faq.md): Najděte odpovědi na často kladené otázky.
- [Poradce při potížích](tshoot-connect-pass-through-authentication.md): Přečtěte si, jak vyřešit běžné problémy s funkcí Předávací ověřování.
- [Azure AD bezproblémové jednotné přihlašovací](how-to-connect-sso.md)služby : Další informace o této doplňkové funkce.
