---
title: Poskytování možností Azure MFA pomocí serveru NPS – Azure Active Directory
description: Přidání cloudových možností ověřování pomocí dvou kroků do stávající ověřovací infrastruktury
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43f355f22774477466d2965cef02adcc4ec4f497
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378113"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrace stávající infrastruktury serveru NPS do Azure Multi-Factor Authentication

Rozšíření serveru NPS (Network Policy Server) pro Azure MFA přidává cloudové možnosti MFA do vaší ověřovací infrastruktury pomocí stávajících serverů. S rozšířením NPS můžete přidat ověřování pomocí telefonního hovoru, textové zprávy nebo aplikace pro telefon do stávajícího toku ověřování, aniž byste museli instalovat, konfigurovat a udržovat nové servery. 

Toto rozšíření bylo vytvořeno pro organizace, které chtějí chránit připojení VPN bez nasazení serveru Azure MFA. Rozšíření serveru NPS funguje jako adaptér mezi POLOMĚRem a cloudovou službou Azure MFA, která poskytuje druhý faktor ověřování pro federované nebo synchronizované uživatele.

Při použití rozšíření NPS pro Azure MFA zahrnuje tok ověřování následující komponenty: 

1. **Server NAS/VPN** přijímá žádosti od klientů VPN a převede je na žádosti RADIUS na servery NPS. 
2. **Server NPS** se připojí ke službě Active Directory, aby provedl primární ověřování pro žádosti RADIUS a po úspěšném dokončení předává požadavek do všech nainstalovaných rozšíření.  
3. **Rozšíření serveru NPS** aktivuje požadavek na Azure MFA pro sekundární ověřování. Jakmile rozšíření obdrží odpověď a v případě úspěšného ověření MFA selže, dokončí žádost o ověření tím, že server NPS poskytne tokeny zabezpečení, které zahrnují deklaraci MFA, kterou vystavila služba Azure STS.  
4. **Azure MFA** komunikuje s Azure Active Directory a načítá podrobnosti o uživateli a provádí sekundární ověřování pomocí metody ověřování nakonfigurované pro uživatele.

Následující diagram znázorňuje tento tok požadavků na ověření na nejvyšší úrovni: 

![Diagram toku ověřování](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Plánování nasazení

Rozšíření serveru NPS automaticky zpracovává redundanci, takže nepotřebujete zvláštní konfiguraci.

V případě potřeby můžete vytvořit libovolný počet serverů NPS s povolenou službou Azure MFA. Pokud instalujete více serverů, měli byste pro každý z nich použít rozdílový klientský certifikát. Vytvořením certifikátu pro každý server znamená, že můžete každý certifikát aktualizovat jednotlivě a nedělejte si starosti s výpadky napříč všemi servery.

Servery VPN směrují žádosti o ověření, takže potřebují vědět o nových serverech NPS s povolenou službou Azure MFA.

## <a name="prerequisites"></a>Předpoklady

Rozšíření serveru NPS je určeno pro práci s vaší stávající infrastrukturou. Než začnete, ujistěte se, že máte následující předpoklady.

### <a name="licenses"></a>Licence

Rozšíření serveru NPS pro Azure MFA je dostupné pro zákazníky, kteří mají [licence pro azure Multi-Factor Authentication](multi-factor-authentication.md) (jsou součástí Azure AD Premium, EMS nebo samostatné licence MFA). Licence založené na spotřebě pro Azure MFA, jako jsou licence vázané na uživatele nebo na ověřování, nejsou kompatibilní s rozšířením NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 nebo novější.

### <a name="libraries"></a>Knihovny

Tyto knihovny jsou nainstalovány automaticky s příponou.

- [Visual C++ Redistributable balíčky pro Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [1.1.166.0 verze Modul Microsoft Azure Active Directory pro Windows PowerShell](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Modul Microsoft Azure Active Directory pro Windows PowerShell je nainstalován, pokud ještě neexistuje, prostřednictvím konfiguračního skriptu, který spustíte jako součást procesu instalace. Tento modul není potřeba instalovat předem, pokud ještě není nainstalovaný.

### <a name="azure-active-directory"></a>Azure Active Directory

Každý, kdo používá rozšíření serveru NPS, musí být synchronizovaný Azure Active Directory pomocí Azure AD Connect a musí být registrovaný pro MFA.

Když nainstalujete rozšíření, budete potřebovat ID adresáře a přihlašovací údaje správce pro vašeho tenanta Azure AD. ID vašeho adresáře najdete v [Azure Portal](https://portal.azure.com). Přihlaste se jako správce. Vyhledejte a vyberte **Azure Active Directory**a pak vyberte **vlastnosti**. Zkopírujte identifikátor GUID do pole **ID adresáře** a uložte ho. Tento identifikátor GUID použijete jako ID tenanta při instalaci rozšíření serveru NPS.

![V části vlastnosti Azure Active Directory Najděte ID vašeho adresáře.](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Síťové požadavky

Server NPS musí být schopný komunikovat s následujícími adresami URL přes porty 80 a 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Kromě toho je potřeba připojení k následujícím adresám URL, aby se dokončilo [nastavení adaptéru pomocí zadaného skriptu PowerShellu](#run-the-powershell-script) .

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Příprava prostředí

Než nainstalujete rozšíření serveru NPS, budete chtít připravit prostředí pro zpracování provozu ověřování.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Povolení role NPS na serveru připojeném k doméně

Server NPS se připojí k Azure Active Directory a ověří požadavky MFA. Vyberte jeden server pro tuto roli. Doporučujeme vybrat server, který nezpracovává požadavky z jiných služeb, protože rozšíření serveru NPS vyvolá chyby pro všechny požadavky, které nejsou POLOMĚRem. Server NPS musí být nastavený jako primární a sekundární ověřovací server pro vaše prostředí. nedokáže proxy požadavky RADIUS na jiný server.

1. Na serveru otevřete **Průvodce přidáním rolí a funkcí** z nabídky pro rychlý Start správce serveru.
2. Pro typ instalace vyberte instalace na základě **rolí nebo na základě funkcí** .
3. Vyberte roli serveru **Služba Síťové zásady a přístup** . Okno se může zobrazit pro informování o požadovaných funkcích pro spuštění této role.
4. Pokračujte v průvodci, dokud nebude stránka potvrzení. Vyberte **Install** (Nainstalovat).

Když teď máte server určený pro server NPS, měli byste tento server taky nakonfigurovat tak, aby zpracovával příchozí žádosti RADIUS z řešení VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurace řešení sítě VPN pro komunikaci se serverem NPS

V závislosti na použitém řešení sítě VPN se liší postup konfigurace zásad ověřování RADIUS. Nakonfigurujte tuto zásadu tak, aby odkazovala na server NPS protokolu RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronizovat uživatele domény s cloudem

Tento krok již může být dokončen ve vašem tenantovi, ale je vhodné považovat kontrolu, že Azure AD Connect nedávno synchronizoval vaše databáze.

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Vyberte **Azure Active Directory** > **Azure AD Connect**
3. Ověřte, že je **povolený** stav synchronizace a že poslední synchronizace proběhla před méně než hodinou.

Pokud potřebujete zahájit novou část synchronizace, postupujte podle pokynů v tématu [Azure AD Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Určení metod ověřování, které můžou vaši uživatelé používat

Existují dva faktory, které mají vliv na to, které metody ověřování jsou k dispozici v nasazení rozšíření serveru NPS:

1. Šifrovací algoritmus hesla, který se používá mezi klientem RADIUS (VPN, NetScaler serverem nebo jiným) a servery NPS.
   - Protokol **PAP** podporuje všechny metody ověřování Azure MFA v cloudu: telefonní hovor, jednosměrná textová zpráva, oznámení mobilní aplikace, tokeny hardwaru Oath a ověřovací kód mobilní aplikace.
   - Protokol **CHAPv2** a **EAP** podporují telefonní hovor a oznámení mobilní aplikace.

      > [!NOTE]
      > Když nasadíte rozšíření serveru NPS, použijte tyto faktory k vyhodnocení, které metody jsou pro vaše uživatele k dispozici. Pokud váš klient protokolu RADIUS podporuje protokol PAP, ale uživatelské prostředí klienta nemá vstupní pole pro ověřovací kód, pak jsou k dispozici dvě podporované možnosti pro telefonní hovory a oznámení mobilní aplikace.
      >
      > Pokud navíc vaše uživatelské rozhraní klienta VPN podporuje vstupní pole a nakonfigurovali jste zásady přístupu k síti – ověřování může být úspěšné, ale žádné z atributů RADIUS nakonfigurovaných v zásadách sítě se nepoužijí na zařízení pro přístup k síti, Podobně jako server RRAS, ani klient VPN. Výsledkem je, že klient VPN může mít větší přístup, než kolik jich potřebujete, nebo méně, než bude mít přístup.
      >

2. Metody vstupu, které může klientská aplikace (VPN, NetScaler Server nebo jiný) zpracovat. Například má klient VPN nějaký způsob, jak uživateli dovolit zadat ověřovací kód z textové nebo mobilní aplikace?

V Azure můžete [Zakázat nepodporované metody ověřování](howto-mfa-mfasettings.md#verification-methods) .

### <a name="register-users-for-mfa"></a>Registrace uživatelů pro MFA

Než nasadíte a použijete rozšíření serveru NPS, je potřeba, aby se uživatelé, kteří potřebují provést dvoustupňové ověřování, zaregistrovali pro MFA. K otestování rozšíření hned po jeho nasazení budete potřebovat alespoň jeden testovací účet, který je plně zaregistrován pro Multi-Factor Authentication.

Pomocí těchto kroků si můžete spustit testovací účet:

1. Přihlaste se k [https://aka.ms/mfasetup](https://aka.ms/mfasetup) pomocí testovacího účtu.
2. Podle pokynů nastavte metodu ověřování.
3. [Vytvořte zásady podmíněného přístupu](howto-mfa-getstarted.md#create-conditional-access-policy) , které budou vyžadovat službu Multi-Factor Authentication pro testovací účet.

## <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

> [!IMPORTANT]
> Nainstalujte rozšíření serveru NPS na jiný server, než je přístupový bod sítě VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Stažení a instalace rozšíření serveru NPS pro Azure MFA

1. [Stáhněte rozšíření serveru NPS](https://aka.ms/npsmfa) z webu Microsoft Download Center.
2. Zkopírujte binární soubor do serveru NPS (Network Policy Server), který chcete konfigurovat.
3. Spusťte soubor *Setup. exe* a postupujte podle pokynů k instalaci. Pokud dojde k chybám, dvakrát ověřte, zda byly dvě knihovny z části požadavků úspěšně nainstalovány.

#### <a name="upgrade-the-nps-extension"></a>Upgrade rozšíření serveru NPS

Při upgradu existující instalace rozšíření serveru NPS, aby nedocházelo k restartování základního serveru, proveďte následující kroky:

1. Odinstalace existující verze
1. Spuštění nového instalačního programu
1. Restartujte službu IAS (Network Policy Server).

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Instalační program vytvoří skript prostředí PowerShell v tomto umístění: `C:\Program Files\Microsoft\AzureMfa\Config` (kde C:\ je instalační jednotka). Tento skript PowerShellu provede při každém spuštění následující akce:

- Vytvořte certifikát podepsaný svým držitelem.
- Přidružte veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD.
- Uložte certifikát do úložiště certifikátů místního počítače.
- Udělte uživateli sítě přístup k privátnímu klíči certifikátu.
- Restartujte server NPS.

Pokud nechcete používat vlastní certifikáty (místo certifikátů podepsaných svým držitelem, které generuje skript PowerShellu), dokončete instalaci spuštěním skriptu PowerShellu. Pokud nainstalujete rozšíření na více serverů, každý z nich by měl mít vlastní certifikát.

1. Spusťte prostředí Windows PowerShell jako správce.
2. Změňte adresáře.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Spusťte PowerShellový skript vytvořený instalačním programem.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Přihlaste se k Azure AD jako správce.
5. PowerShell zobrazí výzvu k zadání ID tenanta. Použijte identifikátor GUID ID adresáře, který jste zkopírovali z Azure Portal v části požadavky.
6. Po dokončení skriptu zobrazí prostředí PowerShell zprávu o úspěchu.  

Opakujte tyto kroky na všech dalších serverech NPS, které chcete nastavit pro vyrovnávání zatížení.

Pokud uplynula platnost předchozího certifikátu počítače a vygeneroval se nový certifikát, měli byste odstranit všechny certifikáty s vypršenou platností. Máte-li certifikáty s vypršenou platností, můžete způsobit problémy s počátkem rozšíření serveru NPS

> [!NOTE]
> Pokud místo generování certifikátů pomocí skriptu PowerShell použijete vlastní certifikáty, ujistěte se, že jsou zarovnané na konvence vytváření názvů NPS. Název subjektu musí být **CN =\<TenantID\>, OU = rozšíření Microsoft NPS**. 

### <a name="microsoft-azure-government-additional-steps"></a>Microsoft Azure Government dalších kroků

Pro zákazníky, kteří používají Azure Government Cloud, jsou na každém serveru NPS potřeba tyto další kroky konfigurace:

1. Otevřete **Editor registru** na serveru NPS.
1. Přejděte na adresu `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Nastavte následující hodnoty klíče:

    | Klíč registru       | Hodnota |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Opakujte předchozí dva kroky a nastavte hodnoty klíče registru pro každý server NPS.
1. Restartujte službu NPS pro každý server NPS.

    Pro minimální dopad Převezměte každý server NPS z rotace vyrovnávání zatížení sítě po jednom a počkejte, až se všechna připojení vyprázdní.

### <a name="certificate-rollover"></a>Změna certifikátu

S verzí 1.0.1.32 rozšíření serveru NPS se teď podporuje čtení více certifikátů. Tato funkce pomůže zajistit průběžné aktualizace certifikátů před jejich vypršením platnosti. Pokud vaše organizace používá předchozí verzi rozšíření serveru NPS, měli byste upgradovat na verzi 1.0.1.32 nebo novější.

Certifikáty vytvořené skriptem `AzureMfaNpsExtnConfigSetup.ps1` jsou platné 2 roky. IT organizace by měli monitorovat certifikáty pro vypršení platnosti. Certifikáty pro rozšíření serveru NPS se nacházejí v úložišti certifikátů místního počítače v části osobní a jsou vydávány pro ID tenanta poskytnuté ke skriptu.

Když se certifikát blíží k datu vypršení platnosti, měl by se vytvořit nový certifikát, který ho nahradí.  Tento proces se provádí tak, že po zobrazení výzvy znovu spustíte `AzureMfaNpsExtnConfigSetup.ps1` a zachováte stejné ID tenanta. Tento postup by se měl opakovat na každém serveru NPS ve vašem prostředí.

## <a name="configure-your-nps-extension"></a>Konfigurace rozšíření serveru NPS

Tato část obsahuje doporučení a návrhy pro úspěšná nasazení rozšíření NPS.

### <a name="configuration-limitations"></a>Omezení konfigurace

- Rozšíření serveru NPS pro Azure MFA nezahrnuje nástroje pro migraci uživatelů a nastavení z MFA serveru do cloudu. Z tohoto důvodu doporučujeme použít rozšíření pro nová nasazení, nikoli stávající nasazení. Pokud rozšíření použijete pro existující nasazení, uživatelé musí znovu provést kontrolu a naplnit své podrobnosti MFA v cloudu.  
- Rozšíření serveru NPS používá hlavní název uživatele (UPN) z místní služby Active Directory k identifikaci uživatele v Azure MFA pro provádění sekundárního ověřování. Rozšíření se dá nakonfigurovat tak, aby používalo jiný identifikátor, jako je alternativní přihlašovací ID nebo vlastní jiné pole služby Active Directory než hlavní název uživatele (UPN). Další informace najdete v článku [Rozšířené možnosti konfigurace pro rozšíření serveru NPS pro Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Ne všechny šifrovací protokoly podporují všechny metody ověřování.
   - **PAP** podporuje telefonní hovor, jednosměrnou textovou zprávu, oznámení mobilní aplikace a ověřovací kód mobilní aplikace.
   - Protokol **CHAPv2** a **EAP** Podpora telefonního hovoru a oznámení mobilní aplikace

### <a name="control-radius-clients-that-require-mfa"></a>Řízení klientů RADIUS, kteří vyžadují MFA

Jakmile povolíte MFA pro klienta RADIUS pomocí rozšíření serveru NPS, budou se k provádění MFA vyžadovat všechna ověření tohoto klienta. Pokud chcete povolit vícefaktorové ověřování pro některé klienty RADIUS, ale ne jiné, můžete nakonfigurovat dva servery NPS a nainstalovat rozšíření jenom na jeden z nich. Nakonfigurujte klienty RADIUS, u kterých chcete, aby MFA odesílaly požadavky na server NPS s nakonfigurovaným rozšířením, a jiné klienty RADIUS na server NPS, který není nakonfigurovaný s tímto rozšířením.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Příprava pro uživatele, kteří nejsou zaregistrovaní pro MFA

Pokud máte uživatele, kteří nejsou zaregistrovaní pro MFA, můžete určit, co se stane při pokusu o ověření. K řízení chování funkcí použijte *REQUIRE_USER_MATCH* nastavení registru v cestě registru *HKLM\Software\Microsoft\AzureMFA* . Toto nastavení má jedinou možnost konfigurace:

| Klíč | Hodnota | Výchozí |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE NEBO FALSE | Nenastaveno (ekvivalent hodnoty TRUE) |

Účelem tohoto nastavení je určit, co dělat, když není uživatel zaregistrovaný pro MFA. Pokud klíč neexistuje, není nastaven nebo je nastaven na hodnotu TRUE a uživatel není zaregistrovaný, pak toto rozšíření neprojde výzvou. Když je klíč nastavený na FALSE a uživatel není zaregistrovaný, ověřování pokračuje bez provedení MFA. Pokud je uživatel zaregistrován v MFA, musí se ověřit pomocí VÍCEFAKTOROVÉHO ověřování, i když je REQUIRE_USER_MATCH nastaveno na FALSE.

Můžete zvolit vytvoření tohoto klíče a jeho nastavení na hodnotu NEPRAVDA, pokud se vaši uživatelé chtějí zaregistrovat a nemusí se ještě registrovat pro Azure MFA. Vzhledem k tomu, že nastavení klíče umožňuje uživatelům, kteří nejsou zaregistrovaní pro MFA, přihlásit se, měli byste tento klíč před zahájením provozu odebrat.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="nps-extension-health-check-script"></a>Skript kontroly stavu rozšíření serveru NPS

Následující skript je k dispozici v Galerii TechNet k provedení základních kroků kontroly stavu při řešení potíží s rozšířením NPS.

[MFA_NPS_Troubleshooter. ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Návody ověřte, zda je certifikát klienta nainstalován podle očekávání?

Vyhledejte certifikát podepsaný svým držitelem vytvořeného instalačním programem v úložišti certifikátů a zkontrolujte, zda má privátní klíč oprávnění udělená **síťové službě**uživatele. Certifikát má název subjektu **CN \<tenantid\>, OU = rozšíření Microsoft NPS** .

Certifikáty podepsané svým držitelem generované skriptem *AzureMfaNpsExtnConfigSetup. ps1* mají také životnost po dobu dvou let. Při ověřování, zda je certifikát nainstalován, byste měli také ověřit, zda nevypršela platnost certifikátu.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak můžu ověřit, jestli je můj klientský certifikát přidružený k mému tenantovi v Azure Active Directory?

Otevřete příkazový řádek PowerShellu a spusťte následující příkazy:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Tyto příkazy vytisknou všechny certifikáty, které v relaci PowerShellu přidruží vašeho tenanta k vaší instanci rozšíření serveru NPS. Vyhledejte svůj certifikát tak, že vyexportujete certifikát klienta jako soubor X. 509 (. cer) s kódováním Base-64 (. cer) bez privátního klíče a porovnáte ho se seznamem z PowerShellu.

Následující příkaz vytvoří soubor s názvem "npscertificate" na jednotce "C:" ve formátu. cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Po spuštění tohoto příkazu přejděte na jednotku C, vyhledejte soubor a dvakrát na něj klikněte. Přejděte na podrobnosti a posuňte se dolů k části kryptografický otisk, porovnejte kryptografický otisk certifikátu nainstalovaného na serveru s tímto. Kryptografické otisky certifikátů by se měly shodovat.

Platná – od a platné – dokud časová razítka, která jsou ve formě čitelné pro člověka, lze použít k odfiltrování zjevně Misfits, pokud příkaz vrátí více než jeden certifikát.

---

### <a name="why-cannot-i-sign-in"></a>Proč se nemůžu přihlásit?

Ověřte, že platnost vašeho hesla nevypršela. Rozšíření serveru NPS nepodporuje změnu hesla jako součást pracovního postupu přihlášení. Pokud potřebujete další pomoc, obraťte se na pracovníky IT vaší organizace.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Proč mé požadavky selžou s chybou tokenu ADAL?

K této chybě mohlo dojít z některého z několika důvodů. Pomocí těchto kroků můžete pomoct s řešením:

1. Restartujte server NPS.
2. Ověřte, zda je certifikát klienta nainstalován podle očekávání.
3. Ověřte, jestli je certifikát přidružený k vašemu tenantovi ve službě Azure AD.
4. Ověřte, že ze serveru, na kterém se spouští rozšíření, je přístupný web https://login.microsoftonline.com/.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Proč ověřování selže s chybou v protokolech HTTP s informacemi o tom, že uživatel nebyl nalezen?

Ověřte, zda je spuštěna služba AD Connect a zda je uživatel přítomen v rámci služby Windows Active Directory a Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Proč se zobrazí chyby připojení HTTP v protokolech se selháním všech mých ověřování?

Ověřte, že ze serveru, na kterém se spouští rozšíření NPS, je přístupný web https://adnotifications.windowsazure.com.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Proč ověřování nefunguje, i když je přítomen platný certifikát?

Pokud uplynula platnost předchozího certifikátu počítače a vygeneroval se nový certifikát, měli byste odstranit všechny certifikáty s vypršenou platností. Máte-li certifikáty s vypršenou platností, můžete způsobit problémy s počátkem rozšíření serveru NPS

Pokud chcete zjistit, jestli máte platný certifikát, zkontrolujte úložiště certifikátů účtu místního počítače pomocí konzoly MMC a zajistěte, aby certifikát neprošlý datem vypršení platnosti. Pokud chcete vygenerovat nový platný certifikát, znovu spusťte postup v části[spuštění skriptu PowerShellu](#run-the-powershell-script).

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Správa protokolů TLS a SSL a šifrovacích sad

Doporučuje se, aby starší a slabší šifrovací sady byly zakázané nebo odebrané, pokud to vaše organizace nevyžaduje. Informace o tom, jak to provést, najdete v článku [Správa protokolů SSL a TLS a šifrovacích sad pro AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

### <a name="additional-troubleshooting"></a>Další řešení potíží

Další pokyny k odstraňování potíží a možná řešení najdete v článku [řešení chybových zpráv z rozšíření serveru NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Další kroky

- Nakonfigurujte alternativní ID pro přihlášení nebo nastavte seznam výjimek pro IP adresy, které by se nemusely provádět dvoustupňové ověřování v [pokročilých možnostech konfigurace pro rozšíření serveru NPS pro Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Naučte se integrovat servery [Brána vzdálené plochy](howto-mfa-nps-extension-rdg.md) a [VPN](howto-mfa-nps-extension-vpn.md) pomocí rozšíření NPS.

- [řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
