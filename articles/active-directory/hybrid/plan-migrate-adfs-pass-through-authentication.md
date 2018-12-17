---
title: 'Azure AD Connect: Migrace z federace na předávací ověřování pro službu Azure AD | Dokumentace Microsoftu'
description: Informace o hybridní identity prostředí převádíte z federace na předávací ověřování.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5361c8940c8c7dba5338a3f5a0ed18910f7e45a0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410325"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Migrace z federace na předávací ověřování pro službu Azure AD
Následující dokument obsahuje pokyny k přechod ze služby AD FS na předávací ověřování.

>[!NOTE]
>Ke stažení kopii tohoto dokumentu je k dispozici [tady](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-pass-through-authentication"></a>Požadavky na předávací ověřování.
Následující závislosti jsou požadovány, než bude možné migrovat.
### <a name="update-azure-ad-connect"></a>Aktualizace služby Azure AD Connect

Jako minimum úspěšně provést kroky k migraci na předávací ověřování, měli byste mít [služby Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Tato verze obsahuje významné změny způsobu, jakým se provádí převod přihlášení a snižuje celkový čas na migraci z federace na cloudové ověřování z potenciálně hodin, minut.

> [!IMPORTANT]
> Zastaralé dokumentaci, nástroje a blogy znamenat, že převod uživatelů o požadovaný krok při převodu domény federativní na spravované. **Převod uživatelů** je už není nutné a společnost Microsoft pracuje na aktualizaci dokumentace a nástroje pro tyto změny projeví.

Aktualizace služby Azure AD Connect na nejnovější verzi této [aktualizace pokynů](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Plán ověření agenta počet a umístění

Předávací ověřování provádí nasazení agentů nižšími nároky na Azure AD Connect serveru a na serverech Windows v místním prostředí. Nainstalujte agenty co nejblíže k řadičů domény služby Active Directory snížit latenci.

Pro většinu ověřování zákazníků, dva nebo tři agenti jsou dostatečné pro vysokou dostupnost a kapacitu a tenanta může mít více než 12 agenti zaregistrovaní. První agent je nainstalována vždy na vlastním serveru AAD Connect. Odkazovat [informace o síťové přenosy odhady a Průvodce výkonem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations) k pochopení omezení agenta a možností agenta nasazení.

### <a name="plan-migration-method"></a>Plánování migrace – metoda

Existují dvě metody k migraci z federovaného ověřování PTA a bezproblémového jednotného přihlašování. Metodu, kterou použijete, bude záviset na původně konfiguraci služby AD FS.

- **Používání služby Azure AD Connect**. Pokud službu AD FS byla původně nakonfigurován pomocí služby Azure AD Connect a pak změnu na předávací ověřování *musí* provést prostřednictvím Průvodce Azure AD Connect.

Při použití služby Azure AD Connect, spuštění rutiny Set-MsolDomainAuthentication za vás automaticky při změnit metodu přihlašování uživatele, a proto nemají žádnou kontrolu nad ním unfederating všechny ověřené federovaných domén ve vašem tenantovi Azure AD.  
‎  
> [!NOTE]
> V tuto chvíli není možné vyhnout se zrušení federating všechny domény ve vašem tenantovi, když změníte uživatele přihlášení na předávací ověřování při AAD Connect byl původně použit ke konfiguraci služby AD FS za vás.  
‎
- **Používání služby Azure AD Connect pomocí Powershellu**. Tato metoda může použít jenom v případě, že služba AD FS nebyl původně nakonfigurovaný službou Azure AD Connect. Nevyhnete se změna uživatele přihlášení metody prostřednictvím Průvodce Azure AD Connect, ale základní rozdíl je, že ji nebude automaticky rutinu Set-MsolDomainAuthentication pro vás nemá žádné povědomí o farmu služby AD FS, a proto máte plnou kontrolu nad které domény budou převedeny a v jakém pořadí.

Pokud chcete pochopit, jakou metodu použijete, proveďte kroky v následující části.

#### <a name="verify-current-user-sign-in-settings"></a>Ověřte nastavení aktuálního uživatele přihlášení

Ověřte vaše nastavení aktuálního uživatele přihlásit po přihlášení na portálu Azure AD [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) pomocí účtu globálního správce. 

V **uživatele přihlásit** části, ověřte, že **federace** je **povoleno** a že **bezproblémové jednotné přihlašování** a  **Předávací ověřování** jsou **zakázané**. 

![Obrázek 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Ověření konfigurace federace

   1. Přejděte na server Azure AD Connect a spuštění služby Azure AD Connect a pak vyberte **konfigurovat**.
   2. Na **další úkoly** obrazovky, vyberte **zobrazit aktuální konfiguraci** a pak vyberte **Další**.</br>
   ![Obrázek 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. V **Kontrola řešení** obrazovky, posuňte se dolů k **Active Directory Federation Services (AD FS)**.</br>
   Pokud se zobrazí, že konfigurace služby AD FS je v této části a potom můžete bezpečně předpokládat služby AD FS se původně nakonfigurovalo prostřednictvím služby Azure AD Connect a proto převod vaší domény ze Federovaná do služby managed mohou být řízeny prostřednictvím Azure AD Connect  **Změnit přihlášení uživatele** možnost, tento proces je podrobně popsán v části **možnost 1: Konfigurace předávacího ověřování s využitím Azure AD Connect**.  
‎
   4. Pokud nevidíte Active Directory Federation Services uvedené v aktuální nastavení, pak budete muset ručně převést domény ze federovanou se spravuje přes PowerShell, který je podrobně popsán v části **možnost 2 - přepnutí z federace na PTA pomocí služby Azure AD Connect a prostředí PowerShell**.

### <a name="document-current-federation-settings"></a>Aktuální nastavení federace dokumentu

Aktuální nastavení federace získáte spuštěním příkazu Get-MsolDomainFederationSettings rutiny.

Má příkaz tuto podobu:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Příklad:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


Ověření nastavení, která může mít byl přizpůsoben, aby dokumentaci návrhu a nasazení vaší federační konkrétně **PreferredAuthenticationProtocol**, **SupportsMfa**, a  **PromptLoginBehavior**.

Další informace o co tato nastavení najdete níže.

[Active Directory Federation Services výzvu = Podpora parametrů přihlášení](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Pokud SupportsMfa hodnota aktuálně nastavená na hodnotu "True" pak to znamená, že používáte MFA On-Premises řešení 2. faktor challenge vložení do tok ověřování uživatele. To nebude fungovat pro scénáře ověřování služby Azure AD a místo toho budete muset využít služby Azure MFA (cloudové) se provede stejnou funkci. Pečlivě vyhodnotit požadavky na vícefaktorové ověřování před v budoucnu a ujistěte se, že chápete, jak využít Azure MFA, dopadům na licencování a proces registrace koncového uživatele před převodem domén.

#### <a name="backup-federation-settings"></a>Nastavení zálohování federace

I když nebudou provedeny žádné změny k jiné předávající strany ve vaší farmě služby AD FS během tohoto procesu, se doporučuje Ujistěte se, že máte aktuální platné zálohy farmy služby AD FS, který může být obnovena. Můžete provést pomocí bezplatné Microsoft [AD FS rychlé obnovení nástroj](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Tento nástroj slouží k zálohování a obnovení služby AD FS, buď do existující farmy nebo novou farmu.

Pokud se rozhodnete nepoužívat nástroje Rychlé obnovení aplikace AD FS, pak minimálně je třeba exportovat "platforma Microsoft Office 365 Identity" předávající straně důvěryhodnosti a všechny přidružené vlastní pravidla deklarací identity, které jste možná přidali. Můžete to provést prostřednictvím následující příklad Powershellu

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Důležité informace o nasazení a využití služby AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Ověřit aktuální využití služby AD FS

Před převodem z federativní na spravované, byste se podívat úzce na způsobu jeho použití služby AD FS už dnes pro Azure AD nebo Office 365 a další aplikace (vztahy důvěryhodnosti předávající strany). Konkrétně byste měli zvážit v následující tabulce:

| If| Potom |
|-|-|
| Chystáte se zachovat služby AD FS pro tyto aplikace.| Budete používat AD FS a Azure AD a muset vzít v úvahu činnost koncového uživatele ve výsledku. Uživatelé mohou potřebovat k ověření dvakrát v některých scénářích, jakmile do služby Azure AD (kde dostanou jednotného přihlašování a vyšší do jiných aplikací, jako je Office 365) a opakujte pro všechny aplikace stále vázaná na službu AD FS jako vztah důvěryhodnosti předávající strany. |
| Služba AD FS je silně přizpůsobený a závislé na konkrétní vlastní nastavení v souboru onload.js, která nemůže být duplicitní ve službě Azure AD (například jste změnili přihlašovací prostředí tak, aby uživatelé pouze zadat formátu SamAccountName pro své uživatelské jméno, nikoli na UPN, nebo máte silně brandingem přihlašovací prostředí)| Je potřeba ověřit, že aktuální požadavků na přizpůsobení mohou být splněny Azure AD, než budete pokračovat. Naleznete v částech Branding pro AD FS a vlastního nastavení AD FS pro další informace a pokyny.|
| Se blokování klientů starší verze ověřování pomocí služby AD FS.| Zvažte nahrazení ovládací prvky pro blokování starší verze ověřování klientů, které jsou aktuálně k dispozici ve službě AD FS s kombinací [řídí podmíněného přístupu pro starší verze ověřování](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) a [Exchange Online Client Access Pravidla](http://aka.ms/EXOCAR). |
| Vyžadujete, aby uživatelům s místním MFA server řešením provádíte MFA při ověřování se službou AD FS.| Nebudete mít vložení výzvu MFA prostřednictvím místní řešení vícefaktorového ověřování do tok ověření pro spravované domény, ale může využívat služby Azure MFA k tomu od nynějška jednou domény je převeden. Pokud uživatelé nepoužívají vícefaktorové ověřování Azure ještě dnes, to bude zahrnovat registračního kroku jednorázově koncového uživatele, který budete muset připravit a komunikovat se svým koncovým uživatelům. |
| Zásady řízení přístupu (pravidel AuthZ) už dnes používáte ve službě AD FS pro řízení přístupu k Office 365.| Zvažte nahrazení těchto prvků s ekvivalentní Azure AD [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) a [Exchange Online pravidla klientského přístupu](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Důležité informace týkající se běžných vlastní nastavení služby AD FS

#### <a name="inside-corporate-network-claim"></a>Deklarace identity uvnitř podnikové sítě

Deklarace identity InsideCorporateNetwork vystaví služba AD FS při ověřování uživatele uvnitř podnikové sítě. Tato deklarace identity můžete potom předány do služby Azure AD a umožňuje obejít ověřování Multi-Factor Authentication podle umístění v síti uživatelů. Zobrazit [důvěryhodné IP adresy pro federované uživatele](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) informace o tom, jak zjistit, jestli je to aktuálně povolené ve službě AD FS.

Deklarace identity InsideCorporateNetwork nebude k dispozici už po domén se převedou na předávací ověřování. [Pojmenovaná umístění ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) je možné nahradit tuto funkci.

Jakmile jsou nakonfigurovaná umístění s názvem, všechny zásady podmíněného přístupu nakonfigurované pro zahrnutí nebo vyloučení umístění v síti "Všechna důvěryhodná umístění" nebo "Důvěryhodné IP adresy MFA" musí být aktualizovány tak, aby odrážely nově vytvořený umístění s názvem.

Zobrazit [Active Directory podmíněný přístup k umístění](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) Další informace o umístění podmínka podmíněného přístupu.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojeno k Azure AD

Připojení zařízení k Azure AD umožňuje vytvořit pravidla podmíněného přístupu, které vynucují zařízení splňují vaše standardy zabezpečení a dodržování předpisů přístup a umožňuje uživatelům umožní přihlásit k zařízení pomocí organizační pracovního nebo školního účtu namísto osobní účet. Zařízení připojená k hybridní službě Azure AD umožňuje připojovat zařízení připojených k doméně AD do Azure AD. Federovaném prostředí může mít nakonfigurovaná pomocí této funkce.

K zajištění, že hybridní připojení pokračuje v práci pro všechna nová zařízení připojené k doméně, jakmile domén byly převedeny na předávací ověřování, je třeba nastavit Azure AD Connect pro synchronizaci účtů počítače služby Active Directory pro klienty Windows 10 Azure AD. Pro účty počítačů Windows 7 a Windows 8 hybridní připojení se pomocí bezproblémového jednotného přihlašování k registraci počítače ve službě Azure AD a nemají synchronizovat je se stejně jako pro zařízení s Windows 10. Budete ale muset k nasazení souboru aktualizované workplacejoin.exe (prostřednictvím .msi) tyto klienty nižší úrovně, se můžete zaregistrovat pomocí bezproblémového jednotného přihlašování. [Stáhněte si soubor .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Další informace o tomto požadavku najdete v tématu [jak nakonfigurovat hybridní služby Azure Active Directory zařízení připojená k](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>Branding

Vaše organizace může mít [přizpůsobit přihlašovacích stránek služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) zobrazíte další relevantní informace k organizaci. Pokud ano, zvažte učinění podobné [přizpůsobení přihlašovací stránky Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Podobné úpravy jsou k dispozici, některé vizuální změny chování byste měli očekávat. Můžete chtít zahrnout očekávané změny komunikace pro koncové uživatele.

> [!NOTE]
> Firemní branding je dostupný jenom v případě, že zakoupili pro službu Azure AD Premium nebo licence Basic nebo máte licenci Office 365.

## <a name="planning-for-smart-lockout"></a>Plánování pro inteligentní uzamčení

Inteligentní uzamčení služby Azure AD chrání před útoky na hesla hrubou silou a zabraňuje uzamknutí, pokud se používá předávací ověřování a zásady skupiny uzamčení účtu je nastavena ve službě Active Directory účet místní služby Active Directory. 

Další informace o [funkci inteligentního uzamčení a jak upravit jeho konfiguraci](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="planning-deployment-and-support"></a>Plánování nasazení a podpora

### <a name="plan-the-maintenance-window"></a>Naplánovat časové období údržby

Samotný proces převodu domény je poměrně rychlé, Azure AD může i dál posílat požadavky na ověření na servery služby AD FS po dobu až do 4 hodin po dokončení převodu domény. Během tohoto časového období čtyř hodin a v závislosti na různých mezipaměti na straně služby, nemusí přijmout tyto ověření službou Azure AD a uživateli se zobrazí chyba, budou moct úspěšně ověřit proti stále služby AD FS, ale nebude přijímat Azure AD uživatele vydá token jako tento vztah důvěryhodnosti federace je odebraná.

> [!NOTE]
> Tímto krokem se pouze dopad na uživatele, kteří během tohoto časového intervalu pro převod příspěvek přístup ke službám prostřednictvím prohlížeče, dokud nevymažete mezipaměť na straně služby. Starší verze klientů (Exchange ActiveSync, aplikace Outlook 2010 nebo 2013) by neměla vliv, jako je Exchange Online udržuje mezipaměť přihlašovacích údajů pro určitou dobu, která se používá k ověření uživatele znovu tiše bez nutnosti se vrátíte do služby AD FS. Opakované ověření samotných tiše po to uložené v mezipaměti není zaškrtnuto, a proto uživatelé neměli přijímat případné výzvy heslo jako výsledek procesu převodu domény se používají přihlašovací údaje uložené v zařízení pro tyto klienty. Naopak pro moderní ověřování klientů (Office 2013 nebo 2016, IOS a Android) těchto použít platný aktualizovat Token k získání nových přístupových tokenů pro přístup k prostředkům místo přejít zpět do služby AD FS a je odolný vůči případné výzvy heslo jako Výsledek převodu domény zpracovat a bude i nadále fungovat bez jakékoli další konfigurace vyžaduje.
> [!IMPORTANT]
> Nemáte vypnout prostředí služby AD FS nebo odeberte vztah důvěryhodnosti předávající strany až si ověříte, že všichni uživatelé se úspěšně ověřují pomocí cloudového ověřování Office 365.

### <a name="plan-for-rollback"></a>Plán pro vrácení zpět

Pokud závažný problém se nachází a nelze ho přeložit rychle, že můžete se rozhodnout k vrácení zpět řešení federace. Je důležité mít plán, jak postupovat, pokud vaše nasazení není podle plánu. Pokud převod domény nebo uživatelé selže během nasazení nebo je potřeba vrátit zpět do federace, musíte znát zmírnit jakémkoli výpadku a dopad na vaše uživatele.

#### <a name="rolling-back"></a>Vrácení zpět

Podrobnosti o vašem konkrétní nasazení naleznete v dokumentaci návrhu a nasazení federace. Proces by měl zahrnovat:

* Převést spravované domény pro federované pomocí Convert MSOLDomainToFederated 

* V případě potřeby konfiguraci další pravidla deklarací identity.

### <a name="plan-change-communications"></a>Plánování komunikace změn

Důležitou součástí plánování nasazení a podporu se ujistíte, že se koncovým uživatelům proaktivně informováni o změny a co se může docházet, nebo musíte udělat. 

Po nasazení předávací ověřování a bezproblémové jednotné přihlašování, činnost koncového uživatele při přihlášení se změní při přístupu k Office 365 a jiné přidružené prostředky ověření prostřednictvím služby Azure AD. Pro síť externí uživatelé nyní uvidí Azure AD přihlášení pouze na stránku, na rozdíl od se přesměrovává na stránce založené na formulářích předložený externí různé servery Proxy webových aplikací.

Existuje více elementů s plánováním strategie komunikace. Mezi ně patří:

* Upozornění uživatelů na nadcházející a vydávají se funkce prostřednictvím
  * E-mailu a dalších interní komunikační kanály
  * Vizuály, jako je například plakáty
  * Live nebo jiné komunikaci
* Určení, kdo přizpůsobovat a který bude odesílat komunikace a kdy.

## <a name="implementing-your-solution"></a>Implementace řešení

Teď, když jste naplánovali vaše řešení, jste připraveni na jeho implementaci. Implementace zahrnuje následující součásti:

   1. Příprava pro bezproblémové jednotné přihlašování na
   2. Změna metody přihlašování předávací ověřování a povolením bezproblémového jednotného přihlašování

## <a name="step-1--prepare-for-seamless-sso"></a>Krok 1 – Příprava pro bezproblémové jednotné přihlašování

Do vašeho zařízení pomocí bezproblémového jednotného přihlašování budete muset přidat adresu URL Azure AD do nastavení zóny Intranet uživatele pomocí zásad skupiny ve službě Active Directory.

Ve výchozím nastavení prohlížeč automaticky vypočítá správné zóně, Internetu nebo intranetu, z konkrétní adresy URL. Například "http://contoso/"mapuje do zóny Intranet, zatímco"http://intranet.contoso.com/" mapuje na zóně Internet (vzhledem k tomu, že adresa URL obsahuje tečku). Prohlížeče nebude odesílat lístky protokolu Kerberos do koncového bodu cloudu, podobně jako adresu URL Azure AD, pokud explicitně přidat adresu URL do zóny intranetu prohlížeče.

Postupujte podle [kroků zavedení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) požadované změny pro vaše zařízení.

> [!IMPORTANT]
> Tato změna nebude změnit způsob, jakým uživatelé přihlášení ke službě Azure AD. Je ale důležité, že tato konfigurace platí pro všechna vaše zařízení předtím, než budete pokračovat kroku 3 Všimněte si také, jednoduše muset zadat uživatelské jméno a heslo pro přihlášení k Azure AD uživatelům přihlášení na zařízení, která neobdrželi tuto konfiguraci.

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>Krok 2 – změnit metodu přihlašování do PTA a povolte bezproblémové jednotné přihlašování

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>Možnost A: Konfigurace PTA s využitím Azure AD Connect

Tuto metodu použijte, když služby AD FS byl zpočátku nakonfigurován pomocí služby Azure AD Connect. Tuto metodu nelze použít, pokud vaše služba AD FS nebyl původně nakonfigurované pomocí služby Azure AD Connect.

> [!IMPORTANT]
> Mějte na paměti, že podle kroků níže všechny domény budou převedeny z federativní na spravované. Projděte si část plánu migrace metoda pro další informace.[](#_Plan_Migration_Method)

Nejprve budete muset změnit metodu přihlašování:

   1. V Azure AD Connect serveru spusťte průvodce.
   2. Vyberte **změnit přihlášení uživatele** a pak vyberte **Další**. 
   3. V **připojit ke službě Azure AD** obrazovky poskytnout uživatelské jméno a heslo globálního správce.
   4. **Přihlášení uživatele** obrazovky, změnit přepínač z **federace se službou AD FS** k **předávací ověřování**vyberte **povolit jednotné přihlašování**  vyberte **Další**.
   5. Na obrazovce povolit jednotné přihlašování zadejte přihlašovací údaje účtu správce domény a pak vyberte další.  

   > [!NOTE]
   > Přihlašovací údaje správce domény jsou požadovány pro povolení bezproblémové jednotné přihlašování, jako proces provede následující akce, které vyžadují tyto zvýšenou úroveň oprávnění. Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Budete používat jenom k povolení této funkce a potom je zrušen po úspěšném dokončení.
   >
   > * Účet počítače s názvem AZUREADSSOACC (která představuje Azure AD) se vytvoří ve vaší místní služby Active Directory (AD).
   > * Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
   > * Kromě toho jsou vytvořeny dva Kerberos hlavních názvů služby (SPN) představující dvě adresy URL, které se používají při přihlášení k Azure AD.
   > * Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Budete používat jenom k povolení této funkce a potom je zrušen po úspěšném dokončení.

   6. V **připravení konfigurovat** obrazovky, ujistěte se, že **proces synchronizace spustit po dokončení konfigurace** zaškrtávací políčko zaškrtnuto. Potom vyberte **konfigurovat**.</br>
   ![Obrázek](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. Otevřít **portálu Azure AD**vyberte **Azure Active Directory**a pak vyberte **Azure AD Connect**.
   8. Ověřte, že, který **federace je zakázaná** při **bezproblémové jednotné přihlašování na** a **Pass důkladné ověření** jsou **povoleno**.</br>
   ![Obrázek](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

Dále je nutné nasadit další metody ověřování. Otevřít **webu Azure portal**, přejděte do **Azure Active Directory, Azure AD Connect** a klikněte na tlačítko **předávací ověřování**.

Z **předávací ověřování** stránky, klikněte na tlačítko Stáhnout. Z **Stáhnout** agenta obrazovka, klikněte na **přijměte podmínky a stáhnout**.

Začne stahování agentů další ověřování. Nainstalujte sekundární ověřovací Agent na serveru připojeném k doméně. 

> [!NOTE]
> První agent je vždy nainstalován na serveru služby Azure AD Connect, samostatně jako součást změny konfigurace v části uživatel přihlásit nástroje Azure AD Connect. Žádné další agenty ověřování musí být nainstalován na samostatném serveru. Doporučujeme mít mezi 2 až 3 Další agentů ověřování k dispozici. 

Spusťte instalaci agenta ověřování. Během instalace, budete muset zadat přihlašovací údaje **globálního správce** účtu.

![Obrázek 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![Obrázek 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

Po instalaci agenta ověřování je můžete vrátit na stránku stavu předávací ověřování agenta a kontrolovat stav dalších agentů.


Přejdete na testování a další kroky.

> [!IMPORTANT]
> Přeskočte část možnost B: Přepnutí z federace PTA pomocí Azure AD Connect a prostředí PowerShell je popsaný v tom, že části se nevztahují.  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>Možnost B - přepnutí z federace na PTA pomocí služby Azure AD Connect a prostředí PowerShell

Tuto možnost použijte, když federační nebyl původně nakonfigurován s použitím služby Azure AD Connect. Nejprve musíte povolit předávací ověřování:

   1. V Azure AD Connect serveru spusťte průvodce.
   2. Vyberte **změnit přihlášení uživatele** a pak vyberte **Další**.
   3. V **připojit ke službě Azure AD** obrazovky poskytnout uživatelské jméno a heslo globálního správce.
   4. Na **přihlášení uživatele** obrazovky, změnit přepínač z **nekonfigurujte** k **předávací ověřování**vyberte **povolit jednotné přihlašování** vyberte **Další**.
   5. V **povolit Single Sign-on** obrazovky, zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   > [!NOTE]
   > Přihlašovací údaje správce domény jsou požadovány pro povolení bezproblémové jednotné přihlašování, jako proces provede následující akce, které vyžadují tyto zvýšenou úroveň oprávnění. Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Jste použili pouze k povolení této funkce a potom je zrušen po úspěšném dokončení.
   >
   > * Účet počítače s názvem AZUREADSSOACC (která představuje Azure AD) se vytvoří ve vaší místní služby Active Directory (AD).
   > * Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
   > * Kromě toho jsou vytvořeny dva Kerberos hlavních názvů služby (SPN) představující dvě adresy URL, které se používají při přihlášení k Azure AD.

   6. V **připravení konfigurovat** obrazovky, ujistěte se, že **proces synchronizace spustit po dokončení konfigurace** zaškrtávací políčko zaškrtnuto. Potom vyberte **konfigurovat**.</br>
   ‎![Obrázek](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   Při výběru konfigurace dojde k následujícím krokům:
   * První Agent předávací ověřování je nainstalována.
   * Je povoleno předávací funkcí
   * Bezproblémové jednotné přihlašování je povolený.  
   7. Ověřte, že **federace** je stále **povoleno** a **bezproblémové jednotné přihlašování** a **Pass důkladné ověření** jsou nyní k dispozici.
   ![Obrázek 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. Vyberte **předávací ověřování** a ověřte, zda je stav **aktivní**.</br>
   
   Pokud ověřovací Agent není aktivní, postupujte podle [tyto kroky pro řešení potíží](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) před pokračováním procesu domény konverzace v dalším kroku. Je tady riziko, způsobí výpadek ověřování při převodu domény před ověřuje, že jste agenty PTA byl úspěšně nainstalován a, jejich stav zobrazuje jako "Aktivní" na webu Azure Portal.  
   9. Vedle nasazení agentů další ověřování. Otevřít **webu Azure portal**, přejděte do **Azure Active Directory**, **Azure AD Connect** a klikněte na tlačítko **předávací ověřování**.
   10. Z **předávací ověřování** stránky, klikněte na **Stáhnout** tlačítko. Z **stáhnout agenta** obrazovce, klikněte na **přijměte podmínky a stáhnout**.
   
   Začne stahování agentů další ověřování. Nainstalujte sekundární ověřovací Agent na serveru připojeném k doméně.

  > [!NOTE]
  > První agent je vždy nainstalován na serveru služby Azure AD Connect, samostatně jako součást změny konfigurace v části uživatel přihlásit nástroje Azure AD Connect. Žádné další agenty ověřování musí být nainstalován na samostatném serveru. Doporučujeme mít mezi 2 až 3 Další agentů ověřování k dispozici.
  
   11. Spusťte instalaci agenta ověřování. Během instalace, budete muset zadat přihlašovací údaje **globálního správce** účtu.</br>
   ![Obrázek 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![Obrázek 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. Po instalaci agenta ověřování je můžete vrátit na stránku stavu předávací ověřování agenta a kontrolovat stav dalších agentů.

V tomto okamžiku federace je stále aktivní a provozní domén. Chcete-li pokračovat s nasazením, každou doménu potřeba převést z federativní na spravované tak předávací ověřování spustí obsluhuje žádosti o ověření domény.

Všechny domény musí převést ve stejnou dobu, můžete se rozhodnout začít s testovací doménu vašeho produkčního tenanta nebo domény s nejmenší počet uživatelů, kteří.

Převod se provádí pomocí modulu Azure AD PowerShell.

   1. Otevřít **PowerShell** a přihlaste se k Azure AD používat **globálního správce** účtu.
   2. Převést první doména, spusťte následující příkaz:
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. Otevřít **portálu Azure AD**vyberte **Azure Active Directory**a pak vyberte **Azure AD Connect**.  
   4. Po převedení federovaných domén, ověřte, že, který **federace je zakázaná** při **bezproblémové jednotné přihlašování** a **předávací ověřování** jsou **Povolené**.</br>
   ![Obrázek](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>Testování a další kroky

### <a name="test-pass-through-authentication"></a>Test předávací ověřování 

Při vašeho tenanta se použití federace, uživatelé byly získávání přesměrován na přihlašovací stránku Azure AD do vašeho prostředí služby AD FS. Teď, když klient je nakonfigurován pro použití předávací ověřování namísto federace, uživatelé nebudou získat přesměrováno do služby AD FS a místo toho budou přihlašovat přímo pomocí na přihlašovací stránku Azure AD.

Spusťte aplikaci Internet Explorer v režimu InPrivate vyhnout bezproblémového jednotného přihlašování, budete automaticky přihlášení a přejít na přihlašovací stránku Office 365 ([http://portal.office.com](http://portal.office.com/)). Typ **UPN** uživatele a klikněte na tlačítko **Další**. Ujistěte se, že zadejte hlavní název uživatele hybridní uživatele, který byl synchronizované z Active Directory vaše místní a který byl dříve federované. Uživateli se zobrazí na obrazovce k zadávání uživatelského jména a hesla.

![Obrázek 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![Obrázek 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

Jakmile zadáte heslo, by měl získat přesměrován na portál Office 365.

![Obrázek 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>Testování bezproblémové jednotné přihlašování

   1. Přihlaste se k doméně připojit počítač, který je připojený k podnikové síti. 
   2. Otevřít **aplikace Internet Explorer** nebo **Chrome** a přejít na jednu z následujících adres URL:   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (nahraďte Contoso s doménou).

      Uživatele stručně přesměrováni na přihlašovací stránce služby Azure AD a zobrazí zpráva "Pokusu vás přihlásit" a nesmí se výzva k zadání uživatelského jména nebo hesla.</br>
   ![Obrázek 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. Pak se získat uživatele přesměruje a úspěšně přihlásil na přístupovém panelu:

> [!NOTE]
> Bezproblémové jednotné přihlašování funguje na služby Office 365, které podporuje Nápovědu domény (například myapps.microsoft.com/contoso.com). Portál Office 365 (portal.office.com) aktuálně nepodporuje nápovědu domény a proto se očekává, že uživatelé budou muset zadejte jejich hlavní název uživatele. Jakmile UPN je zadané, bezproblémové jednotné přihlašování na můžete získat lístek protokolu Kerberos jménem uživatele a je do protokolu bez zadávání hesla.
> [!TIP]
> Zvažte nasazení [hybridní Azure AD Join ve Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) pro jednotné přihlašování – vylepšení.

### <a name="removal-of-the-relying-party-trust"></a>Odebrání vztah důvěryhodnosti předávající strany

Jakmile ověříte, že všichni uživatelé a klienti jsou úspěšně ověřování prostřednictvím Azure AD, ho lze považovat za bezpečného odebrání Office 365 vztah důvěryhodnosti předávající strany.

Pokud službu AD FS se nepoužívá pro jiné účely (jiné vztahy důvěryhodnosti předávající strany byly nakonfigurovány), je bezpečné nyní vyřadit z provozu služby AD FS.

### <a name="rollback"></a>Vrácení zpět

Pokud závažný problém se nachází a nelze ho přeložit rychle, že můžete se rozhodnout k vrácení zpět řešení federace.

Podrobnosti o vašem konkrétní nasazení naleznete v dokumentaci návrhu a nasazení federace. Proces by měl zahrnovat:

* Převést spravované domény pro federované pomocí Convert MSOLDomainToFederated
* V případě potřeby konfiguraci další pravidla deklarací identity.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>Povolit synchronizaci aktualizací userPrincipalName

V minulosti aktualizace pomocí služby sync z místní atribut UserPrincipalName je zablokovaný, pokud jsou splněny obě tyto podmínky:

* Jde o spravovaného uživatele (jiné než federované).
* Uživateli nebyla přiřazena licence.

Pokyny, jak ověřit nebo tuto funkci povolit najdete v následujícím článku:

[Synchronizace aktualizací userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Převrácení bezproblémové jednotné přihlašování Kerberos dešifrovací klíč

Je důležité si často nespotřebujete Kerberos dešifrovací klíč účtu AZUREADSSOACC počítače (který představuje Azure AD) vytvořené v místní doménové struktuře Active Directory. Důrazně doporučujeme, že ho znovu vygenerovat dešifrovací klíče Kerberos nejméně každých 30 dnů souladu s jak odeslat změny hesel, členy domény služby Active Directory. Protože není žádná přidružená zařízení připojené k objektu účtu počítače AZUREADSSOACC výměny je potřeba provést ručně.

Proveďte tyto kroky na místní server, kde je spuštěn nástroj Azure AD Connect k zahájení převrácení dešifrovací klíče Kerberos.

[Jak je možné vrátit přes protokol Kerberos dešifrovací klíč účtu počítače AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

Servery s agenty ověřování by monitorovat dostupnost řešení. Kromě čítače výkonu pro hlavní server zveřejnit agentů ověřování objekty výkonu, které lze použít k pochopení ověřování statistiky a chyby.

Agentů ověřování protokolovat operace do protokolů událostí Windows v části "Aplikace a služby Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin".

Řešení potíží s protokoly je možné povolit v případě potřeby.

Přečtěte si další informace o [monitorování a protokolování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Další postup

- [Koncepty návrhu Azure AD Connect](plan-connect-design-concepts.md)
- [Zvolte správné ověřování](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Podporované topologie](plan-connect-design-concepts.md)
