---
title: 'Azure AD Connect: migrace z federace na KOSMETICE pro Azure AD | Microsoft Docs'
description: Tento článek obsahuje informace o přesunutí prostředí Hybrid identity z federace na synchronizaci hodnot hash hesel.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e85d2ef9d75bbff6357466e76ffcf60e3716e78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273670"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrace z federace na synchronizaci hodnot hash hesel pro Azure Active Directory

Tento článek popisuje, jak přesunout domény organizace z Active Directory Federation Services (AD FS) (AD FS) na synchronizaci hodnot hash hesel.

> [!NOTE]
> Změna metody ověřování vyžaduje plánování, testování a případné výpadky. [Připravené zavedení](how-to-connect-staged-rollout.md) nabízí alternativní způsob testování a postupného migrace z federace na cloudové ověřování pomocí synchronizace hodnot hash hesel.
>
> Pokud plánujete použití připraveného zavedení, nezapomeňte po dokončení průřezu vypnout funkce pro přípravu.  Další informace najdete v tématu [migrace do cloudového ověřování pomocí připraveného zavedení](how-to-connect-staged-rollout.md) .


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Předpoklady pro migraci na synchronizaci hodnot hash hesel

K migraci z použití AD FS k použití synchronizace hodnot hash hesel je potřeba splnit následující požadavky.


### <a name="update-azure-ad-connect"></a>Aktualizovat Azure AD Connect

K úspěšnému provedení kroků pro migraci na synchronizaci hodnot hash hesel byste měli mít 1.1.819.0 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) . Tato verze obsahuje významné změny způsobu, jakým se provádí převod při přihlašování, a zkracuje celkovou dobu migrace z federace na cloudové ověřování z potenciálně hodin na minuty.


> [!IMPORTANT]
> V případě, že převedete domény ze federované identity na spravovanou identitu, můžete si přečíst v zastaralých dokumentaci, nástrojích a blogůch, které převod uživatele vyžaduje. *Převod uživatelů* již není vyžadován. Microsoft pracuje na aktualizaci dokumentace a nástrojů k tomu, aby odrážela tuto změnu.

Chcete-li aktualizovat Azure AD Connect, proveďte kroky v [Azure AD Connect: upgrade na nejnovější verzi](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Požadovaná oprávnění pro synchronizaci hodnot hash hesel

Azure AD Connect můžete nakonfigurovat pomocí expresního nastavení nebo vlastní instalace. Pokud jste použili možnost vlastní instalace, nemusí být k dismístě [požadovaná oprávnění](./reference-connect-accounts-permissions.md) pro synchronizaci hodnot hash hesel.

Účet služby Azure AD Connect Active Directory Domain Services (služba AD DS) vyžaduje následující oprávnění pro synchronizaci hodnot hash hesel:

* Replikovat změny adresáře
* Replikovat všechny změny adresáře

Teď je vhodná doba k ověření, že jsou tato oprávnění platná pro všechny domény v doménové struktuře.

### <a name="plan-the-migration-method"></a>Naplánování metody migrace

Můžete si vybrat ze dvou způsobů migrace ze správy federovaných identit na synchronizaci hodnot hash hesel a bezproblémového jednotného přihlašování (SSO). Použitá metoda závisí na tom, jak byla instance AD FS původně nakonfigurovaná.

* **Azure AD Connect:** Pokud jste původně nakonfigurovali AD FS pomocí Azure AD Connect, *musíte* pomocí Průvodce Azure AD Connect změnit na synchronizaci hodnot hash hesel.

   Když změníte metodu přihlašování uživatele, Azure AD Connect automaticky spustí rutinu **set-MsolDomainAuthentication** . Azure AD Connect automaticky unfederates všechny ověřené federované domény v tenantovi Azure AD.

   > [!NOTE]
   > Pokud jste v současnosti původně použili Azure AD Connect ke konfiguraci AD FS, nemůžete se vyhnout unfederatingí všech domén ve vašem tenantovi, když změníte přihlášení uživatele na synchronizaci hodnot hash hesel. ‎
* **Azure AD Connect pomocí prostředí PowerShell**. Tuto metodu můžete použít jenom v případě, že jste původní konfiguraci AD FS pomocí Azure AD Connect. Pro tuto možnost je stále nutné změnit metodu přihlašování uživatelů pomocí Průvodce Azure AD Connect. Základní rozdíl s touto možností je, že průvodce automaticky nespustí rutinu **set-MsolDomainAuthentication** . S touto možností máte plnou kontrolu nad tím, které domény se převádějí a v jakém pořadí.

Chcete-li pochopit, kterou metodu byste měli použít, proveďte kroky v následujících částech.

#### <a name="verify-current-user-sign-in-settings"></a>Ověřit nastavení přihlášení aktuálního uživatele

Ověření nastavení přihlášení aktuálního uživatele:

1. Přihlaste se k [portálu Azure AD](https://aad.portal.azure.com/) pomocí účtu globálního správce.
2. V části **přihlášení uživatele** ověřte následující nastavení:
   * U **federace** je nastavená možnost **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastavené na **zakázáno**.
   * **Předávací ověřování** je nastaveno na **zakázáno**.

   ![Snímek obrazovky s nastavením v oddílu přihlášení k Azure AD Connect uživateli](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Ověření konfigurace Azure AD Connect

1. Na serveru Azure AD Connect otevřete Azure AD Connect. Vyberte **Konfigurovat**.
2. Na stránce **další úlohy** vyberte **Zobrazit aktuální konfigurace**a pak vyberte **Další**.<br />

   ![Snímek obrazovky s možností zobrazit aktuální konfiguraci vybranou na stránce další úlohy](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na stránce **Kontrola řešení** si poznamenejte stav **synchronizace hodnoty hash hesla** .<br /> 

   * Pokud je **synchronizace hodnot hash hesel** nastavená na **disabled**, proveďte kroky v tomto článku, abyste je povolili.
   * Pokud je **synchronizace hodnot hash hesel** nastavená na **povoleno**, můžete přeskočit část **Krok 1: povolení synchronizace hodnot hash hesel** v tomto článku.
4. Na stránce **Kontrola řešení** přejděte na **Active Directory Federation Services (AD FS) (AD FS)**.<br />

   * Pokud se v této části objeví konfigurace AD FS, můžete bezpečně předpokládat, že AD FS byla původně nakonfigurovaná pomocí Azure AD Connect. Domény můžete převést z federované identity na spravovanou identitu pomocí možnosti Azure AD Connect **změnit přihlašování uživatele** . Tento proces je podrobně popsán v části **možnost A: přepnutí z federace na synchronizaci hodnot hash hesel pomocí Azure AD Connect**.
   * Pokud AD FS není uveden v aktuálním nastavení, musíte ručně převést domény ze federované identity na spravovanou identitu pomocí prostředí PowerShell. Další informace o tomto procesu najdete v části **možnost B: přepnutí z federace na synchronizaci hodnot hash hesel pomocí Azure AD Connect a PowerShellu**.

### <a name="document-current-federation-settings"></a>Dokumentovat aktuální nastavení federace

Pokud chcete zjistit aktuální nastavení federace, spusťte rutinu **Get-MsolDomainFederationSettings** :

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Příklad:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Ověřte všechna nastavení, která mohla být přizpůsobená pro návrh federace a dokumentaci k nasazení. Konkrétně hledejte vlastní nastavení v **PreferredAuthenticationProtocol**, **SupportsMfa**a **PromptLoginBehavior**.

Další informace najdete v těchto článcích:

* [AD FS Prompt = podpora parametrů přihlášení](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Pokud je **SupportsMfa** nastavené na **hodnotu true**, použijete místní řešení Multi-Factor Authentication k vložení výzvy ke čtení druhého faktoru do toku ověřování uživatele. Po převedení této domény z federovaného na spravované ověřování už nebude tato instalace fungovat pro scénáře ověřování Azure AD. Po zakázání federace narušíte vztah k vaší místní federaci a k tomu patří místní adaptéry MFA. 
>
> Místo toho použijte službu Azure Multi-Factor Authentication Cloud-based Service k provedení stejné funkce. Než budete pokračovat, pečlivě vyhodnoťte požadavky služby Multi-Factor Authentication. Před převodem domén se ujistěte, že rozumíte tomu, jak používat Azure Multi-Factor Authentication, dopad na licencování a proces registrace uživatelů.

#### <a name="back-up-federation-settings"></a>Zálohování nastavení federace

I když v rámci procesů popsaných v tomto článku nejsou provedeny žádné změny u jiných předávající strany ve vaší AD FS farmě, doporučujeme, abyste měli aktuální platnou zálohu vaší AD FS farmy, kterou můžete obnovit z. Pomocí bezplatného [nástroje Microsoft AD FS Rapid Restore](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)můžete vytvořit aktuální platnou zálohu. Pomocí tohoto nástroje můžete zálohovat AD FS a obnovit existující farmu nebo vytvořit novou farmu.

Pokud se rozhodnete, že nástroj AD FS Rapid Restore nepoužíváte, je třeba exportovat vztah důvěryhodnosti předávající strany Microsoft 365 identity a všechna přidružená vlastní pravidla deklarace identity, která jste přidali. Vztah důvěryhodnosti předávající strany a přidružená pravidla deklarace identity můžete exportovat pomocí následujícího příkladu PowerShellu:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Požadavky na nasazení a použití AD FS

Tato část popisuje požadavky na nasazení a podrobnosti o používání AD FS.

### <a name="current-ad-fs-use"></a>Aktuální AD FS použití

Před převedením z federované identity na spravovanou identitu si pečlivě prohlédněte, jak aktuálně používáte AD FS pro Azure AD, Microsoft 365 a další aplikace (vztahy důvěryhodnosti předávající strany). Konkrétně Vezměte v úvahu scénáře, které jsou popsány v následující tabulce:

| Pokud uživatel | Pak... |
|-|-|
| Plánujete dál používat AD FS s jinými aplikacemi (kromě Azure AD a Microsoft 365). | Po převedení domén budete používat AD FS i Azure AD. Vezměte v úvahu činnost koncového uživatele. V některých scénářích se uživatelé můžou muset ověřit dvakrát: jednou do Azure AD (kde uživatel získá přístup SSO k ostatním aplikacím, třeba Microsoft 365), a znovu pro všechny aplikace, které jsou pořád vázané na AD FS jako vztah důvěryhodnosti předávající strany. |
| Vaše instance AD FS je silně přizpůsobená a spoléhá na konkrétní nastavení přizpůsobení v souboru onload.js (například pokud jste změnili přihlašovací prostředí tak, aby uživatelé jako uživatelské jméno používali jenom formát **sAMAccountName** , a ne hlavní název uživatele (UPN), nebo vaše organizace intenzivně přihlásila vaše prostředí. Soubor onload.js nejde duplikovat v Azure AD. | Než budete pokračovat, musíte ověřit, že služba Azure AD dokáže splnit vaše aktuální požadavky na vlastní nastavení. Další informace a pokyny najdete v částech AD FS brandingu a AD FS přizpůsobení.|
| K blokování starších verzí ověřovacích klientů slouží AD FS.| Zvažte nahrazení AD FS ovládacích prvků, které blokují starší verze ověřování klientů pomocí kombinace [ovládacích prvků podmíněného přístupu](../conditional-access/concept-conditional-access-conditions.md) a [pravidel přístupu klienta Exchange Online](https://aka.ms/EXOCAR). |
| Požadujete, aby uživatelé prováděli vícefaktorové ověřování proti místnímu řešení Multi-Factor Authentication serveru, když se uživatelé ověřují AD FS.| Ve spravované doméně identity nemůžete do toku ověřování vložit výzvu Multi-Factor Authentication prostřednictvím místního řešení Multi-Factor Authentication. Po převodu domény ale můžete službu Azure Multi-Factor Authentication použít pro službu Multi-Factor Authentication.<br /><br /> Pokud uživatelé aktuálně nepoužívají Multi-Factor Authentication Azure, je nutný krok registrace uživatele jednorázová. Musíte připravit na a sdělit plánované registrace vašim uživatelům. |
| V tuto chvíli používáte k řízení přístupu k Microsoft 365 zásady řízení přístupu (pravidla AuthZ) v AD FS.| Zvažte nahrazení zásad odpovídajícími [zásadami podmíněného přístupu](../conditional-access/overview.md) Azure AD a [pravidly přístupu klienta Exchange Online](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Společná přizpůsobení AD FS

Tato část popisuje běžné AD FS přizpůsobení.

#### <a name="insidecorporatenetwork-claim"></a>Deklarace identity InsideCorporateNetwork

AD FS vystaví deklaraci identity **InsideCorporateNetwork** , pokud se uživatel, který ověřuje, nachází ve firemní síti. Tato deklarace se pak může předat do Azure AD. Deklarace identity se používá pro obejít službu Multi-Factor Authentication na základě síťového umístění uživatele. Informace o tom, jak zjistit, jestli je tato funkce v AD FS aktuálně povolená, najdete v tématu [důvěryhodné IP adresy pro federované uživatele](../authentication/howto-mfa-adfs.md).

Deklarace **InsideCorporateNetwork** není k dispozici po převedení domén na synchronizaci hodnot hash hesel. Pomocí [pojmenovaných umístění ve službě Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) můžete tuto funkci nahradit.

Po nakonfigurování pojmenovaných umístění musíte aktualizovat všechny zásady podmíněného přístupu, které byly nakonfigurovány tak, aby zahrnovaly nebo vyloučily **všechna důvěryhodná umístění** nebo hodnoty **důvěryhodných IP adres MFA** tak, aby odrážely nová pojmenovaná umístění.

Další informace o podmínkách **umístění** v podmíněném přístupu najdete v tématu věnovaném [umístěním podmíněného přístupu služby Active Directory](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Zařízení připojená k hybridní službě Azure AD

Když připojíte zařízení ke službě Azure AD, můžete vytvořit pravidla podmíněného přístupu, která zajistí, aby zařízení splňovala vaše standardy přístupu pro zabezpečení a dodržování předpisů. Uživatelé se také můžou k zařízení přihlásit pomocí pracovního nebo školního účtu místo osobního účtu. Když použijete hybridní zařízení připojená k Azure AD, můžete do Azure AD připojit zařízení připojená k doméně služby Active Directory. Je možné, že vaše federované prostředí bylo nastaveno na použití této funkce.

Aby bylo zajištěno, že hybridní připojení bude i nadále fungovat pro všechna zařízení, která jsou připojená k doméně po převodu domén na synchronizaci hodnot hash hesel pro klienty Windows 10, musíte použít možnosti Azure AD Connect zařízení pro synchronizaci účtů počítačů služby Active Directory s Azure AD. 

Pro účty počítačů s Windows 8 a Windows 7 používá hybridní připojení k registraci počítače ve službě Azure AD bezproblémové přihlašování. Nemusíte synchronizovat účty počítačů s Windows 8 a Windows 7, jako je tomu u zařízení s Windows 10. Je ale potřeba nasadit aktualizovaný soubor workplacejoin.exe (prostřednictvím souboru. msi) do klientů Windows 8 a Windows 7, aby se mohli sami zaregistrovat pomocí bezproblémového jednotného přihlašování. [Stáhněte soubor. msi](https://www.microsoft.com/download/details.aspx?id=53554).

Další informace najdete v tématu [Konfigurace hybridních zařízení připojených k Azure AD](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Značka

Pokud vaše organizace [přizpůsobila vaše AD FS přihlašovací stránky](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) , aby zobrazovaly informace, které jsou pro organizaci důležitější, zvažte na [přihlašovací stránce služby Azure AD podobné možnosti přizpůsobení](../fundamentals/customize-branding.md).

Přestože jsou k dispozici podobná přizpůsobení, je nutné po převodu očekávat některé vizuální změny na přihlašovacích stránkách. Může být vhodné poskytnout uživatelům informace o očekávaných změnách v komunikaci.

> [!NOTE]
> Branding organizace je k dispozici pouze v případě, že zakoupíte licenci Premium nebo Basic pro Azure Active Directory nebo máte licenci Microsoft 365.

## <a name="plan-deployment-and-support"></a>Plánování nasazení a podpory

Dokončete úkoly popsané v této části, které vám pomohou naplánovat nasazení a podporu.

### <a name="plan-the-maintenance-window"></a>Plánování časového období údržby

I když je proces převodu domény poměrně rychlý, může Azure AD dál odesílat některé žádosti o ověření na servery AD FS po dobu až čtyř hodin po dokončení převodu domény. V průběhu tohoto čtyřmístného okna a v závislosti na různých mezipamětech na straně služby nemusí služba Azure AD přijímat tato ověření. Uživateli se může zobrazit chyba. Uživatel se může stále úspěšně ověřit proti AD FS, ale Azure AD už nepřijímá vydaný token, protože je teď odebraný vztah důvěryhodnosti federace.

Budou ovlivněny pouze uživatelé, kteří během tohoto okna po převádění přistupují ke službám přes webový prohlížeč. Starší verze klientů (Exchange ActiveSync, Outlook 2010/2013) se neočekávají, protože Exchange Online uchovává mezipaměť svých přihlašovacích údajů po nastavenou dobu. Mezipaměť se používá k tiché opětovné ověření uživatele. Uživatel se nemusí vracet do AD FS. Přihlašovací údaje uložené v zařízení pro tyto klienty se používají k tichému opětovnému ověření po vymazání této mezipaměti. V důsledku procesu převodu domény se uživatelům neočekává, že nebudou dostávat žádné výzvy k zadání hesla. 

Klienti moderního ověřování (aplikace Office 2016 a Office 2013, iOS a Android) používají k získání nových přístupových tokenů pro pokračování přístupu k prostředkům nové přístupové tokeny namísto návratu do AD FS. Tito klienti jsou imunní na jakékoli výzvy k zadání hesla vyplývající z procesu převodu domény. Klienti budou i nadále fungovat bez další konfigurace.

> [!IMPORTANT]
> Nevypněte prostředí AD FS, nebo odeberte Microsoft 365 vztah důvěryhodnosti předávající strany, dokud neověříte, jestli se všichni uživatelé můžou úspěšně ověřit pomocí cloudového ověřování.

### <a name="plan-for-rollback"></a>Plánování vrácení zpět

Pokud dojde k zásadnímu problému, který nelze rychle vyřešit, můžete se rozhodnout vrátit řešení zpět do federace. Je důležité naplánovat, co dělat v případě, že nasazení není součástí zamýšlené. Pokud během nasazení dojde k chybě převodu domény nebo uživatelů, nebo pokud se potřebujete vrátit zpět k federaci, musíte pochopit, jak zmírnit případné výpadky a snížit dopad na uživatele.

#### <a name="to-roll-back"></a>Vrácení zpět

Pokud chcete naplánovat vrácení zpět, podívejte se na dokumentaci pro návrh a nasazení federace, kde najdete podrobnosti o konkrétním nasazení. Proces by měl zahrnovat tyto úlohy:

* Převod spravovaných domén na federované domény pomocí rutiny **Convert-MsolDomainToFederated** .
* V případě potřeby nakonfigurujte další pravidla deklarací identity.

### <a name="plan-communications"></a>Plán komunikace

Důležitou součástí plánování nasazení a podpory je zajistit, že uživatelé budou proaktivní informování o nadcházejících změnách. Uživatelé by měli znát, co se může setkat a co se jim vyžaduje. 

Po nasazení synchronizace hodnot hash hesel a bezproblémového jednotného přihlašování se přihlašuje uživatelské prostředí pro přístup k Microsoft 365 a dalším prostředkům, které jsou ověřeny prostřednictvím služby Azure AD. Uživatelé mimo síť uvidí pouze přihlašovací stránku služby Azure AD. Tito uživatelé nejsou přesměrováni na stránku založenou na formulářích, která je prezentována externími proxy servery webových aplikací.

Do strategie komunikace zahrňte tyto prvky:

* Informujte uživatele o nadcházejících a vydaných funkcích pomocí:
   * E-mail a další kanály interní komunikace.
   * Vizuály, například plakáty.
   * Executive, živá nebo jiná komunikace.
* Určete, kdo bude tuto komunikaci přizpůsobovat a kdo bude posílat komunikaci a kdy.

## <a name="implement-your-solution"></a>Implementace řešení

Naplánovali jste své řešení. Teď ji teď můžete implementovat. Implementace zahrnuje tyto komponenty:

* Povoluje se synchronizace hodnot hash hesel.
* Připravuje se bezproblémové jednotné přihlašování.
* Změna metody přihlašování na synchronizaci hodnot hash hesel a povolení bezproblémového jednotného přihlašování.

### <a name="step-1-enable-password-hash-synchronization"></a>Krok 1: povolení synchronizace hodnot hash hesel

Prvním krokem k implementaci tohoto řešení je povolení synchronizace hodnot hash hesel pomocí Průvodce Azure AD Connect. Synchronizace hodnot hash hesel je volitelná funkce, kterou můžete povolit v prostředích, která používají federaci. Tok ověřování nemá žádný vliv. V takovém případě Azure AD Connect spustí synchronizaci hodnot hash hesel bez ovlivnění uživatelů, kteří se přihlásí pomocí federace.

Z tohoto důvodu doporučujeme, abyste tento krok dokončili jako přípravné úlohy a teprve potom jste změnili způsob přihlášení k doméně. Pak budete mít k dispozici dostatek času k ověření, že synchronizace hodnot hash hesel funguje správně.

Postup povolení synchronizace hodnot hash hesel:

1. Na serveru Azure AD Connect otevřete Průvodce Azure AD Connect a pak vyberte **Konfigurovat**.
2. Vyberte **přizpůsobit možnosti synchronizace**a pak vyberte **Další**.
3. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo účtu globálního správce.
4. Na stránce **připojit adresáře** vyberte **Další**.
5. Na stránce **filtrování domén a organizačních jednotek** vyberte **Další**.
6. Na stránce **volitelné funkce** vyberte **Synchronizace hesel**a pak vyberte **Další**.
 
   ![Snímek obrazovky s možností synchronizace hesel vybraný na stránce volitelné funkce](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Na zbývajících stránkách vyberte **Další** . Na poslední stránce vyberte **Konfigurovat**.
8. Azure AD Connect spustí synchronizaci hodnot hash hesel při příští synchronizaci.

Po povolení synchronizace hodnot hash hesel pro všechny uživatele v oboru synchronizace Azure AD Connect dojde k opětovnému navýšení hodnoty hash hesla pro všechny uživatele v oboru synchronizace do služby Azure AD. V závislosti na počtu uživatelů tato operace může trvat několik minut nebo i několik hodin.

Pro účely plánování byste měli odhadnout, že přibližně 20 000 uživatelů se zpracovává za 1 hodinu.

Chcete-li ověřit, zda synchronizace hodnot hash hesel funguje správně, dokončete úlohu **řešení potíží** v průvodci Azure AD Connect:

1. Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell pomocí možnosti Spustit jako správce.
2. Spusťte `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted` .
3. Spusťte Průvodce Azure AD Connect.
4. Přejít na stránku **další úlohy** , vyberte **řešení potíží**a pak vyberte **Další**.
5. Na stránce **Poradce při potížích** vyberte **Spustit** . tím spustíte nabídku Poradce při potížích v PowerShellu.
6. V hlavní nabídce vyberte **řešit potíže se synchronizací hodnot hash hesel**.
7. V podnabídce vyberte možnost **synchronizace hodnot hash hesel nefunguje vůbec**.

Informace o řešení potíží najdete v tématu [řešení potíží se synchronizací hodnot hash hesel pomocí Azure AD Connect synchronizace](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Krok 2: Příprava na bezproblémové přihlašování

Aby zařízení používalo bezproblémové jednotné přihlašování, musíte do nastavení zóny intranetu pro uživatele přidat adresu URL služby Azure AD pomocí zásad skupiny ve službě Active Directory.

Ve výchozím nastavení webové prohlížeče automaticky vypočítávají správnou zónu, buď Internet, nebo intranet, z adresy URL. Například **http: \/ \/ Contoso/** Maps do zóny intranetu a **http: \/ \/ intranet.contoso.com** se mapuje na zónu Internetu (protože adresa URL obsahuje tečku). Prohlížeče odesílají lístky protokolu Kerberos do koncového bodu cloudu, jako je například adresa URL služby Azure AD, jenom v případě, že explicitně přidáte adresu URL do zóny intranetu prohlížeče.

Dokončete kroky k [zavedení](./how-to-connect-sso-quick-start.md) požadovaných změn vašich zařízení.

> [!IMPORTANT]
> Provedení této změny nemění způsob, jakým se uživatelé přihlásí ke službě Azure AD. Je ale důležité, abyste tuto konfiguraci před pokračováním použili pro všechna vaše zařízení. Uživatelům, kteří se přihlásí v zařízeních, které tuto konfiguraci neobdrželi, je nutné zadat uživatelské jméno a heslo pro přihlášení ke službě Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Krok 3: Změna metody přihlášení na synchronizaci hodnot hash hesel a povolení bezproblémového jednotného přihlašování

Máte dvě možnosti, jak změnit metodu přihlašování na synchronizaci hodnot hash hesel a povolit bezproblémové jednotné přihlašování.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Možnost A: přepnutí z federace na synchronizaci hodnot hash hesel pomocí Azure AD Connect

Tuto metodu použijte, pokud jste původně nakonfigurovali AD FS prostředí pomocí Azure AD Connect. Tuto metodu nemůžete použít, pokud *jste původně nenakonfigurovali* AD FS prostředí pomocí Azure AD Connect.

Nejprve změňte metodu přihlašování:

1. Na serveru Azure AD Connect otevřete Průvodce Azure AD Connect.
2. Vyberte možnost **změnit přihlášení uživatele**a pak vyberte možnost **Další**. 

   ![Snímek obrazovky s možností přihlášení uživatele změny na stránce další úlohy](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo účtu globálního správce.
4. Na **přihlašovací stránce uživatele** vyberte **tlačítko synchronizace hodnoty hash hesla**. Nezapomeňte zaškrtnout políčko **nepřevádět uživatelské účty** . Možnost je zastaralá. Vyberte **Povolit jednotné přihlašování**a pak vyberte **Další**.

   ![Snímek obrazovky se stránkou pro povolení jednotného přihlašování](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Počínaje verzí Azure AD Connect 1.1.880.0 je ve výchozím nastavení zaškrtnuto políčko **bezproblémové jednotné přihlašování** .

   > [!IMPORTANT]
   > Můžete bez obav ignorovat upozornění, která označují, že se při převodu z federace na cloudové ověřování mají zadat synchronizaci hodnot hash uživatelů a úplných hesel. Upozorňujeme, že tyto kroky už nejsou potřebné. Pokud se tato upozornění zobrazují i nadále, ujistěte se, že používáte nejnovější verzi Azure AD Connect a že používáte nejnovější verzi tohoto průvodce. Další informace najdete v části [Azure AD Connect aktualizace](#update-azure-ad-connect).

5. Na stránce **Povolit jednotné přihlašování** zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   ![Snímek obrazovky se stránkou pro povolení jednotného přihlašování, kde můžete zadat přihlašovací údaje účtu správce domény.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > K povolení bezproblémového jednotného přihlašování se vyžadují přihlašovací údaje účtu správce domény. Proces provede následující akce, které vyžadují tato oprávnění vyšší úrovně. Přihlašovací údaje účtu správce domény nejsou uložené v Azure AD Connect ani ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají jenom k zapnutí této funkce. Po úspěšném dokončení procesu se přihlašovací údaje zahodí.
   >
   > 1. Účet počítače s názvem AZUREADSSOACC (který představuje Azure AD) se vytvoří v místní instanci služby Active Directory.
   > 2. Dešifrovací klíč protokolu Kerberos účtu počítače se bezpečně sdílí se službou Azure AD.
   > 3. Vytvoří se dva hlavní názvy služby (SPN) protokolu Kerberos, které budou představovat dvě adresy URL používané během přihlašování pomocí Azure AD.

6. Na stránce **připraveno ke konfiguraci** se ujistěte, že je zaškrtnuté políčko **spustit proces synchronizace po dokončení konfigurace** . Pak vyberte **Konfigurovat**.

      ![Snímek obrazovky připraveno ke konfiguraci stránky](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > V tomto okamžiku se všechny federované domény změní na spravované ověřování. Synchronizace hodnot hash hesel je nová metoda ověřování.

7. Na portálu Azure AD vyberte **Azure Active Directory**  >  **Azure AD Connect**.
8. Ověřte tato nastavení:
   * **Federace** je nastavená na **disabled (zakázáno**).
   * **Bezproblémové jednotné přihlašování** je nastavené na **povoleno**.
   * **Synchronizace hesla** je nastavená na **povoleno**.<br /> 

   ![Snímek obrazovky, který zobrazuje nastavení v části přihlášení uživatele na portálu Azure AD.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Přejděte k [testování a dalším krokům](#testing-and-next-steps).

   > [!IMPORTANT]
   > Přeskočte **možnost oddílu B: přepnutí z federace na synchronizaci hodnot hash hesel pomocí Azure AD Connect a PowerShellu**. Kroky v této části se nepoužívají, pokud jste zvolili možnost A, chcete-li změnit metodu přihlašování na synchronizaci hodnot hash hesel a povolit bezproblémové jednotné přihlašování.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Možnost B: přepnutí z federace na synchronizaci hodnot hash hesel pomocí Azure AD Connect a PowerShellu

Tuto možnost použijte, pokud jste původně nenakonfigurovali federované domény pomocí Azure AD Connect. Během tohoto procesu povolíte bezproblémové přihlašování a přepnete své domény z federované na spravovanou.

1. Na serveru Azure AD Connect otevřete Průvodce Azure AD Connect.
2. Vyberte možnost **změnit přihlášení uživatele**a pak vyberte možnost **Další**.
3. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo pro účet globálního správce.
4. Na **přihlašovací stránce uživatele** vyberte tlačítko **synchronizace hodnoty hash hesla** . Vyberte **Povolit jednotné přihlašování**a pak vyberte **Další**.

   Než povolíte synchronizaci hodnot hash hesel: ![ snímek obrazovky, který ukazuje možnost nekonfigurovat na přihlašovací stránce uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Po povolení synchronizace hodnot hash hesel: ![ snímek obrazovky, který zobrazuje nové možnosti na přihlašovací stránce uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Počínaje verzí Azure AD Connect 1.1.880.0 je ve výchozím nastavení zaškrtnuto políčko **bezproblémové jednotné přihlašování** .

5. Na stránce **Povolit jednotné přihlašování** zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   > [!NOTE]
   > K povolení bezproblémového jednotného přihlašování se vyžadují přihlašovací údaje účtu správce domény. Proces provede následující akce, které vyžadují tato oprávnění vyšší úrovně. Přihlašovací údaje účtu správce domény nejsou uložené v Azure AD Connect ani ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají jenom k zapnutí této funkce. Po úspěšném dokončení procesu se přihlašovací údaje zahodí.
   >
   > 1. Účet počítače s názvem AZUREADSSOACC (který představuje Azure AD) se vytvoří v místní instanci služby Active Directory.
   > 2. Dešifrovací klíč protokolu Kerberos účtu počítače se bezpečně sdílí se službou Azure AD.
   > 3. Vytvoří se dva hlavní názvy služby (SPN) protokolu Kerberos, které budou představovat dvě adresy URL používané během přihlašování pomocí Azure AD.

6. Na stránce **připraveno ke konfiguraci** se ujistěte, že je zaškrtnuté políčko **spustit proces synchronizace po dokončení konfigurace** . Pak vyberte **Konfigurovat**.

   ![Snímek obrazovky zobrazující tlačítko konfigurovat na stránce připraveno ke konfiguraci](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Po výběru tlačítka **Konfigurovat** bude bezproblémové jednotné přihlašování nakonfigurované tak, jak je uvedeno v předchozím kroku. Konfigurace synchronizace hodnot hash hesel se nezměnila, protože byla dříve povolena.

   > [!IMPORTANT]
   > V současné době se neprovádí žádné změny v tom, jak se uživatelé přihlásí.

7. Na portálu Azure AD ověřte tato nastavení:
   * U **federace** je nastavená možnost **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastavené na **povoleno**.
   * **Synchronizace hesla** je nastavená na **povoleno**.

   ![Snímek obrazovky, který zobrazuje nastavení v oddílu přihlášení uživatele](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Převést domény ze federované na spravované

V tomto okamžiku je federace pro vaše domény stále povolená a funkční. Aby bylo možné pokračovat v nasazení, je nutné každou doménu převést z federované na spravovanou, aby se ověřování uživatelů vynutilo pomocí synchronizace hodnot hash hesel.

> [!IMPORTANT]
> Nemusíte převádět všechny domény současně. Můžete začít s testovací doménou v produkčním tenantovi nebo začít s doménou, která má nejnižší počet uživatelů.

Dokončete převod pomocí modulu Azure AD PowerShell:

1. V PowerShellu se přihlaste ke službě Azure AD pomocí účtu globálního správce.
2. Chcete-li převést první doménu, spusťte následující příkaz:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Na portálu Azure AD vyberte **Azure Active Directory**  >  **Azure AD Connect**.
4. Spuštěním následujícího příkazu ověřte, že se doména převedla na spravovanou:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testování a další kroky

Provedením následujících kroků ověřte synchronizaci hodnot hash hesel a dokončete proces převodu.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testování ověřování pomocí synchronizace hodnot hash hesel 

Když váš tenant použil federované identity, přesměruje se uživatelé na přihlašovací stránku Azure AD na vaše AD FS prostředí. Teď, když je tenant nakonfigurovaný tak, aby místo federovaného ověřování používal synchronizaci hodnot hash hesel, nebudou uživatelé přesměrováni na AD FS. Místo toho se uživatelé přihlásí přímo na přihlašovací stránce služby Azure AD.

Test synchronizace hodnot hash hesel:

1. Spusťte Internet Explorer v režimu InPrivate, aby vás bez problémů přihlašování automaticky přihlásilo.
2. Přejít na přihlašovací stránku Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Zadejte hlavní název uživatele (UPN) a pak vyberte **Další**. Ujistěte se, že zadáváte hlavní název uživatele (UPN), který byl synchronizovaný z místní instance služby Active Directory a který dřív používal federované ověřování. Zobrazí se stránka, na které zadáte uživatelské jméno a heslo:

   ![Snímek obrazovky zobrazující přihlašovací stránku, na které zadáte uživatelské jméno](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Snímek obrazovky zobrazující přihlašovací stránku, na které zadáváte heslo](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Po zadání hesla a výběru **Přihlásit**jste přesměrováni na portál Office 365.

   ![Snímek obrazovky zobrazující portál Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Plynulé testování jednotného přihlašování

1. Přihlaste se k počítači připojenému k doméně, který je připojený k podnikové síti.
2. V aplikaci Internet Explorer nebo Chrome použijte jednu z následujících adres URL (v doméně nahraďte "contoso"):

   * https: \/ \/ myapps.Microsoft.com/contoso.com
   * https: \/ \/ myapps.Microsoft.com/contoso.onmicrosoft.com

   Uživatel se krátce přesměruje na přihlašovací stránku služby Azure AD, kde se zobrazí zpráva "Probíhá pokus o přihlášení". Uživateli se nezobrazí výzva k zadání uživatelského jména nebo hesla.<br />

   ![Snímek obrazovky, který zobrazuje přihlašovací stránku a zprávu služby Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. Uživatel je přesměrován a úspěšně přihlášen k přístupovému panelu:

   > [!NOTE]
   > Bezproblémové jednotné přihlašování funguje na Microsoft 365 Services, které podporují doporučení k doméně (například myapps.microsoft.com/contoso.com). Portál Microsoft 365 (portal.office.com) v současné době nepodporuje doporučení domény. Uživatelé musí zadat hlavní název uživatele (UPN). Po zadání hlavního názvu uživatele (UPN) dojde k bezproblémovému přihlášení SSO jménem uživatele k načtení lístku protokolu Kerberos. Uživatel je přihlášený bez zadání hesla.

   > [!TIP]
   > Pro vylepšené prostředí jednotného přihlašování zvažte nasazení [služby Azure AD Hybrid JOIN ve Windows 10](../devices/overview.md) .

### <a name="remove-the-relying-party-trust"></a>Odebrat vztah důvěryhodnosti předávající strany

Po ověření úspěšného ověření všech uživatelů a klientů prostřednictvím služby Azure AD je bezpečné odebrat Microsoft 365 vztah důvěryhodnosti předávající strany.

Pokud nepoužíváte AD FS pro jiné účely (tj. pro jiné vztahy důvěryhodnosti předávající strany), je v tuto chvíli bezpečné vyřazení z provozu AD FS.

### <a name="rollback"></a>Návrat

Pokud zjistíte hlavní problém a nemůžete ho rychle vyřešit, můžete se rozhodnout vrátit řešení do federace.

Podrobnosti o konkrétním nasazení najdete v dokumentaci pro návrh a nasazení federace. Proces by měl zahrnovat tyto úlohy:

* Pomocí rutiny **Convert-MsolDomainToFederated** převeďte spravované domény na federované ověřování.
* V případě potřeby nakonfigurujte další pravidla deklarací identity.

### <a name="sync-userprincipalname-updates"></a>Synchronizovat aktualizace userPrincipalName

Historická aktualizace atributu **userPrincipalName** , který používá synchronizační službu z místního prostředí, se zablokuje, pokud nejsou splněné obě tyto podmínky:

* Uživatel se nachází ve spravované (nefederované) doméně identity.
* Uživatel nemá přiřazenou licenci.

Informace o tom, jak tuto funkci ověřit nebo zapnout, najdete v tématu [synchronizace aktualizací userPrincipalName](./how-to-connect-syncservice-features.md).

### <a name="troubleshooting"></a>Řešení potíží

Váš tým podpory by měl pochopit, jak řešit problémy s ověřováním, které vznikají během nebo po změně federace na spravovanou. Následující dokumentaci k řešení potíží vám pomohou týmu podpory seznámení s běžnými kroky při řešení potíží a s příslušnými akcemi, které vám mohou pomoci izolovat a vyřešit problém.

[Řešení potíží s Azure Active Directory synchronizace hodnot hash hesel](./tshoot-connect-password-hash-synchronization.md)

[Řešení potíží Azure Active Directory bezproblémové jednotné přihlašování](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Převeďte bezproblémové dešifrovací klíč Kerberos pro jednotné přihlašování.

Je důležité často vymezit dešifrovací klíč protokolu Kerberos účtu počítače AZUREADSSOACC (který představuje Azure AD). Účet počítače AZUREADSSOACC se vytvoří v místní doménové struktuře služby Active Directory. Důrazně doporučujeme, abyste převzali šifrovací klíč protokolu Kerberos aspoň každých 30 dní, abyste se zarovnali se způsobem, jakým členové domény služby Active Directory odesílají změny hesel. K objektu účtu počítače AZUREADSSOACC není připojené žádné přidružené zařízení, takže je potřeba provést ruční přecházení.

Zahajte výměnu nebezproblémového dešifrovacího klíče Kerberos pro jednotné přihlašování na místním serveru, na kterém běží Azure AD Connect.

Další informace najdete v tématu [návody převádění dešifrovacího klíče protokolu Kerberos účtu počítače s AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [Azure AD Connect konceptech návrhu](plan-connect-design-concepts.md).
* Vyberte [správné ověřování](./choose-ad-authn.md).
* Přečtěte si o [podporovaných topologiích](plan-connect-design-concepts.md).