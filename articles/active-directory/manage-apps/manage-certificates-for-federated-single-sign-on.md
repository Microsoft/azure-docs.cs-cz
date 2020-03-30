---
title: Správa federačních certifikátů ve službě Azure AD | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přizpůsobit datum vypršení platnosti federačních certifikátů a jak obnovit certifikáty, jejichž platnost brzy vyprší.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159025"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Správa certifikátů pro federované jednotné přihlašování ve službě Azure Active Directory

V tomto článku se zabýváme běžnými otázkami a informacemi týkajícími se certifikátů, které Azure Active Directory (Azure AD) vytvoří za účelem vytvoření federovaného jednotného přihlašování (SSO) k vašim aplikacím softwaru jako služby (SaaS). Přidejte aplikace z galerie aplikací Azure AD nebo pomocí šablony aplikace, která není součástí galerie. Nakonfigurujte aplikaci pomocí možnosti federovaného objektu zabezpečení.

Tento článek je relevantní pouze pro aplikace, které jsou nakonfigurované pro použití Azure AD SSO prostřednictvím federace [kontrolního výrazu zabezpečení značky](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automaticky generovaný certifikát pro galerijní a negalesní aplikace

Když přidáte novou aplikaci z galerie a nakonfigurujete přihlašování na základě SAML (výběrem **jednotného** > přihlášení**SAML** ze stránky přehledu aplikace), Azure AD vygeneruje certifikát pro aplikaci, která je platná po dobu tří let. Chcete-li stáhnout aktivní certifikát jako soubor certifikátu zabezpečení (**cer),** vraťte se na tuto stránku **(přihlašování založené na SAML)** a vyberte odkaz ke stažení v záhlaví **podpisového certifikátu SAML.** Můžete si vybrat mezi nezpracovaným (binárním) certifikátem nebo certifikátem Base64 (základní 64 kódovaný text). V případě aplikací galerie může tato část také zobrazit odkaz ke stažení certifikátu jako federačního xml metadat (soubor **XML)** v závislosti na požadavku aplikace.

![Možnosti stahování aktivního podpisového certifikátu SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Aktivní nebo neaktivní certifikát můžete také stáhnout výběrem ikony **upravit** nadpis **podpisový certifikát SAML** (tužku), která zobrazuje stránku **podpisového certifikátu SAML.** Vyberte tři tečky (**...**) vedle certifikátu, který chcete stáhnout, a zvolte, který formát certifikátu chcete. Máte další možnost stáhnout certifikát ve formátu PEM (Privacy Enhanced Dem). Tento formát je shodný s Base64, ale s příponou **.pem,** která není v systému Windows rozpoznána jako formát certifikátu.

![Možnosti stahování podpisových certifikátů SAML (aktivní a neaktivní)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Přizpůsobení data vypršení platnosti federačního certifikátu a jeho převrácení do nového certifikátu

Ve výchozím nastavení Azure nakonfiguruje vypršení platnosti certifikátu po třech letech, když se automaticky vytvoří během konfigurace jednotného přihlašování SAML. Vzhledem k tomu, že po uložení certifikátu nemůžete změnit datum, musíte:

1. Vytvořte nový certifikát s požadovaným datem.
1. Uložte nový certifikát.
1. Stáhněte si nový certifikát ve správném formátu.
1. Nahrajte nový certifikát do aplikace.
1. Uvažte nový certifikát aktivní na portálu Azure Active Directory.

Následující dvě části vám pomohou provést tyto kroky.

### <a name="create-a-new-certificate"></a>Vytvoření nového certifikátu

Nejprve vytvořte a uložte nový certifikát s jiným datem vypršení platnosti:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se stránka **Centra pro správu Služby Azure Active Directory.**
1. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací ve vašem účtu.
1. Vyberte ovlivněnou aplikaci. Zobrazí se stránka s přehledem aplikace.
1. V levém podokně stránky přehledu aplikace vyberte **Možnost Jednotné přihlašování**.
1. Pokud se zobrazí stránka **Vybrat metodu jednotného přihlášení,** vyberte **možnost SAML**.
1. Na stránce **Nastavit jednotné přihlašování pomocí saml – náhled** najděte záhlaví **podpisového certifikátu SAML** a vyberte ikonu **Upravit** (tužku). Zobrazí se stránka **podpisového certifikátu SAML,** která zobrazuje stav **(Aktivní** nebo **Neaktivní),** datum vypršení platnosti a kryptografický otisk (řetězec hash) každého certifikátu.
1. Vyberte **nový certifikát**. Pod seznamem certifikátů se zobrazí nový řádek, kde je datum vypršení platnosti výchozí přesně tři roky po aktuálním datu. (Změny ještě nebyly uloženy, takže můžete i nadále upravit datum vypršení platnosti.)
1. V novém řádku certifikátu najeďte na sloupec data vypršení platnosti a vyberte ikonu **Vybrat datum** (kalendář). Zobrazí se ovládací prvek kalendáře zobrazující dny měsíce aktuálního data vypršení platnosti nového řádku.
1. Pomocí ovládacího prvku kalendář nastavte nové datum. Můžete nastavit libovolné datum mezi aktuálním datem a třemi roky po aktuálním datu.
1. Vyberte **Uložit**. Nový certifikát se nyní zobrazí se stavem **Neaktivní**, zvoleným datem vypršení platnosti a kryptografickým otiskem.
1. Vyberte **x,** abyste se vrátili na stránku **Nastavit jednotné přihlášení pomocí SAML - Náhled.**

### <a name="upload-and-activate-a-certificate"></a>Nahrání a aktivace certifikátu

Dále si stáhněte nový certifikát ve správném formátu, nahrajte ho do aplikace a uvažte ho ve službě Azure Active Directory:

1. Zobrazit další aplikace SAML pokyny pro přihlášení ke konfiguraci buď:

   - Výběr odkazu **průvodce konfigurací,** který chcete zobrazit v samostatném okně prohlížeče nebo na kartě, nebo
   - Přejdete na **nastavený** nadpis a vyberete **možnost Zobrazit podrobné pokyny** k zobrazení na postranním panelu.

1. V pokynech si poznamenejte formát kódování požadovaný pro nahrávání certifikátu.
1. Postupujte podle pokynů v části [Automaticky generovaný certifikát pro galerijní a neochýlené aplikace](#auto-generated-certificate-for-gallery-and-non-gallery-applications) dříve. Tento krok stáhne certifikát ve formátu kódování vyžadovaného pro nahrání aplikací.
1. Pokud chcete přejít k novému certifikátu, vraťte se na stránku **podpisového certifikátu SAML** a v řádku nově uloženého certifikátu vyberte tři tečky (**...**) a vyberte **Vytvořit certifikát aktivní**. Stav nového certifikátu se změní na **Aktivní**a dříve aktivní certifikát změní stav **Neaktivní**.
1. Pokračujte podle pokynů pro konfiguraci přihlašování SAML aplikace, které jste zobrazili dříve, abyste mohli nahrát podpisový certifikát SAML ve správném formátu kódování.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Přidání e-mailových adres oznámení pro vypršení platnosti certifikátu

Azure AD odešle e-mailové oznámení 60, 30 a 7 dní před vypršením platnosti certifikátu SAML. Můžete přidat více než jednu e-mailovou adresu pro příjem oznámení. Chcete-li zadat e-mailovou adresu( e-mailová adresa), kterou chcete, aby byla oznámení odeslána na adresu:

1. Na stránce **podpisový certifikát SAML** přejděte do záhlaví **e-mailových adres oznámení.** Ve výchozím nastavení používá toto záhlaví pouze e-mailovou adresu správce, který aplikaci přidal.
1. Pod konečnou e-mailovou adresu zadejte e-mailovou adresu, která má obdržet oznámení o vypršení platnosti certifikátu, a stiskněte Enter.
1. Opakujte předchozí krok pro každou e-mailovou adresu, kterou chcete přidat.
1. U každé e-mailové adresy, kterou chcete odstranit, vyberte vedle e-mailové adresy ikonu **Odstranit** (odpadkový koš).
1. Vyberte **Uložit**.

Obdržíte e-mail aadnotification@microsoft.coms oznámením od společnosti . Chcete-li se vyhnout tomu, aby e-mail přešel do vašeho umístění nevyžádané pošty, přidejte tento e-mail ke svým kontaktům.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Obnovení certifikátu, jehož platnost brzy vyprší

Pokud vyprší platnost certifikátu, můžete jej obnovit postupem, který pro uživatele nevede k žádným významným prostojům. Obnovení certifikátu s končící platností:

1. Postupujte podle pokynů v části [Vytvořit nový certifikát](#create-a-new-certificate) dříve pomocí data, které se překrývá s existujícím certifikátem. Toto datum omezuje množství prostojů způsobených vypršením platnosti certifikátu.
1. Pokud aplikace může automaticky převrátit certifikát, nastavte nový certifikát na aktivní následujícím postupem:
   1. Vraťte se na stránku **podpisového certifikátu SAML.**
   1. V řádku nově uloženého certifikátu vyberte tři tečky (**...**) a pak vyberte **Vytvořit certifikát aktivní**.
   1. Přeskočte další dva kroky.

1. Pokud aplikace dokáže zpracovat pouze jeden certifikát najednou, vyberte interval prostojů a proveďte další krok. (V opačném případě, pokud aplikace automaticky nezvedne nový certifikát, ale dokáže zpracovat více než jeden podpisový certifikát, můžete kdykoli provést další krok.)
1. Před vypršením platnosti starého certifikátu postupujte podle pokynů v části [Nahrát a aktivujte certifikát](#upload-and-activate-a-certificate) dříve.
1. Přihlaste se k aplikaci a ujistěte se, že certifikát funguje správně.

## <a name="related-articles"></a>Související články

- [Kurzy integrace aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md)
- [Správa aplikací pomocí služby Azure Active Directory](what-is-application-management.md)
- [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](what-is-single-sign-on.md)
- [Ladění jednotného přihlašování saml k aplikacím ve službě Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
