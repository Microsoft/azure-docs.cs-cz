---
title: Princip jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory
description: Princip jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 28bf7e631c8693434d686022891bb2e45152f0ce
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597906"
---
# <a name="understand-saml-based-single-sign-on"></a>Pochopení jednotného přihlašování založeného na SAML

V [řadě rychlých startů](view-applications-portal.md) při správě aplikací jste zjistili, jak používat Azure AD jako zprostředkovatele identity (IDP) pro aplikaci. V tomto článku najdete další podrobnosti o možnosti založené na SAML pro jednotné přihlašování. 


## <a name="before-you-begin"></a>Než začnete

Použití Azure AD jako zprostředkovatele identity (IdP) a konfigurace jednotného přihlašování (SSO) může být v závislosti na používané aplikaci jednoduchá nebo složitá. Některé aplikace se dají konfigurovat jenom pomocí několika akcí. Jiné vyžadují hloubkovou konfiguraci. Pokud chcete rychle vymezit, Projděte si [řadu rychlých startů](view-applications-portal.md) při správě aplikací. Pokud je aplikace, kterou přidáváte, jednoduchá, pak pravděpodobně nebudete muset číst tento článek. Pokud aplikace, kterou přidáváte, vyžaduje vlastní konfiguraci pro jednotné přihlašování založené na SAML, pak je tento článek pro vás.

V [sérii rychlých startech](add-application-portal-setup-sso.md)je k dispozici článek týkající se konfigurace jednotného přihlašování. V takovém případě se dozvíte, jak získat přístup ke konfigurační stránce SAML pro aplikaci. Stránka Konfigurace SAML obsahuje pět oddílů. Tyto části jsou podrobně popsány v tomto článku.

> [!IMPORTANT] 
> Existují některé scénáře, kdy možnost **jednotného přihlašování** nebude k dispozici v navigaci pro aplikaci v **podnikových aplikacích**. 
>
> Pokud byla aplikace zaregistrovaná pomocí **Registrace aplikací** pak je funkce jednotného přihlašování nakonfigurovaná tak, aby ve výchozím nastavení používala protokol OAuth OIDC. V takovém případě se možnost **jednotného přihlašování** nezobrazí v části **podnikové aplikace**v navigaci. Když použijete **Registrace aplikací** k přidání vlastní aplikace, nakonfigurujete možnosti v souboru manifestu. Další informace o souboru manifestu naleznete v tématu [Azure Active Directory manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Další informace o standardech jednotného přihlašování najdete v tématu [ověřování a autorizace pomocí platformy Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Další scénáře, kdy v navigaci chybí **jednotné přihlašování** , patří mezi ně, pokud je aplikace hostovaná v jiném tenantovi nebo pokud váš účet nemá požadovaná oprávnění (globální správce, správce cloudové aplikace, Správce aplikací nebo vlastník instančního objektu). Oprávnění mohou také způsobit situaci, kdy můžete otevřít **jednotné přihlašování** , ale nebudete je moci uložit. Další informace o rolích pro správu Azure AD najdete v tématu ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Základní konfigurace SAML

Měli byste získat hodnoty od dodavatele aplikace. Můžete ručně zadat hodnoty nebo nahrát soubor metadat a extrahovat tak hodnotu polí.

> [!TIP]
> Mnohé aplikace už jsou předem nakonfigurované pro práci s Azure AD. Tyto aplikace jsou uvedené v galerii aplikací, které můžete procházet při přidávání aplikace do tenanta Azure AD. [Série rychlý Start](add-application-portal-setup-sso.md) vás provede procesem. Pro aplikace v galerii najdete podrobné pokyny, podrobné pokyny. Pro přístup k jednotlivým krokům můžete kliknout na odkaz na stránce konfigurace SAML pro aplikaci, jak je popsáno v části rychlý Start, nebo můžete procházet seznam všech kurzů pro konfiguraci aplikací v tématu [kurzy konfigurace aplikací SaaS](../saas-apps/tutorial-list.md).

| Základní nastavení konfigurace SAML | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
|:--|:--|:--|:--|
| **Identifikátor (ID entity)** | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Aplikace jednoznačně identifikuje. Azure AD odešle identifikátor do aplikace jako parametr cílové skupiny tokenu SAML. Očekává se, že aplikace ověří tuto aplikaci. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací. Zadejte adresu URL, která používá následující vzor: ' https:// <subdomain> . contoso.com ' *tuto hodnotu můžete najít jako element **vystavitele** v **AuthnRequest** (žádost SAML) odeslanou aplikací.* |
| **Adresa URL odpovědi** | Povinné | Povinné | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). Pomocí polí další adresa URL odpovědi můžete zadat několik adres URL odpovědi. Můžete například potřebovat další adresy URL pro odpovědi pro více subdomén. Nebo pro účely testování můžete najednou zadat několik adres URL odpovědí (místní hostitel a veřejné adresy URL). |
| **Přihlašovací adresa URL** | Povinné | Nezadávejte | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD používá adresu URL ke spuštění aplikace z Microsoft 365 nebo Azure AD moje aplikace. Pokud je prázdné, Azure AD provede IdP přihlášení iniciované, když uživatel spustí aplikaci z Microsoft 365, služby Azure AD moje aplikace nebo adresy URL jednotného přihlašování Azure AD.|
| **Stav přenosu** | Volitelné | Volitelné | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle je hodnota platnou adresou URL pro aplikaci. Některé aplikace ale používají toto pole odlišně. Další informace vám sdělí dodavatel aplikace.
| **Odhlašovací adresa URL** | Volitelné | Volitelné | Slouží k odeslání odpovědí na odhlášení SAML zpět do aplikace.

## <a name="user-attributes-and-claims"></a>Atributy a deklarace uživatele 

Když se uživatel do aplikace ověří, služba Azure AD vydá aplikaci token SAML s informacemi (nebo deklaracemi identity) o uživateli, který je jednoznačně identifikoval. Ve výchozím nastavení obsahují tyto informace uživatelské jméno, e-mailovou adresu, jméno a příjmení. Tyto deklarace může být nutné přizpůsobit, pokud například aplikace vyžaduje konkrétní hodnoty deklarace identity nebo jiný formát **názvu** než uživatelské jméno. 

> [!IMPORTANT]
> Mnoho aplikací je už předem nakonfigurovaných a v galerii aplikací a nemusíte si dělat starosti s nastavením deklarací identity uživatelů a skupin. [Série rychlý Start](add-application-portal.md) vás provede přidáním a konfigurací aplikací.


Hodnota identifikátoru **jedinečného identifikátoru uživatele (ID názvu)** je požadovaná deklarace identity a je důležitá. Výchozí hodnota je *User. userPrincipalName*. Identifikátor uživatele jednoznačně identifikuje každého uživatele v rámci aplikace. Pokud je například uživatelským jménem i jedinečným identifikátorem e-mailová adresa, nastavte tuto hodnotu na *user.mail*.

Další informace o přizpůsobení deklarací SAML najdete v tématu [Postupy: přizpůsobení deklarací identity vydaných v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).

Můžete přidat nové deklarace identity. Další informace najdete v tématu věnovaném [Přidání deklarací identity specifických pro aplikaci](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) nebo přidání deklarací skupin v tématu [Konfigurace deklarací identity skupiny](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Další způsoby přizpůsobení tokenu SAML z Azure AD do vaší aplikace najdete v následujících zdrojích.
>- Pokud chcete vytvořit vlastní role prostřednictvím Azure Portal, přečtěte si téma [Konfigurace deklarací identity rolí](../develop/active-directory-enterprise-app-role-management.md).
>- Informace o přizpůsobení deklarací prostřednictvím PowerShellu najdete v tématu [přizpůsobení deklarací – PowerShell](../develop/active-directory-claims-mapping.md).
>- Postup úpravy manifestu aplikace pro konfiguraci volitelných deklarací identity aplikace najdete v tématu [konfigurace volitelných deklarací identity](../develop/active-directory-optional-claims.md).
>- Informace o nastavení zásad životnosti tokenů pro aktualizační tokeny, přístupové tokeny, tokeny relací a tokeny ID najdete v tématu [Konfigurace životností tokenů](../develop/active-directory-configurable-token-lifetimes.md). Nebo pokud chcete omezit ověřovací relace přes podmíněný přístup Azure AD, přečtěte si téma [Možnosti správy relace ověřování](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="saml-signing-certificate"></a>Podpisový certifikát SAML

Azure AD pomocí certifikátu podepisuje tokeny SAML, které posílá do aplikace. Tento certifikát potřebujete ke konfiguraci vztahu důvěryhodnosti mezi službou Azure AD a aplikací. Podrobnosti o formátu certifikátu najdete v dokumentaci SAML aplikace. Další informace najdete v tématu [Správa certifikátů pro federované jednotné přihlašování](manage-certificates-for-federated-single-sign-on.md) a [Rozšířené možnosti podepisování certifikátů v tokenu SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Mnoho aplikací je už předem nakonfigurovaných a v galerii aplikací a nemusíte podrobně do certifikátů. [Série rychlý Start](add-application-portal.md) vás provede přidáním a konfigurací aplikací.

Z Azure AD si můžete stáhnout aktivní certifikát ve formátu Base64 nebo RAW přímo z hlavního **nastavení jednotné přihlašování se** stránkou SAML. Aktivní certifikát můžete také získat stažením souboru XML s metadaty aplikace nebo pomocí adresy URL federačních metadat aplikace. Pokud chcete zobrazit, vytvořit nebo stáhnout svoje certifikáty (aktivní nebo neaktivní), postupujte podle těchto kroků.

Mezi běžné věci ověření certifikátu patří: 
   - *Správné datum vypršení platnosti.* V budoucnu můžete nakonfigurovat datum vypršení platnosti až na tři roky.
   - *Stav aktivní pro správný certifikát.* Pokud je stav **neaktivní**, změňte stav na **aktivní**. Chcete-li změnit stav, klikněte pravým tlačítkem myši na řádek certifikátu a vyberte možnost **nastavit certifikát jako aktivní**.
   - *Správná možnost podepisování a algoritmus.*
   - *Správná e-mailová adresa (y) oznámení.* Když se aktivní certifikát blíží datu vypršení platnosti, Azure AD pošle oznámení na e-mailovou adresu nakonfigurovanou v tomto poli.

Někdy může být nutné stáhnout certifikát. Dejte pozor, abyste si ho uložili i přes! Chcete-li stáhnout certifikát, vyberte jednu z možností pro Formát Base64, nezpracovaný formát nebo XML federačních metadat. Azure AD taky poskytuje **adresu URL federačních metadat aplikace** , kde můžete získat přístup k metadatům specifickým pro aplikaci ve formátu `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

Chcete-li provést změny certifikátu, vyberte tlačítko Upravit. Na stránce **podpisového certifikátu SAML** můžete udělat několik věcí:
   - Vytvořit nový certifikát: vyberte **nový certifikát**, vyberte **Datum vypršení platnosti**a pak vyberte **Uložit**. Certifikát aktivujete tak, že vyberete kontextovou nabídku (**...**) a vyberete **nastavit certifikát jako aktivní**.
   - Odeslat certifikát s privátním klíčem a přihlašovacími údaji PFX: vyberte **importovat certifikát** a vyhledejte certifikát. Zadejte **heslo PFX**a pak vyberte **Přidat**.  
   - Nakonfigurujte rozšířené podepisování certifikátů. Další informace o těchto možnostech najdete v tématu [Rozšířené možnosti podepisování certifikátů](certificate-signing-options.md).
   - Oznámit dalším lidem, když se aktivní certifikát blíží datu vypršení platnosti: Zadejte e-mailové adresy do polí **e-mailových adres oznámení** .

## <a name="set-up-the-application-to-use-azure-ad"></a>Nastavení aplikace pro použití Azure AD

V části **nastavení \<applicationName> ** jsou uvedené hodnoty, které je třeba v aplikaci nakonfigurovat tak, aby používaly Azure AD jako zprostředkovatele identity SAML. Hodnoty se nastavují na stránce konfigurace na webu aplikace. Pokud například konfigurujete GitHub, přejdete na web github.com a nastavíte hodnoty. Pokud je aplikace už předem nakonfigurovaná a v galerii Azure AD, najdete odkaz k **zobrazení podrobných pokynů**. V opačném případě bude nutné najít dokumentaci k aplikaci, kterou konfigurujete. 

Hodnoty **adresy** URL pro přihlášení a adresy **URL pro odhlášení** se obě překládají na stejný koncový bod, což je koncový bod pro zpracování požadavků SAML pro tenanta Azure AD. 

**Identifikátor Azure AD** je hodnota **vystavitele** v tokenu SAML, který je vydaný pro aplikaci.

## <a name="test-single-sign-on"></a>Test jednotného přihlašování

Jakmile nakonfigurujete aplikaci tak, aby používala Azure AD jako zprostředkovatele identity založeného na SAML, můžete otestovat nastavení a zjistit, jestli pro váš účet funguje jednotné přihlašování. 

Vyberte **test** a pak zvolte možnost testovat s aktuálně přihlášeným uživatelem nebo jako někdo jiný. 

Pokud je přihlášení úspěšné, budete připraveni přiřadit uživatele a skupiny do aplikace SAML. Blahopřejeme vám.

Pokud se zobrazí chybová zpráva, proveďte následující kroky:

1. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?**.

    ![Získání postupu řešení](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Vyberte možnost **získat pokyny k řešení**. Zobrazí se pokyny k hlavní příčině a řešení.  V tomto příkladu uživatel nebyl přiřazen k aplikaci.

3. Přečtěte si doprovodné materiály k řešení a pokuste se problém vyřešit.

4. Spusťte test znovu, dokud se úspěšně nedokončí.

Další informace najdete v tématu [ladění jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).


## <a name="next-steps"></a>Další kroky

- [Série rychlý Start při správě aplikací](view-applications-portal.md)
- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protokol SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md)
