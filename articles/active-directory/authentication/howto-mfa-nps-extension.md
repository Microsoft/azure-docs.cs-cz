---
title: Použití Azure Multi-Factor Authentication se serverem NPS – Azure Active Directory
description: Naučte se používat funkce Azure Multi-Factor Authentication s vaší stávající infrastrukturou ověřování serveru NPS (Network Policy Server).
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f7214668fc63ec8bb2e0c23d264c1aa01c9148ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91368346"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrace stávající infrastruktury serveru NPS (Network Policy Server) s Azure Multi-Factor Authentication

Rozšíření serveru NPS (Network Policy Server) pro Azure Multi-Factor Authentication do vaší ověřovací infrastruktury pomocí stávajících serverů přidává cloudové možnosti MFA. S rozšířením NPS můžete přidat ověřování pomocí telefonního hovoru, textové zprávy nebo aplikace pro telefon do stávajícího toku ověřování, aniž byste museli instalovat, konfigurovat a udržovat nové servery.

Rozšíření serveru NPS funguje jako adaptér mezi POLOMĚRem a cloudovou Multi-Factor Authentication Azure, která poskytuje druhý faktor ověřování pro federované nebo synchronizované uživatele.

## <a name="how-the-nps-extension-works"></a>Jak funguje rozšíření serveru NPS

Když použijete rozšíření serveru NPS pro Azure Multi-Factor Authentication, bude tok ověřování zahrnovat tyto komponenty:

1. **Server NAS/VPN** přijímá žádosti od klientů VPN a převede je na žádosti RADIUS na servery NPS.
2. **Server NPS** se připojí k Active Directory Domain Services (služba AD DS), aby provedl primární ověřování pro žádosti RADIUS a po úspěšném dokončení předává požadavek do všech nainstalovaných rozšíření.  
3. **Rozšíření serveru NPS** aktivuje požadavek na Azure Multi-Factor Authentication pro sekundární ověřování. Jakmile rozšíření obdrží odpověď a v případě úspěšného ověření MFA selže, dokončí žádost o ověření tím, že server NPS poskytne tokeny zabezpečení, které zahrnují deklaraci MFA, kterou vystavila služba Azure STS.
4. **Azure MFA** komunikuje s Azure Active Directory (Azure AD) a načítá podrobnosti o uživateli a provádí sekundární ověřování pomocí metody ověřování nakonfigurované pro uživatele.

Následující diagram znázorňuje tento tok požadavků na ověření na nejvyšší úrovni:

![Diagram toku ověřování pro ověřování uživatelů pomocí serveru VPN na server NPS a rozšíření Azure Multi-Factor Authentication NPS](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Chování protokolu RADIUS a rozšíření serveru NPS

Jelikož je poloměr protokolu UDP, odesílatel předpokládá ztrátu paketů a očekává odpověď. Po uplynutí určité doby může dojít k vypršení časového limitu připojení. Pokud ano, bude paket znovu odeslán, protože odesílatel předpokládá, že se paket nedostal do cíle. Ve scénáři ověřování v tomto článku servery VPN odesílají požadavek a čekají na odpověď. Pokud vyprší časový limit připojení, server VPN odešle požadavek znovu.

![Diagram toku paketů UDP protokolu RADIUS a požadavků po vypršení časového limitu odpovědi ze serveru NPS](./media/howto-mfa-nps-extension/radius-flow.png)

Server NPS pravděpodobně neodpoví na původní požadavek serveru VPN dřív, než vyprší časový limit připojení, protože může být stále zpracováván požadavek MFA. Uživatel nemusí úspěšně reagovat na výzvu MFA, takže rozšíření služby Azure Multi-Factor Authentication NPS čeká na dokončení této události. V této situaci server NPS identifikuje další žádosti serveru VPN jako duplicitní požadavek. Server NPS tyto duplicitní žádosti serveru VPN zahodí.

![Diagram serveru NPS, který vyřazuje duplicitní požadavky ze serveru RADIUS](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Pokud se podíváte na protokoly serveru NPS, může se stát, že tyto další požadavky budou zahozeny. Toto chování je záměrné pro ochranu koncového uživatele při získávání více požadavků na jeden pokus o ověření. Zrušené požadavky v protokolu událostí serveru NPS neznamená, že došlo k potížím se serverem NPS nebo s rozšířením Azure Multi-Factor Authentication NPS.

Pro minimalizaci zahozených požadavků doporučujeme, aby servery VPN byly nakonfigurovány s časovým limitem alespoň 60 sekund. V případě potřeby můžete zvýšit hodnotu časového limitu serveru VPN na 90 nebo 120 sekund.

Z důvodu tohoto chování protokolu UDP může server NPS obdržet duplicitní požadavek a poslat další výzvu MFA, i když uživatel už odpoví na původní požadavek. Aby nedocházelo k tomuto časování, rozšíření serveru Azure Multi-Factor Authentication NPS nadále filtruje a zahodí duplicitní žádosti po dobu až 10 sekund po odeslání úspěšné odpovědi na server VPN.

![Diagram serveru NPS pokračuje v zahození duplicitních žádostí ze serveru VPN po dobu deseti sekund po vrácení úspěšné odpovědi.](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Znovu se můžete podívat na Zahozené požadavky v protokolech událostí serveru NPS, a to i v případě, že se zobrazila výzva služby Azure Multi-Factor Authentication. Jedná se o očekávané chování a neznamená problém se serverem NPS nebo rozšířením Azure Multi-Factor Authentication NPS.

## <a name="plan-your-deployment"></a>Plánování nasazení

Rozšíření serveru NPS automaticky zpracovává redundanci, takže nepotřebujete zvláštní konfiguraci.

V případě potřeby můžete vytvořit libovolný počet serverů NPS s podporou Multi-Factor Authentication Azure. Pokud instalujete více serverů, měli byste pro každý z nich použít rozdílový klientský certifikát. Vytvoření certifikátu pro každý server znamená, že můžete každý certifikát aktualizovat jednotlivě a nedělejte si starosti s výpadky napříč všemi servery.

Servery VPN směrují žádosti o ověření, takže potřebují vědět o nových serverech NPS s povolenou službou Azure Multi-Factor Authentication.

## <a name="prerequisites"></a>Požadavky

Rozšíření serveru NPS je určeno pro práci s vaší stávající infrastrukturou. Než začnete, ujistěte se, že máte následující předpoklady.

### <a name="licenses"></a>Licenses

Rozšíření serveru NPS pro Azure Multi-Factor Authentication je k dispozici pro zákazníky s [licencemi na azure Multi-Factor Authentication](multi-factor-authentication.md). Licence založené na spotřebě pro Azure Multi-Factor Authentication, jako jsou licence vázané na uživatele nebo na ověřování, nejsou kompatibilní s rozšířením NPS.

### <a name="software"></a>Software

Windows Server 2012 nebo vyšší.

### <a name="libraries"></a>Knihovny

Je nutné ručně nainstalovat následující knihovnu:

- [Visual C++ Redistributable pro Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Následující knihovny jsou nainstalovány automaticky s příponou.

- [Visual C++ Distribuovatelné balíčky pro Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [1.1.166.0 verze Modul Microsoft Azure Active Directory pro Windows PowerShell](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Modul Microsoft Azure Active Directory pro Windows PowerShell se nainstaluje taky prostřednictvím konfiguračního skriptu, který spouštíte jako součást procesu instalace, pokud ještě není k dispozici. Tento modul není potřeba instalovat předem, pokud ještě není nainstalovaný.

### <a name="azure-active-directory"></a>Azure Active Directory

Každý, kdo používá rozšíření serveru NPS, musí být synchronizovaný s Azure AD pomocí Azure AD Connect a musí být registrovaný pro MFA.

Když nainstalujete rozšíření, budete potřebovat *ID tenanta* a přihlašovací údaje správce pro vašeho TENANTA Azure AD. Chcete-li získat ID tenanta, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce tenanta Azure.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. Na stránce **Přehled** se zobrazí *informace o tenantovi* . Vedle *ID tenanta*vyberte ikonu **kopírování** , jak je znázorněno na následujícím ukázkovém snímku obrazovky:

   ![Získává se ID tenanta z Azure Portal.](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Požadavky sítě

Server NPS musí být schopný komunikovat s následujícími adresami URL přes porty 80 a 443:

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /Login.microsoftonline.com*
* *https: \/ /Credentials.Azure.com*

Kromě toho je potřeba připojení k následujícím adresám URL, aby se dokončilo [nastavení adaptéru pomocí zadaného skriptu PowerShellu](#run-the-powershell-script):

* *https: \/ /Login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.NET*

## <a name="prepare-your-environment"></a>Příprava prostředí

Než nainstalujete rozšíření serveru NPS, připravte prostředí pro zpracování provozu ověřování.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Povolení role NPS na serveru připojeném k doméně

Server NPS se připojí k Azure AD a ověří požadavky MFA. Vyberte jeden server pro tuto roli. Doporučujeme vybrat server, který nezpracovává požadavky z jiných služeb, protože rozšíření serveru NPS vyvolá chyby pro všechny požadavky, které nejsou POLOMĚRem. Server NPS musí být nastaven jako primární a sekundární ověřovací server pro vaše prostředí. Nedokáže proxy požadavky RADIUS na jiný server.

1. Na serveru otevřete **Správce serveru**. V nabídce *rychlý Start* vyberte **Průvodce přidáním rolí a funkcí** .
2. Pro typ instalace vyberte **instalace na základě rolí nebo na základě funkcí**.
3. Vyberte roli serveru **Služba Síťové zásady a přístup** . Okno se může zobrazit jako informování o dalších požadovaných funkcích pro spuštění této role.
4. Pokračujte v průvodci, dokud nebude stránka *potvrzení* . Až budete připraveni, vyberte **nainstalovat**.

Instalace role serveru NPS může trvat několik minut. Až budete hotovi, pokračujte v následujících částech a nakonfigurujte tento server tak, aby zpracovával příchozí žádosti RADIUS z řešení sítě VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurace řešení sítě VPN pro komunikaci se serverem NPS

V závislosti na použitém řešení sítě VPN se liší postup konfigurace zásad ověřování RADIUS. Nakonfigurujte zásady sítě VPN tak, aby odkazovaly na server NPS protokolu RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronizovat uživatele domény s cloudem

Tento krok již může být dokončen ve vašem tenantovi, ale je vhodné považovat kontrolu, že Azure AD Connect nedávno synchronizoval vaše databáze.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
2. Vybrat **Azure Active Directory**  >  **Azure AD Connect**
3. Ověřte, že je **povolený** stav synchronizace a že poslední synchronizace proběhla před méně než hodinou.

Pokud potřebujete zahájit novou operaci synchronizace, přečtěte si téma [Azure AD Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Určení metod ověřování, které můžou vaši uživatelé používat

Existují dva faktory, které mají vliv na to, které metody ověřování jsou k dispozici v nasazení rozšíření serveru NPS:

1. Šifrovací algoritmus hesla, který se používá mezi klientem RADIUS (VPN, NetScaler serverem nebo jiným) a servery NPS.
   - Protokol **PAP** podporuje všechny metody ověřování Azure Multi-Factor Authentication v cloudu: telefonní hovor, jednosměrná textová zpráva, oznámení mobilní aplikace, tokeny hardwaru Oath a ověřovací kód mobilní aplikace.
   - Protokol **CHAPv2** a **EAP** podporují telefonní hovor a oznámení mobilní aplikace.

      > [!NOTE]
      > Když nasadíte rozšíření serveru NPS, použijte tyto faktory k vyhodnocení, které metody jsou pro vaše uživatele k dispozici. Pokud váš klient protokolu RADIUS podporuje protokol PAP, ale uživatelské prostředí klienta nemá vstupní pole pro ověřovací kód, pak jsou k dispozici dvě podporované možnosti pro telefonní hovory a oznámení mobilní aplikace.
      >
      > Kromě toho, pokud uživatelské rozhraní klienta VPN podporuje vstupní pole a máte nakonfigurované zásady přístupu k síti, ověření může být úspěšné. Žádné z atributů RADIUS nakonfigurovaných v zásadách sítě se ale nepoužijí pro zařízení s přístupem k síti, jako je třeba server RRAS ani klient VPN. Výsledkem je, že klient VPN může mít k dispozici větší přístup, než je požadováno, nebo méně pro přístup.

2. Metody vstupu, které může klientská aplikace (VPN, NetScaler Server nebo jiný) zpracovat. Například má klient VPN nějaký způsob, jak uživateli dovolit zadat ověřovací kód z textové nebo mobilní aplikace?

V Azure můžete [Zakázat nepodporované metody ověřování](howto-mfa-mfasettings.md#verification-methods) .

### <a name="register-users-for-mfa"></a>Registrace uživatelů pro MFA

Než nasadíte a použijete rozšíření serveru NPS, uživatelé, kteří jsou potřeba k provedení Multi-Factor Authentication Azure, musí být zaregistrovaní pro MFA. K otestování rozšíření při jeho nasazení budete také potřebovat alespoň jeden testovací účet, který je plně zaregistrován pro Azure Multi-Factor Authentication.

Pokud potřebujete vytvořit a nakonfigurovat testovací účet, použijte následující postup:

1. Přihlaste se k [https://aka.ms/mfasetup](https://aka.ms/mfasetup) pomocí testovacího účtu.
2. Podle pokynů nastavte metodu ověřování.
3. V Azure Portal jako uživatel s oprávněními správce [vytvořte zásadu podmíněného přístupu](howto-mfa-getstarted.md#create-conditional-access-policy) , která bude vyžadovat službu Multi-Factor Authentication pro testovací účet.

> [!IMPORTANT]
>
> Ujistěte se, že se uživatelé úspěšně zaregistrovali pro Azure Multi-Factor Authentication. Pokud se uživatelé dřív zaregistrovali jenom pro Samoobslužné resetování hesla (SSPR), *StrongAuthenticationMethods* je pro svůj účet povolený. Při konfiguraci *StrongAuthenticationMethods* se vynutila Azure Multi-Factor Authentication, a to i v případě, že se uživatel zaregistroval jenom pro SSPR.
>
> Je možné povolit kombinovanou registraci zabezpečení, která konfiguruje SSPR a Azure Multi-Factor Authentication ve stejnou dobu. Další informace najdete v tématu [Povolení registrace kombinovaných informací o zabezpečení v Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Můžete taky [vynutit, aby uživatelé znovu zaregistrovali metody ověřování](howto-mfa-userdevicesettings.md#manage-user-authentication-options) , pokud předtím SSPR jenom.

## <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

> [!IMPORTANT]
> Nainstalujte rozšíření serveru NPS na jiný server, než je přístupový bod sítě VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Stažení a instalace rozšíření serveru NPS pro Azure MFA

Pokud chcete stáhnout a nainstalovat rozšíření serveru NPS, proveďte následující kroky:

1. [Stáhněte rozšíření serveru NPS](https://aka.ms/npsmfa) z webu Microsoft Download Center.
1. Zkopírujte binární soubor do serveru NPS (Network Policy Server), který chcete konfigurovat.
1. Spusťte *setup.exe* a postupujte podle pokynů k instalaci. Pokud narazíte na chyby, ujistěte se, že [knihovny z oddílu požadavků](#libraries) byly úspěšně nainstalovány.

#### <a name="upgrade-the-nps-extension"></a>Upgrade rozšíření serveru NPS

Pokud později upgradujete existující instalaci rozšíření serveru NPS, abyste se vyhnuli restartování základního serveru, proveďte následující kroky:

1. Odinstalujte stávající verzi.
1. Spusťte nový instalační program.
1. Restartujte službu *IAS (Network Policy Server)* .

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Instalační program vytvoří skript prostředí PowerShell v umístění `C:\Program Files\Microsoft\AzureMfa\Config` (kde `C:\` je instalační jednotka). Tento skript PowerShellu provede při každém spuštění následující akce:

* Vytvoří certifikát podepsaný svým držitelem.
* Přidruží veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD.
* Uloží certifikát do úložiště certifikátů místního počítače.
* Udělí síťovému uživateli přístup k privátnímu klíči certifikátu.
* Restartuje službu NPS.

Pokud nechcete používat vlastní certifikáty (místo certifikátů podepsaných svým držitelem, které generuje skript PowerShellu), dokončete instalaci rozšíření serveru NPS spuštěním skriptu PowerShellu. Pokud nainstalujete rozšíření na více serverů, každý server by měl mít vlastní certifikát.

Pokud chcete poskytnout funkce Vyrovnávání zatížení nebo redundanci, opakujte tento postup na dalších serverech NPS podle potřeby:

1. Otevřete příkazový řádek Windows PowerShellu jako správce.
1. Změňte adresáře na místo, kde instalační program vytvořil skript prostředí PowerShell:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Spusťte PowerShellový skript vytvořený instalačním programem.

   > [!IMPORTANT]
   > Pro zákazníky, kteří používají Azure Government nebo cloudy Azure Čína 21Vianet, nejprve upravte `Connect-MsolService` rutiny ve skriptu *AzureMfaNpsExtnConfigSetup.ps1* tak, aby zahrnovaly parametry *AzureEnvironment* pro požadovaný Cloud. Zadejte například *-AzureEnvironment USGovernment* nebo *-AzureEnvironment AzureChinaCloud*.
   >
   > Další informace najdete v tématu [Reference k parametrům Connect-MsolService](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Po zobrazení výzvy se přihlaste k Azure AD jako správce.
1. PowerShell zobrazí výzvu k zadání ID tenanta. Použijte identifikátor GUID *ID tenanta* , který jste zkopírovali z Azure Portal v části požadavky.
1. Po dokončení skriptu se zobrazí zpráva o úspěchu.  

Pokud uplynula platnost předchozího certifikátu počítače a vygeneroval se nový certifikát, měli byste odstranit všechny certifikáty s vypršenou platností. Máte-li certifikáty s vypršenou platností, můžete způsobit problémy s počátkem rozšíření serveru NPS

> [!NOTE]
> Pokud místo generování certifikátů pomocí skriptu PowerShell použijete vlastní certifikáty, ujistěte se, že jsou zarovnané na konvence vytváření názvů NPS. Název subjektu musí být **CN = \<TenantID\> , OU = rozšíření Microsoft NPS**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Government nebo Azure Čína 21Vianet – další kroky

Pro zákazníky, kteří používají Azure Government nebo cloudy Azure Čína 21Vianet, jsou na každém serveru NPS potřeba tyto další kroky konfigurace.

> [!IMPORTANT]
> Tato nastavení registru nakonfigurujte jenom v případě, že jste zákazník Azure Government nebo Azure Čína 21Vianet.

1. Pokud jste zákazníkem Azure Government nebo Azure Čína 21Vianet, otevřete **Editor registru** na serveru NPS.
1. Přejděte na adresu `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Pro Azure Government zákazníky nastavte následující hodnoty klíče:

    | Klíč registru       | Hodnota |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Pro zákazníky Azure Čína 21Vianet nastavte následující klíčové hodnoty:

    | Klíč registru       | Hodnota |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Opakujte předchozí dva kroky a nastavte hodnoty klíče registru pro každý server NPS.
1. Restartujte službu NPS pro každý server NPS.

    Pro minimální dopad Převezměte každý server NPS z rotace vyrovnávání zatížení sítě po jednom a počkejte, až se všechna připojení vyprázdní.

### <a name="certificate-rollover"></a>Změna certifikátu

S verzí *1.0.1.32* rozšíření serveru NPS se teď podporuje čtení více certifikátů. Tato možnost pomáhá zajistit průběžné aktualizace certifikátů před jejich vypršením platnosti. Pokud vaše organizace používá předchozí verzi rozšíření serveru NPS, upgradujte na verzi *1.0.1.32* nebo vyšší.

Certifikáty vytvořené `AzureMfaNpsExtnConfigSetup.ps1` skriptem jsou platné 2 roky. Monitorování certifikátů za účelem vypršení platnosti. Certifikáty pro rozšíření serveru NPS se nacházejí v úložišti certifikátů *místního počítače* v části *osobní* a jsou *vydávány* ID tenanta poskytovanému instalačnímu skriptu.

Když se certifikát blíží k datu vypršení platnosti, měl by se vytvořit nový certifikát, který ho nahradí.  Tento proces se provádí tak, že se `AzureMfaNpsExtnConfigSetup.ps1` znovu spustí a po zobrazení výzvy zůstane stejné ID tenanta. Tento postup by se měl opakovat na každém serveru NPS ve vašem prostředí.

## <a name="configure-your-nps-extension"></a>Konfigurace rozšíření serveru NPS

Když je vaše prostředí připravené a rozšíření serveru NPS je teď nainstalované na požadovaných serverech, můžete nakonfigurovat rozšíření.

Tato část obsahuje doporučení a návrhy pro úspěšná nasazení rozšíření NPS.

### <a name="configuration-limitations"></a>Omezení konfigurace

- Rozšíření serveru NPS pro Azure Multi-Factor Authentication nezahrnuje nástroje pro migraci uživatelů a nastavení z MFA serveru do cloudu. Z tohoto důvodu doporučujeme použít rozšíření pro nová nasazení, nikoli stávající nasazení. Pokud rozšíření použijete pro existující nasazení, uživatelé musí znovu provést kontrolu a naplnit své podrobnosti MFA v cloudu.  
- Rozšíření serveru NPS používá hlavní název uživatele (UPN) z místního prostředí služba AD DS k identifikaci uživatele v Azure Multi-Factor Authentication pro provádění sekundárního ověřování. Rozšíření se dá nakonfigurovat tak, aby používalo jiný identifikátor, jako je alternativní přihlašovací ID nebo vlastní služba AD DS pole než hlavní název uživatele (UPN). Další informace najdete v článku [Rozšířené možnosti konfigurace pro rozšíření serveru NPS pro Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Ne všechny šifrovací protokoly podporují všechny metody ověřování.
   - **PAP** podporuje telefonní hovor, jednosměrnou textovou zprávu, oznámení mobilní aplikace a ověřovací kód mobilní aplikace.
   - Protokol **CHAPv2** a **EAP** Podpora telefonního hovoru a oznámení mobilní aplikace

### <a name="control-radius-clients-that-require-mfa"></a>Řízení klientů RADIUS, kteří vyžadují MFA

Jakmile povolíte MFA pro klienta RADIUS pomocí rozšíření serveru NPS, budou se k provádění MFA vyžadovat všechna ověření tohoto klienta. Pokud chcete povolit vícefaktorové ověřování pro některé klienty RADIUS, ale ne jiné, můžete nakonfigurovat dva servery NPS a nainstalovat rozšíření jenom na jeden z nich.

Nakonfigurujte klienty RADIUS, u kterých chcete, aby MFA odesílaly požadavky na server NPS s nakonfigurovaným rozšířením, a jiné klienty RADIUS na server NPS, který není nakonfigurovaný s tímto rozšířením.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Příprava pro uživatele, kteří nejsou zaregistrovaní pro MFA

Pokud máte uživatele, kteří nejsou zaregistrovaní pro MFA, můžete určit, co se stane při pokusu o ověření. K řízení tohoto chování použijte *REQUIRE_USER_MATCH* nastavení v cestě registru *HKLM\Software\Microsoft\AzureMFA*. Toto nastavení má jedinou možnost konfigurace:

| Klíč | Hodnota | Výchozí |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE NEBO FALSE | Nenastaveno (ekvivalent hodnoty TRUE) |

Toto nastavení určuje, co dělat, když se uživatel nezaregistruje pro MFA. Pokud klíč neexistuje, není nastaven nebo je nastaven na *hodnotu true*a uživatel není zaregistrován, rozšíření MFA neuspěje.

Když je klíč nastavený na *false* a uživatel není zaregistrovaný, ověřování pokračuje bez provedení MFA. Pokud je uživatel zaregistrován v MFA, musí se ověřit pomocí VÍCEFAKTOROVÉHO ověřování, i když je *REQUIRE_USER_MATCH* nastaveno na *false*.

Můžete zvolit vytvoření tohoto klíče a jeho nastavení na *hodnotu NEPRAVDA* , pokud se vaši uživatelé chtějí zaregistrovat a nemusí se zatím zaregistrovat pro Azure Multi-Factor Authentication. Vzhledem k tomu, že nastavení klíče umožňuje uživatelům, kteří nejsou zaregistrovaní pro MFA, přihlásit se, měli byste tento klíč před zahájením provozu odebrat.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="nps-extension-health-check-script"></a>Skript kontroly stavu rozšíření serveru NPS

Následující skript je k dispozici k provedení základních kroků kontroly stavu při řešení potíží s rozšířením NPS.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Návody ověřte, zda je certifikát klienta nainstalován podle očekávání?

Vyhledejte certifikát podepsaný svým držitelem vytvořeného instalačním programem v úložišti certifikátů a zkontrolujte, zda má privátní klíč oprávnění udělená *síťové službě*uživatele. Certifikát má název subjektu **CN \<tenantid\> , OU = rozšíření Microsoft NPS** .

Certifikáty podepsané svým držitelem generované `AzureMfaNpsExtnConfigSetup.ps1` skriptem mají dobu platnosti dvou let. Při ověřování, zda je certifikát nainstalován, byste měli také ověřit, že platnost certifikátu nevypršela.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Jak můžu ověřit, jestli je můj klientský certifikát přidružený k mému tenantovi ve službě Azure AD?

Otevřete příkazový řádek PowerShellu a spusťte následující příkazy:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Tyto příkazy vytisknou všechny certifikáty, které v relaci PowerShellu přidruží vašeho tenanta k vaší instanci rozšíření serveru NPS. Vyhledejte svůj certifikát tak, že vyexportujete certifikát klienta jako soubor *X. 509 (. cer) s kódováním Base-64* bez privátního klíče a porovnáte ho se seznamem z PowerShellu.

Následující příkaz vytvoří soubor s názvem *npscertificate* v kořenu jednotky *C:* ve formátu *. cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Po spuštění tohoto příkazu přejděte do kořenového adresáře jednotky *C:* , vyhledejte soubor a dvakrát na něj klikněte. Přejděte na podrobnosti a posuňte se dolů k části kryptografický otisk. Porovnejte kryptografický otisk certifikátu nainstalovaného na serveru s tímto. Kryptografické otisky certifikátů by se měly shodovat.

*Platná – od* a *platné – dokud* časová razítka, která jsou ve formě čitelné pro člověka, lze použít k odfiltrování zjevně Misfits, pokud příkaz vrátí více než jeden certifikát.

### <a name="why-cannot-i-sign-in"></a>Proč se nemůžu přihlásit?

Ověřte, že platnost vašeho hesla nevypršela. Rozšíření serveru NPS nepodporuje změnu hesla jako součást pracovního postupu přihlášení. Pokud potřebujete další pomoc, obraťte se na pracovníky IT vaší organizace.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Proč mé požadavky selžou s chybou tokenu ADAL?

K této chybě mohlo dojít z některého z několika důvodů. K odstranění potíží použijte následující postup:

1. Restartujte server NPS.
2. Ověřte, zda je certifikát klienta nainstalován podle očekávání.
3. Ověřte, jestli je certifikát přidružený k vašemu tenantovi ve službě Azure AD.
4. Ověřte, že ze serveru, na kterém se spouští rozšíření, je přístupný web `https://login.microsoftonline.com/`.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Proč ověřování selže s chybou v protokolech HTTP s informacemi o tom, že uživatel nebyl nalezen?

Ověřte, že služba AD Connect běží a že uživatel je přítomen v místním služba AD DS prostředí i ve službě Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Proč se zobrazí chyby připojení HTTP v protokolech se selháním všech mých ověřování?

Ověřte, že ze serveru, na kterém se spouští rozšíření NPS, je přístupný web https://adnotifications.windowsazure.com.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Proč ověřování nefunguje, i když je přítomen platný certifikát?

Pokud uplynula platnost předchozího certifikátu počítače a vygeneroval se nový certifikát, odstraňte všechny certifikáty, jejichž platnost vypršela. Certifikáty s vypršenou platností můžou způsobit problémy s počátkem rozšíření serveru NPS.

Pokud chcete zjistit, jestli máte platný certifikát, zkontrolujte *úložiště certifikátů účtu místního počítače* pomocí konzoly MMC a zajistěte, aby certifikát neprošlý datem vypršení platnosti certifikátu. Pokud chcete vygenerovat nový platný certifikát, znovu spusťte postup z části [spuštění skriptu instalačního programu PowerShellu](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Proč se v protokolech serveru NPS zobrazují zrušené požadavky?

Server VPN může odesílat opakované žádosti na server NPS, pokud je hodnota časového limitu příliš nízká. Server NPS tyto duplicitní žádosti detekuje a zahodí. Toto chování je záměrné a neindikuje problém se serverem NPS nebo rozšířením Azure Multi-Factor Authentication NPS.

Další informace o tom, proč vidíte zahozené pakety v protokolech serveru NPS, najdete v tématu [chování protokolu RADIUS a rozšíření serveru NPS](#radius-protocol-behavior-and-the-nps-extension) na začátku tohoto článku.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Správa protokolů TLS a SSL a šifrovacích sad

Doporučuje se zakázat nebo odebrat starší a slabší šifrovací sady, pokud to vaše organizace nevyžaduje. Informace o tom, jak tento úkol provést, najdete v článku [Správa protokolů SSL/TLS a šifrovací sady pro AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Další řešení potíží

Další pokyny k odstraňování potíží a možná řešení najdete v článku o [řešení chybových zpráv z rozšíření serveru NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Další kroky

- [Přehled a konfigurace serveru NPS (Network Policy Server) v systému Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Nakonfigurujte alternativní ID pro přihlášení nebo nastavte seznam výjimek pro IP adresy, které by se nemusely provádět dvoustupňové ověřování v [pokročilých možnostech konfigurace pro rozšíření serveru NPS pro Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Naučte se integrovat servery [Brána vzdálené plochy](howto-mfa-nps-extension-rdg.md) a [VPN](howto-mfa-nps-extension-vpn.md) pomocí rozšíření NPS.

- [řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)