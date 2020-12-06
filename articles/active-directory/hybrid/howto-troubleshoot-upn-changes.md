---
title: Plánování a odstraňování potíží se změnami hlavního názvu uživatele Azure
description: Porozumění známým problémům a rizikům pro změny hlavního názvu uživatele
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59b304505c29f424d85d5b1e16dc83c060ac2ac1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744594"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Plánování a odstraňování potíží se změnami hlavního názvu uživatele v Azure Active Directory

Hlavní název uživatele (UPN) je atribut, který je standardem Internet Communications pro uživatelské účty. Hlavní název uživatele se skládá z předpony hlavního názvu uživatele (UPN) (název uživatelského účtu) a přípony UPN (název domény DNS). Předpona spojuje příponu pomocí \@ symbolu "". Například, someone@example.com. Hlavní název uživatele (UPN) musí být jedinečný mezi všemi objekty zabezpečení v rámci doménové struktury adresáře. 

**V tomto článku se předpokládá, že jako identifikátor uživatele použijete hlavní název uživatele (UPN). Zaměřuje se na plánování pro změny UPN a obnovuje se z problémů, které mohou vzniknout ze změn hlavního názvu uživatele.**

> [!NOTE]
> Pro vývojáře doporučujeme použít ID objectID uživatele jako neproměnlivý identifikátor, nikoli hlavní název uživatele (UPN) nebo e-mailové adresy, protože jejich hodnoty se můžou změnit.


## <a name="learn-about-upns-and-upn-changes"></a>Další informace o UPN a UPN
Přihlašovací stránky často vyzvat uživatele k zadání e-mailové adresy, když je požadovaná hodnota ve skutečnosti hlavní název uživatele (UPN). Proto byste měli mít jistotu, že uživatel hlavní název uživatele (UPN) si bude moci kdykoli změnit jeho primární e-mailovou adresu.

Primární e-mailová adresa uživatele se může změnit z mnoha důvodů:

* přizpůsobení společnosti

* zaměstnanci, kteří se přesunou do různých divizí společnosti 

* fúze a akvizice

* změny názvu zaměstnance

### <a name="types-of-upn-changes"></a>Typy hlavního názvu uživatele (UPN)

Můžete změnit hlavní název uživatele (UPN) změnou předpony, přípony nebo obojího.

* **Změna předpony**.

   *  Pokud se například změní jméno osoby, můžete změnit jejich název účtu:  
BSimon@contoso.comnaBJohnson@contoso.com

   * Můžete také změnit firemní standard pro předpony:  
Bsimon@contoso.comnaBritta.Simon@contoso.com

* **Změna přípony**. <br>

    Například pokud osoba změnila divize, můžete změnit jejich doménu: 

   * Britta.Simon@contoso.com schopn Britta.Simon@contosolabs.com <br>
     Nebo<br>
    * Britta.Simon@corp.contoso.com schopn Britta.Simon@labs.contoso.com 

Doporučujeme změnit hlavní název uživatele (UPN) při každém aktualizaci primární e-mailové adresy.

Při počáteční synchronizaci z Active Directory do Azure AD zajistěte, aby e-maily uživatelů byly stejné jako názvy UPN.

### <a name="upns-in-active-directory"></a>UPN ve službě Active Directory

Ve službě Active Directory je výchozí příponou UPN název DNS domény, ve které jste vytvořili uživatelský účet. Ve většině případů se jedná o název domény, který zaregistrujete jako doménu organizace na internetu. Pokud vytvoříte uživatelský účet v doméně contoso.com, použije se výchozí hlavní název uživatele (UPN).

username@contoso.com

 Pomocí domén a vztahů důvěryhodnosti služby Active Directory ale můžete [Přidat další přípony UPN](../fundamentals/add-custom-domain.md) . 

Například můžete chtít přidat labs.contoso.com a nechat si uživatelské názvy UPN a e-mail odrážet. Pak se stanou

username@labs.contoso.com.

>[!IMPORTANT]
> Pokud [měníte příponu ve službě Active Directory](../fundamentals/add-custom-domain.md), musíte zajistit, aby se [v Azure AD přidala a ověřila](../fundamentals/add-custom-domain.md)odpovídající vlastní název domény. 

![Snímek obrazovky ověřených domén](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN v Azure Active Directory

Uživatelé se přihlásí k Azure AD s hodnotou v atributu userPrincipalName. 

Když použijete Azure AD ve spojení s místní službou Active Directory, uživatelské účty se synchronizují pomocí služby Azure AD Connect. Ve výchozím nastavení používá Průvodce Azure AD Connect atribut userPrincipalName z místní služby Active Directory jako hlavní název uživatele (UPN) ve službě Azure AD. Můžete ho změnit na jiný atribut ve vlastní instalaci.

Pokud aktualizujete hlavní název uživatele (UPN) jednoho uživatele nebo pro celou organizaci, je důležité, abyste měli definovaný proces. 

Podívejte se na známé problémy a alternativní řešení v tomto dokumentu.

Při synchronizaci uživatelských účtů ze služby Active Directory do Azure AD zajistěte, aby se UPN ve službě Active Directory mapovaly na ověřené domény ve službě Azure AD.

![Snímek obrazovky zobrazující příklady názvů UPN mapovaných k ověřeným doménám Azure A D](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Pokud hodnota atributu userPrincipalName neodpovídá ověřené doméně ve službě Azure AD, proces synchronizace nahradí příponu výchozí hodnotou. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Zavedení hromadných změn hlavního názvu uživatele (UPN)

Pro hromadné změny UPN použijte [osvědčené postupy pro pilotní nasazení](../fundamentals/active-directory-deployment-plans.md) . Pokud zjistíte problémy, které se nedají rychle vyřešit, měli byste také mít testovaný plán vrácení zpět pro hlavní názvy uživatelů (UPN). Po spuštění pilotního projektu můžete začít cílit na malé skupiny uživatelů s různými organizačními rolemi a jejich konkrétními sadami aplikací nebo zařízení.

Tato první podmnožina uživatelů vám poskytne lepší představu o tom, co by uživatelé měli očekávat jako součást změny. Tyto informace zahrňte do komunikace uživatele.

Vytvořte definovanou proceduru pro změnu UPN na jednotlivých uživatelích v rámci běžných operací. Doporučujeme, abyste měli testovaný postup, který obsahuje dokumentaci o známých problémech a jejich řešení.

V následujících částech jsou uvedené podrobnosti o potenciálních známých problémech a alternativním řešení, když se změní hlavní názvy uživatelů.

## <a name="apps-known-issues-and-workarounds"></a>Známé problémy a řešení pro aplikace

[Software jako služba (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) a obchodní aplikace (LOB) se často spoléhají na hlavní názvy uživatelů (UPN), které hledají uživatele a ukládají informace o profilu uživatele, včetně rolí. Aplikace, které používají [zřizování přesně](../app-provisioning/user-provisioning.md) při vytváření profilu uživatele, když se uživatelé poprvé přihlásí k aplikaci, můžou mít vliv na změny UPN.

**Známý problém**<br>
Změna hlavního názvu uživatele (UPN) může přerušit relaci mezi uživatelem služby Azure AD a profilem uživatele vytvořeným v aplikaci. Pokud aplikace používá  [k zřizování pouze v čase](../app-provisioning/user-provisioning.md), může vytvořit značku nový profil uživatele. Tato akce vyžaduje, aby správce aplikace provedl ruční změny pro opravu tohoto vztahu.

**Alternativní řešení**<br>
[Automatizované zřizování uživatelů Azure AD](../app-provisioning/user-provisioning.md) umožňuje automaticky vytvářet, udržovat a odebírat identity uživatelů v podporovaných cloudových aplikacích. Konfigurace automatického zřizování uživatelů na vašich aplikacích automaticky aktualizuje hlavní názvy uživatelů (UPN) v aplikacích. Otestujte aplikace jako součást progresivního zavedení, abyste ověřili, že tyto změny nejsou ovlivněny pomocí hlavního názvu uživatele (UPN).
Pokud jste vývojář, zvažte [Přidání podpory SCIM do vaší aplikace](../app-provisioning/use-scim-to-provision-users-and-groups.md) , aby bylo možné Automatické zřizování uživatelů z Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Spravovaná zařízení – známé problémy a jejich řešení

Díky [zavedení zařízení do Azure AD](../devices/overview.md)maximalizujete produktivitu vašich uživatelů prostřednictvím jednotného přihlašování (SSO) napříč vaším cloudem a místními prostředky.

### <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Zařízení [připojená k Azure AD](../devices/concept-azure-ad-join.md) jsou přímo připojená k Azure AD a umožňují uživatelům přihlásit se k zařízení pomocí identity své organizace.

**Známé problémy** <br>
Uživatelé můžou při ověřování narazit na problémy s jednotným přihlašováním u aplikací, které jsou závislé na službě Azure AD.

**Řešení** <br>
Problémy uvedené v této části byly opraveny v systému Windows 10 Květen 2020 Update (2004).

**Alternativní řešení** <br>
Umožněte, aby se změna názvu UPN synchronizoval na Azure AD. Jakmile ověříte, že se nový hlavní název uživatele (UPN) projeví na portálu Azure AD, požádejte uživatele, aby při přihlášení pomocí nového hlavního názvu uživatele vybral dlaždici jiný uživatel. Můžete také ověřit pomocí [prostředí PowerShell](/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Po přihlášení pomocí nového hlavního názvu uživatele (UPN) se můžou odkazy na starý hlavní název uživatele zobrazovat i v nastavení systému Windows přístup do práce nebo do školy.

![Snímek obrazovky ověřených domén](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Zařízení [připojená k hybridní službě Azure AD](../devices/concept-azure-ad-join-hybrid.md) jsou připojená ke službě Active Directory a službě Azure AD. Můžete implementovat službu Azure AD JOIN, pokud má vaše prostředí místní služby Active Directory a Vy taky chcete využívat výhod funkcí poskytovaných službou Azure AD.

**Známé problémy** 

Zařízení připojená k hybridní službě Azure AD ve Windows 10 mají nejspíš neočekávané restartování a problémy s přístupem.

Pokud se uživatelé přihlásí k Windows předtím, než se nový hlavní název uživatele synchronizuje do Azure AD, nebo budou dál používat existující relaci Windows, můžou při ověřování pomocí služby Azure AD pro přístup k prostředkům zaznamenat problémy s jednotným přihlašováním k aplikacím, které používají Azure AD. 

Kromě toho se zobrazí následující zpráva s vynucením restartování po jedné minutě. 

"Počítač se automaticky restartuje za jednu minutu. Systém Windows narazil na problém a je třeba jej restartovat. Tuto zprávu byste teď měli zavřít a uložit svou práci.

**Řešení** <br>
Problémy uvedené v této části byly opraveny v systému Windows 10 Květen 2020 Update (2004).

**Alternativní řešení** 

Zařízení se musí odpojovat z Azure AD a restartovat. Po restartování se zařízení automaticky připojí k Azure AD a uživatel se musí přihlásit pomocí nového hlavního názvu uživatele (UPN), a to tak, že vybere dlaždici jiný uživatel. Pokud chcete odpojte zařízení ze služby Azure AD, spusťte na příkazovém řádku tento příkaz:

**dsregcmd /leave**

Pokud se uživatel používá, bude nutné ho [znovu zaregistrovat](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) pro Windows Hello pro firmy. U zařízení s Windows 7 a 8,1 se tento problém neprojeví po změně hlavního názvu uživatele (UPN).


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator známé problémy a alternativní řešení

Vaše organizace může pro přihlášení a přístup k podnikovým aplikacím a datům vyžadovat použití [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) . I když se v aplikaci může zobrazit uživatelské jméno, účet není nastavený tak, aby fungoval jako metoda ověřování, dokud uživatel nedokončí proces registrace.

[Aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) má čtyři hlavní funkce:

* Multi-Factor Authentication prostřednictvím nabízeného oznámení nebo ověřovacího kódu

* Slouží jako zprostředkovatel ověřování na zařízeních s iOS a Androidem, aby poskytovala jednotné přihlašování pro aplikace, které používají zprostředkované [ověřování](../develop/msal-android-single-sign-on.md) .

* Registrace zařízení (označovaná také jako Workplace Join) do služby Azure AD, což je požadavek na jiné funkce, jako je Intune App Protection a registrace/Správa zařízení,

* Přihlaste se telefonicky, což vyžaduje MFA a registraci zařízení.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication se zařízeními s Androidem

Aplikace Microsoft Authenticator nabízí možnost vzdáleného ověřování. Místo vložení automatizovaného telefonního hovoru nebo SMS uživateli během přihlašování se [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) vloží oznámení do Microsoft Authenticator aplikace na telefonu nebo tabletu uživatele. Uživatel jednoduše klepne na schválit (nebo do aplikace zadá PIN nebo biometrické údaje a klepne na "ověřit"), aby bylo možné dokončit přihlášení.

**Známé problémy** 

Když změníte hlavní název uživatele (UPN), původní hlavní název uživatele se pořád zobrazuje na uživatelském účtu a oznámení se nemusí přijmout. [Ověřovací kódy](../user-help/user-help-auth-app-faq.md) fungují i nadále.

**Alternativní řešení**

Pokud obdržíte oznámení, dejte uživateli pokyn, aby oznámení odkáže, otevřete aplikaci Authenticator, klepněte na možnost kontrolovat oznámení a schvalte výzvu MFA. V tomto případě se hlavní název uživatele zobrazený v účtu aktualizuje. Poznámka: aktualizovaný hlavní název uživatele (UPN) se může zobrazit jako nový účet, protože se používají jiné funkce ověřovatele. Další informace najdete v dalších známých problémech v tomto článku.

### <a name="brokered-authentication"></a>Zprostředkované ověřování

U zprostředkovatelů pro Android a iOS, jako je Microsoft Authenticator povolit:

* Jednotné přihlašování (SSO) – uživatelé se nebudou muset přihlašovat ke každé aplikaci.

* Identifikace zařízení – zprostředkovatel přistupuje k certifikátu zařízení vytvořenému v zařízení, když se připojil k síti na pracovišti.

* Ověření identifikace aplikace – když aplikace volá zprostředkovatele, předá adresu URL pro přesměrování a zprostředkovatel ji ověří.

Kromě toho umožňuje aplikacím přispívat do pokročilejších funkcí, jako je [podmíněný přístup](../conditional-access/index.yml), a podporuje [scénáře Microsoft Intune](../develop/msal-net-use-brokers-with-xamarin-apps.md).

**Známé problémy**<br>
Uživateli se zobrazí další výzvy k interaktivnímu ověřování v nových aplikacích, které používají přihlašování s asistencí pro zprostředkovatele, protože se neshoduje mezi login_hint předanými aplikací a hlavním názvem uživatele uloženým ve službě Broker.

**Alternativní řešení** <br> Uživatel musí účet ručně odebrat z Microsoft Authenticator a zahájit nové přihlášení z aplikace s asistencí pro zprostředkovatele. Po počátečním ověření se účet automaticky přidá.

### <a name="device-registration"></a>Registrace zařízení

Aplikace Microsoft Authenticator zodpovídá za registraci zařízení do služby Azure AD. Registrace zařízení umožňuje zařízení ověřit ve službě Azure AD a jedná se o požadavek v následujících situacích:

* Intune App Protection

* Registrace zařízení v Intune

* Přihlášení telefonem

**Známé problémy**<br>
Když změníte hlavní název uživatele (UPN), zobrazí se nový účet s novým hlavním názvem uživatele (UPN), který je uvedený v Microsoft Authenticator aplikaci, zatímco účet s původním hlavním názvem uživatele (UPN) je stále uvedený Starý hlavní název uživatele (UPN) se navíc zobrazí v části registrace zařízení v nastavení aplikace. Nedošlo k žádné změně v normálním fungování registrace zařízení nebo v závislých scénářích.

**Alternativní řešení** <br> Pokud chcete odebrat všechny odkazy na starý hlavní název uživatele (UPN) na Microsoft Authenticator aplikaci, dejte uživateli pokyn, aby ručně odebral staré i nové účty z Microsoft Authenticator, znovu se zaregistrují pro MFA a znovu se připojí k zařízení.

### <a name="phone-sign-in"></a>Přihlášení telefonem

Přihlášení telefonem umožňuje uživatelům přihlásit se k Azure AD bez hesla. Pokud chcete povolit přihlašování telefonem, musí se uživatel zaregistrovat pro MFA pomocí ověřovací aplikace a pak povolit přihlašování telefonem přímo na ověřovacích počítačích. V rámci konfigurace se zařízení registruje ve službě Azure AD.

**Známé problémy** <br>
Uživatelé nemůžou používat přihlášení telefonem, protože neobdrží žádná oznámení. Pokud uživatel klepne na kontrolovat oznámení, zobrazí se chyba.

**Alternativní řešení**<br>
Uživatel musí vybrat rozevírací nabídku u účtu s povoleným přihlášením k telefonu a vybrat zakázat přihlašování telefonem. V případě potřeby je možné telefonické přihlášení znovu povolit.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Bezpečnostní klíč (FIDO2) – známé problémy a jejich řešení

**Známé problémy** <br>
Pokud je u stejného klíče registrováno více uživatelů, zobrazí se na obrazovce pro přihlášení stránka pro výběr účtu, kde se zobrazí starý hlavní název uživatele (UPN). Přihlášení pomocí bezpečnostních klíčů nejsou ovlivněny změnami UPN.  

**Alternativní řešení**<br>
Pokud chcete odebrat odkazy na staré hlavní názvy uživatelů (UPN), musí [si uživatel resetovat klíč zabezpečení a znovu ho zaregistrovat](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Známé problémy a řešení na OneDrivu

Uživatelé OneDrivu mají zkušenosti s problémy po změně hlavního názvu uživatele (UPN). Další informace najdete v tématu [jak změny hlavního názvu uživatele mají vliv na adresu URL OneDrivu a funkce OneDrivu](/onedrive/upn-changes).

## <a name="next-steps"></a>Další kroky

Podívejte se na tyto prostředky:
* [Azure AD Connect: koncepty návrhu](./plan-connect-design-concepts.md)

* [Naplnění Azure AD UserPrincipalName](./plan-connect-userprincipalname.md)

* [Tokeny ID platformy Microsoft identity](../develop/id-tokens.md)