---
title: 'Azure AD Connect: migrace z federace na PTA pro Azure AD'
description: Tento článek obsahuje informace o přesunu hybridního prostředí identity z federace do předávacího ověřování.
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
ms.openlocfilehash: 7e5a5b06bc95d022cfad66118db4b55e9369b5bd
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661892"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrace z federace na předávací ověřování pro Azure Active Directory

Tento článek popisuje, jak přesunout domény organizace z Active Directory Federation Services (AD FS) (AD FS) do předávacího ověřování.

> [!NOTE]
> Změna metody ověřování vyžaduje plánování, testování a případné výpadky. [Příprava na přípravu](how-to-connect-staged-rollout.md) nabízí alternativní způsob testování a postupného migrace z federace na cloudové ověřování pomocí předávacího ověřování.
> 
> Pokud plánujete použití připraveného zavedení, nezapomeňte po dokončení průřezu vypnout funkce pro přípravu.  Další informace najdete v tématu [migrace do cloudového ověřování pomocí připraveného zavedení](how-to-connect-staged-rollout.md) .


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Předpoklady pro migraci na předávací ověřování

K migraci z použití AD FS k použití předávacího ověřování se vyžadují následující požadavky.

### <a name="update-azure-ad-connect"></a>Aktualizovat Azure AD Connect

K úspěšnému dokončení kroků, které je potřeba provést při migraci na použití předávacího ověřování, musíte mít [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 nebo novější verze. V Azure AD Connect 1.1.819.0 se způsob, jakým se provádí Změna přihlašování, významně mění. Celková doba migrace z AD FS do cloudového ověřování v této verzi se zkracuje z potenciálně hodin na minuty.

> [!IMPORTANT]
> V případě, že převedete domény ze federované identity na spravovanou identitu, můžete si přečíst v zastaralých dokumentaci, nástrojích a blogůch, které převod uživatele vyžaduje. *Převod uživatelů* již není vyžadován. Microsoft pracuje na aktualizaci dokumentace a nástrojů k tomu, aby odrážela tuto změnu.

Chcete-li aktualizovat Azure AD Connect, proveďte kroky v [Azure AD Connect: upgrade na nejnovější verzi](./how-to-upgrade-previous-version.md).

### <a name="plan-authentication-agent-number-and-placement"></a>Plánování a číslo a umístění agenta ověřování

Předávací ověřování vyžaduje nasazení nenáročných agentů na serveru Azure AD Connect a na místním počítači, na kterém běží Windows Server. Pokud chcete snížit latenci, nainstalujte agenty co nejblíže k řadičům domény služby Active Directory.

Pro většinu zákazníků stačí dva nebo tři ověřovací agenty k zajištění vysoké dostupnosti a požadované kapacity. Tenant může mít zaregistrovaný maximálně 12 agentů. První Agent je vždy nainstalován na samotném Azure AD Connect serveru. Další informace o omezeních agentů a možnostech nasazení agenta najdete v tématu [předávací ověřování Azure AD: aktuální omezení](./how-to-connect-pta-current-limitations.md).

### <a name="plan-the-migration-method"></a>Naplánování metody migrace

Můžete si vybrat ze dvou metod migrace ze správy federovaných identit do předávacího ověřování a bezproblémového jednotného přihlašování (SSO). Použitá metoda závisí na tom, jak byla instance AD FS původně nakonfigurovaná.

* **Azure AD Connect:** Pokud jste původně nakonfigurovali AD FS pomocí Azure AD Connect, *musíte* se pomocí Průvodce Azure AD Connect změnit na předávací ověřování.

   Když změníte metodu přihlašování uživatele, Azure AD Connect automaticky spustí rutinu **set-MsolDomainAuthentication** . Azure AD Connect automaticky unfederates všechny ověřené federované domény v tenantovi Azure AD.

   > [!NOTE]
   > Pokud jste v současnosti původně použili Azure AD Connect ke konfiguraci AD FS, nemůžete se vyhnout unfederatingí všech domén ve vašem tenantovi, když změníte přihlášení uživatele na předávací ověřování.
‎
* **Azure AD Connect pomocí prostředí PowerShell**. Tuto metodu můžete použít jenom v případě, že jste původní konfiguraci AD FS pomocí Azure AD Connect. Pro tuto možnost je stále nutné změnit metodu přihlašování uživatelů pomocí Průvodce Azure AD Connect. Základní rozdíl s touto možností je, že průvodce automaticky nespustí rutinu **set-MsolDomainAuthentication** . S touto možností máte plnou kontrolu nad tím, které domény se převádějí a v jakém pořadí.

Chcete-li pochopit, kterou metodu byste měli použít, proveďte kroky v následujících částech.

#### <a name="verify-current-user-sign-in-settings"></a>Ověřit nastavení přihlášení aktuálního uživatele

1. Přihlaste se k [portálu Azure AD](https://aad.portal.azure.com/) pomocí účtu globálního správce.
2. V části **přihlášení uživatele** ověřte následující nastavení:
   * U **federace** je nastavená možnost **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastavené na **zakázáno**.
   * **Předávací ověřování** je nastaveno na **zakázáno**.

   ![Snímek obrazovky s nastavením v oddílu přihlášení k Azure AD Connect uživateli](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Ověření konfigurace federace

1. Na serveru Azure AD Connect otevřete Azure AD Connect. Vyberte **Konfigurovat**.
2. Na stránce **další úlohy** vyberte **Zobrazit aktuální konfigurace**a pak vyberte **Další**.<br />
 
   ![Snímek obrazovky s možností zobrazení aktuální konfigurace na stránce další úlohy](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. V části **Další úkoly > Správa federace**přejděte na **Active Directory Federation Services (AD FS) (AD FS)**.<br />

   * Pokud se v této části objeví konfigurace AD FS, můžete bezpečně předpokládat, že AD FS byla původně nakonfigurovaná pomocí Azure AD Connect. Domény můžete převést z federované identity na spravovanou identitu pomocí možnosti Azure AD Connect **změnit přihlašování uživatele** . Další informace o tomto procesu najdete v části **možnost a: Konfigurace předávacího ověřování pomocí Azure AD Connect**.
   * Pokud AD FS není uveden v aktuálním nastavení, musíte ručně převést domény ze federované identity na spravovanou identitu pomocí prostředí PowerShell. Další informace o tomto procesu najdete v části **možnost B: přepnutí z federace na předávací ověřování pomocí Azure AD Connect a PowerShellu**.

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
> Pokud je **SupportsMfa** nastavené na **hodnotu true**, použijete místní řešení Multi-Factor Authentication k vložení výzvy ke čtení druhého faktoru do toku ověřování uživatele. Tato instalace už nefunguje pro scénáře ověřování Azure AD. 
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

AD FS vystaví deklaraci identity **InsideCorporateNetwork** , pokud se uživatel, který ověřuje, nachází ve firemní síti. Tato deklarace se pak může předat do Azure AD. Deklarace identity se používá pro obejít službu Multi-Factor Authentication na základě síťového umístění uživatele. Informace o tom, jak zjistit, jestli je tato funkce v AD FS v tuto chvíli dostupná, najdete v tématu [důvěryhodné IP adresy pro federované uživatele](../authentication/howto-mfa-adfs.md).

Deklarace **InsideCorporateNetwork** není k dispozici po převedení domén na předávací ověřování. Pomocí [pojmenovaných umístění ve službě Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) můžete tuto funkci nahradit.

Po nakonfigurování pojmenovaných umístění musíte aktualizovat všechny zásady podmíněného přístupu, které byly nakonfigurovány tak, aby zahrnovaly nebo vyloučily **všechna důvěryhodná umístění** nebo hodnoty **důvěryhodných IP adres MFA** tak, aby odrážely nová pojmenovaná umístění.

Další informace o podmínkách **umístění** v podmíněném přístupu najdete v tématu věnovaném [umístěním podmíněného přístupu služby Active Directory](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Zařízení připojená k hybridní službě Azure AD

Když připojíte zařízení ke službě Azure AD, můžete vytvořit pravidla podmíněného přístupu, která zajistí, aby zařízení splňovala vaše standardy přístupu pro zabezpečení a dodržování předpisů. Uživatelé se také můžou k zařízení přihlásit pomocí pracovního nebo školního účtu místo osobního účtu. Když použijete hybridní zařízení připojená k Azure AD, můžete do Azure AD připojit zařízení připojená k doméně služby Active Directory. Je možné, že vaše federované prostředí bylo nastaveno na použití této funkce.

Aby se zajistilo, že hybridní připojení bude i nadále fungovat pro všechna zařízení, která jsou připojená k doméně po převodu domén na předávací ověřování, musíte pro klienty s Windows 10 použít Azure AD Connect k synchronizaci účtů počítačů služby Active Directory s Azure AD.

Pro účty počítačů s Windows 8 a Windows 7 používá hybridní připojení k registraci počítače ve službě Azure AD bezproblémové přihlašování. Nemusíte synchronizovat účty počítačů s Windows 8 a Windows 7, jako je tomu u zařízení s Windows 10. Je ale potřeba nasadit aktualizovaný soubor workplacejoin.exe (prostřednictvím souboru. msi) do klientů Windows 8 a Windows 7, aby se mohli sami zaregistrovat pomocí bezproblémového jednotného přihlašování. [Stáhněte soubor. msi](https://www.microsoft.com/download/details.aspx?id=53554).

Další informace najdete v tématu [Konfigurace hybridních zařízení připojených k Azure AD](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Značka

Pokud vaše organizace [přizpůsobila vaše AD FS přihlašovací stránky](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) , aby zobrazovaly informace, které jsou pro organizaci důležitější, zvažte na [přihlašovací stránce služby Azure AD podobné možnosti přizpůsobení](../fundamentals/customize-branding.md).

Přestože jsou k dispozici podobná přizpůsobení, je nutné po převodu očekávat některé vizuální změny na přihlašovacích stránkách. Může být vhodné poskytnout uživatelům informace o očekávaných změnách v komunikaci.

> [!NOTE]
> Branding organizace je k dispozici pouze v případě, že zakoupíte licenci Premium nebo Basic pro Azure Active Directory nebo máte licenci Microsoft 365.

## <a name="plan-for-smart-lockout"></a>Plánování inteligentního uzamčení

Inteligentní uzamčení Azure AD chrání před útoky hrubou silou hesla. Inteligentní uzamčení znemožňuje uzamknutí místního účtu služby Active Directory, když se používá předávací ověřování a že zásady skupiny uzamčení účtu jsou nastavené ve službě Active Directory.

Další informace najdete v tématu [inteligentní uzamčení Azure Active Directory](../authentication/howto-password-smart-lockout.md).

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

Po nasazení obou předávacích ověřování a bezproblémového jednotného přihlašování se přihlašuje uživatelské prostředí pro přístup k Microsoft 365 a dalším prostředkům, které jsou ověřeny prostřednictvím služby Azure AD. Uživatelé mimo síť uvidí pouze přihlašovací stránku služby Azure AD. Tito uživatelé nejsou přesměrováni na stránku založenou na formulářích, která je prezentována externími proxy servery webových aplikací.

Do strategie komunikace zahrňte tyto prvky:

* Informujte uživatele o nadcházejících a vydaných funkcích pomocí:
   * E-mail a další kanály interní komunikace.
   * Vizuály, například plakáty.
   * Executive, živá nebo jiná komunikace.
* Určete, kdo bude tuto komunikaci přizpůsobovat a kdo bude posílat komunikaci a kdy.

## <a name="implement-your-solution"></a>Implementace řešení

Naplánovali jste své řešení. Teď ji teď můžete implementovat. Implementace zahrnuje tyto komponenty:

* Připravuje se bezproblémové jednotné přihlašování.
* Změna metody přihlášení na předávací ověřování a povolení bezproblémového jednotného přihlašování.

### <a name="step-1-prepare-for-seamless-sso"></a>Krok 1: Příprava na bezproblémové přihlašování

Aby zařízení používalo bezproblémové jednotné přihlašování, musíte do nastavení zóny intranetu pro uživatele přidat adresu URL služby Azure AD pomocí zásad skupiny ve službě Active Directory.

Ve výchozím nastavení webové prohlížeče automaticky vypočítávají správnou zónu, buď Internet, nebo intranet, z adresy URL. Například **http: \/ \/ Contoso/** Maps do zóny intranetu a **http: \/ \/ intranet.contoso.com** se mapuje na zónu Internetu (protože adresa URL obsahuje tečku). Prohlížeče odesílají lístky protokolu Kerberos do koncového bodu cloudu, jako je například adresa URL služby Azure AD, jenom v případě, že explicitně přidáte adresu URL do zóny intranetu prohlížeče.

Dokončete kroky k [zavedení](./how-to-connect-sso-quick-start.md) požadovaných změn vašich zařízení.

> [!IMPORTANT]
> Provedení této změny nemění způsob, jakým se uživatelé přihlásí ke službě Azure AD. Je ale důležité, abyste tuto konfiguraci před pokračováním použili pro všechna vaše zařízení. Uživatelům, kteří se přihlásí v zařízeních, které tuto konfiguraci neobdrželi, je nutné zadat uživatelské jméno a heslo pro přihlášení ke službě Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Krok 2: Změna metody přihlášení na předávací ověřování a povolení bezproblémového jednotného přihlašování

Máte dvě možnosti, jak změnit metodu přihlašování na předávací ověřování a povolit bezproblémové jednotné přihlašování.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Možnost A: Konfigurace předávacího ověřování pomocí Azure AD Connect

Tuto metodu použijte, pokud jste původně nakonfigurovali AD FS prostředí pomocí Azure AD Connect. Tuto metodu nemůžete použít, pokud *jste původně nenakonfigurovali* AD FS prostředí pomocí Azure AD Connect.

> [!IMPORTANT]
> Po dokončení následujících kroků se všechny domény převedou z federované identity na spravovanou identitu. Další informace najdete v [části plánování metody migrace](#plan-the-migration-method).

Nejprve změňte metodu přihlašování:

1. Na serveru Azure AD Connect otevřete Průvodce Azure AD Connect.
2. Vyberte možnost **změnit přihlášení uživatele**a pak vyberte možnost **Další**. 
3. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo účtu globálního správce.
4. Na **přihlašovací stránce uživatele** vyberte tlačítko **předávací ověřování** , vyberte **Povolit jednotné přihlašování**a pak vyberte **Další**.
5. Na stránce **Povolit jednotné přihlašování** zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   > [!NOTE]
   > K povolení bezproblémového jednotného přihlašování se vyžadují přihlašovací údaje účtu správce domény. Proces provede následující akce, které vyžadují tato oprávnění vyšší úrovně. Přihlašovací údaje účtu správce domény nejsou uložené v Azure AD Connect ani ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají jenom k zapnutí této funkce. Po úspěšném dokončení procesu se přihlašovací údaje zahodí.
   >
   > 1. Účet počítače s názvem AZUREADSSOACC (který představuje Azure AD) se vytvoří v místní instanci služby Active Directory.
   > 2. Dešifrovací klíč protokolu Kerberos účtu počítače se bezpečně sdílí se službou Azure AD.
   > 3. Vytvoří se dva hlavní názvy služby (SPN) protokolu Kerberos, které budou představovat dvě adresy URL používané během přihlašování pomocí Azure AD.

6. Na stránce **připraveno ke konfiguraci** se ujistěte, že je zaškrtnuté políčko **spustit proces synchronizace po dokončení konfigurace** . Pak vyberte **Konfigurovat**.<br />

   ![Snímek obrazovky připraveno ke konfiguraci stránky](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Na portálu Azure AD vyberte **Azure Active Directory**a pak vyberte **Azure AD Connect**.
8. Ověřte tato nastavení:
   * **Federace** je nastavená na **disabled (zakázáno**).
   * **Bezproblémové jednotné přihlašování** je nastavené na **povoleno**.
   * **Předávací ověřování** je nastavené na **povoleno**.<br />

   ![Snímek obrazovky, který zobrazuje nastavení v oddílu přihlášení uživatele](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Další. Nasaďte další metody ověřování:

1. V Azure Portal klikněte na **Azure Active Directory**  >  **Azure AD Connect**a pak vyberte **předávací ověřování**.
2. Na stránce **předávací ověřování** vyberte tlačítko **Stáhnout** .
3. Na stránce **Stáhnout agenta** vyberte **přijmout podmínky a stáhnout**.

   Další ověřovací agenti začínají stahovat. Nainstalujte sekundárního ověřovacího agenta na server připojený k doméně. 

   > [!NOTE]
   > První Agent je vždy nainstalován na samotném serveru Azure AD Connect jako součást změn konfigurace v části **přihlášení uživatele** v nástroji Azure AD Connect. Nainstalujte všechny další ověřovací agenty na samostatný server. Doporučujeme, abyste měli k dispozici dva nebo tři další ověřovací agenty. 

4. Spusťte instalaci ověřovacího agenta. Během instalace musíte zadat přihlašovací údaje účtu globálního správce.

   ![Snímek obrazovky, který zobrazuje tlačítko instalovat na stránce balíčku Microsoft Azure AD připojit ověřovacího agenta](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Snímek obrazovky zobrazující přihlašovací stránku](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Po nainstalování ověřovacího agenta se můžete vrátit na stránku stav předávacího agenta pro ověřování a ověřit stav dalších agentů.

Přejděte k [testování a dalším krokům](#testing-and-next-steps).

> [!IMPORTANT]
> Přeskočte **možnost oddílu B: přepnutí z federace na předávací ověřování pomocí Azure AD Connect a PowerShellu**. Kroky v této části se nepoužívají, pokud jste zvolili možnost A, chcete-li změnit metodu přihlašování na předávací ověřování a povolit bezproblémové jednotné přihlašování. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Možnost B: přepnutí z federace na předávací ověřování pomocí Azure AD Connect a PowerShellu

Tuto možnost použijte, pokud jste původně nenakonfigurovali federované domény pomocí Azure AD Connect.

Nejdřív povolte předávací ověřování:

1. Na serveru Azure AD Connect otevřete Průvodce Azure AD Connect.
2. Vyberte možnost **změnit přihlášení uživatele**a pak vyberte možnost **Další**.
3. Na stránce **připojit ke službě Azure AD** zadejte uživatelské jméno a heslo účtu globálního správce.
4. Na **přihlašovací stránce uživatele** vyberte tlačítko **předávací ověřování** . Vyberte **Povolit jednotné přihlašování**a pak vyberte **Další**.
5. Na stránce **Povolit jednotné přihlašování** zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   > [!NOTE]
   > K povolení bezproblémového jednotného přihlašování se vyžadují přihlašovací údaje účtu správce domény. Proces provede následující akce, které vyžadují tato oprávnění vyšší úrovně. Přihlašovací údaje účtu správce domény nejsou uložené v Azure AD Connect ani ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají jenom k zapnutí této funkce. Po úspěšném dokončení procesu se přihlašovací údaje zahodí.
   > 
   > 1. Účet počítače s názvem AZUREADSSOACC (který představuje Azure AD) se vytvoří v místní instanci služby Active Directory.
   > 2. Dešifrovací klíč protokolu Kerberos účtu počítače se bezpečně sdílí se službou Azure AD.
   > 3. Vytvoří se dva hlavní názvy služby (SPN) protokolu Kerberos, které budou představovat dvě adresy URL používané během přihlašování pomocí Azure AD.

6. Na stránce **připraveno ke konfiguraci** se ujistěte, že je zaškrtnuté políčko **spustit proces synchronizace po dokončení konfigurace** . Pak vyberte **Konfigurovat**.<br />

   ![Snímek obrazovky, který ukazuje stránku připravenou ke konfiguraci a tlačítko konfigurovat](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Pokud vyberete **Konfigurovat**, dojde k následujícím krokům:

   1. Je nainstalován první agent předávacího ověřování.
   2. Funkce předávacího předávání je povolena.
   3. Bezproblémové jednotné přihlašování je povolené.

7. Ověřte tato nastavení:
   * U **federace** je nastavená možnost **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastavené na **povoleno**.
   * **Předávací ověřování** je nastavené na **povoleno**.
   
   ![Snímek obrazovky, který zobrazuje nastavení v oddílu přihlášení uživatele](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Vyberte **předávací ověřování** a ověřte, jestli je stav **aktivní**.<br />
   
   Pokud není ověřovací agent aktivní, před pokračováním v dalším kroku dokončete některé [kroky pro řešení potíží](./tshoot-connect-pass-through-authentication.md) . Když převedete své domény, riskujete, že při jejich převádění dojde k výpadku při ověřování, kdy se úspěšně nainstalují agenti předávaných ověřování a že jejich stav je **aktivní** v Azure Portal.

Dále nasaďte další ověřovací agenty:

1. V Azure Portal klikněte na **Azure Active Directory**  >  **Azure AD Connect**a pak vyberte **předávací ověřování**.
2. Na stránce **předávací ověřování** vyberte tlačítko **Stáhnout** . 
3. Na stránce **Stáhnout agenta** vyberte **přijmout podmínky a stáhnout**.
 
   Agent ověřování začne stahovat. Nainstalujte sekundárního ověřovacího agenta na server připojený k doméně.

   > [!NOTE]
   > První Agent je vždy nainstalován na samotném serveru Azure AD Connect jako součást změn konfigurace v části **přihlášení uživatele** v nástroji Azure AD Connect. Nainstalujte všechny další ověřovací agenty na samostatný server. Doporučujeme, abyste měli k dispozici dva nebo tři další ověřovací agenty.
 
4. Spusťte instalaci ověřovacího agenta. Během instalace musíte zadat přihlašovací údaje účtu globálního správce.<br />

   ![Snímek obrazovky, který zobrazuje tlačítko instalovat na stránce balíčku Microsoft Azure AD připojit ověřovacího agenta](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Snímek obrazovky zobrazující přihlašovací stránku](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Po nainstalování ověřovacího agenta se můžete vrátit na stránku stav předávacího agenta pro předávání a ověřit stav dalších agentů.

V tuto chvíli je federované ověřování pro vaše domény pořád aktivní a funkční. Pokud chcete v nasazení pokračovat, musíte převést každou doménu ze federované identity na spravovanou identitu, aby předávací ověřování spouštělo žádosti o ověření pro doménu.

Nemusíte převádět všechny domény současně. Můžete začít s testovací doménou v produkčním tenantovi nebo začít s doménou, která má nejnižší počet uživatelů.

Dokončete převod pomocí modulu Azure AD PowerShell:

1. V PowerShellu se přihlaste ke službě Azure AD pomocí účtu globálního správce.
2. Chcete-li převést první doménu, spusťte následující příkaz:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Na portálu Azure AD vyberte **Azure Active Directory**  >  **Azure AD Connect**.
4. Po převedení všech federovaných domén ověřte tato nastavení:
   * **Federace** je nastavená na **disabled (zakázáno**).
   * **Bezproblémové jednotné přihlašování** je nastavené na **povoleno**.
   * **Předávací ověřování** je nastavené na **povoleno**.<br />

   ![Snímek obrazovky, který zobrazuje nastavení v oddílu přihlášení uživatele](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testování a další kroky

Provedením následujících kroků ověříte předávací ověřování a dokončete proces převodu.

### <a name="test-pass-through-authentication"></a>Test předávacího ověřování 

Když váš tenant použil federované identity, přesměruje se uživatelé na přihlašovací stránku Azure AD na vaše AD FS prostředí. Teď, když je tenant nakonfigurovaný tak, aby používal předávací ověřování místo federovaného ověřování, nebudou uživatelé přesměrováni na AD FS. Místo toho se uživatelé přihlásí přímo na přihlašovací stránce služby Azure AD.

Postup testování předávacího ověřování:

1. Spusťte Internet Explorer v režimu InPrivate, aby vás bez problémů přihlašování automaticky přihlásilo.
2. Přejít na přihlašovací stránku Office 365 ( [https://portal.office.com](https://portal.office.com/) ).
3. Zadejte hlavní název uživatele (UPN) a pak vyberte **Další**. Ujistěte se, že zadáváte hlavní název uživatele (UPN), který byl synchronizovaný z místní instance služby Active Directory a který dřív používal federované ověřování. Zobrazí se stránka, na které zadáte uživatelské jméno a heslo:

   ![Snímek obrazovky zobrazující přihlašovací stránku, na které zadáte uživatelské jméno](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Snímek obrazovky zobrazující přihlašovací stránku, na které zadáváte heslo](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Po zadání hesla a výběru **Přihlásit**jste přesměrováni na portál Office 365.

   ![Snímek obrazovky zobrazující portál Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Plynulé testování jednotného přihlašování

K otestování bezproblémového jednotného přihlašování:

1. Přihlaste se k počítači připojenému k doméně, který je připojený k podnikové síti.
2. V aplikaci Internet Explorer nebo Chrome použijte jednu z následujících adres URL (v doméně nahraďte "contoso"):

   * https: \/ \/ myapps.Microsoft.com/contoso.com
   * https: \/ \/ myapps.Microsoft.com/contoso.onmicrosoft.com

   Uživatel se krátce přesměruje na přihlašovací stránku služby Azure AD, kde se zobrazí zpráva "Probíhá pokus o přihlášení". Uživateli se nezobrazí výzva k zadání uživatelského jména nebo hesla.<br />

   ![Snímek obrazovky, který zobrazuje přihlašovací stránku a zprávu služby Azure AD](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
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

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Převeďte bezproblémové dešifrovací klíč Kerberos pro jednotné přihlašování.

Je důležité často vymezit dešifrovací klíč protokolu Kerberos účtu počítače AZUREADSSOACC (který představuje Azure AD). Účet počítače AZUREADSSOACC se vytvoří v místní doménové struktuře služby Active Directory. Důrazně doporučujeme, abyste převzali šifrovací klíč protokolu Kerberos aspoň každých 30 dní, abyste se zarovnali se způsobem, jakým členové domény služby Active Directory odesílají změny hesel. K objektu účtu počítače AZUREADSSOACC není připojené žádné přidružené zařízení, takže je potřeba provést ruční přecházení.

Zahajte výměnu nebezproblémového dešifrovacího klíče Kerberos pro jednotné přihlašování na místním serveru, na kterém běží Azure AD Connect.

Další informace najdete v tématu [návody převádění dešifrovacího klíče protokolu Kerberos účtu počítače s AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

Monitorujte servery, na kterých běží agenti ověřování, a udržujte dostupnost řešení. Kromě obecných čítačů výkonu serveru zpřístupňují agenti ověřování objekty výkonu, které vám pomůžou pochopit údaje o ověřování a chybách.

Agenti ověřování protokolují operace do protokolů událostí systému Windows, které jsou umístěny v části Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Můžete také zapnout protokolování pro řešení potíží.

Další informace najdete v tématu [řešení potíží s předávacím ověřováním Azure Active Directory](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [Azure AD Connect konceptech návrhu](plan-connect-design-concepts.md).
* Vyberte [správné ověřování](./choose-ad-authn.md).
* Přečtěte si o [podporovaných topologiích](plan-connect-design-concepts.md).