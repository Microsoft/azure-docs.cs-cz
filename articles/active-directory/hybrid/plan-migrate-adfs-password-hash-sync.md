---
title: 'Azure AD Connect: Migrace z federace na synchronizaci hodnot hash hesel pro službu Azure AD | Dokumentace Microsoftu'
description: Informace o hybridní identity prostředí převádíte z federace na synchronizaci hodnot hash hesel.
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
ms.openlocfilehash: cad368cb968b94d1327cc99ed4dfa6df0aedd2cd
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555094"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Migrace z federace na synchronizaci hodnot hash hesel pro službu Azure AD
Následující dokument obsahuje pokyny k přechod ze služby AD FS na synchronizaci hodnot hash hesel.

>[!NOTE]
>Ke stažení kopii tohoto dokumentu je k dispozici [tady](https://aka.ms/ADFSTOPHSDPDownload).


## <a name="prerequisites-for-the-migration"></a>Požadavky na migraci 
Následující závislosti jsou požadovány, než bude možné migrovat.
### <a name="update-azure-ad-connect"></a>Aktualizace služby Azure AD Connect

Jako minimum úspěšně provést kroky k migraci na předávací ověřování, měli byste mít [služby Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Tato verze obsahuje významné změny způsobu, jakým se provádí převod přihlášení a snižuje celkový čas na migraci z federace na cloudové ověřování z potenciálně hodin, minut.

> [!IMPORTANT]
> Zastaralé dokumentaci, nástroje a blogy znamenat, že převod uživatelů o požadovaný krok při převodu domény federativní na spravované. Všimněte si, že převod uživatele není potřeba zobrazovat a společnost Microsoft pracuje na aktualizaci dokumentace a nástroje pro tyto změny projeví.

Aktualizace služby Azure AD Connect na nejnovější verzi této [aktualizace pokynů](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Oprávnění požadována synchronizace hodnot hash hesel

Azure AD Connect můžete konfigurovat pomocí nastavení Express nebo vlastní instalace. Pokud jste použili možnost Vlastní instalace [požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) pro synchronizaci hodnot Hash hesel nemusí být na místě.

Vyžaduje účet následující oprávnění, aby mohli k synchronizaci hodnot hash hesel služby Azure AD Connect AD DS.

* Replikace změn adresáře

* Replikace adresáře se změní všechny

Teď je vhodná doba k ověření těchto oprávnění jsou nastavené pro všechny domény v doménové struktuře.

### <a name="plan-migration-method"></a>Plánování migrace – metoda

Existují dvě metody k migraci z federovaného ověřování na synchronizaci hodnot Hash hesel a bezproblémového jednotného přihlašování. Metodu, kterou použijete, bude záviset na původně konfiguraci služby AD FS. 



- **Možnost A: Používání služby Azure AD Connect**. Služby AD FS se původně nakonfigurovalo pomocí služby Azure AD Connect, musíte změnit na synchronizaci hodnot Hash hesel jako metodu přihlašování uživatele provádí prostřednictvím Průvodce Azure AD Connect.   
Při použití služby Azure AD Connect, spuštění rutiny Set-MsolDomainAuthentication za vás automaticky při změnit metodu přihlašování uživatele, a proto nemají žádnou kontrolu nad ním unfederating všechny ověřené federovaných domén ve vašem tenantovi Azure AD.

> [!NOTE]
> V tuto chvíli nelze vyhnout unfederating všechny domény ve vašem tenantovi, když změníte přihlášení uživatele na synchronizaci hodnot Hash hesel při AAD Connect byl původně použit ke konfiguraci služby AD FS za vás.  



- **Možnost B: Používání služby Azure AD Connect pomocí Powershellu**. Tato metoda může použít jenom v případě, že služba AD FS nebyl původně nakonfigurovaný službou Azure AD Connect. Nevyhnete se změna uživatele přihlášení metody prostřednictvím Průvodce Azure AD Connect, ale základní rozdíl je, že ji nebude automaticky rutinu Set-MsolDomainAuthentication pro vás nemá žádné povědomí o farmu služby AD FS, a proto máte plnou kontrolu nad které domény budou převedeny a v jakém pořadí.

Pokud chcete pochopit, jakou metodu použijete, proveďte kroky v následující části.

#### <a name="verify-current-user-sign-in-settings"></a>Ověřte nastavení aktuálního uživatele přihlášení

Ověřte vaše nastavení aktuálního uživatele přihlásit po přihlášení na portálu Azure AD [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) pomocí účtu globálního správce.

V části uživatel přihlásit ověřte, že federace je povolené a zakázané bezproblémové jednotné přihlašování a předávací ověřování. Dál ověřte tento stav synchronizace hesla, které by měly zobrazovat jako zakázaný, pokud to dříve bylo zapnuto.

![Obrázek 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Ověřte konfiguraci služby Azure AD Connect

   1. Přejděte k vašemu serveru služby Azure AD Connect a otevřete Azure AD Connect a pak vyberete konfigurovat. 
   2. Na obrazovce další úlohy vyberte Zobrazit aktuální konfiguraci a klepněte na tlačítko Další.</br>
   ![Obrázek 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. Na obrazovce Kontrola řešení poznamenejte si stav synchronizace hesel.</br> 

   Pokud se synchronizace hodnot Hash hesel je aktuálně nastavený na zakázáno, je potřeba postupujte podle kroků v tomto průvodci, aby je. Pokud synchronizaci hodnot Hash hesel je aktuálně nastaven na povoleno, můžete bezpečně přeskočit část [krok 1 – povolení synchronizace hodnot Hash hesel](#step-1--enable-password-hash-synchronization) v této příručce.
   4. Na obrazovce Kontrola řešení posuňte se dolů Active Directory Federation Services (AD FS).</br>
 
   Pokud se zobrazí, že konfigurace služby AD FS je v této části a potom můžete bezpečně předpokládat služby AD FS se původně nakonfigurovalo prostřednictvím služby Azure AD Connect a proto převod vaší domény ze Federovaná do služby managed mohou být řízeny prostřednictvím Azure AD Connect "Změna uživatele přihlášení – v "možnost, tento proces je podrobně popsán v části **možnost A – přepínače z federace na synchronizaci hodnot Hash hesel pomocí služby Azure AD Connect**.
   5. Pokud nevidíte Active Directory Federation Services uvedené v aktuální nastavení, pak budete muset ručně převést domény ze federovanou se spravuje přes PowerShell, který je podrobněji popsán v části **možnost B - přepnutí z federace Synchronizace hodnot Hash hesel pomocí služby Azure AD Connect a prostředí PowerShell**.

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
Ověření nastavení, která může přizpůsobit dokumentaci návrhu a nasazení vaší federační konkrétně PreferredAuthenticationProtocol SupportsMfa a PromptLoginBehavior.

Další informace o co tato nastavení najdete níže.

[Active Directory Federation Services výzvu = Podpora parametrů přihlášení](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Pokud SupportsMfa hodnota aktuálně nastavená na hodnotu "True" pak to znamená, že používáte MFA On-Premises řešení 2. faktor challenge vložení do tok ověřování uživatele. To nebude fungovat pro scénáře ověřování služby Azure AD a místo toho budete muset využít služby Azure MFA (cloudové) se provede stejnou funkci. Pečlivě vyhodnotit požadavky na vícefaktorové ověřování před v budoucnu a ujistěte se, že chápete, jak využít Azure MFA, dopadům na licencování a proces registrace koncového uživatele před převodem domén. Naše Průvodce nasazením pro Azure MFA, který obsahuje další podrobnosti najdete tady [ https://aka.ms/deploymentplans ](https://aka.ms/deploymentplans).

#### <a name="backup-federation-settings"></a>Nastavení zálohování federace

I když nebudou provedeny žádné změny k jiné předávající strany ve vaší farmě služby AD FS během tohoto procesu, se doporučuje Ujistěte se, že máte aktuální platné zálohy farmy služby AD FS, který může být obnovena. Můžete provést pomocí bezplatné Microsoft [AD FS rychlé obnovení nástroj](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Tento nástroj můžete použít k zálohování a obnovení služby AD FS, buď do existující farmy nebo novou farmu.

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
| Se blokování klientů starší verze ověřování pomocí služby AD FS.| Zvažte nahrazení ovládací prvky pro blokování starší verze ověřování klientů, které jsou aktuálně k dispozici ve službě AD FS s kombinací [řídí podmíněného přístupu pro starší verze ověřování](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) a [Exchange Online Client Access Pravidla](http://aka.ms/EXOCAR).|
| Vyžadujete, aby uživatelům s místním MFA server řešením provádíte MFA při ověřování se službou AD FS.| Nebudete mít vložení výzvu MFA prostřednictvím místní řešení vícefaktorového ověřování do tok ověření pro spravované domény, ale může využívat služby Azure MFA k tomu od nynějška jednou domény je převeden. Pokud uživatelé nepoužívají vícefaktorové ověřování Azure ještě dnes, to bude zahrnovat registračního kroku jednorázové koncového uživatele, který budete muset připravit a komunikovat se svým koncovým uživatelům.|
| Zásady řízení přístupu (pravidel AuthZ) už dnes používáte ve službě AD FS pro řízení přístupu k Office 365.| Zvažte nahrazení těchto prvků s ekvivalentní Azure AD [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) a [Exchange Online pravidla klientského přístupu](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Důležité informace týkající se běžných vlastní nastavení služby AD FS

#### <a name="inside-corporate-network-claim"></a>Deklarace identity uvnitř podnikové sítě

Deklarace identity InsideCorporateNetwork vystaví služba AD FS při ověřování uživatele uvnitř podnikové sítě. Tato deklarace identity můžete potom předány do služby Azure AD a umožňuje obejít ověřování Multi-Factor Authentication podle umístění v síti uživatelů. Zobrazit [důvěryhodné IP adresy pro federované uživatele](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) informace o tom, jak zjistit, jestli je to aktuálně povolené ve službě AD FS.

Deklarace identity InsideCorporateNetwork nebude k dispozici už po domén se převedou na synchronizaci hodnot Hash hesel. [Pojmenovaná umístění ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) je možné nahradit tuto funkci.

Jakmile jsou nakonfigurovaná umístění s názvem, všechny zásady podmíněného přístupu nakonfigurované pro zahrnutí nebo vyloučení umístění v síti "Všechna důvěryhodná umístění" nebo "Důvěryhodné IP adresy MFA" musí být aktualizovány tak, aby odrážely nově vytvořený umístění s názvem.

Zobrazit [Active Directory podmíněný přístup k umístění](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) Další informace o umístění podmínka podmíněného přístupu.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojeno k Azure AD

Připojení zařízení k Azure AD umožňuje vytvořit pravidla podmíněného přístupu, které vynucují zařízení splňují vaše standardy zabezpečení a dodržování předpisů přístup a umožňuje uživatelům přihlásit se k zařízení pomocí organizační pracovního nebo školního účtu namísto osobní účet. Zařízení připojená k hybridní službě Azure AD umožňuje připojovat zařízení připojených k doméně AD do Azure AD. Federovaném prostředí může mít nakonfigurovaná pomocí této funkce.

K zajištění, že hybridní připojení pokračuje v práci pro všechna nová zařízení připojené k doméně, jakmile domén byly převedeny na synchronizaci hodnot Hash hesel, je třeba nastavit Azure AD Connect pro synchronizaci účtů počítače služby Active Directory pro klienty s Windows 10 na Azure AD. Pro účty počítačů Windows 7 a Windows 8 hybridní připojení se pomocí bezproblémového jednotného přihlašování k registraci počítače ve službě Azure AD a nemají synchronizovat je se stejně jako pro zařízení s Windows 10. Budete ale muset k nasazení souboru aktualizované workplacejoin.exe (prostřednictvím .msi) tyto klienty nižší úrovně, se můžete zaregistrovat pomocí bezproblémového jednotného přihlašování. [Stáhněte si soubor .msi](https://www.microsoft.com/download/details.aspx?id=53554). 

Další informace najdete v [jak nakonfigurovat hybridní služby Azure Active Directory zařízení připojená k](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Branding

Vaše organizace může mít [přizpůsobit přihlašovacích stránek služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) zobrazíte další relevantní informace k organizaci. Pokud ano, zvažte učinění podobné [přizpůsobení přihlašovací stránky Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Podobné úpravy jsou k dispozici, některé vizuální změny chování byste měli očekávat. Můžete chtít zahrnout očekávané změny komunikace pro koncové uživatele.

> [!NOTE]
> Firemní branding je dostupný jenom v případě, že zakoupili pro službu Azure AD Premium nebo licence Basic nebo máte licenci Office 365.

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

Po synchronizaci hodnot Hash hesel a bezproblémového jednotného přihlašování se nasazení, činnost koncového uživatele při přihlášení se změní při přístupu k Office 365 a jiné přidružené prostředky ověření prostřednictvím služby Azure AD. Pro síť externí uživatelé nyní uvidí Azure AD přihlašovací stránku, na rozdíl od se přesměrovává na stránce založené na formulářích předložený externí různé servery Proxy webových aplikací.

Existuje více elementů s plánováním strategie komunikace. Mezi ně patří:

* Upozornění uživatelů na nadcházející a vydávají se funkce prostřednictvím
  * E-mailu a dalších interní komunikační kanály
  * Vizuály, jako je například plakáty
  * Live nebo jiné komunikaci
* Určení, kdo přizpůsobovat a který bude odesílat komunikace a kdy.

## <a name="implementing-your-solution"></a>Implementace řešení

Teď, když jste naplánovali vaše řešení, jste připraveni na jeho implementaci. Implementace zahrnuje následující součásti:

1. Povolení synchronizace hodnot hash hesel

2. Příprava pro bezproblémové jednotné přihlašování na

3. Změna metody přihlašování synchronizaci hodnot Hash hesel a umožnění bezproblémového jednotného přihlašování

### <a name="step-1--enable-password-hash-synchronization"></a>Krok 1 – povolení synchronizace hodnot hash hesel

Prvním krokem při implementaci tohoto řešení je povolení synchronizace hodnot Hash hesel pro Průvodce Azure AD Connect. Synchronizace hodnot Hash hesel je volitelná funkce, která se dá nastavit pro prostředí využívající federaci se žádný vliv na tok ověřování. V tomto případě Azure AD Connect se spustí synchronizaci hodnot hash hesel, aniž by to ovlivnilo uživatele přihlášení pomocí federace.

Z tohoto důvodu doporučujeme provést tento krok jako přípravný úkol i před změnou domény přihlásit metodu. Tato položka vám poskytne dostatek času k ověření synchronizace hodnot Hash hesel pracuje správně.

Pokud chcete povolit synchronizaci hodnot Hash hesel:

   1. Na Azure AD Connect serveru spusťte průvodce a vyberte konfigurovat.
   2. Vyberte přizpůsobit možnosti synchronizace a potom klepněte na tlačítko Další.
   3. V okně připojení k Azure AD obrazovky poskytnout uživatelské jméno a heslo globálního správce.
   4. V okně připojení adresáře obrazovce klikněte na tlačítko Další.
   5. Domény a organizační jednotky filtrování obrazovce klikněte na tlačítko Další.
   6. Na obrazovce volitelných funkcí vyberte synchronizace hesel a klepněte na tlačítko Další.
   ![Obrázek 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Dále vyberte na všechny zbývající obrazovek a konfigurovat na poslední obrazovce.
   8. Azure AD Connect se spustí synchronizaci hodnot hash hesel při příští synchronizaci.

Po povolení synchronizace hodnot Hash hesel hash hesla pro všechny uživatele ve službě Azure AD Connect synchronizaci oboru zpracování, který se zapisují do služby Azure AD. V závislosti na počtu uživatelů tato operace může trvat od minut i několik hodin.

Pro účely plánování, by měl odhadnou, může zpracovat přibližně 20 000 uživatelů za 1 hodinu.

Ověření synchronizace hodnot Hash hesel správně funguje, pomocí úlohy řešení potíží v průvodce službou Azure AD Connect.

   1. Otevřete novou relaci prostředí Windows PowerShell na vašem serveru Azure AD Connect s spustit jako správce.
   2. Spusťte Set-ExecutionPolicy RemoteSigned nebo Set-ExecutionPolicy Unrestricted.
   3. Spusťte Průvodce Azure AD Connect.
   4. Přejděte na stránku pro další úlohy, vyberte Poradce při potížích a klikněte na tlačítko Další.
   5. Na stránce Poradce při potížích s kliknutím na tlačítko spustit Poradce při potížích nabídky start v prostředí PowerShell.
   6. V hlavní nabídce vyberte synchronizaci hodnot hash hesel pro řešení potíží.
   7. V nabídce sub vyberte heslo synchronizaci hodnot hash nebude vůbec fungovat.

Pokud narazíte na problémy, použijte informace na [v tomto článku](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) řešení.

### <a name="step-2--prepare-for-seamless-sso"></a>Krok 2 – příprava pro bezproblémové jednotné přihlašování

Do vašeho zařízení pomocí bezproblémového jednotného přihlašování budete muset přidat adresu URL Azure AD do nastavení zóny Intranet uživatele pomocí zásad skupiny ve službě Active Directory.

Ve výchozím nastavení prohlížeč automaticky vypočítá správné zóně, Internetu nebo intranetu, z konkrétní adresy URL. Například "http://contoso/"mapuje do zóny Intranet, zatímco"http://intranet.contoso.com/" mapuje na zóně Internet (vzhledem k tomu, že adresa URL obsahuje tečku). Prohlížeče nebude odesílat lístky protokolu Kerberos do koncového bodu cloudu, podobně jako adresu URL Azure AD, pokud explicitně přidat adresu URL do zóny intranetu prohlížeče.

Postupujte podle [kroků zavedení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) požadované změny pro vaše zařízení.

> [!IMPORTANT]
> Tato změna nebude změnit způsob, jakým uživatelé přihlášení ke službě Azure AD. Je ale důležité, že tato konfigurace platí pro všechna vaše zařízení předtím, než budete pokračovat kroku 3. Všimněte si také, že se uživatelům přihlášení na zařízení, která tuto konfiguraci neobdrželi jednoduše muset zadat uživatelské jméno a heslo pro přihlášení k Azure AD.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>Krok 3 – změnit metodu přihlašování do PHS a povolte bezproblémové jednotné přihlašování

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>Možnost A – Přepnutí z federace na PHS pomocí služby Azure AD Connect

Tuto metodu použijte, když služby AD FS byl zpočátku nakonfigurován pomocí služby Azure AD Connect. Tuto metodu nelze použít, pokud vaše služba AD FS nebyl původně nakonfigurované pomocí služby Azure AD Connect. První **změnit metodu přihlašování uživatele**

   1. V Azure AD Connect serveru spusťte průvodce.
   2. Vyberte Změnit uživatele přihlásit a pak klepněte na tlačítko Další.
   ![Obrázek 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. V **připojit ke službě Azure AD** obrazovky poskytnout uživatelské jméno a heslo **globálního správce**.
   4. V **přihlášení uživatele** obrazovky, změňte přepínač z federace se službou AD FS na předání synchronizaci hodnot Hash a nezapomeňte zaškrtnout políčko nepřevádět uživatelské účty, a to je zastaralé krok se odebere z budoucí verze aad Connect. Také vyberte Povolit jednotné přihlašování a pak vyberte **Další**.
   ![Obrázek 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Spouští se službou Azure AD Connect verze 1.1.880.0, je ve výchozím nastavení povolena na bezproblémové jednotné přihlašování – zaškrtávací políčko.
   
   > [!IMPORTANT]
   > Můžete ignorovat varování, která uživatel převodu a úplnou synchronizaci hodnoty hash jsou požadované kroky pro převod z federace na cloudové ověřování. Mějte prosím na paměti, že tyto kroky již nejsou vyžadovány, budoucí verze služby Azure AD Connect nebude mít možnost a převést uživatele. Pokud se stále zobrazuje tato upozornění, zkontrolujte, že používáte nejnovější verzi Azure AD Connect a, které používají nejnovější verzi této příručky. Další informace najdete v tématu [část aktualizace služby Azure AD Connect](#_Update_Azure_AD).
   
   5. Na obrazovce povolit jednotné přihlašování zadejte přihlašovací údaje účtu správce domény a pak vyberte další.
   ![Obrázek 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Přihlašovací údaje správce domény jsou požadovány pro povolení bezproblémové jednotné přihlašování, jako proces provede následující akce, které vyžadují tyto zvýšenou úroveň oprávnění. Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Budete používat jenom k povolení této funkce a potom je zrušen po úspěšném dokončení.
   >  * Účet počítače s názvem AZUREADSSOACC (která představuje Azure AD) se vytvoří ve vaší místní služby Active Directory (AD).
   >  * Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
   >  * Kromě toho jsou vytvořeny dva Kerberos hlavních názvů služby (SPN) představující dvě adresy URL, které se používají při přihlášení k Azure AD.
   >  * Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Budete používat jenom k povolení této funkce a potom je zrušen po úspěšném dokončení.
   
   6. V části připraveno k obrazovce konfigurace, ujistěte se, že "Proces počáteční synchronizace po dokončení konfigurace" zaškrtávací políčko zaškrtnuto. Vyberte položku konfigurace.
   ![Obrázek 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > V tomto okamžiku federovaných domén se změní na spravované ověřování, které bude nyní využívat synchronizaci hodnot Hash hesel jako metodu ověřování.
       
   7. Otevřít na portálu Azure AD, vyberte Azure Active Directory a potom vyberte Azure AD Connect.
   8. Ověřte, zda je při bezproblémové jednotné přihlašování zakázáno federační a jsou povolené synchronizace hesel.  
  ![Obrázek 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Přejděte na [testování a další kroky](#testing-and-next-steps).
   
   > [!IMPORTANT]
   > Přeskočit část možnost B - přepnutí z federace na synchronizaci hodnot Hash hesel pomocí služby Azure AD Connect a prostředí PowerShell je popsaný v této části se nevztahují.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>Možnost B - přepnutí z federace na PHS pomocí služby Azure AD Connect a prostředí PowerShell

Tuto možnost použijte, když federační nebyl původně nakonfigurován s použitím služby Azure AD Connect.

Jako součást tohoto procesu bude bezproblémové jednotné přihlašování a přepněte do spravované domény z federativní.

   1. V Azure AD Connect serveru spusťte průvodce.
   2. Vyberte Změnit uživatele přihlásit a pak klepněte na tlačítko Další. 
   3. V okně připojení k Azure AD obrazovky poskytnout uživatelské jméno a heslo globálního správce.
   4. Na obrazovce přihlášení uživatele, změnit přepínač z neprovádějte konfiguraci na synchronizaci hodnot Hash hesel, vyberte Povolit jednotné přihlašování vyberte další.
   
   Před provedením změny: ![Obrázek 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Po provedení změny:  
   ![Obrázek 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Spouští se službou Azure AD Connect verze 1.1.880.0, je ve výchozím nastavení povolena na bezproblémové jednotné přihlašování – zaškrtávací políčko.
   
   5. Na obrazovce povolit jednotné přihlašování zadejte přihlašovací údaje účtu správce domény a pak vyberte další.
   
   > [!NOTE]
   > Přihlašovací údaje správce domény jsou požadovány pro povolení bezproblémové jednotné přihlašování, jako proces provede následující akce, které vyžadují tyto zvýšenou úroveň oprávnění. Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Jste použili pouze k povolení této funkce a potom je zrušen po úspěšném dokončení.
   > * Účet počítače s názvem AZUREADSSOACC (která představuje Azure AD) se vytvoří ve vaší místní služby Active Directory (AD).
   > * Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
   > * Kromě toho jsou vytvořeny dva Kerberos hlavních názvů služby (SPN) představující dvě adresy URL, které se používají při přihlášení k Azure AD.
   
   6. V části připraveno k obrazovce konfigurace, ujistěte se, že "Proces počáteční synchronizace po dokončení konfigurace" zaškrtávací políčko zaškrtnuto. Vyberte položku konfigurace.
   ![Obrázek 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   Při výběru konfigurace bezproblémového jednotného přihlašování se nakonfigurují podle kroku verze Preview. Konfigurace synchronizace hodnot Hash hesel se nezmění, jak již bylo povoleno.
   
   > [!IMPORTANT]
   > Způsob, jakým se uživatelé registrují v tuto chvíli budou provedeny žádné změny.  
   
   7. Na portálu Azure AD ověřte, zda federace i nadále možné a nyní bezproblémové jednotné přihlašování je povolený.
   ![Obrázek 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Převést domény ze Federovaná do služby managed

V tomto okamžiku federace je stále aktivní a provozní domén. Pokud chcete pokračovat s nasazením, každou doménu je potřeba převést z federativní na spravované k vynucení ověřování uživatelů pomocí synchronizace hodnot Hash hesel.

> [!IMPORTANT]
> Všechny domény musí převést ve stejnou dobu, můžete se rozhodnout začít s testovací doménu vašeho produkčního tenanta nebo domény s nejmenší počet uživatelů, kteří.

Převod se provádí pomocí modulu Azure AD PowerShell.

   1. Otevřete okno Powershellu a přihlaste se k Azure AD pomocí účtu globálního správce.  
   2. Převést první doména, spusťte následující příkaz:  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. Otevřít na portálu Azure AD, vyberte Azure Active Directory a potom vyberte Azure AD Connect.
   4. Ověřte, že domény byl převeden na spravovaný spuštěním následujícího příkazu:
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Testování a další kroky

### <a name="test-authentication-with-phs"></a>Testovací ověření s PHS

Při vašeho tenanta se použití federace, uživatelé byly získávání přesměrován na přihlašovací stránku Azure AD do vašeho prostředí služby AD FS. Teď, když klient je nakonfigurován pro použití synchronizace hodnot Hash hesel místo federace, uživatelé nebudou získat přesměrováno do služby AD FS a místo toho budou přihlašovat přímo pomocí na přihlašovací stránku Azure AD.

Spusťte aplikaci Internet Explorer v režimu InPrivate vyhnout bezproblémového jednotného přihlašování, budete automaticky přihlášení a přejít na přihlašovací stránku Office 365 ([http://portal.office.com](http://portal.office.com/)). Zadejte uživatele (UPN) a klikněte na tlačítko Další. Ujistěte se, že zadejte hlavní název uživatele hybridní uživatele, který byl synchronizované z Active Directory vaše místní a který byl dříve federované. Uživateli se zobrazí na obrazovce k zadávání uživatelského jména a hesla.

![Obrázek 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Obrázek 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

Jakmile zadáte heslo, by měl získat přesměrován na portál Office 365.

![Obrázek 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Testování bezproblémové jednotné přihlašování

Přihlaste se k počítači připojeném k doméně, který je připojený k podnikové síti. Spusťte aplikaci Internet Explorer a přejděte do jednoho z následujících adres URL:  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (nahraďte Contoso s doménou).

Uživatele stručně přesměrováni na přihlašovací stránku Azure AD a zobrazí zpráva "Pokusu vás přihlásit" a nesmí se výzva k zadání uživatelského jména nebo hesla.

![Obrázek 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

Pak se získat uživatele přesměruje a úspěšně přihlásil na přístupovém panelu:

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

### <a name="troubleshooting"></a>Řešení potíží

Váš tým podpory by měl pochopit, jak řešení potíží, které vzniknou během nebo po provedení změny z federace na spravované ověřování. Váš tým podpory bližšímu seznámení s společných kroků řešení potíží a příslušné akce, které mohou pomoci izolovat a vyřešte problém pomocí následující dokumentace k řešení potíží.

[Řešení potíží s Azure Active Directory synchronizaci hodnot Hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Řešení potíží s Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Převrácení dešifrování bezproblémové jednotné přihlašování Kerberos

Je důležité si často nespotřebujete Kerberos dešifrovací klíč účtu AZUREADSSOACC počítače (který představuje Azure AD) vytvořené v místní doménové struktuře Active Directory. Důrazně doporučujeme, že ho znovu vygenerovat dešifrovací klíče Kerberos nejméně každých 30 dnů souladu s jak odeslat změny hesel, členy domény služby Active Directory. Protože není přidružené žádné zařízení připojené k objektu účtu počítače AZUREADSSOACC vrácení selhání je třeba provést ručně.

Proveďte tyto kroky na místním serveru, kde je spuštěn nástroj Azure AD Connect k zahájení výměny dešifrovací klíče Kerberos.

[Jak je možné vrátit přes protokol Kerberos dešifrovací klíč účtu počítače AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>Další postup

- [Koncepty návrhu Azure AD Connect](plan-connect-design-concepts.md)
- [Zvolte správné ověřování](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Podporované topologie](plan-connect-design-concepts.md)
