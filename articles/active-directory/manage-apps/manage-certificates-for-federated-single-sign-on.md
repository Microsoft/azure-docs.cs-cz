---
title: Správa federačních certifikátů ve službě Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit datum vypršení platnosti federačních certifikátů a jak obnovit certifikáty, které budou brzy vyprší.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: celested
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c2d14a2aa6fc6b53260912b5bead2bd7c01e8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440564"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory

V tomto článku se budeme zabývat běžné otázky a informace související s certifikáty, který vytvoří Azure Active Directory (Azure AD) k vytvoření federovaného jednotného přihlašování (SSO) software jako služba (SaaS) aplikací. Přidáte aplikace z Galerie aplikací Azure AD nebo pomocí šablony aplikace mimo galerii. Konfigurace aplikace s použitím možnosti federované jednotné přihlašování.

Tento článek je relevantní pouze pro aplikace, které jsou nakonfigurovány pro použití jednotného přihlašování k Azure AD prostřednictvím [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) federation (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automaticky generovaný certifikát pro galerie i mimo Galerii aplikace

Když přidejte novou aplikaci z galerie a nakonfigurovat základě SAML přihlašování (tak, že vyberete **jednotného přihlašování** > **SAML** ze stránky přehled aplikace), Azure AD vygeneruje certifikát pro aplikaci, která platí pro tři roky. Chcete-li stáhnout aktivní certifikát jako certifikát zabezpečení (**.cer**) soubor, vraťte se na této stránce (**přihlašování na základě SAML**) a vyberte odkaz ke stažení v **podpisový certifikát SAML** záhlaví. Můžete si vybrat nezpracované (binární) certifikátu nebo certifikátu Base64 (základní text kódováním base64). U aplikací, galerie, může zobrazit tato část také odkaz ke stažení certifikátu jako kód XML metadat federace ( **.xml** souboru), v závislosti na požadavku aplikace.

![SAML aktivní podpisový certifikát možnosti stahování](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Aktivních nebo neaktivních certifikátů můžete také stáhnout tak, že vyberete **podpisový certifikát SAML** nadpisu **upravit** ikonu (tužka), která se zobrazí **podpisový certifikát SAML** stránky. Vyberte tři tečky (**...** ) vedle certifikátu, kterou chcete stáhnout a pak zvolte který formát certifikátu chcete. Máte další možnost stažení certifikátu ve formátu pošty s vylepšenou ochranou osobních údajů (PEM). Tento formát je stejný jako Base64, ale **.pem** příponu, která není rozpoznána ve Windows jako formát certifikátu.

![Podpisového certifikátu možnosti stahování (aktivní a neaktivní) SAML](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Upravit datum vypršení platnosti vašeho certifikátu federace a přejít do nového certifikátu

Ve výchozím nastavení Azure nakonfiguruje certifikát vyprší po uplynutí tří let, když je vytvořena automaticky během SAML jednotné přihlašování. Vzhledem k tomu, že datum certifikátu nelze změnit, jakmile ho uložíte, budete muset:

1. Vytvořte nový certifikát s požadovaného data.
2. Uložte nový certifikát.
3. Stáhněte si nový certifikát ve správném formátu.
4. Nahrajte nový certifikát k aplikaci.
5. Nastavit nový certifikát jako aktivní na portálu Azure Active Directory.

V následujících dvou částech vám pomohou provést tyto kroky.

### <a name="create-a-new-certificate"></a>Vytvořte nový certifikát

Nejprve vytvořte a uložte nový certifikát s jinou platnosti:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). **Centra pro správu Azure Active Directory** se zobrazí stránka.

2. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací ve vašem účtu.

3. Vyberte příslušné aplikace. Zobrazí se stránka s přehledem pro aplikaci.

4. V levém podokně na stránce Přehled aplikace vyberte **jednotného přihlašování**.

5. Pokud **vybrat jedinou metodu přihlašování** stránky se zobrazí, vyberte **SAML**.

6. V **nastavte si jednotné přihlašování pomocí SAML - Preview** stránky, vyhledejte **podpisový certifikát SAML** nadpis a vyberte **upravit** ikonu (tužky). **Podpisový certifikát SAML** se zobrazí stránka, která zobrazuje stav (**aktivní** nebo **neaktivní**), datum vypršení platnosti a kryptografický otisk (řetězec hash) jednotlivých certifikátů.

7. Vyberte **nový certifikát**. Nový řádek pod se zobrazí seznam certifikátů, kde datum vypršení platnosti výchozí hodnota je přesně tři roky po aktuálním datu. (Vaše změny nebyly uloženy ještě, abyste mohli dál upravit datum vypršení platnosti.)

8. Na novém řádku certifikát, najeďte myší sloupce Datum vypršení platnosti a vyberte **vybrat datum** ikonu (kalendář). Ovládací prvek calendar zobrazí dny v měsíci, který aktuální datum vypršení platnosti na novém řádku.

9. Chcete-li nastavit nové datum pomocí ovládacího prvku kalendář. Můžete nastavit libovolné datum v rozmezí aktuální datum a tři roky po aktuálním datu.

10. Vyberte **Uložit**. Nový certifikát se teď zobrazí se stavem **neaktivní**, vypršení platnosti datum, které jste zvolili a kryptografický otisk.

11. Vyberte **X** se vraťte do **nastavte si jednotné přihlašování pomocí SAML - Preview** stránky.

### <a name="upload-and-activate-a-certificate"></a>Nahrát a aktivovat certifikát

Dále stáhněte nový certifikát ve správném formátu, nahrajte ho do aplikace a byl aktivní ve službě Azure Active Directory:

1. Zobrazení aplikace Další pokyny ke konfiguraci přihlašování SAML podle:
   - Výběr **Průvodce konfigurací** odkaz na zobrazení v samostatném okně prohlížeče nebo karty, nebo
   - má **nastavení** záhlaví a vyberete **zobrazit podrobný** zobrazíte bočním panelu.

2. V pokynech mějte na paměti formát kódování, které jsou potřebné pro nahrání certifikátu.

3. Postupujte podle pokynů [automaticky generovaný certifikát pro galerie i mimo Galerii aplikace](#auto-generated-certificate-for-gallery-and-non-gallery-applications) výše v části. Tento krok stáhne certifikátu ve formátu kódování pro nahrávání vyžadovaného aplikací.

4. Pokud chcete přejít na nový certifikát, vraťte se do **podpisový certifikát SAML** stránce a na řádku nově uložený certifikát, vyberte tři tečky (**...** ) a vyberte **aktivovat certifikát**. Stav tohoto nového certifikátu se změní na **aktivní**, a dříve aktivní certifikát se změní na stav **neaktivní**.

5. Pokračujte následujícím pokyny ke konfiguraci přihlašování SAML aplikace, které jste zobrazili dříve, tak, že můžete nahrát SAML podepisování certifikátů ve správném formátu kódování.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Přidat e-mailové adresy pro oznámení pro vypršení platnosti certifikátu

Azure AD pošle e-mailové oznámení 60, 30 a 7 dnů před vypršením platnosti certifikátu SAML. Můžete přidat více než jednu e-mailovou adresu pro příjem oznámení. Zadání e-mailové adresy, které chcete oznámení k odeslání do:

1. V **podpisový certifikát SAML** stránky, přejděte **oznámení e-mailové adresy** záhlaví. Ve výchozím nastavení používá toto záhlaví e-mailovou adresu správce, kteří přidali aplikace.

2. Pod poslední e-mailovou adresu zadejte e-mailovou adresu, která by měla přijímat oznámení o vypršení platnosti certifikátu a stiskněte klávesu Enter.

3. Opakujte předchozí krok pro každou e-mailovou adresu, kterou chcete přidat.

4. Pro každou e-mailovou adresu, kterou chcete odstranit, vyberte **odstranit** ikonu (a uvolňování paměti může) vedle e-mailovou adresu.

5. Vyberte **Uložit**.

Obdržíte e-mailových oznámení z aadnotification@microsoft.com. Aby se zabránilo e-mailu, že přejdete do umístění nevyžádané pošty, přidejte tento e-mail od svých kontaktů.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Obnovit certifikát, jehož platnost brzo vyprší

Pokud certifikátu brzy vyprší platnost, můžete obnovit pomocí procedury, která má za následek bez významnějších výpadků pro vaše uživatele. Postup obnovení u nichž vyprší platnost certifikátu:

1. Postupujte podle pokynů [vytvořte nový certifikát](#create-a-new-certificate) části dříve, pomocí datum, které se překrývá s existující certifikát. Toto datum omezuje množství prostoje způsobené vypršení platnosti certifikátu.

2. Pokud aplikace může automaticky nespotřebujete certifikát, nastavte nový certifikát na aktivní pomocí následujících kroků:
   1. Přejděte zpět **podpisový certifikát SAML** stránky.
   2. V řádku nově uložený certifikát, vyberte tři tečky (**...** ) a pak vyberte **aktivovat certifikát**.
   3. Přeskočte následující dva kroky.

3. Pokud aplikace může najednou zpracovat pouze jeden certifikát, vyberte výpadek interval provádět další krok. (Jinak, pokud aplikace nebude automaticky výběr nového certifikátu ale může zpracovávat více než jeden podpisový certifikát, můžete provést další krok kdykoli.)

4. Předtím, než vyprší platnost původního certifikátu, postupujte podle pokynů [nahrát a aktivovat certifikát](#upload-and-activate-a-certificate) výše v části.

5. Přihlásit se k aplikaci a ujistit se, že certifikát pracuje správně.

## <a name="related-articles"></a>Související články

* [Kurzy integrace aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md)
* [Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
* [Jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md)
* [Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
