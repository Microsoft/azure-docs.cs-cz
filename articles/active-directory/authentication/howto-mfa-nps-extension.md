---
title: Použít existující servery NPS k poskytování funkcí Azure MFA
description: Přidání možnosti založené na cloudu dvoustupňové ověření do vaší stávající infrastruktury pro ověřování
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: a24988bb9866dde72769107f1c45fc461c039f9a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161053"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrace vaší stávající infrastruktury NPS pomocí ověřování Azure Multi-Factor Authentication

Rozšíření serveru NPS (Network Policy Server) pro Azure MFA přidává funkce vícefaktorové ověřování založené na cloudu pro vaši infrastrukturu ověřování pomocí existujících serverů. Rozšíření serveru NPS můžete přidat telefonní hovor, textová zpráva nebo ověřovacích telefonních aplikací do vašeho existujícího toku ověřování bez nutnosti instalovat, konfigurovat a spravovat nové servery. 

Toto rozšíření byla vytvořena pro organizace, které chcete chránit pomocí sítě VPN bez nasazení zásady Azure MFA serveru. Rozšíření NPS server slouží jako adaptér mezi RADIUS a založené na cloudu Azure MFA, aby zajišťoval druhý faktor ověřování pro federované nebo synchronizovaných uživatelů.

Pokud používáte rozšíření NPS pro Azure MFA, tok ověřování zahrnuje následující součásti: 

1. **Server NAS/VPN** přijímá požadavky od klientů VPN a převede je na požadavky protokolu RADIUS serveru NPS servery. 
2. **NPS Server** připojí ke službě Active Directory pro primární ověřování pro požadavky protokolu RADIUS a po úspěšném nasazení, předává žádosti do jakékoli nainstalovaná rozšíření.  
3. **Rozšíření serveru NPS** aktivuje požadavek na Azure MFA pro sekundární ověřování. Po rozšíření obdrží odpověď, a pokud bude úspěšné ověřovacím testem MFA, ten se dokončí požadavek na ověření tím, že poskytuje NPS server s tokeny zabezpečení, které obsahují deklaraci identity MFA, vystavené služby tokenů zabezpečení Azure.  
4. **Azure MFA** komunikuje se službou Azure Active Directory k načtení podrobností uživatele a provádí sekundární ověření pomocí jiné metody ověřování nakonfigurovat tak, aby uživatel.

Následující diagram znázorňuje tento tok požadavku základní ověřování: 

![Vývojový diagram ověřování](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Plánování nasazení

Rozšíření NPS automaticky zpracovává redundance, takže není nutné zvláštní konfiguraci.

Můžete vytvořit libovolný počet serverů povolit Azure MFA NPS podle potřeby. Pokud instalujete více serverů, používejte rozdíl klientský certifikát pro každou z nich. Vytvoření certifikátu pro každý server znamená, že můžete aktualizovat jednotlivě každý certifikát a bez starostí o výpadku napříč všemi servery.

Servery VPN směrovat žádosti o ověření, takže je třeba mít na paměti nové servery NPS pro Azure MFA povolena.

## <a name="prerequisites"></a>Požadavky

Rozšíření NPS je určená pro práci s vaší současnou infrastrukturou. Ujistěte se, že máte splněné následující požadavky před zahájením.

### <a name="licenses"></a>Licence

Rozšíření NPS pro Azure MFA je dostupný pro zákazníky s [licencí pro ověřování Azure Multi-Factor Authentication](multi-factor-authentication.md) (je součástí samostatné licence služby MFA, Azure AD Premium a EMS). Založenou na skutečné spotřebě licencí Azure MFA jako ověřování licencí vázaných na uživatele nebo nejsou kompatibilní s rozšíření serveru NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 nebo novější.

### <a name="libraries"></a>Knihovny

Tyto knihovny jsou automaticky nainstalovány s příponou.

- [Distribuovatelné balíčky Visual C++ pro Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory modulu pro Windows PowerShell verze 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Je nainstalovaný Microsoft Azure Active Directory modulu pro Windows PowerShell, pokud ještě není k dispozici prostřednictvím skript konfigurace, které spustíte jako součást procesu instalace. Není nutné k instalaci tohoto modulu předem, pokud ještě není nainstalovaná.

### <a name="azure-active-directory"></a>Azure Active Directory

Každý používá rozšíření serveru NPS musí synchronizovat do Azure Active Directory pomocí služby Azure AD Connect a musí být zaregistrovaný pro MFA.

Při instalaci rozšíření, musíte pro vašeho tenanta Azure AD directory ID a správce přihlašovacích údajů. Do své ID adresáře můžete najít [webu Azure portal](https://portal.azure.com). Přihlaste se jako správce, vyberte **Azure Active Directory** ikonu na levé straně vyberte **vlastnosti**. Zkopírujte identifikátor GUID v **ID adresáře** pole a uložte ho. Při instalaci rozšíření NPS použijete tento identifikátor GUID jako ID tenanta.

![Vyhledejte své ID adresáře ve vlastnostech služby Azure Active Directory](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Síťové požadavky

NPS server musí být schopný komunikovat s následujícími adresami URL přes porty 80 a 443.

* https://adnotifications.windowsazure.com  
* https://login.microsoftonline.com

## <a name="prepare-your-environment"></a>Příprava prostředí

Před instalací rozšíření serveru NPS, budete chtít Příprava prostředí pro zpracování ověřovacího provozu.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Povolení role NPS na serveru připojeném k doméně

NPS server se připojí k Azure Active Directory a ověřuje požadavky vícefaktorového ověřování. Zvolte jeden server pro tuto roli. Doporučujeme vám, vyberete server, který nemá zpracování požadavků od jiných služeb, protože rozšíření NPS vyvolá chyb pro všechny žádosti, které nejsou RADIUS. NPS server musí být nastavení jako serveru primární a sekundární ověřování pro vaše prostředí; nelze požadavků proxy protokolu RADIUS na jiný server.

1. Na serveru, otevřete **Průvodce přidání rolí a funkcí** v nabídce Správce serveru rychlý start.
2. Zvolte **instalace na základě rolí nebo na základě funkcí** pro typ instalace.
3. Vyberte **síťové zásady a přístup ke službám** role serveru. Okno může automaticky otevře, které informují o požadované funkce pro tuto roli spouštět.
4. Postupujte podle pokynů Průvodce až na potvrzovací stránku. Vyberte **Install** (Nainstalovat).

Teď, když máte server určený pro server NPS, by měl také konfigurovat tento server zpracovávat příchozí požadavky protokolu RADIUS z daného řešení VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurace řešení sítě VPN komunikovat se serverem NPS

V závislosti na tom, jaké používáte řešení sítě VPN lišit kroky ke konfiguraci zásady ověřování pomocí protokolu RADIUS. Nakonfigurujte tuto zásadu tak, aby odkazovala na váš server protokolu RADIUS serveru NPS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronizace uživatele domény do cloudu

Tento krok může už měla být dokončená ve svém tenantovi, ale je vhodné zkontrolovat, že Azure AD Connect naposledy dokončí synchronizaci vašich databází.

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Vyberte **Azure Active Directory** > **služby Azure AD Connect**
3. Ověřte, zda je stav synchronizace **povoleno** a poslední synchronizace byla menší než hodinou.

Pokud je potřeba aktivovat nový kolo kempů synchronizace, nám podle pokynů v [synchronizace Azure AD Connect: Plánovač](../connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Určit, jaké metody ověřování uživatelům používat

Existují dva faktory, které ovlivňují, jaké metody ověřování jsou k dispozici s nasazením rozšíření serveru NPS:

1. Heslo šifrovací algoritmus použitý mezi klienta protokolu RADIUS (síť VPN, Netscaler server či jiné) a servery NPS.
   - **PAP** podporuje všechny metody ověřování Azure mfa v cloudu: telefonní hovor, jednosměrná textová zpráva, oznámení mobilní aplikace a ověřovací kód z mobilní aplikace.
   - **CHAPV2** a **EAP** podporují telefonních hovorů a oznámení mobilní aplikace.
2. Metody zadávání znaků, které klientská aplikace (VPN, Netscaler server nebo jiné) může zpracovat. Například klient VPN máte některé prostředky, aby uživatel mohl zadat ověřovací kód z mobilní aplikace nebo text?

Při nasazení rozšíření NPS se používá k vyhodnocení, které metody jsou k dispozici pro vaši uživatelé tyto faktory. Pokud podporuje protokol PAP vašeho klienta protokolu RADIUS, ale klient uživatelského prostředí nemá vstupní pole pro ověřovací kód, pak telefonních hovorů a oznámení mobilní aplikace jsou dvě podporované možnosti.

Je možné [zakázat metody nepodporované ověřování](howto-mfa-mfasettings.md#selectable-verification-methods) v Azure.

### <a name="register-users-for-mfa"></a>Registrace uživatelů pro MFA

Než nasadíte a pomocí rozšíření serveru NPS, uživatelé, kteří jsou potřebná k provedení dvoustupňového ověřování musí být zaregistrovaní pro vícefaktorové ověřování. Více okamžitě k testování rozšíření, jako je nasazení, budete potřebovat alespoň jeden účet, který je plně zaregistrovaný k Vícefaktorovému ověřování.

Pomocí těchto kroků můžete získat zkušební účet spustit:
1. Přihlaste se k [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) zkušební účet. 
2. Postupujte podle pokynů k nastavení metodu ověření.
3. Buď vytvořte zásady podmíněného přístupu nebo [změňte stav uživatele](howto-mfa-userstates.md) a vyžadovat dvoustupňové ověřování pro zkušební účet. 

Uživatelé také potřebovat postupovat podle následujících kroků k registraci předtím, než můžete ověřit pomocí rozšíření NPS.

## <a name="install-the-nps-extension"></a>Instalace rozšíření serveru NPS

> [!IMPORTANT]
> Instalace rozšíření serveru NPS na jiném serveru než VPN přístupový bod.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Stáhněte a nainstalujte rozšíření NPS pro Azure MFA

1. [Stáhněte si rozšíření NPS](https://aka.ms/npsmfa) z webu Microsoft Download Center.
2. Binární soubor zkopírujte do Network Policy Server, kterou chcete konfigurovat.
3. Spustit *setup.exe* a postupujte podle pokynů k instalaci. Pokud narazíte na chyby, zkontrolujte, že dvě knihovny z část předpoklady v tématu byly úspěšně nainstalovány.

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Instalační program vytvoří skript Powershellu v tomto umístění: `C:\Program Files\Microsoft\AzureMfa\Config` (kde C:\ je instalační disk). Tento skript Powershellu provede následující akce pokaždé, když je spuštění:

- Vytvořte certifikát podepsaný svým držitelem.
- Přidružte veřejný klíč certifikátu pro instanční objekt v Azure AD.
- Store certifikátu v úložišti certifikátů místního počítače.
- Udělení přístupu k privátnímu klíči certifikátu do síťového uživatele.
- Restartování serveru NPS.

Pokud chcete použít vlastní certifikáty (a nikoli certifikáty podepsané svým držitelem, které generuje skript prostředí PowerShell), spusťte skript prostředí PowerShell pro dokončení instalace. Pokud nainstalujete rozšíření na několik serverů, každý z nich by měl mít svůj vlastní certifikát.

1. Spusťte prostředí Windows PowerShell jako správce.
2. Změňte adresáře.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Spusťte skript Powershellu vytvoří pomocí Instalační služby.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Přihlaste se ke službě Azure AD jako správce.
5. Prostředí PowerShell vyzve k zadání ID vašeho tenanta. Použijte identifikátor GUID ID adresáře, který jste zkopírovali z portálu Azure portal v části předpoklady.
6. PowerShell zobrazí zpráva o úspěšném provedení po dokončení skriptu.  

Opakujte tyto kroky na dalších serverech NPS, které chcete nastavit pro vyrovnávání zatížení.

> [!NOTE]
> Pokud používáte vlastní certifikáty místo aby generovala certifikáty pomocí skriptu prostředí PowerShell, ujistěte se, že jejich zarovnání bylo k serveru NPS zásady vytváření názvů. Název subjektu musí být **CN =\<TenantID\>, OU = rozšíření NPS Microsoft**. 

## <a name="configure-your-nps-extension"></a>Konfigurace rozšíření serveru NPS

Tato část obsahuje aspekty návrhu a návrhy pro úspěšné nasazení rozšíření serveru NPS.

### <a name="configuration-limitations"></a>Konfigurace omezení

- Rozšíření NPS pro Azure MFA nezahrnuje nástroje pro migraci uživatelů a nastavení z MFA serveru do cloudu. Z tohoto důvodu doporučujeme používat rozšíření pro nová nasazení, spíše než existující nasazení. Pokud používáte rozšíření na stávající nasazení, vaši uživatelé muset provést výš znovu k naplnění jejich podrobnosti MFA v cloudu.  
- Rozšíření NPS používá k identifikaci uživatele v Azure MFA pro provádění sekundární tyto hlavní název uživatele z místní služby Active directory Rozšíření lze nastavit na jiný identifikátor jako alternativního přihlašovacího ID nebo vlastní pole Active Directory než hlavní název uživatele. Další informace najdete v článku, [rozšířených možnostech konfigurace pro rozšíření NPS pro ověřování službou Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Ne všechny protokoly šifrování podporují všechny metody ověřování.
   - **PAP** podporuje telefonního hovoru, jednosměrná textová zpráva, oznámení mobilní aplikace a ověřovací kód z mobilní aplikace
   - **CHAPV2** a **EAP** podporují telefonních hovorů a oznámení mobilní aplikace

### <a name="control-radius-clients-that-require-mfa"></a>Ovládací prvek klientů RADIUS, které vyžadují vícefaktorové ověřování

Jakmile povolíte MFA pro klienta RADIUS pomocí rozšíření serveru NPS, veškeré ověřování pro tohoto klienta jsou potřebná k provedení vícefaktorové ověřování. Pokud chcete povolit MFA pro některé klienty RADIUS a jiné ne, můžete nakonfigurovat dva servery NPS a nainstalujte rozšíření na pouze jeden z nich. Konfigurace klientů RADIUS, které chcete vyžadovat vícefaktorové ověřování k odesílání požadavků na server NPS, který je nakonfigurovaný s příponou a dalších klientů RADIUS serveru NPS není nakonfigurováno s příponou.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Příprava pro uživatele, která nejsou zaregistrovaná pro vícefaktorové ověřování

Pokud máte uživatele, která nejsou zaregistrovaná pro vícefaktorové ověřování, můžete určit, co se stane při pokusu o ověření. Pomocí nastavení registru *REQUIRE_USER_MATCH* v cestě registru *HKLM\Software\Microsoft\AzureMFA* můžete řídit chování funkce. Toto nastavení nemá možnost jediné konfiguraci:

| Klíč | Hodnota | Výchozí |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE NEBO FALSE | Není nastavený (ekvivalentní na hodnotu TRUE) |

Účelem tohoto nastavení je určit, co dělat, když uživatel není zaregistrovaný pro MFA. Pokud klíč neexistuje, není nastavena nebo je nastavena na hodnotu TRUE a uživatel není zaregistrovaný, pak rozšíření selže ověřovacím testem MFA. Když uživatel není zaregistrovaný klíč je nastaven na hodnotu FALSE, ověření pokračuje bez provedení vícefaktorové ověřování. Pokud je určitý uživatel zapsaná v MFA, se musí ověřit pomocí vícefaktorového ověřování i v případě, že REQUIRE_USER_MATCH je nastavena na hodnotu FALSE.

Můžete k vytvoření tohoto klíče a nastavte na hodnotu FALSE, při připojování jsou vaši uživatelé a všechny je možné zaregistrovat pro Azure MFA ještě. Ale protože klíče umožňuje uživatelům, která nejsou zaregistrovaná pro vícefaktorové ověřování pro přihlášení, byste měli odstranit tento klíč před přechodem do produkčního prostředí.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak můžu ověřit, že klientský certifikát je nainstalovaný podle očekávání?

Vyhledejte certifikát podepsaný svým držitelem vytvořený instalační program do úložiště certifikátů a zkontrolujte, že privátní klíč má oprávnění udělená uživateli **síťová služba**. Certifikát má název předmětu **CN \<tenantid\>, OU = rozšíření NPS společnosti Microsoft**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Jak můžu ověřit, že moje klientský certifikát je přidružená k mému tenantovi v Azure Active Directory?

Otevřete příkazový řádek Powershellu a spusťte následující příkazy:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Tyto příkazy vytiskne všechny certifikáty přidružení tenanta s vaší instancí rozšíření NPS v relaci Powershellu. Vyhledejte certifikát tak, že vyexportujete klientský certifikát jako soubor "x.509, kódování Base-64" bez soukromého klíče a porovná ho s seznamu z Powershellu.

Platné – a – dokud časová razítka, která jsou v čitelné formě, lze použít k filtrování zřejmé misfits Pokud příkaz vrátí více než jeden certifikát.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Proč se Moje žádosti nedaří s Chyba tokenu ADAL?

Tato chyba mohla být způsobené jedním z několika důvodů. Tyto kroky použijte k řešení potíží:

1. Restartujte NPS server.
2. Ověřte, že tento klientský certifikát je nainstalovaný podle očekávání.
3. Ověřte, zda je certifikát přidružený vašeho tenanta v Azure AD.
4. Ověřte, že https://login.microsoftonline.com/ přístupný ze serveru se spuštěným rozšíření.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Proč ověřování selže s chybou v protokolech HTTP s informacemi o tom, že uživatel není nalezen?

Ověřte, že AD Connect je spuštěná a že uživatel je k dispozici ve Windows Active Directory a Azure Active Directory.

-------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Proč vidím HTTP chyby v protokolech, spojte se s Moje ověřování služeb při selhání?

Ověřte, že https://adnotifications.windowsazure.com dostupný ze serveru se spuštěným rozšíření NPS.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Správa protokolů TLS a SSL a šifrovacích sad

Doporučuje se, že starší nebo slabší šifrovací sady být vypnuto nebo odstraněno. Pokud vaše organizace vyžaduje. Informace o tom, jak to provést, najdete v článku [Správa protokolů SSL a TLS a šifrovacích sad pro AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

## <a name="next-steps"></a>Další postup

- Konfigurovat alternativní ID přihlášení nebo nastavit seznam výjimek pro IP adresy, která by neměla provést dvoustupňové ověřování v [rozšířených možnostech konfigurace pro rozšíření NPS pro ověřování službou Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Zjistěte, jak integrovat [Brána vzdálené plochy](howto-mfa-nps-extension-rdg.md) a [servery VPN jiných](howto-mfa-nps-extension-vpn.md) pomocí rozšíření NPS

- [řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
