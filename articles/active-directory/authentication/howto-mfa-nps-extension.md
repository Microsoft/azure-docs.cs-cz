---
title: Použít existující servery NPS k poskytování funkcí Azure MFA | Microsoft Docs
description: Přidání možností cloudového dvoustupňové vericiation do vaší stávající infrastruktury pro ověřování
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 05/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: a4a882781562ae71ad6c578577a9f9cd9096ef36
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331785"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Stávající infrastruktury serveru NPS integrovat Azure Multi-Factor Authentication

Rozšíření serveru NPS (Network Policy Server) pro Azure MFA přidá možnosti vícefaktorového ověřování založená na cloudu k infrastruktuře ověřování pomocí existujících serverů. Rozšíření serveru NPS můžete přidat telefonní hovor, textová zpráva nebo ověření telefonu v aplikaci k vaší stávající tok ověřování bez nutnosti instalovat, konfigurovat a spravovat nové servery. 

Toto rozšíření byla vytvořena pro organizace, které chcete chránit připojení k síti VPN bez nasazení Azure MFA serveru. Rozšíření serveru NPS slouží jako adaptér mezi RADIUS a cloudu Azure MFA pro poskytování druhý faktor ověřování pro federované nebo synchronizovaných uživatelů.

Pokud používáte rozšíření NPS pro Azure MFA, tok ověřování zahrnuje následující součásti: 

1. **Server NAS nebo virtuální privátní sítě** přijímá požadavky od klientů VPN a převede je na požadavky protokolu RADIUS na serverech NPS. 
2. **NPS Server** připojuje ke službě Active Directory a provést primární ověřování pro požadavky protokolu RADIUS a na úspěch, předává žádosti žádné nainstalovaná rozšíření.  
3. **Rozšíření serveru NPS** aktivuje požadavek na Azure MFA pro sekundární ověřování. Jakmile rozšíření obdrží odpověď, a pokud ověřovací test MFA úspěšné, dokončení žádosti o ověření a poskytovat tokeny zabezpečení, které obsahují deklaraci identity MFA NPS server, vydaný služby tokenů zabezpečení Azure.  
4. **Azure MFA** komunikuje s Azure Active Directory se načíst podrobnosti o uživatele a provádí pomocí metody ověřování nakonfigurovat tak, aby uživatel sekundární ověřování.

Následující diagram znázorňuje tento tok požadavku vysoké úrovně ověřování: 

![Vývojový diagram ověřování](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Plánování nasazení

Rozšíření serveru NPS automaticky zpracovává redundance, takže není nutné žádnou zvláštní konfiguraci.

Můžete vytvořit tolik serverů povolena Azure MFA serveru NPS podle potřeby. Pokud instalujete několik serverů, používejte rozdíl klientský certifikát pro každou z nich. Vytvoření certifikátu pro každý server znamená, že můžete aktualizovat jednotlivě každý certifikát a nestarat se o výpadek napříč všemi servery.

Servery VPN směrovat žádosti o ověření, takže je třeba vědět nové servery NPS povolená Azure MFA.

## <a name="prerequisites"></a>Požadavky

Rozšíření serveru NPS je určená pro práci s vaší stávající infrastruktury. Ujistěte se, že máte následující předpoklady, než začnete.

### <a name="licenses"></a>Licence

Server NPS rozšíření pro Azure MFA není k dispozici pro zákazníky s [licencí pro ověřování Azure Multi-Factor Authentication](multi-factor-authentication.md) (zahrnutá v Azure AD Premium, EMS nebo samostatné licence MFA). Na základě spotřeby licencí Azure MFA jako ověřování licencí vázaných na uživatele nebo nejsou kompatibilní s příponou serveru NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 nebo novější.

### <a name="libraries"></a>Knihovny

Tyto knihovny jsou automaticky nainstalovány s příponou.

-   [Distribuovatelné balíčky Visual C++ pro Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory modul pro prostředí Windows PowerShell verze 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory modul pro prostředí Windows PowerShell je nainstalován, pokud ještě není přítomný, prostřednictvím konfigurační skript, které spustíte jako součást procesu instalace. Není nutné k instalaci tohoto modulu předem, pokud již není nainstalována.

### <a name="azure-active-directory"></a>Azure Active Directory

Všechny, kteří používají rozšíření NPS musí synchronizovat s Azure Active Directory přes Azure AD Connect a musí být zaregistrovaný pro MFA.

Při instalaci rozšíření, potřebujete přihlašovací údaje pro ID a správce adresáře pro vašeho tenanta Azure AD. Vaše ID adresáře v můžete najít [portál Azure](https://portal.azure.com). Přihlaste se jako správce, vyberte **Azure Active Directory** ikonu na levé straně vyberte **vlastnosti**. Zkopírovat identifikátor GUID v **ID adresáře** pole a uložte ho. Můžete použít tento identifikátor GUID jako ID klienta při instalaci rozšíření serveru NPS.

![Najít ID vašeho adresáře v rámci Azure Active Directory vlastnosti](./media/howto-mfa-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Příprava prostředí

Před instalací NPS rozšíření, které chcete připravit můžete prostředí pro zpracování provozu ověřování.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Povolení role NPS na server připojený k doméně

NPS server se připojuje k Azure Active Directory a ověřuje požadavky vícefaktorového ověřování. Vyberte jeden server pro tuto roli. Doporučujeme vybrat server, který nemůže pracovat s požadavky z jiných služeb, protože server NPS rozšíření vyvolá chyby pro všechny žádosti, které nejsou protokolu RADIUS. NPS server musí být nastavený jako ověřování primární a sekundární server pro vaše prostředí; ji nelze požadavky proxy protokolu RADIUS na jiný server.

1. Na serveru, otevřete **Průvodce přidáním rolí a funkcí** v nabídce rychlý start správce serveru.
2. Zvolte **instalace na základě rolí nebo na základě funkcí** pro váš typ instalace.
3. Vyberte **služba síťové zásady a přístup** role serveru. O požadované funkce pro tuto roli spouštět může překryvné okno.
4. Postupujte podle pokynů Průvodce až potvrzovací stránku. Vyberte **Install** (Nainstalovat).

Nyní když máte server určený pro server NPS, byste měli nakonfigurovat tento server zpracovávat příchozí požadavky protokolu RADIUS z daného řešení VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurace řešení sítě VPN pro komunikaci se serverem NPS

V závislosti na tom, které použijete řešení sítě VPN postup konfigurace vaší zásady ověřování RADIUS lišit. Nakonfigurujte tuto zásadu tak, aby odkazovaly na váš server RADIUS serveru NPS.

### <a name="sync-domain-users-to-the-cloud"></a>Uživatelé domény synchronizace do cloudu

Tento krok již mohou být dokončené na klientovi, ale je dobrým znovu zkontrolovat, že Azure AD Connect nedávno synchronizoval vaší databáze.

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Vyberte **Azure Active Directory** > **Azure AD Connect**
3. Ověřte, zda je vaše stav synchronizace **povoleno** a že poslední synchronizace byla menší než hodinou.

Pokud je potřeba ji nové kolo synchronizace, nám podle pokynů v [synchronizace Azure AD Connect: Scheduler](../connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Určit, jaké metody ověřování mohou uživatelé používat

Existují dva faktory, které ovlivňují, které metody ověřování jsou k dispozici s nasazením rozšíření serveru NPS:

1. Heslo šifrovacího algoritmu používaného mezi klientem RADIUS (sítě VPN, Netscaler server či jiné) a servery NPS.
   - **PAP** podporuje všechny metody ověřování Azure mfa v cloudu: telefonní hovor, jednosměrné textová zpráva, oznámení mobilní aplikace a kód ověření mobilní aplikace.
   - **CHAPV2** a **EAP** podporují telefonní hovory a oznámení mobilní aplikace.
2. Metody zadávání znaků, klientská aplikace (VPN, Netscaler server či jiné) může zpracovat. Například klienta VPN mít některé prostředky, které chcete umožnit uživatelům zadejte ověřovací kód z mobilní aplikace nebo text?

Když nasadíte rozšíření serveru NPS, použijte tyto faktory k vyhodnocení, které metody jsou k dispozici pro vaše uživatele. Pokud váš klient RADIUS podporuje protokol PAP, ale klient UX nemá vstupní pole pro ověřovací kód, pak telefonní hovory a oznámení mobilní aplikace jsou dvě podporované možnosti.

Můžete [zakázat nepodporované ověřování metody](howto-mfa-mfasettings.md#selectable-verification-methods) v Azure.

### <a name="register-users-for-mfa"></a>Registrace uživatelů pro MFA

Před nasazení a používání rozšíření serveru NPS, uživatelé, požadovaná pro dvoustupňové ověření musí být zaregistrovaný pro MFA. Více okamžitě k testovací rozšíření, jako je nasadit, potřebujete účet alespoň jeden test, který je plně zaregistrován u služby Multi-Factor Authentication.

Získat účet testu, spuštění pomocí těchto kroků:
1. Přihlaste se k [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) s testovací účet. 
2. Postupujte podle pokynů k nastavení metodu ověřování.
3. Buď vytvoření zásady podmíněného přístupu nebo [změnit stav uživatele](howto-mfa-userstates.md) tak, aby vyžadovala dvoustupňové ověřování pro testovací účet. 

Vaši uživatelé také muset postupovat podle těchto kroků k registraci předtím, než můžete ověřit pomocí rozšíření serveru NPS.

## <a name="install-the-nps-extension"></a>Nainstalujte rozšíření serveru NPS

> [!IMPORTANT]
> Nainstalujte rozšíření serveru NPS na jiném serveru než VPN přístupový bod.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Stáhnout a nainstalovat rozšíření NPS pro Azure MFA

1.  [Stáhněte si rozšíření NPS](https://aka.ms/npsmfa) z webu Microsoft Download Center.
2.  Zkopírujte binárního souboru na serveru Network Policy Server, kterou chcete konfigurovat.
3.  Spustit *setup.exe* a postupujte podle pokynů pro instalaci. Pokud narazíte na chyby, zkontrolujte, že dvě knihovny z části požadovaných součástí byly úspěšně nainstalovány.

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Instalační program vytvoří skript prostředí PowerShell v tomto umístění: `C:\Program Files\Microsoft\AzureMfa\Config` (kde C:\ představuje instalační jednotce). Tento skript prostředí PowerShell provede následující akce:

-   Vytvořte certifikát podepsaný svým držitelem.
-   Přidružte veřejný klíč certifikátu pro službu objektu zabezpečení v Azure AD.
-   Uložte certifikát v úložišti certifikátů místního počítače.
-   Udělit přístup k privátní klíč certifikátu do síťového uživatele.
-   Restartujte serveru NPS.

Pokud chcete používat vlastní certifikáty (namísto certifikáty podepsané svým držitelem, které generuje skript prostředí PowerShell), spusťte skript prostředí PowerShell pro dokončení instalace. Pokud instalujete rozšíření na více serverech, každé z nich by měl mít svůj vlastní certifikát.

1. Spusťte prostředí Windows PowerShell jako správce.
2. Změňte adresáře.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Spusťte skript prostředí PowerShell vytvořené instalační služby.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Přihlaste se ke službě Azure AD jako správce.
5. Prostředí PowerShell vyzve k zadání vašeho ID klienta. Použijte identifikátor GUID ID adresáře, který jste zkopírovali z portálu Azure v části předpoklady.
6. PowerShell zobrazí zpráva o úspěšném provedení až po dokončení skriptu.  

Opakujte tyto kroky na všechny další servery NPS, které chcete nastavit pro vyrovnávání zatížení.

>[!NOTE]
>Pokud používáte vlastní certifikáty místo aby generovala certifikáty pomocí skriptu prostředí PowerShell, ujistěte se, že správně zarovnané na serveru NPS zásady vytváření názvů. Název subjektu musí být **CN =\<TenantID\>, OU = Microsoft NPS rozšíření**. 

## <a name="configure-your-nps-extension"></a>Konfigurace serveru NPS rozšíření

Tato část obsahuje důležité informace o návrhu a návrhy pro úspěšné nasazení rozšíření serveru NPS.

### <a name="configuration-limitations"></a>Konfigurace omezení

- Server NPS rozšíření pro Azure MFA nezahrnuje nástroje pro migraci uživatelů a nastavení z MFA serveru do cloudu. Z tohoto důvodu doporučujeme pomocí rozšíření pro nová nasazení, nikoli stávajícího nasazení. Pokud používáte rozšíření na stávajícího nasazení, uživatelé musí provést výš znovu k naplnění jejich podrobnosti MFA v cloudu.  
- Rozšíření serveru NPS používá název UPN z místní služby Active directory k identifikaci uživatele v Azure MFA pro provádění sekundární umožňuje Rozšíření lze nakonfigurovat k využívání jiný identifikátor jako alternativního přihlašovacího ID nebo vlastních polí služby Active Directory než UPN. V tématu [rozšířených možnostech konfigurace pro NPS rozšíření pro službu Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md) Další informace.
- Ne všechny protokoly šifrování podporují všechny metody ověřování.
   - **PAP** podporuje telefonní hovor, jednosměrné textová zpráva, oznámení mobilní aplikace a kód ověření mobilní aplikace
   - **CHAPV2** a **EAP** podporují telefonní hovory a oznámení mobilní aplikace

### <a name="control-radius-clients-that-require-mfa"></a>Ovládací prvek klientů RADIUS, které vyžadují vícefaktorové ověřování

Jakmile povolíte MFA pro klienta RADIUS pomocí rozšíření serveru NPS, všechny ověřování pro tohoto klienta jsou potřebná k provedení vícefaktorového ověřování. Pokud chcete povolit MFA pro některé klienty RADIUS a jiné ne, můžete nakonfigurovat dva servery NPS a nainstalovat rozšíření pouze v jedné z nich. Konfigurace klientů RADIUS, které chcete, aby se vyžadovalo k posílání požadavků na server NPS, který je nakonfigurovaný s příponou a další klienti RADIUS na server NPS není nakonfigurované s příponou.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Příprava pro uživatele, která nejsou zaregistrovaná pro MFA

Pokud máte uživatele, která nejsou zaregistrovaná pro MFA, můžete určit, co se stane při pokusu o ověření. Použít nastavení registru *REQUIRE_USER_MATCH* v cestě registru *HKLM\Software\Microsoft\AzureMFA* můžete řídit chování funkce. Toto nastavení má možnost jeden konfigurace:

| Klíč | Hodnota | Výchozí |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | HODNOTU TRUE NEBO FALSE | Není nastavena (ekvivalentní na hodnotu TRUE) |

Účelem tohoto nastavení je určit, co dělat, když uživatel není zaregistrovaný pro MFA. Pokud klíč neexistuje, není nastavena nebo je nastaven na hodnotu TRUE a uživatel není registrovaný, potom rozšíření selže ověřovací test MFA. Když klíč nastaven na hodnotu FALSE a uživatel není registrovaný, ověření pokračuje bez vícefaktorového ověřování. Pokud uživatel je zaregistrovaný v MFA, musí ověřit pomocí vícefaktorového ověřování i v případě, že REQUIRE_USER_MATCH nastaven na hodnotu FALSE.

Můžete vytvořit tento klíč a nastavte ji na hodnotu FALSE, zatímco jsou vaši uživatelé registrace, a ne všechny možné registrovat pro Azure MFA ještě. Ale vzhledem k tomu, že nastavení klíče umožňuje uživatelům, která nejsou zaregistrovaná pro vícefaktorové ověřování pro přihlášení, byste měli odebrat tento klíč před přechodem do produkčního prostředí.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak ověřit, zda je nainstalován klientský certifikát, podle očekávání?

Vyhledejte certifikát podepsaný svým držitelem, které jsou vytvořené instalační program v úložiště certifikátu a zkontrolujte, zda má privátní klíč oprávnění udělená uživateli **síťové služby**. Certifikát obsahuje název subjektu z **CN \<tenantid\>, OU = Microsoft NPS rozšíření**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak můžete ověřit, že moje klientského certifikátu je přidružena k klienta v Azure Active Directory?

Otevřete příkazový řádek prostředí PowerShell a spusťte následující příkazy:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Tyto příkazy vytisknout všechny certifikáty přidružení klientovi s vaší instancí rozšíření serveru NPS v relaci prostředí PowerShell. Vyhledejte certifikát tak, že vyexportujete klientského certifikátu jako soubor "x.509 s kódováním Base-64" bez soukromého klíče a jejímu porovnání s seznamu z prostředí PowerShell.

Platný-z a platné – dokud časová razítka, které jsou v podobě čitelný, lze filtrovat zřejmé misfits Pokud příkaz vrátí více než jeden certifikát.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Proč se nedaří Moje žádosti s ADAL Chyba tokenu?

Tato chyba může být způsobeno jedním z několika důvodů. Tyto kroky použijte k řešení:

1. Restartujte NPS server.
2. Ověřte, zda je tento certifikát klienta nainstalovány podle očekávání.
3. Ověřte, zda je certifikát přidružený vašeho klienta na Azure AD.
4. Ověřte, že https://login.microsoftonline.com/ je přístupná ze serveru s příponou.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Proč ověřování nezdaří s chybou v protokolech HTTP s oznámením, že uživatel není nalezen?

Ověřte, zda je spuštěna AD Connect a že uživatel je součástí Windows Active Directory a Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Proč vidí HTTP připojit chyby v protokolech s Moje ověřování selhání?

Ověřte, že https://adnotifications.windowsazure.com dostupný ze serveru se spuštěným serverem NPS rozšíření.


## <a name="next-steps"></a>Další postup

- Konfigurovat alternativní ID pro přihlášení, nebo nastavit seznam výjimek pro IP adresy, který by neměl provádět dvoustupňové ověření v [rozšířených možnostech konfigurace pro NPS rozšíření pro službu Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Zjistěte, jak integrovat [Brána vzdálené plochy](howto-mfa-nps-extension-rdg.md) a [servery VPN](howto-mfa-nps-extension-vpn.md) pomocí rozšíření serveru NPS

- [řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
