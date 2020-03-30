---
title: 'Azure AD Connect: Migrace z federace do PHS pro Azure AD | Dokumenty společnosti Microsoft'
description: Tento článek obsahuje informace o přesunutí prostředí hybridní identity z federace do synchronizace hash hesel.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b621c9cbc35d0e9956f6648d870102affd84c24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028393"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrace ze federace na synchronizaci hash hesel pro Službu Azure Active Directory

Tento článek popisuje, jak přesunout domény organizace ze služby AD FS (AD FS) do synchronizace hodnot hash hesel.

> [!NOTE]
> Změna metody ověřování vyžaduje plánování, testování a potenciálně prostoje. [Postupné zavádění](how-to-connect-staged-rollout.md) poskytuje alternativní způsob testování a postupné migraci z federace do cloudového ověřování pomocí synchronizace hodnot hash hesel.

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Předpoklady pro migraci na synchronizaci hodnot hash hesel

Následující předpoklady jsou nutné pro migraci z použití služby AD FS na synchronizaci hodnot hash hesel.

### <a name="update-azure-ad-connect"></a>Aktualizace služby Azure AD Connect

Minimálně úspěšně provést kroky k migraci na synchronizaci hodnot hash hesla, měli byste mít [Azure AD připojit](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Tato verze obsahuje významné změny ve způsobu provádění převodu přihlášení a zkracuje celkovou dobu migrace z federace do cloudového ověřování z potenciálně hodin na minuty.


> [!IMPORTANT]
> V zastaralé dokumentaci, nástrojích a blozích se může číst, že při převodu domén z federované identity na spravovanou identitu je vyžadován převod uživatelů. *Převod uživatelů* již není nutný. Společnost Microsoft pracuje na aktualizaci dokumentace a nástrojů, aby tuto změnu odrážela.

Chcete-li aktualizovat Azure AD Connect, proveďte kroky ve [službě Azure AD Connect: Upgrade na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Oprávnění vyžadována synchronizace hodnot hash hesel

Azure AD Connect můžete nakonfigurovat pomocí expresnínastavení nebo vlastní instalace. Pokud jste použili vlastní možnost instalace, [požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) pro synchronizaci hodnot hash hesel nemusí být na místě.

Účet služby Azure AD Connect Active Directory Domain Services (AD DS) vyžaduje následující oprávnění k synchronizaci hash hesel:

* Replikovat změny adresáře
* Replikovat všechny změny adresáře

Nyní je vhodná doba k ověření, že tato oprávnění jsou na místě pro všechny domény v doménové struktuře.

### <a name="plan-the-migration-method"></a>Plánování metody migrace

Můžete si vybrat ze dvou metod migrace z federované správy identit na synchronizaci hash hesel a bezproblémové jednotné přihlašování (SSO). Metoda, kterou použijete, závisí na tom, jak byla instance služby AD FS původně nakonfigurována.

* **Azure AD Connect**. Pokud jste původně nakonfigurovali službu AD FS pomocí služby Azure AD Connect, *musíte* změnit synchronizaci hash heslem pomocí průvodce Azure AD Connect.

   Azure AD Connect automaticky spustí rutinu **Set-MsolDomainAuthentication** při změně metody přihlášení uživatele. Azure AD Connect automaticky nenasytí všechny ověřené federované domény ve vašem tenantovi Azure AD.

   > [!NOTE]
   > V současné době pokud jste původně použili Azure AD Connect ke konfiguraci služby AD FS, nemůžete se vyhnout nefedrování všech domén ve vašem tenantovi při změně synchronizace nastavení hash hesla uživatelem. ‎
* **Azure AD Připojení s PowerShell .** Tuto metodu můžete použít pouze v případě, že jste původně nenakonfigurovali službu AD FS pomocí služby Azure AD Connect. Pro tuto možnost stále musíte změnit metodu přihlášení uživatele pomocí průvodce Azure AD Connect. Hlavní rozdíl s touto možností spoánět tak, že průvodce automaticky nespustí rutinu **Set-MsolDomainAuthentication.** S touto volbou máte plnou kontrolu nad tím, které domény jsou převedeny a v jakém pořadí.

Chcete-li pochopit, kterou metodu byste měli použít, proveďte kroky v následujících částech.

#### <a name="verify-current-user-sign-in-settings"></a>Ověření aktuálního nastavení přihlášení uživatele

Ověření aktuálního nastavení přihlášení uživatele:

1. Přihlaste se k [portálu Azure AD](https://aad.portal.azure.com/) pomocí účtu globálního správce.
2. V části **Přihlášení uživateleověřte** následující nastavení:
   * **Federace** je nastavena na **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastaveno na **zakázáno**.
   * **Předávací ověřování** je nastaveno na **zakázáno**.

   ![Snímek obrazovky s nastavením v části přihlášení uživatele služby Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Ověření konfigurace služby Azure AD Connect

1. Na serveru Azure AD Connect otevřete Azure AD Connect. Vyberte **Konfigurovat**.
2. Na stránce **Další úkoly** vyberte **Zobrazit aktuální konfiguraci**a pak vyberte **Další**.<br />

   ![Snímek obrazovky s možností Zobrazit aktuální konfiguraci vybranou na stránce Další úkoly](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na stránce **Zkontrolovat řešení** si poznamenejte stav **synchronizace hodnoty hash hesla.**<br /> 

   * Pokud je **synchronizace hodnot hash hesla** nastavena na **hodnotu Zakázáno**, povolte ji postupem uvedeným v tomto článku.
   * Pokud je **synchronizace hodnot hash hesla** **nastavena**na povoleno , můžete v tomto článku přeskočit **oddíl Krok 1: Povolit synchronizaci hodnot hash hesel.**
4. Na stránce **Kontrola řešení** přejděte na **službu AD FS (Active Directory Federation Services).**<br />

   * Pokud konfigurace služby AD FS se zobrazí v této části, můžete bezpečně předpokládat, že služba AD FS byla původně nakonfigurována pomocí služby Azure AD Connect. Můžete převést své domény z federované identity na spravovanou identitu pomocí **možnosti přihlášení uživatele** Azure AD Connect Change. Proces je podrobně popsán v části **Možnost A: Přepnutí ze federace na synchronizaci hash hesel pomocí Azure AD Connect**.
   * Pokud služba AD FS není uvedena v aktuálním nastavení, je nutné ručně převést domény z federované identity na spravovanou identitu pomocí prostředí PowerShell. Další informace o tomto procesu najdete v části **Možnost B: Přepnutí ze federace na synchronizaci hash hesel pomocí Azure AD Connect a PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentovat aktuální nastavení federace

Chcete-li najít aktuální nastavení federace, spusťte rutinu **Get-MsolDomainFederationSettings:**

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Příklad:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Ověřte všechna nastavení, která mohla být přizpůsobena pro dokumentaci k návrhu a nasazení federace. Konkrétně vyhledejte vlastní nastavení v **preferredauthenticationprotocol**, **SupportsMfa**a **PromptLoginBehavior**.

Další informace najdete v těchto článcích:

* [AD FS prompt=podpora parametrů přihlášení](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Pokud **SupportsMfa** je nastavena na **True**, používáte místní vícefaktorové ověřování řešení vstříknutí výzvu druhého faktoru do toku ověřování uživatelů. Toto nastavení již funguje pro scénáře ověřování Azure AD po převodu této domény z federované na spravované ověřování. Po zakázání federace, můžete přerušit vztah k místní federaci a to zahrnuje místní adaptéry MFA. 
>
> Místo toho použijte cloudovou službu Azure Multi-Factor Authentication k provedení stejné funkce. Pečlivě vyhodnoťte požadavky na vícefaktorové ověřování, než budete pokračovat. Než převedete domény, ujistěte se, že rozumíte tomu, jak používat azure multifaktorové ověřování, důsledky licencování a proces registrace uživatelů.

#### <a name="back-up-federation-settings"></a>Zálohování nastavení federace

Přestože během procesů popsaných v tomto článku nejsou provedeny žádné změny v jiných předávajících stranách ve farmě služby AD FS, doporučujeme mít aktuální platnou zálohu farmy služby AD FS, ze které můžete obnovit. Aktuální platnou zálohu můžete vytvořit pomocí bezplatného nástroje Microsoft [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Pomocí nástroje můžete zálohovat nástroj AD FS a obnovit existující farmu nebo vytvořit novou farmu.

Pokud se rozhodnete nástroj rychlého obnovení služby AD FS nepoužívat, měli byste exportovat důvěryhodnou důvěryhodnost platformy Microsoft Office 365 Identity Platform a všechna související vlastní pravidla deklarace identity, která jste přidali. Důvěryhodný certifikát předávající strany a přidružená pravidla deklarací zabezpečení můžete exportovat pomocí následujícího příkladu prostředí PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Důležité informace o nasazení a používání služby AD FS

Tato část popisuje aspekty nasazení a podrobnosti o používání služby AD FS.

### <a name="current-ad-fs-use"></a>Aktuální použití ad FS

Než převedete z federované identity na spravovanou identitu, podívejte se pozorně na to, jak aktuálně používáte službu AD FS pro Azure AD, Office 365 a další aplikace (vztahy důvěryhodnosti předávající strany). Konkrétně zvažte scénáře, které jsou popsány v následující tabulce:

| Pokud uživatel | Pak... |
|-|-|
| Chcete nadále používat službu AD FS s jinými aplikacemi (kromě Azure AD a Office 365). | Po převodu domén použijete službu AD FS i Azure AD. Zvažte uživatelské prostředí. V některých případech mohou být uživatelé vyžadováni k ověření dvakrát: jednou do Azure AD (kde uživatel získá přístup k sismu s přistupujícím službou SSO k jiným aplikacím, jako je Office 365), a znovu pro všechny aplikace, které jsou stále vázány na službu AD FS jako důvěryhodný certifikát předávající strany. |
| Vaše instance služby AD FS je silně přizpůsobená a spoléhá na konkrétní nastavení vlastního nastavení v souboru onload.js (například pokud jste změnili přihlašovací prostředí tak, aby uživatelé používali pouze formát **SamAccountName** pro své uživatelské jméno namísto hlavního uživatelského jména (UPN) nebo vaše organizace silně označila přihlašovací prostředí). Soubor onload.js nelze duplikovat ve službě Azure AD. | Než budete pokračovat, musíte ověřit, že Azure AD může splňovat vaše aktuální požadavky na přizpůsobení. Další informace a pokyny najdete v oddílech o značce aslužbě AD FS a přizpůsobení ad fs.|
| Službu AD FS slouží k blokování starších verzí ověřovacích klientů.| Zvažte nahrazení ovládacích prvků služby AD FS, které blokují starší verze ověřovacích klientů, pomocí kombinace [ovládacích prvků podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) a [pravidel klientského přístupu Exchange Online](https://aka.ms/EXOCAR). |
| Vyžadujete, aby uživatelé prováděli vícefaktorové ověřování proti místnímu řešení vícefaktorového ověřovacího serveru při ověřování uživatelů ve službě AD FS.| V doméně spravované identity nelze do toku ověřování vložit výzvu vícefaktorového ověřování prostřednictvím místního řešení vícefaktorového ověřování. Službu Azure Multi-Factor Authentication však můžete použít pro vícefaktorové ověřování po převodu domény.<br /><br /> Pokud vaši uživatelé aktuálně nepoužívají Azure Multi-Factor Authentication, je vyžadován jednorázový krok registrace uživatele. Musíte se připravit a sdělit plánovanou registraci svým uživatelům. |
| V současné době používáte zásady řízení přístupu (pravidla AuthZ) ve správě AD FS k řízení přístupu k Office 365.| Zvažte nahrazení zásad ekvivalentními [zásadami podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) Azure AD a [pravidly přístupu klienta Exchange Online](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Běžná vlastní nastavení ad fs

Tato část popisuje běžné vlastní nastavení ad FS.

#### <a name="insidecorporatenetwork-claim"></a>Deklarace uvnitř corporatenetwork

AD FS vydává **Deklarace InsideCorporateNetwork,** pokud uživatel, který je ověřování je uvnitř podnikové sítě. Tato deklarace pak může být předána do služby Azure AD. Deklarace se používá k obejití vícefaktorového ověřování na základě umístění v síti uživatele. Informace o tom, jak zjistit, zda je tato funkce aktuálně povolena ve službě AD FS, naleznete [v tématu Důvěryhodné IP adresy pro federované uživatele](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

Deklarace **InsideCorporateNetwork** není po převodu domén na synchronizaci hodnot hash hesel k dispozici. Můžete použít [pojmenované umístění ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) nahradit tuto funkci.

Po konfiguraci pojmenovaných umístění je nutné aktualizovat všechny zásady podmíněného přístupu, které byly nakonfigurovány tak, aby zahrnovaly nebo vyloučily síť **Všechny důvěryhodné umístění** nebo Důvěryhodné ip adresy **MFA,** aby odrážely nová pojmenovaná umístění.

Další informace o podmínce **Umístění** v podmíněném přístupu naleznete v [tématu Umístění podmíněného přístupu služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Když se připojíte k zařízení Azure AD, můžete vytvořit pravidla podmíněného přístupu, která vynucují, aby zařízení splňovala vaše standardy přístupu pro zabezpečení a dodržování předpisů. Uživatelé se také mohou přihlásit k zařízení pomocí organizačního pracovního nebo školního účtu namísto osobního účtu. Když používáte hybridní zařízení připojená k Azure AD, můžete se připojit k zařízením spojených s doménou Služby Active Directory do Azure AD. Federované prostředí mohlo být nastaveno tak, aby používalo tuto funkci.

Chcete-li zajistit, aby hybridní připojení fungovalo i po převodu domén na synchronizaci hodnot hash hesel pro hybridní připojení, je nutné k synchronizaci počítače služby Active Directory použít možnosti zařízení Azure AD Connect. účty do Azure AD. 

Pro účty počítačů se systémem Windows 8 a Windows 7 hybridní spojení používá bezproblémové jednotné přihlašovat k registraci počítače ve službě Azure AD. Nemusíte synchronizovat účty počítačů se systémem Windows 8 a Windows 7, jako to děláte pro zařízení s Windows 10. Je však nutné nasadit aktualizovaný soubor workplacejoin.exe (prostřednictvím souboru MSI) do klientů windows 8 a Windows 7, aby se mohli zaregistrovat pomocí bezproblémového jednotného přihlašování. [Stáhněte soubor MSI](https://www.microsoft.com/download/details.aspx?id=53554).

Další informace najdete [v tématu Konfigurace hybridních zařízení spojených s Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Branding

Pokud vaše organizace [přizpůsobila přihlašovací stránky služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) tak, aby zobrazovala informace, které jsou pro organizaci vhodnější, zvažte provedení [podobných úprav na přihlašovací stránce Služby Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Přestože jsou k dispozici podobné vlastní nastavení, některé vizuální změny na přihlašovacích stránkách by měly být očekávány po převodu. Uživatelům můžete poskytnout informace o očekávaných změnách v komunikaci.

> [!NOTE]
> Značka organizace je dostupná jenom v případě, že si zakoupíte licenci Premium nebo Basic pro Azure Active Directory nebo pokud máte licenci Office 365.

## <a name="plan-deployment-and-support"></a>Plánování nasazení a podpory

Dokončete úkoly popsané v této části, které vám pomohou naplánovat nasazení a podporu.

### <a name="plan-the-maintenance-window"></a>Plánování okna údržby

I když proces převodu domény je poměrně rychlý, Azure AD může pokračovat v odesílání některých požadavků na ověření na servery služby AD FS až čtyři hodiny po dokončení převodu domény. Během tohoto čtyřhodinového okna a v závislosti na různých mezipamětích na straně služby azure ad nemusí přijmout tato ověřování. Uživatelům se může zobrazit chyba. Uživatel může stále úspěšně ověřit proti AD FS, ale Azure AD již přijímá uživatele vydaný token, protože tento federační vztah důvěryhodnosti je nyní odebrána.

Ovlivněny jsou pouze uživatelé, kteří přistupují ke službám prostřednictvím webového prohlížeče během tohoto okna po převodu před vymazáním mezipaměti na straně služby. Starší klienti (Exchange ActiveSync, Outlook 2010/2013) se neočekávají ovlivněny, protože Exchange Online uchovává mezipaměť svých přihlašovacích údajů po stanovenou dobu. Mezipaměť se používá k tichému opětovnému ověření uživatele. Uživatel se nemusí vracet do ad FS. Pověření uložená v zařízení pro tyto klienty se používají k tichému opětovnému ověření sebe sama po vymazání této mezipaměti. Neočekává se, že uživatelé v důsledku procesu převodu domény obdrží žádné výzvy k zadání hesla. 

Moderní ověřovací klienti (Office 2016 a Office 2013, iOS a Android) používají platný obnovovací token k získání nových přístupových tokenů pro trvalý přístup k prostředkům namísto návratu do služby AD FS. Tito klienti jsou imunní vůči všem pokynům k zadání hesla vyplývajícím z procesu převodu domény. Klienti budou nadále fungovat bez další konfigurace.

> [!IMPORTANT]
> Nevypínejte prostředí služby AD FS ani neodstraňujte důvěryhodný certifikát předávající strany Office 365, dokud neověříte, že se všichni uživatelé mohou úspěšně ověřit pomocí cloudového ověřování.

### <a name="plan-for-rollback"></a>Plán vrácení zpět

Pokud narazíte na závažný problém, který nelze vyřešit rychle, můžete se rozhodnout vrátit zpět řešení federace. Je důležité naplánovat, co dělat, pokud vaše nasazení není zavést, jak bylo zamýšleno. Pokud se během nasazení nezdaří převod domény nebo uživatelů nebo pokud potřebujete vrátit zpět do federace, musíte pochopit, jak zmírnit výpadek a snížit dopad na uživatele.

#### <a name="to-roll-back"></a>Chcete-li vrátit zpět

Chcete-li naplánovat vrácení zpět, podívejte se do dokumentace k návrhu federace a nasazení pro podrobnosti o konkrétním nasazení. Proces by měl zahrnovat tyto úkoly:

* Převod spravovaných domén do federovaných domén pomocí rutiny **Convert-MSOLDomainToFederated.**
* V případě potřeby konfigurace dalších pravidel deklarací identity.

### <a name="plan-communications"></a>Plán komunikace

Důležitou součástí plánování nasazení a podpory je zajistit, aby vaši uživatelé byli aktivně informováni o nadcházejících změnách. Uživatelé by měli předem vědět, co by mohli zažít a co je od nich požadováno. 

Po nasazení synchronizace hodnot hash hesla a bezproblémového jednotného přihlašování se uživatelské prostředí pro přístup k Office 365 a dalším prostředkům, které jsou ověřené prostřednictvím změn Azure AD. Uživatelé, kteří jsou mimo síť zobrazit jenom přihlašovací stránku Azure AD. Tito uživatelé nejsou přesměrováni na stránku založenou na formulářích, která je prezentována servery proxy webových aplikací směřujících k externím.

Do komunikační strategie zahrňte následující prvky:

* Upozorněte uživatele na nadcházející a vydané funkce pomocí:
   * E-mail a další interní komunikační kanály.
   * Vizuály, například plakáty.
   * výkonná, živá nebo jiná komunikace.
* Určete, kdo bude přizpůsobovat komunikaci a kdo ji bude odesílat a kdy.

## <a name="implement-your-solution"></a>Implementace řešení

Naplánoval jsi své řešení. Nyní jej můžete implementovat. Implementace zahrnuje následující součásti:

* Povolení synchronizace hash hesel.
* Příprava na bezproblémové jednotné přihlašovat.
* Změna metody přihlášení na synchronizaci hodnot hash hesla a povolení bezproblémového jednotného přihlašování.

### <a name="step-1-enable-password-hash-synchronization"></a>Krok 1: Povolení synchronizace hodnot hash hesel

Prvním krokem k implementaci tohoto řešení je povolení synchronizace hodnot hash hesla pomocí průvodce Azure AD Connect. Synchronizace hodnot hash hesla je volitelná funkce, kterou můžete povolit v prostředích používajících federaci. Nemá to žádný vliv na tok ověřování. V takovém případě Azure AD Connect spustí synchronizaci hesel hashebez ovlivnění uživatelů, kteří se přihlašují pomocí federace.

Z tohoto důvodu doporučujeme dokončit tento krok jako úlohu přípravy v hodně před změnou metody přihlášení dodomény. Potom budete mít dostatek času na ověření, že synchronizace hodnot hash hesla funguje správně.

Povolení synchronizace hodnot hash hesel:

1. Na serveru Azure AD Connect otevřete Průvodce připojením Azure AD a pak vyberte **Konfigurovat**.
2. Vyberte **Možnost Přizpůsobit možnosti synchronizace**a pak vyberte **Další**.
3. Na stránce **Připojit k Azure AD** zadejte uživatelské jméno a heslo účtu globálního správce.
4. Na stránce **Připojit adresáře** vyberte **Další**.
5. Na stránce **Filtrování domény a ou** vyberte **Další**.
6. Na stránce **Volitelné funkce** vyberte **Synchronizace hesel**a pak vyberte **Další**.
 
   ![Snímek obrazovky s možností synchronizace hesla vybranou na stránce Volitelné funkce](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Na zbývajících stránkách vyberte **Další.** Na poslední stránce vyberte **Konfigurovat**.
8. Azure AD Connect spustí synchronizaci hodnot hashe hesel při další synchronizaci.

Po povolení synchronizace hodnot hash hesla se hashy hesel pro všechny uživatele v oboru synchronizace Azure AD Connect přemísťují a zapisují do služby Azure AD. V závislosti na počtu uživatelů může tato operace trvat několik minut nebo několik hodin.

Pro účely plánování byste měli odhadnout, že přibližně 20 000 uživatelů je zpracováno za 1 hodinu.

Chcete-li ověřit, zda synchronizace hodnot hash hesla funguje správně, **dokončete** úlohu řešení potíží v průvodci připojením Azure AD Connect:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect pomocí možnosti Spustit jako správce.
2. Spustit `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`nebo .
3. Spusťte Průvodce připojením Azure AD Connect.
4. Přejděte na stránku **Další úkoly,** vyberte **Poradce při potížích**a pak vyberte **Další**.
5. Na stránce **Poradce při potížích** vyberte **Spustit** a spusťte nabídku řešení potíží v PowerShellu.
6. V hlavní nabídce vyberte **Poradce při potížích s synchronizací hash hesel**.
7. V podnabídce vyberte **Synchronizace hodnot hash hesla vůbec nefunguje**.

Problémy s řešením potíží najdete [v tématu Poradce při potížích se synchronizací hash hesel pomocí synchronizace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>Krok 2: Příprava na bezproblémové jednotné přihlašovat

Aby vaše zařízení mohli používat bezproblémové jednotné přihlašování, musíte přidat adresu URL Azure AD do nastavení zóny intranetu uživatelů pomocí zásad skupiny ve službě Active Directory.

Ve výchozím nastavení webové prohlížeče automaticky vypočítávají správnou zónu, internet nebo intranet, z adresy URL. Například **http:\/\/contoso/** mapuje na intranetovou zónu a **\/\/http: intranet.contoso.com** mapuje na internetovou zónu (protože adresa URL obsahuje tečku). Prohlížeče odesílají lístky protokolu Kerberos do koncového bodu cloudu, jako je adresa URL Azure AD, pouze v případě, že explicitně přidáte adresu URL do zóny intranetu prohlížeče.

Proveďte kroky k [zavedení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) požadovaných změn v zařízeních.

> [!IMPORTANT]
> Provedení této změny nezmění způsob, jakým se uživatelé přihlašují k Azure AD. Je však důležité, abyste tuto konfiguraci před pokračováním použili na všechna zařízení. Uživatelé, kteří se přihlašují na zařízeních, která neobdrželi tuto konfiguraci, jednoduše musí zadat uživatelské jméno a heslo pro přihlášení do Služby Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Krok 3: Změna metody přihlášení na synchronizaci hodnot hash hesel a povolení bezproblémového jednotného přihlašování

Máte dvě možnosti pro změnu metody přihlášení na synchronizaci hodnot hash hesla a povolení bezproblémového jednotného přihlašování.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Možnost A: Přechod z federace na synchronizaci hash hesel pomocí Azure AD Connect

Tuto metodu použijte, pokud jste původně nakonfigurovali prostředí služby AD FS pomocí služby Azure AD Connect. Tuto metodu nelze použít, *didn't* pokud jste původně nenakonfigurovali prostředí služby AD FS pomocí služby Azure AD Connect.

Nejprve změňte metodu přihlášení:

1. Na serveru Azure AD Connect otevřete Průvodce azure ad connect.
2. Vyberte **Změnit přihlášení uživatele**a pak vyberte **Další**. 

   ![Snímek obrazovky s možností Změnit přihlášení uživatele na stránce Další úkoly](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na stránce **Připojit k Azure AD** zadejte uživatelské jméno a heslo účtu globálního správce.
4. Na přihlašovací stránce **uživatele** vyberte **tlačítko synchronizace hodnot hash hesla**. Nezapomeňte zaškrtnout políčko **Nepřevádět uživatelské účty.** Možnost je zastaralá. Vyberte **Povolit jednotné přihlašování**a pak vyberte **Další**.

   ![Snímek obrazovky se stránkou Povolit jednotné přihlašování](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Počínaje Azure AD Connect verze 1.1.880.0, **bezešvé jednotné přihlašování** je zaškrtnuté ve výchozím nastavení.

   > [!IMPORTANT]
   > Můžete bezpečně ignorovat upozornění, která označují, že převod uživatele a úplné synchronizace hodnot hash hesel jsou povinné kroky pro převod z federace na cloudové ověřování. Všimněte si, že tyto kroky již nejsou vyžadovány. Pokud se tato upozornění stále zobrazují, ujistěte se, že používáte nejnovější verzi Služby Azure AD Connect a že používáte nejnovější verzi této příručky. Další informace naleznete v části [Aktualizace služby Azure AD Connect](#update-azure-ad-connect).

5. Na stránce **Povolit jednotné přihlašování** zadejte přihlašovací údaje účtu správce domény a vyberte **další**.

   ![Snímek obrazovky se stránkou Povolit jednotné přihlašování](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Pověření účtu správce domény jsou vyžadována k povolení bezproblémového jednotného přihlašovacího systému. Proces dokončí následující akce, které vyžadují tato zvýšená oprávnění. Přihlašovací údaje účtu správce domény se neukládají ve službě Azure AD Connect ani ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají pouze k zapnutí funkce. Pověření jsou zahozeny po úspěšném dokončení procesu.
   >
   > 1. Účet počítače s názvem AZUREADSSOACC (který představuje Azure AD) se vytvoří v místní instanci služby Active Directory.
   > 2. Dešifrovací klíč protokolu Kerberos účtu počítače je bezpečně sdílen se službou Azure AD.
   > 3. Dva hlavní názvy služeb Kerberos (SPN) jsou vytvořeny tak, aby představovaly dvě adresy URL, které se používají během přihlášení azure ad.

6. Na stránce **Připraveno ke konfiguraci** zkontrolujte, zda je zaškrtnuté políčko **Spustit proces synchronizace po dokončení konfigurace.** Potom vyberte **Konfigurovat**.

      ![Snímek obrazovky stránky Připraveno ke konfiguraci](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > V tomto okamžiku se všechny federované domény změní na spravované ověřování. Synchronizace hodnot hash hesla je nová metoda ověřování.

7. Na portálu Azure AD vyberte **Azure Active Directory** > **Azure AD Connect**.
8. Ověřte tato nastavení:
   * **Federace** je nastavena na **zakázáno**.
   * **Bezproblémové jednotné přihlašování** je nastaveno na **povoleno**.
   * **Synchronizace hesel** je **nastavena**na povoleno .<br /> 

   ![Snímek obrazovky s nastavením v části Přihlášení uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Přejděte na [testování a další kroky](#testing-and-next-steps).

   > [!IMPORTANT]
   > Přeskočte část **Možnost B: Přepnutí ze federace na synchronizaci hash hesel pomocí Azure AD Connect a PowerShellu**. Kroky v této části neplatí, pokud jste zvolili možnost A změnit metodu přihlášení na synchronizaci hodnoty hash hesla a povolit bezproblémové jednotné přihlašování.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Možnost B: Přechod z federace na synchronizaci hash hesel pomocí Azure AD Connect a PowerShellu

Tuto možnost použijte, pokud jste zpočátku nenakonfigurovali federované domény pomocí služby Azure AD Connect. Během tohoto procesu povolíte bezproblémové jednotné přihlašování a přepnete domény z federované na spravované.

1. Na serveru Azure AD Connect otevřete Průvodce azure ad connect.
2. Vyberte **Změnit přihlášení uživatele**a pak vyberte **Další**.
3. Na stránce **Připojit k Azure AD** zadejte uživatelské jméno a heslo pro účet globálního správce.
4. Na přihlašovací stránce **uživatele** vyberte tlačítko **synchronizace hodnot hash hesla.** Vyberte **Povolit jednotné přihlašování**a pak vyberte **Další**.

   Před povolením synchronizace hodnot ![hash hesel: Snímek obrazovky s možností Nekonfigurovat na přihlašovací stránce uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Po povolení synchronizace hodnot ![hash hesla: Snímek obrazovky s novými možnostmi na přihlašovací stránce uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Počínaje Azure AD Connect verze 1.1.880.0, **bezešvé jednotné přihlašování** je zaškrtnuté ve výchozím nastavení.

5. Na stránce **Povolit jednotné přihlašování** zadejte přihlašovací údaje pro účet správce domény a vyberte **Další**.

   > [!NOTE]
   > Pověření účtu správce domény jsou vyžadována k povolení bezproblémového jednotného přihlašovacího systému. Proces dokončí následující akce, které vyžadují tato zvýšená oprávnění. Přihlašovací údaje účtu správce domény se neukládají ve službě Azure AD Connect ani ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají pouze k zapnutí funkce. Pověření jsou zahozeny po úspěšném dokončení procesu.
   >
   > 1. Účet počítače s názvem AZUREADSSOACC (který představuje Azure AD) se vytvoří v místní instanci služby Active Directory.
   > 2. Dešifrovací klíč protokolu Kerberos účtu počítače je bezpečně sdílen se službou Azure AD.
   > 3. Dva hlavní názvy služeb Kerberos (SPN) jsou vytvořeny tak, aby představovaly dvě adresy URL, které se používají během přihlášení azure ad.

6. Na stránce **Připraveno ke konfiguraci** zkontrolujte, zda je zaškrtnuté políčko **Spustit proces synchronizace po dokončení konfigurace.** Potom vyberte **Konfigurovat**.

   ![Snímek obrazovky s tlačítkem Konfigurovat na stránce Připraveno ke konfiguraci](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Když vyberete **tlačítko Konfigurovat,** bezproblémové jednotné přihlašování je nakonfigurováno, jak je uvedeno v předchozím kroku. Konfigurace synchronizace hodnot hash hesla není změněna, protože byla povolena dříve.

   > [!IMPORTANT]
   > V současné době nejsou prováděny žádné změny ve způsobu, jakým se uživatelé přihlašují.

7. Na portálu Azure AD ověřte tato nastavení:
   * **Federace** je nastavena na **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastaveno na **povoleno**.
   * **Synchronizace hesel** je **nastavena**na povoleno .

   ![Snímek obrazovky s nastavením v části Přihlášení uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Převod domén z federovaných na spravované

V tomto okamžiku federace je stále povolena a funkční pro vaše domény. Chcete-li pokračovat v nasazení, musí být každá doména převedena z federované na spravované vynucení ověřování uživatelů prostřednictvím synchronizace hodnot hash hesla.

> [!IMPORTANT]
> Není třeba převádět všechny domény současně. Můžete začít s testovací doménou v produkčním tenantovi nebo začít s doménou, která má nejnižší počet uživatelů.

Dokončení převodu pomocí modulu Azure AD PowerShell:

1. V PowerShellu se přihlaste k Azure AD pomocí účtu globálního správce.
2. Chcete-li převést první doménu, spusťte následující příkaz:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Na portálu Azure AD vyberte **Azure Active Directory** > **Azure AD Connect**.
4. Ověřte, zda byla doména převedena na spravovanou, spuštěním následujícího příkazu:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testování a další kroky

Proveďte následující úkoly a ověřte synchronizaci hodnot hash hesel a dokončete proces převodu.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testování ověřování pomocí synchronizace hodnot hash hesla 

Když váš tenant používal federovanou identitu, uživatelé byli přesměrováni ze přihlašovací stránky Azure AD do prostředí služby AD FS. Teď, když je klient nakonfigurován tak, aby používal synchronizaci hodnot hash hesel namísto federovaného ověřování, nebudou uživatelé přesměrováni do služby AD FS. Místo toho se uživatelé přihlašují přímo na přihlašovací stránce Azure AD.

Testování synchronizace hodnot hash hesel:

1. Sem Internet Explorer v režimu InPrivate tak, aby bezproblémové jednotné přihlašování nepřihlašoval vás automaticky.
2. Přejděte na přihlašovací stránku Office[https://portal.office.com](https://portal.office.com/)365 ( ).
3. Zadejte uživatelský upn a pak vyberte **Další**. Ujistěte se, že jste zadali hlavní název uživatele hybridního uživatele, který byl synchronizován z vaší místní instance služby Active Directory a který dříve používal federované ověřování. Zobrazí se stránka, na které zadáte uživatelské jméno a heslo:

   ![Snímek obrazovky s přihlašovací stránkou, na kterou zadáváte uživatelské jméno](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Snímek obrazovky s přihlašovací stránkou, na které zadáváte heslo](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Po zadání hesla a **výběru možnosti Přihlásit**se budete přesměrováni na portál Office 365.

   ![Snímek obrazovky s portálem Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testování bezproblémového jednotného přihlašuje

1. Přihlaste se k počítači připojenému k doméně, který je připojen k podnikové síti.
2. V Internet Exploreru nebo Chromu přejděte na jednu z následujících adres URL (nahraďte doménu "contoso"):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Uživatel je krátce přesměrován na přihlašovací stránku Služby Azure AD, která zobrazuje zprávu "Pokouším se přihlásit." Uživatel není vyzván k zadání uživatelského jména nebo hesla.<br />

   ![Snímek obrazovky s přihlašovací stránkou Azure AD a zprávou](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. Uživatel je přesměrován a je úspěšně přihlášen k přístupovému panelu:

   > [!NOTE]
   > Bezproblémové jednotné přihlašovací služby funguje na službách Office 365, které podporují nápovědu k doméně (například myapps.microsoft.com/contoso.com). Portál Office 365 (portal.office.com) v současné době nepodporuje rady při dolu. Uživatelé jsou povinni zadat upn. Po zadání hlavního zapsaného čísla uživatele načte bezproblémové jednotné přihlašovat cenovou většinu lístku jménem uživatele. Uživatel je přihlášen bez zadání hesla.

   > [!TIP]
   > Zvažte nasazení [hybridního připojení Azure AD ve Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) pro lepší možnosti přihlašování.

### <a name="remove-the-relying-party-trust"></a>Odebrání vztahu důvěryhodnosti předávající strany

Po ověření, že všichni uživatelé a klienti úspěšně ověřování prostřednictvím Služby Azure AD, je bezpečné odebrat Office 365 předávající strany důvěryhodnosti.

Pokud nepoužíváte ad FS pro jiné účely (to znamená pro jiné důvěryhodné vztahy předávající strany), je v tomto okamžiku bezpečné vyřadit ze zařízení AD FS.

### <a name="rollback"></a>Vrácení zpět

Pokud zjistíte závažný problém a nemůžete jej rychle vyřešit, můžete se rozhodnout vrátit řešení zpět do federace.

Podrobnosti o konkrétním nasazení nahlédněte do dokumentace k návrhu a nasazení federace. Tento proces by měl zahrnovat tyto úkoly:

* Převod spravovaných domén na federované ověřování pomocí rutiny **Convert-MSOLDomainToFederated.**
* V případě potřeby nakonfigurujte další pravidla deklarací identity.

### <a name="sync-userprincipalname-updates"></a>Synchronizace aktualizací userPrincipalName

Historicky jsou blokovány aktualizace atributu **UserPrincipalName,** který používá službu synchronizace z místního prostředí, pokud nejsou splněny obě tyto podmínky:

* Uživatel je ve spravované (nefederované) doméně identity.
* Uživateli nebyla přiřazena licence.

Informace o ověření nebo zapnutí této funkce naleznete v [tématu Synchronizace aktualizací userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Řešení potíží

Váš tým podpory by měl pochopit, jak řešit všechny problémy s ověřováním, které vznikají během nebo po změně z federace na spravované. Pomocí následující dokumentace pro řešení potíží můžete týmu podpory pomoci seznámit se s běžnými kroky řešení potíží a příslušnými akcemi, které mohou pomoci izolovat a vyřešit problém.

[Poradce při potížích se synchronizací hash hesel služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Poradce při potížích s bezproblémovým jednotném přihlašování služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Převrácení bezešvého dešifrovacího klíče protokolu SSO Kerberos

Je důležité často převrátit dešifrovací klíč Kerberos účtu počítače AZUREADSSOACC (který představuje Azure AD). Účet počítače AZUREADSSOACC se vytvoří v místní doménové struktuře služby Active Directory. Důrazně doporučujeme, abyste kód dešifrovacího klíče protokolu Kerberos přeřadili alespoň každých 30 dní, abyste se přizpůsobili způsobu, jakým členové domény služby Active Directory odesílají změny hesla. K objektu účtu počítače AZUREADSSOACC není připojeno žádné přidružené zařízení, takže je nutné provést přechod ručně.

Spusťte přechod bezešvého dešifrovacího klíče protokolu SSO Kerberos na místním serveru, na který běží Azure AD Connect.

Další informace naleznete v [tématu Jak lze převrátit dešifrovací klíč protokolu Kerberos účtu počítače AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Další kroky

* Další informace o [konceptech návrhu Azure AD Connect](plan-connect-design-concepts.md).
* Zvolte [správné ověřování](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).
* Další informace o [podporovaných topologii](plan-connect-design-concepts.md).
