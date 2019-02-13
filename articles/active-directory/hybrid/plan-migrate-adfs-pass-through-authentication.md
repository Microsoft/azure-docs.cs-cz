---
title: 'Azure AD Connect: Migrace z federace na předávací ověřování služby Azure Active Directory | Dokumentace Microsoftu'
description: Tento článek obsahuje informace o přechodu prostředí hybridní identity z federace na předávací ověřování.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0df959439eae703d18d8777e8d433e1ee176556c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184614"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrace z federace na předávací ověřování služby Azure Active Directory

Tento článek popisuje, jak přesunout domény organizace ze služby Active Directory Federation Services (AD FS) na předávací ověřování.

Je možné [stáhněte si tento článek](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Požadavky pro migraci na předávací ověřování.

Následující závislosti jsou požadovány k migraci pomocí služby AD FS na předávací ověřování.

### <a name="update-azure-ad-connect"></a>Aktualizace služby Azure AD Connect

Chcete-li úspěšně provést kroky je potřeba k migraci pomocí předávacího ověřování, musíte mít [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 nebo novější. Ve službě Azure AD Connect 1.1.819.0 způsob, jakým přihlášení převod je výrazně provést změny. Celkový čas migrace ze služby AD FS do cloudového ověřování v této verzi je omezený ze potenciálně hodin, minut.

> [!IMPORTANT]
> Může číst v dokumentaci k zastaralé, nástroje a blogy, že je uživatel převod vyžadována při převádění domén federovaných identit pro spravovanou identitu. *Převod uživatelů* se už nevyžaduje. Microsoft pracuje na aktualizaci dokumentace a nástroje tak, aby odrážela tuto změnu.

Pokud chcete aktualizovat Azure AD Connect, proveďte kroky v [Azure AD Connect: Upgrade na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Plán ověření agenta počet a umístění

Předávací ověřování vyžaduje nasazení jednoduché agenty na serveru služby Azure AD Connect a v místním počítači, na kterém běží Windows Server. Pokud chcete snížit latenci, nainstalujte agenty co nejblíže k vašim řadičům domény služby Active Directory.

Pro většinu zákazníků dvě nebo tři agenti ověřování jsou dostatečná pro poskytování vysoké dostupnosti a požadovanou kapacitou. Tenanta může mít nejvýše 12 agenti zaregistrovaní. První agent je nainstalována vždy na vlastním serveru služby Azure AD Connect. Další informace o omezení agenta a možnosti nasazení agenta najdete v tématu [předávací ověřování služby Azure AD: Aktuální omezení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations).

### <a name="plan-the-migration-method"></a>Plánování migrace – metoda

Můžete ze dvou metod pro migraci ze správy federovaných identit k předávacímu ověřování a bezproblémové jednotné přihlašování (SSO). Metodu, kterou používáte, závisí na původně konfiguraci vaší instance služby AD FS.

* **Azure AD Connect**. Pokud jste nakonfigurovali službu AD FS pomocí služby Azure AD Connect, můžete *musí* změnit pomocí Průvodce Azure AD Connect pro předávací ověřování.

   Azure AD Connect se automaticky spustí **Set-MsolDomainAuthentication** rutiny, když se změní metodu přihlašování uživatele. Azure AD Connect unfederates automaticky všechny ověřené federovaných domén ve vašem tenantovi Azure AD.

   > [!NOTE]
   > Nyní Pokud jste původně použili ke konfiguraci služby AD FS Azure AD Connect, nelze neměli unfederating všechny domény ve vašem tenantovi, když změníte přihlášení uživatele na předávací ověřování.
‎
* **Azure AD Connect s prostředím PowerShell**. Tuto metodu můžete použít pouze v případě, že nebyla původně konfiguraci služby AD FS pomocí služby Azure AD Connect. Pro tuto možnost stále musíte změnit uživatele přihlásit metodu prostřednictvím Průvodce Azure AD Connect. Základní rozdíl s touto možností je, že průvodce nespustí automaticky **Set-MsolDomainAuthentication** rutiny. Pomocí této možnosti máte plnou kontrolu nad které domény budou převedeny a v jakém pořadí.

Pokud chcete pochopit, jakou metodu použijete, proveďte kroky v následujících částech.

#### <a name="verify-current-user-sign-in-settings"></a>Ověřte nastavení aktuálního uživatele přihlášení

1. Přihlaste se k [portálu Azure AD](https://aad.portal.azure.com/) pomocí účtu globálního správce.
2. V **přihlášení uživatele** části, ověřte následující nastavení:
   * **Federace** je nastavena na **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastavena na **zakázané**.
   * **Předávací ověřování** je nastavena na **zakázané**.

   ![Snímek obrazovky nastavení v části přihlášení uživatele Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Ověření konfigurace federace

1. Na serveru služby Azure AD Connect otevřete Azure AD Connect. Vyberte **nakonfigurovat**.
2. Na **další úkoly** stránce **zobrazit aktuální konfiguraci**a pak vyberte **Další**.<br />
 
   ![Snímek obrazovky zobrazení aktuální konfigurace možnost na stránce další úkoly](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Na **Kontrola řešení** stránce, přejděte k položce **Active Directory Federation Services (AD FS)**.<br />

   * V případě, že v této části se zobrazí konfiguraci služby AD FS, můžete bezpečně předpokládat, že služba AD FS byla původně nakonfigurován s použitím služby Azure AD Connect. Můžete převést domény z federovaných identit na spravovanou identitu pomocí Azure AD Connect **změnit přihlášení uživatele** možnost. Další informace o procesu najdete v části **možnost 1: Konfigurace předávacího ověřování s použitím služby Azure AD Connect**.
   * Pokud službu AD FS není uveden v aktuální nastavení, je nutné ručně převést domén z federovaných identit na spravovanou identitu pomocí prostředí PowerShell. Další informace o tomto procesu najdete v části **možnost 2: Přepnutí z federace na předávací ověřování pomocí Azure AD Connect a prostředí PowerShell**.

### <a name="document-current-federation-settings"></a>Aktuální nastavení federace dokumentu

Chcete-li najít vaše aktuální nastavení federace, spusťte **příkazu Get-MsolDomainFederationSettings** rutiny:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Příklad:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Zkontrolujte všechna nastavení, která může přizpůsobit pro dokumentaci návrhu a nasazení federace. Hledejte především provádět přizpůsobení v **PreferredAuthenticationProtocol**, **SupportsMfa**, a **PromptLoginBehavior**.

Další informace najdete v těchto článcích:

* [Služba AD FS řádku = Podpora parametrů přihlášení](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Pokud **SupportsMfa** je nastavena na **True**, používáte místním řešením ověřování službou Multi-Factor Authentication dvouúrovňové challenge vložení do tok ověřování uživatele. Toto nastavení nefunguje pro scénáře ověřování služby Azure AD. 
>
> Místo toho použijte cloudovou službu Azure Multi-Factor Authentication k provedení stejné funkce. Než budete pokračovat, pečlivě vyhodnoťte vaše požadavky na vícefaktorové ověřování. Před převedením domény, ujistěte se, že chápete, jak používat Azure Multi-Factor Authentication, dopadům na licencování a proces registrace uživatele.

#### <a name="back-up-federation-settings"></a>Zálohování nastavení federace

I když se k jiné předávající strany ve vaší farmě služby AD FS nebudou provedeny žádné změny během postupů popsaných v tomto článku, doporučujeme, že máte aktuální platné zálohy, kterou můžete obnovit z farmy služby AD FS. Můžete vytvořit zálohu aktuální platný pomocí bezplatné aplikace Microsoft [AD FS rychlé obnovení nástroj](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Vám pomůže nástroj pro zálohování služby AD FS a obnovit existující farmě nebo vytvořte novou farmu.

Pokud se rozhodnete nepoužívat AD FS rychlý nástroj obnovení, minimálně, je třeba exportovat Office 365 platforma Microsoft Identity předávající straně důvěryhodnosti a všechny přidružené vlastní pravidla deklarací identity, které jste přidali. Vztah důvěryhodnosti předávající strany a pravidel deklarací identity přidružené můžete exportovat pomocí následující příklad Powershellu:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Důležité informace o nasazení a pomocí služby AD FS

Tato část popisuje důležité informace o nasazení a podrobnosti o použití služby AD FS.

### <a name="current-ad-fs-use"></a>Použít aktuální služby AD FS

Předtím, než převedete z federovaných identit na spravovanou identitu, prohlédněte si blíže jak aktuálně používáte službu AD FS pro službu Azure AD, Office 365 a dalších aplikací (vztahy důvěryhodnosti předávající strany). Konkrétně zvažte scénáře, které jsou popsány v následující tabulce:

| If | Potom |
|-|-|
| Plánujete dál používat službu AD FS s jinými aplikacemi (jiné než Azure AD a Office 365). | Po převodu domény použijete službu AD FS a Azure AD. Zvažte uživatelské prostředí. V některých scénářích, uživatelé musí pravděpodobně k ověření dvakrát: jednou do služby Azure AD (ve kterém uživatel získá přístup přes jednotné přihlašování k ostatním aplikacím, jako je Office 365) a opakujte pro všechny aplikace, které jsou stále vázaná na službu AD FS jako vztah důvěryhodnosti předávající strany. |
| Vaše instance služby AD FS je silně přizpůsobený a závisí na konkrétní vlastní nastavení v souboru onload.js (např. Pokud jste změnili přihlašovací prostředí tak, aby uživatelé používat pouze **SamAccountName** formát pro své uživatelské jméno Namísto uživatele hlavní název (UPN), nebo vaše organizace má silně pod značkou jiných přihlašovací prostředí). Soubor onload.js nemůže být duplicitní ve službě Azure AD. | Než budete pokračovat, je nutné ověřit, že Azure AD můžete požadavkům vaší aktuální vlastní nastavení. Další informace a pokyny najdete v částech o značku služby AD FS a vlastního nastavení služby AD FS.|
| Použití služby AD FS pro blokování starší verze ověřování klientů.| Zvažte možnost nahrazení služby AD FS ovládacích prvků, které blokují starší klienti ověřování s použitím kombinace [řízení podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) a [Exchange Online pravidla klientského přístupu](http://aka.ms/EXOCAR). |
| Vyžadujete, aby uživatelům provádět ověřování službou Multi-Factor Authentication u místního ověřování službou Multi-Factor Authentication server řešení při ověřování uživatele se službou AD FS.| V doméně spravovanou identitu nelze vložit výzvu ověřování službou Multi-Factor Authentication prostřednictvím místní řešení vícefaktorového ověřování do tok ověřování. Můžete však použít službu Azure Multi-Factor Authentication pro ověřování službou Multi-Factor Authentication po převodu domény.<br /><br /> Pokud uživatele není aktuálně používat ověřování Azure Multi-Factor Authentication, je vyžadována registračního kroku jednorázově uživatele. Musíte připravit a komunikaci plánované registrace pro vaše uživatele. |
| Aktuálně používáte zásady řízení přístupu (pravidel AuthZ) ve službě AD FS pro řízení přístupu k Office 365.| Zvažte nahrazení zásady s Azure AD ekvivalentní [zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) a [Exchange Online pravidla klientského přístupu](http://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Běžné vlastní nastavení služby AD FS

Tato část popisuje běžné vlastní nastavení služby AD FS.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork claim

Problémy s AD FS **InsideCorporateNetwork** deklarace identity, pokud uživatel, který ověřuje je uvnitř podnikové sítě. Tato deklarace identity je pak možné předat službě Azure AD. Deklarace identity se používá k obejít ověřování Multi-Factor Authentication podle umístění uživatele v síti. Zjistěte, jak určit, zda tato funkce je aktuálně dostupná ve službě AD FS, najdete v článku [důvěryhodné IP adresy pro federované uživatele](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

**InsideCorporateNetwork** deklarace identity není k dispozici po domén se převedou na předávací ověřování. Můžete použít [pojmenovaná umístění ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) nahradit tuto funkci.

Po dokončení konfigurace pojmenovaná umístění, je nutné aktualizovat všechny zásady podmíněného přístupu, které byly nakonfigurované pro zahrnutí nebo vyloučení síti **všechna důvěryhodná umístění** nebo **důvěryhodné IP adresy MFA** hodnoty odrážet nové pojmenovaná umístění.

Další informace o **umístění** podmínka podmíněného přístupu, přečtěte si téma [podmíněný přístup k umístění služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní Azure zařízení připojených k doméně AD

Při připojení zařízení k Azure AD, můžete vytvořit pravidla podmíněného přístupu, které zajišťují, že zařízení splňují vaše standardy zabezpečení a dodržování předpisů přístup. Také uživatelé můžou přihlásit k zařízení pomocí organizace pracovní nebo školní účet místo osobního účtu. Při použití Azure hybridních zařízení připojených k doméně AD vás vaše zařízení připojených k doméně služby Active Directory připojit k Azure AD. Federovaném prostředí může mít nastavený pro použití této funkce.

Pokud chcete zajistit, že připojení k hybridní službě i nadále fungovat pro jakékoli zařízení, která jsou připojená k doméně, po domén se převedou na předávací ověřování klientů s Windows 10, je nutné použít Azure AD Connect pro synchronizaci účtů počítače služby Active Directory do služby Azure AD.

Pro účty počítačů Windows 8 a Windows 7 připojení k hybridní službě pomocí bezproblémového jednotného přihlašování k registraci počítače ve službě Azure AD. Není nutné synchronizace systému Windows 8 a Windows 7 účty počítačů stejným způsobem jako pro zařízení s Windows 10. K souboru aktualizované workplacejoin.exe (prostřednictvím souboru .msi) však musíte nasadit do klientů Windows 8 a Windows 7, takže se můžete zaregistrovat pomocí bezproblémového jednotného přihlašování. [Stáhněte soubor .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Další informace najdete v tématu [hybridní konfigurace Azure zařízení připojených k doméně AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Branding

Pokud vaše organizace [přizpůsobit přihlašovací stránky služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) zobrazíte informace, které jsou vhodnější pro organizaci zvažte učinění podobné [přizpůsobení přihlašovací stránky Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

I když jsou k dispozici podobné přizpůsobení, třeba po převodu očekávat některé vizuální změny na přihlašovací stránky. Můžete chtít poskytnout uživatelům informace o očekávané změny v komunikace.

> [!NOTE]
> Branding organizace je k dispozici jenom v případě, že zakoupíte licence Premium nebo Basic služby Azure Active Directory nebo pokud máte licenci Office 365.

## <a name="plan-for-smart-lockout"></a>Plán pro inteligentní uzamčení

Inteligentní uzamčení služby Azure AD chrání před útoky na hesla hrubou silou. Inteligentní uzamčení brání uzamknutí, pokud se používá předávací ověřování a zásady skupiny uzamčení účtu je nastavena ve službě Active Directory účet místní služby Active Directory.

Další informace najdete v tématu [inteligentním uzamčením Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="plan-deployment-and-support"></a>Plánování nasazení a podpora

Dokončení úkolů, které jsou popsané v této části vám pomohou při plánování nasazení a podporu.

### <a name="plan-the-maintenance-window"></a>Naplánovat časové období údržby

I když proces převodu domény je poměrně rychlé, Azure AD může nadále odesílat požadavky na ověření na servery služby AD FS po dobu až čtyř hodin po dokončení převodu domény. Během tohoto časového období 4 hodiny a v závislosti na různých mezipaměti na straně služby Azure AD nemusí přijmout tyto ověření. Uživatelům se může zobrazit chyba. Uživatel může přesto úspěšně ověřování na základě služby AD FS, ale Azure AD již přijímá uživatele vydaný token, protože tento vztah důvěryhodnosti federace je odebraná.

Jenom uživatelé, kteří přístup ke službám prostřednictvím webového prohlížeče během tohoto časového intervalu po převodu před nevymažete mezipaměť na straně služby jsou ovlivněny. Starší verze klientů (Exchange ActiveSync, aplikace Outlook 2010 nebo 2013) se nepředpokládá vliv, protože Exchange Online udržuje mezipaměť přihlašovacích údajů pro nastaveném časovém období. Mezipaměť se používá bez upozornění uživatele donutit k. Uživatel nebude muset vrátit do služby AD FS. Přihlašovací údaje uložené v zařízení pro tyto klienty se používají k tiše sami donutit po to do mezipaměti je zrušeno. Uživatelé se nepředpokládá přijímat případné výzvy heslo jako výsledek procesu převodu domény.

Klienti moderního ověřování (Office 2016 a Office 2013, iOS a aplikace pro Android) použít platný obnovovací token k získání nových přístupových tokenů pro přístup k prostředkům místo vrácení se službou AD FS. Tito klienti jsou imunní případné výzvy heslo vyplývající z procesu převodu domény. Klienti budou dál fungovat bez další konfigurace.

> [!IMPORTANT]
> Nemáte vypnout prostředí služby AD FS nebo odeberte vztah důvěryhodnosti předávající strany Office 365, až si ověříte, že všichni uživatelé můžete provádět ověření pomocí cloudového ověřování.

### <a name="plan-for-rollback"></a>Plán pro vrácení zpět

Pokud narazíte na závažný problém, který nelze přeložit rychle, můžete se rozhodnout pro vrácení zpět řešení federace. Je důležité mít plán, jak postupovat, pokud vaše nasazení není možné zahrnout tak, jak má. Pokud převod domény nebo uživatelé selže při nasazování nebo pokud je potřeba vrátit zpět do federace, musíte pochopit, jak zmírnit jakémkoli výpadku a snižuje vliv na vaši uživatelé.

#### <a name="to-roll-back"></a>Chcete-li vrátit zpět

Plánování pro vrácení zpět, zkontrolujte federace návrhu a nasazení vaší konkrétní nasazení. Podrobnosti naleznete v dokumentaci. Proces by měl obsahovat tyto úlohy:

* Převodu spravovaných domén federovaných domén pomocí **Convert-MSOLDomainToFederated** rutiny.
* V případě potřeby konfiguraci další pravidla deklarací identity.

### <a name="plan-communications"></a>Plánování komunikace

Důležitou součástí plánování nasazení a podporu zajišťuje, že jsou vaši uživatelé aktivně informovat o nadcházejících změnách. Uživatelé měli předem vědět, co může docházet a co je potřeba z nich.

Po nasazení předávací ověřování a bezproblémové jednotné přihlašování uživatele přihlašovací prostředí pro přístup k Office 365 a dalších prostředků, která se ověřuje prostřednictvím služby Azure AD se změní. Uživatelé, kteří jsou mimo síť najdete v článku pouze služby Azure AD přihlašovací stránky. Tito uživatelé přesměrování na stránku, který je předkládán externího webové aplikace proxy servery založené na formulářích.

Strategie komunikace obsahovat následující prvky:

* Upozornit uživatele na nadcházející a vydávají se funkce s použitím:
   * E-mailu a dalších interní komunikační kanály.
   * Vizuály, jako je například plakáty.
   * Výkonný, live nebo jiné sdělení.
* Určit, který přizpůsobí komunikace a který bude odesílat komunikace a kdy.

## <a name="implement-your-solution"></a>Implementovat řešení

Jste naplánovali vaše řešení. Nyní teď můžete implementovat ho. Implementace zahrnuje následující součásti:

* Probíhá příprava na bezproblémového jednotného přihlašování.
* Změna metody přihlašování předávací ověřování a umožnění bezproblémového jednotného přihlašování.

### <a name="step-1-prepare-for-seamless-sso"></a>Krok 1: Příprava pro bezproblémové jednotné přihlašování

Pro svoje zařízení, abyste pomocí bezproblémového jednotného přihlašování musíte přidat adresu URL Azure AD do nastavení zóny intranet uživatele prostřednictvím zásad skupiny ve službě Active Directory.

Ve výchozím nastavení, webové prohlížeče automaticky vypočítat správné zóny Internetu nebo intranetu, z adresy URL. Například **http:\/\/contoso /** mapuje na zóny intranetu a **http:\/\/intranet.contoso.com** (protože se mapuje na zónu Internetu Adresa URL obsahuje tečku). Pouze v případě, že explicitně přidat adresu URL do prohlížeče zóny intranetu prohlížeče odesílají lístky protokolu Kerberos na koncového bodu cloudu, podobně jako adresu URL Azure AD.

Dokončete postup [zavádět](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) požadované změny pro vaše zařízení.

> [!IMPORTANT]
> Touto změnou nemění způsob, jakým uživatelé přihlášení ke službě Azure AD. Je ale důležité použití této konfigurace pro všechna vaše zařízení, než budete pokračovat. Uživatelé, kteří se přihlaste se na zařízení, která tuto konfiguraci neobdrželi jednoduše je potřeba zadat uživatelské jméno a heslo pro přihlášení k Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Krok 2: Změňte metodu přihlašování na předávací ověřování a bezproblémové jednotné přihlašování

Máte dvě možnosti pro změnu metodu přihlašování na předávací ověřování a povolením bezproblémového jednotného přihlašování.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Možnost A: Konfigurace předávacího ověřování s použitím služby Azure AD Connect

Tuto metodu použijte, pokud je ve výchozím nastavení vašeho prostředí služby AD FS pomocí služby Azure AD Connect. Tuto metodu nelze použít, pokud jste *neměli* původní konfiguraci vašeho prostředí služby AD FS pomocí služby Azure AD Connect.

> [!IMPORTANT]
> Po dokončení následujících kroků všechny domény budou převedeny z federovaných identit na spravovanou identitu. Další informace najdete v tématu [plánování migrace metoda](#plan-the-migration-method).

Nejprve změňte metodu přihlašování:

1. Na serveru služby Azure AD Connect otevřete Průvodce Azure AD Connect.
2. Vyberte **změnit přihlášení uživatele**a pak vyberte **Další**. 
3. Na **připojit ke službě Azure AD** stránky, zadejte uživatelské jméno a heslo pro účet globálního správce.
4. Na **přihlášení uživatele** stránky, vyberte **předávací ověřování** tlačítka, vyberte **povolit jednotné přihlašování**a pak vyberte **Další**.
5. Na **povolit jednotné přihlašování** stránky, zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   > [!NOTE]
   > Umožňuje bezproblémové jednotné přihlašování se vyžadují přihlašovací údaje účtu správce domény. Dokončení procesu následující akce, které vyžadují tyto zvýšenou úroveň oprávnění. Přihlašovací údaje účtu správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají pouze k zapnutí funkce. Přihlašovací údaje se zahodí, až se proces úspěšně dokončí.
   >
   > 1. Účet počítače s názvem AZUREADSSOACC (která představuje Azure AD) se vytvoří v místní instanci Active Directory.
   > 2. Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
   > 3. K reprezentaci dvě adresy URL, které se používají při přihlášení k Azure AD jsou vytvořeny dva Kerberos hlavních názvů služby (SPN).

6. Na **připraveno ke konfiguraci** stránky, ujistěte se, že **po dokončení konfigurace spustit proces synchronizace** je zaškrtnuto políčko. Vyberte **konfigurovat**.<br />

   ![Snímek obrazovky stránce Připraveno ke konfiguraci](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Na portálu Azure AD, vyberte **Azure Active Directory**a pak vyberte **Azure AD Connect**.
8. Ověřte tato nastavení:
  * **Federace** je nastavena na **zakázané**.
  * **Bezproblémové jednotné přihlašování** je nastavena na **povoleno**.
  * **Předávací ověřování** je nastavena na **povoleno**.<br />

  ![Snímek obrazovky, který zobrazuje nastavení v části přihlášení uživatele](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Další. nasazení dalších metod ověřování:

1. Na webu Azure Portal, přejděte na **Azure Active Directory** > **Azure AD Connect**a pak vyberte **předávací ověřování**.
2. Na **předávací ověřování** stránky, vyberte **Stáhnout** tlačítko.
3. Na **stáhnout agenta** stránce **přijměte podmínky a stáhnout**.

  Zahájit stahování agentů další ověřování. Nainstalujte sekundární ověřovací agent na serveru připojeném k doméně. 

  > [!NOTE]
  > První agent je vždy nainstalován na Azure AD Connect serveru jako součást konfigurace změn provedených v **přihlášení uživatele** části nástroje Azure AD Connect. Nainstalujte agenty žádné další ověření na samostatném serveru. Doporučujeme, abyste měli dva nebo tři agenti dodatečného ověřování k dispozici. 

4. Spusťte instalaci agenta ověřování. Během instalace musíte zadat přihlašovací údaje účtu globálního správce.

  ![Snímek obrazovky zobrazující tlačítko instalovat na stránce balíček agenta služby Microsoft Azure AD Connect ověřování](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

  ![Snímek obrazovky zobrazující přihlašovací stránku](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Pokud je nainstalován agent ověřování, může vrátit na stránku stavu agenta předávací ověřování a kontrolovat stav dalších agentů.

Přejděte k [testování a další kroky](#testing-and-next-steps).

> [!IMPORTANT]
> Přeskočit část **možnost B: Přepnutí z federace na předávací ověřování pomocí Azure AD Connect a prostředí PowerShell**. Pokud jste zvolili možnost A změňte metodu přihlašování na předávací ověřování a bezproblémové jednotné přihlašování, kroky v této části se nevztahují. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Možnost B: Přepnutí z federace na předávací ověřování pomocí Azure AD Connect a prostředí PowerShell

Tuto možnost použijte, pokud nebyla původně konfiguraci federovaných domén pomocí Azure AD Connect.

Nejprve povolte předávací ověřování:

1. V Azure AD Connect serveru spusťte Průvodce Azure AD Connect.
2. Vyberte **změnit přihlášení uživatele**a pak vyberte **Další**.
3. Na **připojit ke službě Azure AD** stránky, zadejte uživatelské jméno a heslo pro účet globálního správce.
4. Na **přihlášení uživatele** stránky, vyberte **předávací ověřování** tlačítko. Vyberte **povolit jednotné přihlašování**a pak vyberte **Další**.
5. Na **povolit jednotné přihlašování** stránky, zadejte přihlašovací údaje účtu správce domény a pak vyberte **Další**.

   > [!NOTE]
   > Umožňuje bezproblémové jednotné přihlašování se vyžadují přihlašovací údaje účtu správce domény. Dokončení procesu následující akce, které vyžadují tyto zvýšenou úroveň oprávnění. Přihlašovací údaje účtu správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Přihlašovací údaje účtu správce domény se používají pouze k zapnutí funkce. Přihlašovací údaje se zahodí, až se proces úspěšně dokončí.
   > 
   > 1. Účet počítače s názvem AZUREADSSOACC (která představuje Azure AD) se vytvoří v místní instanci Active Directory.
   > 2. Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
   > 3. K reprezentaci dvě adresy URL, které se používají při přihlášení k Azure AD jsou vytvořeny dva Kerberos hlavních názvů služby (SPN).

6. Na **připraveno ke konfiguraci** stránky, ujistěte se, že **po dokončení konfigurace spustit proces synchronizace** je zaškrtnuto políčko. Vyberte **konfigurovat**.<br />

   ‎![Snímek obrazovky zobrazující připraveno ke konfiguraci stránky a tlačítka Konfigurovat](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Když vyberete dojde k následujícím krokům **konfigurovat**:

   1. První agent předávací ověřování je nainstalovaný.
   2. Předávací funkce je povolená.
   3. Bezproblémové jednotné přihlašování je povolený.

7. Ověřte tato nastavení:
   * **Federace** je nastavena na **povoleno**.
   * **Bezproblémové jednotné přihlašování** je nastavena na **povoleno**.
   * **Předávací ověřování** je nastavena na **povoleno**.
   
   ![Snímek obrazovky, který zobrazuje nastavení v části přihlášení uživatele](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Vyberte **předávací ověřování** a ověřte, zda je stav **aktivní**.<br />
   
   Pokud se ověřovací agent není aktivní, provádět některé [kroků pro řešení potíží](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) předtím, než budete pokračovat s procesem převodu domény v dalším kroku. Je tady riziko, způsobí výpadek ověřování při převodu domény předtím, než ověříte, že jsou úspěšně nainstalováni agenti vaše předávací ověřování a že jejich stav **aktivní** na webu Azure Portal.

V dalším kroku nasaďte další ověření agentů:

1. Na webu Azure Portal, přejděte na **Azure Active Directory** > **Azure AD Connect**a pak vyberte **předávací ověřování**.
2. Na **předávací ověřování** stránky, vyberte **Stáhnout** tlačítko. 
3. Na **stáhnout agenta** stránce **přijměte podmínky a stáhnout**.
 
   Ověřovací agent začne stahování. Nainstalujte sekundární ověřovací agent na serveru připojeném k doméně.

   > [!NOTE]
   > První agent je vždy nainstalován na Azure AD Connect serveru jako součást konfigurace změn provedených v **přihlášení uživatele** části nástroje Azure AD Connect. Nainstalujte agenty žádné další ověření na samostatném serveru. Doporučujeme, abyste měli dva nebo tři agenti dodatečného ověřování k dispozici.
 
4. Spusťte instalaci agenta ověřování. Během instalace musíte zadat přihlašovací údaje účtu globálního správce.<br />

   ![Snímek obrazovky zobrazující tlačítko instalovat na stránce balíček agenta služby Microsoft Azure AD Connect ověřování](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Snímek obrazovky zobrazující přihlašovací stránku](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Po dokončení instalace ověřovacího agenta může vrátit na stránku stavu agenta předávací ověřování a kontrolovat stav dalších agentů.

V tomto okamžiku federovaného ověřování je stále aktivní a provozní domén. Chcete-li pokračovat s nasazením, je nutné převést každou doménu z federovaných identit na spravovanou identitu tak, že předávací ověřování spustí poskytování obsahu žádosti o ověření domény.

Není nutné převést všechny domény ve stejnou dobu. Můžete začít s testovací doméně na produkčního tenanta nebo spustit v doméně, která má nejnižší počet uživatelů.

Dokončení převodu pomocí modulu Azure AD Powershellu:

1. V prostředí PowerShell Přihlaste se ke službě Azure AD s použitím účtu globálního správce.
2. Převést první doména, spusťte následující příkaz:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Na portálu Azure AD, vyberte **Azure Active Directory** > **Azure AD Connect**.
4. Po převodu federovaných domén, ověřte tato nastavení:
   * **Federace** je nastavena na **zakázané**.
   * **Bezproblémové jednotné přihlašování** je nastavena na **povoleno**.
   * **Předávací ověřování** je nastavena na **povoleno**.<br />

   ![Snímek obrazovky, který zobrazuje nastavení v části přihlášení uživatele](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testování a další kroky

Proveďte následující úkoly k ověření předávací ověřování a dokončit proces převodu.

### <a name="test-pass-through-authentication"></a>Test předávací ověřování 

Při použití vašeho tenanta federovanou identitu, uživatelé byli přesměrováni z na přihlašovací stránku Azure AD pro vaše prostředí služby AD FS. Teď, když klient je nakonfigurován pro použití předávací ověřování namísto federovaného ověřování, uživatelé přesměrování do služby AD FS. Místo toho uživatelé přihlašovat přímo na přihlašovací stránce služby Azure AD.

K otestování předávací ověřování:

1. Spusťte aplikaci Internet Explorer v režimu InPrivate tak, aby bezproblémového jednotného přihlašování není přihlásíte taky automaticky.
2. Přejděte na stránku pro přihlášení k Office 365 ([http://portal.office.com](http://portal.office.com/)).
3. Zadejte název uživatele (UPN) a pak vyberte **Další**. Ujistěte se, že zadáte hlavní název uživatele hybridní uživatele, který byl synchronizované z vaší místní instancí Active Directory a kteří dříve používali federovaného ověřování. Zobrazí se stránka, na kterém můžete zadat uživatelské jméno a heslo:

   ![Snímek obrazovky zobrazující přihlašovací stránku ve kterém zadáte uživatelské jméno](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Snímek obrazovky zobrazující přihlašovací stránku ve kterém můžete zadat heslo](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Po zadání hesla a vyberte **přihlášení**, budete přesměrováni na portál Office 365.

   ![Snímek obrazovky zobrazující portálu služeb Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Test bezproblémového jednotného přihlašování

K otestování bezproblémového jednotného přihlašování:

1. Přihlaste se k počítači připojeném k doméně, která je připojená k podnikové síti.
2. V aplikaci Internet Explorer nebo Chrome přejděte na jednu z následujících adres URL (nahradit "contoso" s doménou):

   * protokol https:\/\/myapps.microsoft.com/contoso.com
   * protokol https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Uživatel se stručně přesměrován na Azure AD – přihlašovací stránku, která se zobrazí zpráva "Pokusu vás přihlásit." Uživatel nebude vyzván k zadání uživatelského jména nebo hesla.<br />

   ![Snímek obrazovky zobrazující přihlašovací stránku Azure AD a zpráv](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. Uživatel se přesměruje a je úspěšně přihlášení k přístupovému panelu:

   > [!NOTE]
   > Bezproblémové jednotné přihlašování funguje na služby Office 365, které podporují nápovědu domény (například myapps.microsoft.com/contoso.com). Portál služeb Office 365 (portal.office.com) v současné době nepodporuje pomocné parametry domény. Uživatelům je potřeba zadat název UPN. Po zadání UPN bezproblémového jednotného přihlašování načte lístek protokolu Kerberos jménem uživatele. Uživatel je přihlášený bez zadávání hesla.

   > [!TIP]
   > Zvažte nasazení [připojte se k hybridní službě Azure AD na Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) zlepšit možnosti jednotného přihlašování.

### <a name="remove-the-relying-party-trust"></a>Odeberte vztah důvěryhodnosti předávající strany

Po ověření, že všichni uživatelé a klienti jsou úspěšně ověřování prostřednictvím Azure AD je bezpečného odebrání Office 365 vztah důvěryhodnosti předávající strany.

Pokud nepoužíváte služby AD FS pro jiné účely (to znamená pro jiné vztahy důvěryhodnosti předávající strany), můžete bezpečně vyřadit z provozu služby AD FS v tomto okamžiku.

### <a name="rollback"></a>Vrácení zpět

Pokud zjistíte velký problém a nejde přeložit rychle, můžete vrátit zpět řešení federace.

V dokumentaci federace návrhu a nasazení pro vaše podrobnosti o konkrétní nasazení. Proces by měl zahrnovat tyto úlohy:

* Převést spravované domény pomocí federovaného ověřování **Convert-MSOLDomainToFederated** rutiny.
* V případě potřeby nakonfigurujte další deklarace identity pravidla.

### <a name="sync-userprincipalname-updates"></a>Aktualizace synchronizace userPrincipalName

V minulosti, aktualizuje **UserPrincipalName** atribut, který používá služby sync z místního prostředí, jsou blokovány, pokud jsou splněny obě tyto podmínky:

* Uživatel je v doméně spravovanou identitu (jiné než federované).
* Uživateli nebyla přiřazena licence.

Zjistěte, jak ověřit nebo tuto funkci zapnout, najdete v článku [synchronizovat aktualizace userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Převrácení bezproblémové jednotné přihlašování Kerberos dešifrovací klíč

Je důležité často nespotřebujete dešifrovací klíč protokolu Kerberos účtu AZUREADSSOACC počítače (který představuje Azure AD). Účet počítače AZUREADSSOACC se vytvoří ve vaší místní doménové struktuře služby Active Directory. Důrazně doporučujeme, že ho znovu vygenerovat dešifrovací klíče Kerberos nejméně každých 30 dnů se zarovnají se tak, že členy domény služby Active Directory odeslat změny hesla. Neexistuje žádný přidružený zařízení připojené k AZUREADSSOACC objekt účtu počítače, takže je nutné ručně provést výměny.

Zahajte výměny bezproblémové jednotné přihlašování Kerberos dešifrovací klíč na místním serveru, na kterém běží služby Azure AD Connect.

Další informace najdete v tématu [jak je vrátit přes protokol Kerberos dešifrovací klíč účtu počítače AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

Monitorování serverů, na kterých běží agentů ověřování a zachovat dostupnost řešení. Kromě čítače výkonu pro hlavní server zveřejnit agentů ověřování objekty výkonu, které vám pomůžou pochopit, ověřování statistiky a chyby.

Agentů ověřování protokolovat do protokolů událostí Windows, které se nacházejí v rámci aplikace a služby Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin operace.

Můžete také zapnout protokolování pro řešení potíží.

Další informace najdete v tématu [předávací ověřování řešení potíží s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Další postup

* Další informace o [koncepty návrhu Azure AD Connect](plan-connect-design-concepts.md).
* Zvolte [správné ověřování](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).
* Další informace o [podporované topologie](plan-connect-design-concepts.md).
