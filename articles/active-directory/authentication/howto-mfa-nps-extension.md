---
title: Poskytování funkcí Azure MFA pomocí serveru NPS – Azure Active Directory
description: Přidání cloudových funkcí dvoufázového ověřování do stávající infrastruktury ověřování
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
ms.openlocfilehash: cc1be4637d56d7205d50ebfc6f7d1d5d22e62edf
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617661"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrace vaší stávající infrastruktury NPS se službou Multi-Factor Authentication

Rozšíření servernps (Network Policy Server) pro Azure MFA přidává cloudové funkce MFA do infrastruktury ověřování pomocí stávajících serverů. Pomocí rozšíření NPS můžete do stávajícího toku ověřování přidat telefonní hovor, textovou zprávu nebo ověření telefonní aplikace, aniž byste museli instalovat, konfigurovat a udržovat nové servery. 

Toto rozšíření bylo vytvořeno pro organizace, které chtějí chránit připojení VPN bez nasazení serveru Azure MFA. Rozšíření NPS funguje jako adaptér mezi RADIUS a cloudové Azure MFA poskytnout druhý faktor ověřování pro federované nebo synchronizované uživatele.

Při použití rozšíření NPS pro Azure MFA, tok ověřování zahrnuje následující součásti: 

1. **Server NAS/VPN** přijímá požadavky od klientů VPN a převádí je na požadavky RADIUS na servery NPS. 
2. **Server NPS** se připojuje ke službě Active Directory, aby provedl primární ověřování požadavků RADIUS a po úspěchu předá požadavek všem nainstalovaným rozšířením.  
3. **Rozšíření NPS** aktivuje požadavek na Azure MFA pro sekundární ověřování. Jakmile rozšíření obdrží odpověď a pokud výzva mfa úspěšné, dokončí požadavek na ověření tím, že server NPS s tokeny zabezpečení, které obsahují deklaraci mfa, vydané Azure STS.  
4. **Azure MFA** komunikuje s Azure Active Directory načíst podrobnosti o uživateli a provádí sekundární ověřování pomocí metody ověření nakonfigurované pro uživatele.

Následující diagram znázorňuje tento tok požadavku na ověření na vysoké úrovni: 

![Diagram toku ověřování](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Plánování nasazení

Rozšíření NPS automaticky zpracovává redundanci, takže nepotřebujete speciální konfiguraci.

Můžete vytvořit tolik serverů NPS s podporou Azure MFA, kolik potřebujete. Pokud nainstalujete více serverů, měli byste použít rozdíl klientský certifikát pro každý z nich. Vytvoření certifikátu pro každý server znamená, že můžete každý certifikát aktualizovat jednotlivě a nemusíte se obávat prostojů na všech serverech.

Servery VPN směrují požadavky na ověření, takže si musí být vědomy nových serverů NPS s podporou Azure MFA.

## <a name="prerequisites"></a>Požadavky

Rozšíření NPS je určeno pro práci s vaší stávající infrastrukturou. Než začnete, ujistěte se, že máte následující požadavky.

### <a name="licenses"></a>Licence

Rozšíření NPS pro Azure MFA je k dispozici zákazníkům s [licencemi pro Azure Multi-Factor Authentication](multi-factor-authentication.md) (součástí Azure AD Premium, EMS nebo mfa samostatné licence). Licence založené na spotřebě pro Azure MFA, například pro uživatele nebo ověřování licencí nejsou kompatibilní s rozšířením NPS. 

### <a name="software"></a>Software

Systém Windows Server 2008 R2 SP1 nebo vyšší.

### <a name="libraries"></a>Knihovny

Tyto knihovny jsou nainstalovány automaticky s rozšířením.

- [Redistribuovatelné balíčky Visual C++ pro Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Modul Microsoft Azure Active Directory pro Prostředí Windows PowerShell verze 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Modul Microsoft Azure Active Directory module maskérna pro prostředí Windows PowerShell je nainstalován, pokud ještě není k dispozici, prostřednictvím konfiguračního skriptu, který spustíte jako součást procesu instalace. Není třeba tento modul instalovat předem, pokud ještě není nainstalován.

### <a name="azure-active-directory"></a>Azure Active Directory

Všichni uživatelé rozšíření NPS musí být synchronizováni do služby Azure Active Directory pomocí služby Azure AD Connect a musí být registrováni pro vícefaktorové informace.

Při instalaci rozšíření potřebujete ID adresáře a přihlašovací údaje správce pro vašeho klienta Azure AD. ID adresáře najdete na [webu Azure Portal](https://portal.azure.com). Přihlaste se jako správce. Vyhledejte a vyberte **službu Azure Active Directory**a vyberte **možnost Vlastnosti**. Zkopírujte identifikátor GUID do pole **ID adresáře** a uložte jej. Tento identifikátor GUID použijete jako ID klienta při instalaci rozšíření NPS.

![Vyhledání ID adresáře ve vlastnostech služby Azure Active Directory](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Síťové požadavky

Server NPS musí být schopen komunikovat s následujícími adresami URL přes porty 80 a 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https:\//credentials.azure.com

K dokončení [instalace adaptéru pomocí zadaný skript powershellu](#run-the-powershell-script) je navíc nutné připojení k následujícím adresám URL.

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Příprava prostředí

Před instalací rozšíření NPS chcete připravit prostředí pro zpracování ověřovacího provozu.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Povolení role serveru NPS na serveru přilehlém k doméně

Server NPS se připojí ke službě Azure Active Directory a ověří požadavky mfa. Zvolte jeden server pro tuto roli. Doporučujeme zvolit server, který nezpracovává požadavky z jiných služeb, protože rozšíření NPS vyvolá chyby pro všechny požadavky, které nejsou RADIUS. Server NPS musí být nastaven jako primární a sekundární ověřovací server pro vaše prostředí. nemůže proxy radius požadavky na jiný server.

1. Na serveru otevřete **Průvodce přidáním rolí a funkcí** z nabídky Rychlý start Správce serveru.
2. Pro typ instalace zvolte **instalaci založenou na rolích nebo funkcích.**
3. Vyberte roli serveru **Zásady sítě a přístupové služby.** Může se objevit okno, které vás informuje o požadovaných funkcích pro spuštění této role.
4. Pokračujte průvodcem až na stránku Potvrzení. Vyberte **Install** (Nainstalovat).

Nyní, když máte server určený pro server NPS, měli byste také nakonfigurovat tento server tak, aby zpracovával příchozí požadavky RADIUS z řešení VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurace řešení VPN pro komunikaci se serverem NPS

V závislosti na tom, které řešení VPN používáte, se postup konfigurace zásad ověřování RADIUS liší. Nakonfigurujte tuto zásadu tak, aby přecšují na server RADIUS NPS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronizace uživatelů domény s cloudem

Tento krok už může být dokončena na vašem tenantovi, ale je vhodné zkontrolovat, že Azure AD Connect má synchronizované databáze v poslední době.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Vyberte **Azure Active Directory** > **Azure AD Connect**
3. Ověřte, zda je stav synchronizace **povolen** a zda byla poslední synchronizace před méně než hodinou povolena.

Pokud potřebujete zahájit nové kolo synchronizace, nám pokyny v [synchronizaci Azure AD Connect: Plánovač](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Určení metod ověřování, které mohou uživatelé používat

Existují dva faktory, které ovlivňují, které metody ověřování jsou k dispozici s nasazením rozšíření NPS:

1. Algoritmus šifrování hesla používaný mezi klientem RADIUS (VPN, servernetscaler nebo jiným) a servery NPS.
   - **Protokol PAP** podporuje všechny metody ověřování Azure MFA v cloudu: telefonní hovor, jednosměrné textové zprávy, oznámení o mobilní aplikaci, hardwarové tokeny OATH a ověřovací kód mobilní aplikace.
   - **ChapV2** a **EAP** podporují telefonní hovor a oznámení o mobilních aplikacích.

      > [!NOTE]
      > Při nasazení rozšíření NPS použijte tyto faktory k vyhodnocení, které metody jsou k dispozici pro uživatele. Pokud klient RADIUS podporuje protokol PAP, ale uživatelské pole klienta nemá vstupní pole pro ověřovací kód, pak jsou dvě podporované možnosti telefonního hovoru a oznámení mobilní aplikace.
      >
      > Kromě toho, pokud uživatelské rozhraní klienta VPN podporuje vstupní pole a nakonfigurovali jste zásady přístupu k síti - ověřování může být úspěšné, ale žádný z atributů RADIUS nakonfigurovaných v zásadách sítě nebude použit ani na zařízení pro přístup k síti, jako je server RRAS, ani na klienta VPN. V důsledku toho může mít klient VPN větší přístup, než je žádoucí, nebo méně k žádnému přístupu.
      >

2. Vstupní metody, které klientská aplikace (VPN, Netscaler server nebo jiné) může zpracovat. Má například klient VPN nějaké prostředky, které uživateli umožňují zadat ověřovací kód z textové nebo mobilní aplikace?

V Azure můžete [zakázat nepodporované metody ověřování.](howto-mfa-mfasettings.md#verification-methods)

### <a name="register-users-for-mfa"></a>Registrace uživatelů pro vícefaktorové finanční účty

Před nasazením a použitím rozšíření NPS musí být uživatelé, kteří jsou povinni provádět dvoustupňové ověření, registrováni pro vícefaktorové ověřování. Další okamžitě otestovat rozšíření při nasazení, budete potřebovat alespoň jeden testovací účet, který je plně registrován pro vícefaktorové ověřování.

Pomocí následujících kroků můžete spustit testovací účet:

1. Přihlaste [https://aka.ms/mfasetup](https://aka.ms/mfasetup) se k testovacímu účtu.
2. Podle pokynů nastavte metodu ověření.
3. [Vytvořte zásadu podmíněného přístupu,](howto-mfa-getstarted.md#create-conditional-access-policy) která bude vyžadovat vícefaktorové ověřování pro testovací účet.

> [!IMPORTANT]
>
> Ujistěte se, že uživatelé úspěšně zaregistrovali pro Azure Multi-Factor Authentication. Pokud uživatelé dříve zaregistrovali pouze pro samoobslužné resetování hesla (SSPR), *StrongAuthenticationMethods* je povolena pro jejich účet. Azure Multi-Factor Authentication je vynuceno při *strongAuthenticationMethods* je nakonfigurován, i v případě, že uživatel registrovány pouze pro samodotykové ho disponu.
>
> Kombinovaná registrace zabezpečení může být povolena, která konfiguruje samoobslužné ověřování a Azure Vícefaktorové ověřování současně. Další informace naleznete v [tématu Enable combined security information registration in Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Můžete také [vynutit uživatelům znovu zaregistrovat metody ověřování,](howto-mfa-userdevicesettings.md#manage-user-authentication-options) pokud dříve povolili pouze samodotykové nařízení .

## <a name="install-the-nps-extension"></a>Instalace rozšíření NPS

> [!IMPORTANT]
> Nainstalujte rozšíření NPS na jiný server, než je přístupový bod VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Stažení a instalace rozšíření NPS pro Azure MFA

1. [Stáhněte si rozšíření NPS](https://aka.ms/npsmfa) ze služby Stažení softwaru.
2. Zkopírujte binární soubor na server zásad sítě, který chcete konfigurovat.
3. Spusťte *program setup.exe* a postupujte podle pokynů k instalaci. Pokud narazíte na chyby, zkontrolujte, zda byly obě knihovny z části požadavků úspěšně nainstalovány.

#### <a name="upgrade-the-nps-extension"></a>Upgrade rozšíření nps

Při inovaci existující instalace rozšíření NPS, aby se zabránilo restartování základního serveru, proveďte následující kroky:

1. Odinstalace existující verze
1. Spuštění nového instalačního programu
1. Restartování služby Server síťových zásad (IAS)

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Instalační program vytvoří skript prostředí PowerShell `C:\Program Files\Microsoft\AzureMfa\Config` v tomto umístění: (kde C:\ je vaše instalační jednotka). Tento skript prostředí PowerShell provádí při každém spuštění následující akce:

- Vytvořte certifikát podepsaný svým držitelem.
- Přidružte veřejný klíč certifikátu k instančnímu objektu ve službě Azure AD.
- Uložte certifikát do místního úložiště certifikátů počítače.
- Udělte přístup k soukromému klíči certifikátu uživateli sítě.
- Restartujte server NPS.

Pokud nechcete používat vlastní certifikáty (namísto certifikátů podepsaných svým držitelem, které generuje skript prostředí PowerShell), spusťte k dokončení instalace skript prostředí PowerShell. Pokud nainstalujete rozšíření na více serverů, každý z nich by měl mít svůj vlastní certifikát.

1. Spusťte prostředí Windows PowerShell jako správce.
2. Změňte adresáře.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Spusťte skript Prostředí PowerShell vytvořený instalačním programem.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Přihlaste se k Azure AD jako správce.
5. PowerShell vyzve k ID vašeho klienta. Použijte identifikátor GUID ID adresáře, který jste zkopírovali z portálu Azure v části požadavky.
6. Prostředí PowerShell zobrazí zprávu o úspěchu po dokončení skriptu.  

Opakujte tyto kroky na všech dalších serverech NPS, které chcete nastavit pro vyrovnávání zatížení.

Pokud platnost předchozího certifikátu počítače vypršela a byl vygenerován nový certifikát, měli byste odstranit všechny certifikáty, jejichž platnost vypršela. Certifikáty, jejichž platnost vypršela, mohou způsobit problémy se spuštěním rozšíření serveru NPS.

> [!NOTE]
> Pokud používáte vlastní certifikáty namísto generování certifikátů se skriptem prostředí PowerShell, ujistěte se, že jsou zarovnány s konvencí pojmenování serveru NPS. Název subjektu musí být **CN=\<\>TenantID ,OU=Microsoft NPS Extension**. 

### <a name="microsoft-azure-government-additional-steps"></a>Další kroky pro azure pro státní správu

Pro zákazníky, kteří používají cloud Azure Government, jsou na každém serveru NPS vyžadovány následující další kroky konfigurace:

1. Otevřete **Editor registru** na serveru NPS.
1. Přejděte na adresu `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Nastavte následující hodnoty klíče:

    | Klíč registru       | Hodnota |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Opakováním předchozích dvou kroků nastavte hodnoty klíče registru pro každý server NPS.
1. Restartujte službu NPS pro každý server NPS.

    Chcete-li minimální dopad, vyřaďte každý server NPS z rotace služby Vyrovnávání zatížení sítě jeden po druhém a počkejte, až se všechna připojení vyprázdní.

### <a name="certificate-rollover"></a>Převrácení certifikátu

S verzí 1.0.1.32 rozšíření NPS je nyní podporováno čtení více certifikátů. Tato funkce pomůže usnadnit postupné aktualizace certifikátů před vypršením jejich platnosti. Pokud vaše organizace používá předchozí verzi rozšíření NPS, měli byste upgradovat na verzi 1.0.1.32 nebo vyšší.

Certifikáty vytvořené `AzureMfaNpsExtnConfigSetup.ps1` skriptem jsou platné po dobu 2 let. IT organizace by měly monitorovat vypršení platnosti certifikátů. Certifikáty pro rozšíření serveru NPS jsou umístěny v úložišti certifikátů místního počítače v části Osobní a jsou vydány ID klienta poskytnutého skriptu.

Pokud se certifikát blíží datu vypršení platnosti, měl by být vytvořen nový certifikát, který jej nahradí.  Tento proces se provádí `AzureMfaNpsExtnConfigSetup.ps1` spuštěním znovu a zachování stejné id klienta po zobrazení výzvy. Tento proces by měl být opakován na každém serveru NPS ve vašem prostředí.

## <a name="configure-your-nps-extension"></a>Konfigurace rozšíření serveru NPS

Tato část obsahuje aspekty návrhu a návrhy pro úspěšná nasazení rozšíření NPS.

### <a name="configuration-limitations"></a>Omezení konfigurace

- Rozšíření NPS pro Azure MFA neobsahuje nástroje pro migraci uživatelů a nastavení ze serveru MFA do cloudu. Z tohoto důvodu doporučujeme použít rozšíření pro nová nasazení, nikoli pro stávající nasazení. Pokud používáte rozšíření na existující nasazení, vaši uživatelé musí provést korektní stav znovu naplnit své podrobnosti mfa v cloudu.  
- Rozšíření NPS používá hlavní název uživatele z místního adresáře Active k identifikaci uživatele v Azure MFA pro provádění sekundární ověření. Rozšíření lze nakonfigurovat tak, aby používalo jiný identifikátor, jako je alternativní přihlašovací ID nebo vlastní pole služby Active Directory, jiné než hlavní název služby. Další informace naleznete v článku [Rozšířené možnosti konfigurace pro rozšíření nps pro vícefaktorové ověřování](howto-mfa-nps-extension-advanced.md).
- Ne všechny šifrovací protokoly podporují všechny metody ověřování.
   - **PAP** podporuje telefonní hovor, jednosměrnou textovou zprávu, oznámení o mobilní aplikaci a ověřovací kód mobilní aplikace
   - **Podpora chapv2** a **EAP** pro telefonické volání a oznámení o mobilní aplikaci

### <a name="control-radius-clients-that-require-mfa"></a>Řízení klientů RADIUS, kteří vyžadují vícefaktorové ověřování

Jakmile povolíte vícefaktorové ověřování pro klienta RADIUS pomocí rozšíření NPS, všechna ověřování pro tohoto klienta jsou vyžadována k provedení vícefaktorového ověřování. Chcete-li povolit vícefaktorové ověřování pro některé klienty RADIUS, ale jiné nikoli, můžete nakonfigurovat dva servery NPS a nainstalovat rozšíření pouze na jeden z nich. Nakonfigurujte klienty RADIUS, které chcete vyžadovat, aby vícefaktorové ověřování odesílala požadavky na server NPS nakonfigurovaný s rozšířením, a další klienti RADIUS na server NPS, který není nakonfigurován s rozšířením.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Příprava pro uživatele, kteří nejsou zaregistrovaní pro vícefaktorové hodnocení

Pokud máte uživatele, kteří nejsou zapsáni pro vícefaktorové ověřování, můžete určit, co se stane při pokusu o ověření. Pomocí nastavení registru *REQUIRE_USER_MATCH* v cestě registru *HKLM\Software\Microsoft\AzureMFA* můžete řídit chování funkce. Toto nastavení má jednu možnost konfigurace:

| Klíč | Hodnota | Výchozí |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | PRAVDA/NEPRAVDA | Není nastaveno (odpovídá hodnotě TRUE) |

Účelem tohoto nastavení je určit, co dělat, když uživatel není zapsán pro vícefaktorové ověřování. Pokud klíč neexistuje, není nastaven nebo je nastaven na hodnotu TRUE a uživatel není zaregistrován, rozšíření se nezdaří výzvu Vícefaktorové ověřování. Pokud je klíč nastaven na hodnotu FALSE a uživatel není zaregistrován, ověřování pokračuje bez provedení vícefaktorové ověřování. Pokud je uživatel zaregistrován v mfa, musí se ověřit pomocí vícefaktorové ověřování i v případě, že je REQUIRE_USER_MATCH nastavena na hodnotu FALSE.

Tento klíč můžete vytvořit a nastavit na hodnotu FALSE, zatímco vaši uživatelé se zařazují a nemusí být všechny zaregistrované pro Azure MFA. Nicméně, vzhledem k tomu, že nastavení klíče umožňuje uživatelům, kteří nejsou zapsáni pro Vícefaktorové řízení přihlásit, měli byste tento klíč před odchodem do produkčního prostředí odebrat.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="nps-extension-health-check-script"></a>Skript kontroly stavu rozšíření nps

Následující skript je k dispozici pro provedení základních kroků kontroly stavu při řešení potíží s rozšířením NPS.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak ověřím, že certifikát klienta je nainstalován podle očekávání?

Vyhledejte certifikát podepsaný svým držitelem vytvořený instalačním programem v úložišti certifikátů a zkontrolujte, zda má soukromý klíč oprávnění udělená službě USER **NETWORK SERVICE**. Certifikát má název předmětu ** \<tenantid\>Cn , OU = Rozšíření Microsoft NPS**

Certifikáty podepsané svým držitelem generované skriptem *AzureMfaNpsExtnConfigSetup.ps1* mají také životnost platnosti dva roky. Při ověřování, zda je certifikát nainstalován, byste měli také zkontrolovat, zda platnost certifikátu nevypršela.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak můžu ověřit, že je certifikát klienta přidružený k mému tenantovi ve službě Azure Active Directory?

Otevřete příkazový řádek Prostředí PowerShell a spusťte následující příkazy:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Tyto příkazy vytisknou všechny certifikáty, které připodobují vašeho tenanta s vaší instancí rozšíření NPS v relaci prostředí PowerShell. Vyhledejte certifikát exportem klientského certifikátu jako souboru "Base-64 kódovaného X.509(.cer)" bez soukromého klíče a porovnejte ho se seznamem z prostředí PowerShell.

Následující příkaz vytvoří soubor s názvem "npscertificate" na jednotce "C:" ve formátu .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Po spuštění tohoto příkazu přejděte na jednotku C, vyhledejte soubor a poklepejte na něj. Přejděte na podrobnosti a přejděte dolů na "kryptografický otisk", porovnejte kryptografický otisk certifikátu nainstalovaného na serveru s tímto. Kryptografické otisky certifikátu by se měly shodovat.

Platná časová razítka platná od a platná do, která jsou ve formě čitelné pro člověka, lze použít k odfiltrování zřejmých chyb, pokud příkaz vrátí více než jeden certifikát.

---

### <a name="why-cannot-i-sign-in"></a>Proč se nemohu přihlásit?

Zkontrolujte, zda platnost hesla nevypršela. Rozšíření NPS nepodporuje změnu hesla jako součást pracovního postupu přihlášení. Další pomoc vám poskytne it personál vaší organizace.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Proč moje požadavky selhávají s chybou tokenu ADAL?

Tato chyba může být z jednoho z několika důvodů. Řešení potíží vám pomohou následující kroky:

1. Restartujte server NPS.
2. Ověřte, zda je certifikát klienta nainstalován podle očekávání.
3. Ověřte, že certifikát je přidružený k vašemu tenantovi ve službě Azure AD.
4. Ověřte, že ze serveru, na kterém se spouští rozšíření, je přístupný web `https://login.microsoftonline.com/`.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Proč se ověření nezdaří s chybou v protokolech HTTP oznamující, že uživatel nebyl nalezen?

Ověřte, zda je služba AD Connect spuštěna a zda je uživatel přítomen ve službě Windows Active Directory i ve službě Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Proč se v protokolech zobrazují chyby připojení HTTP, se všemi ověřováními se lhanými?

Ověřte, že ze serveru, na kterém se spouští rozšíření NPS, je přístupný web https://adnotifications.windowsazure.com.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Proč ověřování nefunguje, přestože je k dispozici platný certifikát?

Pokud platnost předchozího certifikátu počítače vypršela a byl vygenerován nový certifikát, měli byste odstranit všechny certifikáty, jejichž platnost vypršela. Certifikáty, jejichž platnost vypršela, mohou způsobit problémy se spuštěním rozšíření serveru NPS.

Chcete-li zkontrolovat, zda máte platný certifikát, zkontrolujte úložiště certifikátů místního účtu počítače pomocí konzoly MMC a ujistěte se, že certifikát nepřekročil datum vypršení platnosti. Chcete-li vygenerovat nově platný certifikát, spusťte kroky v části[Spuštění skriptu prostředí PowerShell.](#run-the-powershell-script)

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Správa protokolů TLS a SSL a šifrovacích sad

Doporučujese, aby starší a slabší šifrovací sady byly zakázány nebo odebrány, pokud to vaše organizace nevyžaduje. Informace o tom, jak to provést, najdete v článku [Správa protokolů SSL a TLS a šifrovacích sad pro AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

### <a name="additional-troubleshooting"></a>Další řešení potíží

Další pokyny pro řešení potíží a možná řešení najdete v článku [Vyřešit chybové zprávy z rozšíření NPS pro azure multi-factor authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Další kroky

- [Přehled a konfigurace serveru zásad sítě v systému Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Konfigurace alternativních ID pro přihlášení nebo nastavení seznamu výjimek pro IP adresy, které by neměly provádět dvoustupňové ověření v [rozšířených možnostech konfigurace pro rozšíření NPS pro vícefaktorové ověřování](howto-mfa-nps-extension-advanced.md)

- Zjistěte, jak integrovat [servery](howto-mfa-nps-extension-vpn.md) [služby Brána vzdálené plochy](howto-mfa-nps-extension-rdg.md) a VPN pomocí rozšíření NPS

- [řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
