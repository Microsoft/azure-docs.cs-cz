---
title: Správa federačních certifikátů ve službě Azure AD | Microsoft Docs
description: Naučte se přizpůsobit datum vypršení platnosti federačních certifikátů a postup obnovení certifikátů, jejichž platnost brzy vyprší.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594af54221dd242bd481fe3d2fc823f628c1f955
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379447"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory

V tomto článku se zaměříme na běžné otázky a informace týkající se certifikátů, které Azure Active Directory (Azure AD) vytvoří, k vytvoření federovaného jednotného přihlašování (SSO) k vašim aplikacím typu software jako služba (SaaS). Přidejte aplikace z Galerie aplikací Azure AD nebo pomocí šablony aplikace mimo galerii. Nakonfigurujte aplikaci pomocí možnosti federovaného jednotného přihlašování.

Tento článek je relevantní jenom pro aplikace, které jsou nakonfigurované tak, aby používaly federaci služby Azure AD SSO prostřednictvím [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automaticky vygenerovaný certifikát pro galerie a aplikace mimo galerii

Když přidáte novou aplikaci z galerie a nakonfigurujete přihlášení založené na SAML (tím, že na stránce Přehled aplikace vyberete **jednotné přihlašování** pomocí  >  **SAML** ), Azure AD vygeneruje certifikát pro aplikaci, která je platná po dobu tří let. Pokud chcete stáhnout aktivní certifikát jako soubor certifikátu zabezpečení (**. cer**), vraťte se na tuto stránku (**přihlášení založené na SAML**) a vyberte odkaz ke stažení v záhlaví **podpisového certifikátu SAML** . Můžete si vybrat mezi nezpracovaným (binárním) certifikátem nebo certifikátem Base64 (základní text s kódováním Base 64). V případě aplikací galerie se v této části můžou zobrazit také odkazy na stažení certifikátu jako federační metadata XML (soubor **. XML** ) v závislosti na požadavku aplikace.

![Možnosti stažení aktivního podpisového certifikátu SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Aktivní nebo neaktivní certifikát si můžete stáhnout také tak, že vyberete ikonu pro **Úpravy** záhlaví **podpisového certifikátu SAML** (tužka), která zobrazí stránku **podpisový certifikát SAML** . Vyberte tři tečky (**...**) vedle certifikátu, který chcete stáhnout, a pak zvolte požadovaný formát certifikátu. Máte další možnost stáhnout certifikát ve formátu ochrany osobních údajů – Enhanced mail (PEM). Tento formát je stejný jako base64, ale s příponou názvu souboru **. pem** , která se v systému Windows nerozpoznala jako formát certifikátu.

![Možnosti stažení podpisového certifikátu SAML (aktivní a neaktivní)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Přizpůsobte datum vypršení platnosti federačního certifikátu a převeďte ho na nový certifikát.

Ve výchozím nastavení Azure nakonfiguruje certifikát na vypršení platnosti po třech letech, když se vytvoří automaticky během konfigurace jednotného přihlašování SAML. Vzhledem k tomu, že nemůžete změnit datum certifikátu po jeho uložení, musíte:

1. Vytvořte nový certifikát s požadovaným datem.
1. Uložte nový certifikát.
1. Stáhněte si nový certifikát ve správném formátu.
1. Nahrajte nový certifikát do aplikace.
1. Zajistěte, aby byl nový certifikát aktivní na portálu Azure Active Directory.

Následující dvě části vám pomůžou provést tyto kroky.

### <a name="create-a-new-certificate"></a>Vytvořit nový certifikát

Nejdřív vytvořte a uložte nový certifikát s jiným datem vypršení platnosti:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se stránka **Centrum pro správu Azure Active Directory** .
1. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací ve vašem účtu.
1. Vyberte ovlivněnou aplikaci. Zobrazí se stránka s přehledem pro aplikaci.
1. V levém podokně stránky přehled aplikace vyberte **jednotné přihlašování**.
1. Pokud se zobrazí stránka **Vybrat metodu jednotného přihlašování** , vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se službou SAML-Preview** Najděte hlavičku **podpisového certifikátu SAML** a vyberte ikonu pro **Úpravy** (tužka). Zobrazí se stránka **podpisový certifikát SAML** , ve kterém se zobrazí stav (**aktivní** nebo **neaktivní**), datum vypršení platnosti a kryptografický otisk (řetězec hash) každého certifikátu.
1. Vyberte **nový certifikát**. Pod seznamem certifikátů se zobrazí nový řádek, kde datum vypršení platnosti je ve výchozím nastavení přesně tři roky od aktuálního data. (Vaše změny se zatím neuložily, takže stále můžete upravit datum vypršení platnosti.)
1. V řádku nový certifikát umístěte ukazatel myši na sloupec Datum vypršení platnosti a vyberte ikonu **vybrat datum** (kalendář). Zobrazí se ovládací prvek Kalendář, který zobrazuje dny v měsíci aktuálního data vypršení platnosti nového řádku.
1. Pomocí ovládacího prvku Kalendář nastavte nové datum. Můžete nastavit jakékoli datum mezi aktuálním datem a třemi roky po aktuálním datu.
1. Vyberte **Uložit**. Nový certifikát se teď zobrazí se stavem **neaktivní**, datum vypršení platnosti, které jste zvolili, a kryptografický otisk.
1. Vyberte **X** a vraťte se na stránku **nastavit jeden Sign-On se stránkou SAML-Preview** .

### <a name="upload-and-activate-a-certificate"></a>Nahrát a aktivovat certifikát

Pak stáhněte nový certifikát ve správném formátu, nahrajte ho do aplikace a zpřístupněte ho v Azure Active Directory:

1. Další pokyny pro konfiguraci aplikace pro přihlášení k protokolu SAML najdete v těchto pokynech:

   - Výběr odkazu **Průvodce konfigurací** , který se má zobrazit v samostatném okně nebo záložce prohlížeče nebo
   - Přesměrujte se na záhlaví **Nastavení** a vyberte **Zobrazit podrobné pokyny** k zobrazení v postranním panelu.

1. V pokynech si poznamenejte formát kódování vyžadovaný pro nahrání certifikátu.
1. Postupujte podle pokynů v části [automaticky vygenerovaný certifikát pro galerii a mimo galerii aplikací](#auto-generated-certificate-for-gallery-and-non-gallery-applications) . Tento krok stáhne certifikát ve formátu kódování potřebném pro nahrání aplikací.
1. Pokud chcete přejít na nový certifikát, vraťte se na stránku **podpisový certifikát SAML** a na nově uložený řádek certifikátu vyberte tři tečky (**...**) a vyberte **nastavit certifikát jako aktivní**. Stav nového certifikátu se změní na **aktivní** a dříve aktivní certifikát se změní na stav **neaktivní**.
1. Pokračujte podle pokynů pro konfiguraci přihlášení SAML aplikace, které jste zobrazili dříve, abyste mohli podpisový certifikát SAML nahrát ve správném formátu kódování.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Přidání e-mailových oznámení adres pro vypršení platnosti certifikátu

Azure AD pošle e-mailové oznámení 60, 30 a 7 dní před vypršením platnosti certifikátu SAML. Pro příjem oznámení můžete přidat více než jednu e-mailovou adresu. Chcete-li zadat e-mailové adresy, na které chcete odesílat oznámení:

1. Na stránce **podpisový certifikát SAML** přejdete na záhlaví **e-mailových adres oznámení** . Ve výchozím nastavení používá tento nadpis jenom e-mailovou adresu správce, který aplikaci přidal.
1. Pod koncovou e-mailovou adresou napište e-mailovou adresu, která by měla obdržet oznámení o vypršení platnosti certifikátu, a pak stiskněte ENTER.
1. Opakujte předchozí krok pro každou e-mailovou adresu, kterou chcete přidat.
1. Pro každou e-mailovou adresu, kterou chcete odstranit, vyberte ikonu **Odstranit** (může uvolnění paměti) vedle e-mailové adresy.
1. Vyberte **Uložit**.

Do seznamu oznámení můžete přidat až 5 e-mailových adres (včetně e-mailové adresy správce, který aplikaci přidal). Pokud potřebujete, aby byli uživatelé upozorněni, použijte distribuční seznam e-maily.

Obdržíte e-mail s oznámením od aadnotification@microsoft.com . Pokud chcete e-mailem zabránit v umístění spamu, přidejte tento e-mail do kontaktů.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Prodloužit platnost certifikátu, který brzo vyprší

Pokud se brzo vyprší platnost certifikátu, můžete ho obnovit pomocí postupu, který pro uživatele nevede k výraznému výpadku. Obnovení certifikátu s vypršenou platností:

1. Postupujte podle pokynů v části [Vytvoření nového certifikátu](#create-a-new-certificate) dříve pomocí data, které se překrývá s existujícím certifikátem. Toto datum omezuje množství výpadku způsobené vypršením platnosti certifikátu.
1. Pokud se aplikace může automaticky vrátit k certifikátu, nastavte nový certifikát na aktivní pomocí následujících kroků:
   1. Vraťte se na stránku **podpisový certifikát SAML** .
   1. Na řádku nově uložený certifikát vyberte tři tečky (**...**) a pak vyberte **nastavit certifikát jako aktivní**.
   1. Přeskočte následující dva kroky.

1. Pokud aplikace může zpracovávat jenom jeden certifikát najednou, vyberte interval výpadku, který provede další krok. (Jinak, pokud aplikace automaticky nevybrala nový certifikát, ale může zpracovat více než jeden podpisový certifikát, můžete provést další krok kdykoli.)
1. Než starý certifikát vyprší, postupujte podle pokynů v části [nahrání a aktivace certifikátu](#upload-and-activate-a-certificate) výše.
1. Přihlaste se k aplikaci, abyste se ujistili, že certifikát funguje správně.

## <a name="related-articles"></a>Související články

- [Kurzy integrace aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md)
- [Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md)
- [Ladění jednotného přihlašování k aplikacím v Azure Active Directory založeného na SAML](./debug-saml-sso-issues.md)
