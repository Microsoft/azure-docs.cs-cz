---
title: Sestavení cílové stránky bezplatné nebo zkušební SaaS nabídky na komerčním webu Marketplace
description: Naučte se, jak vytvořit cílovou stránku pro bezplatnou nebo zkušební SaaS nabídku.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: 699271316eccec1244db886ed2296f87c52f91ae
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348351"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Sestavení cílové stránky bezplatné nebo zkušební SaaS nabídky na komerčním webu Marketplace

Tento článek vás provede procesem vytvoření cílové stránky pro bezplatnou nebo zkušební aplikaci SaaS, která se prodává na komerčním webu Microsoft Marketplace.

## <a name="overview"></a>Přehled

Cílovou stránku si můžete představit jako "předsálí" pro nabídku software jako službu (SaaS). Když se zákazník rozhodne získat vaši aplikaci, komerční tržiště je přesměruje na cílovou stránku, aby aktivovala a nakonfigurovala své předplatné vaší aplikace v SaaS. Když vytvoříte nabídku software jako službu (SaaS), můžete v partnerském centru zvolit, jestli chcete [prodávat přes Microsoft](plan-saas-offer.md#listing-options). Pokud chcete zobrazit seznam nabídek jenom na komerčním webu Microsoft Marketplace a neprodávat prostřednictvím Microsoftu, můžete určit, jak můžou potenciální zákazníci s nabídkou pracovat. Pokud povolíte možnost **získat nyní (zdarma)** nebo **bezplatná zkušební verze** výpisu, musíte zadat adresu URL cílové stránky, na kterou může uživatel přejít, abyste mohli získat přístup k bezplatnému nebo zkušebnímu předplatnému.

Účelem cílové stránky je jednoduše získat uživatele, aby mohl aktivovat bezplatnou zkušební verzi nebo bezplatné předplatné. Pomocí Azure Active Directory (Azure AD) a Microsoft Graph pro uživatele povolíte jednotné přihlašování (SSO) a získáte důležité informace o uživateli, které můžete použít k aktivaci bezplatné zkušební verze nebo bezplatného předplatného, včetně jejich jména, e-mailové adresy a organizace.

Vzhledem k tomu, že informace potřebné k aktivaci předplatného jsou omezené a poskytované službou Azure AD a Microsoft Graph, neměli byste vyžadovat informace, které vyžadují více než základní souhlas. Pokud potřebujete informace o uživateli, které vyžadují další souhlas s vaší aplikací, měli byste tyto informace požádat po dokončení Aktivace předplatného. To umožňuje bezproblémovou aktivaci předplatného pro uživatele a snižuje riziko jeho zrušení.

Cílová stránka obvykle obsahuje následující informace a možnosti výpisu:

- Prezentovat název a podrobnosti bezplatné zkušební verze nebo bezplatného předplatného. Zadejte například omezení využití nebo dobu trvání zkušební verze.
- Prezentovat podrobnosti o účtu uživatele, včetně křestního jména a příjmení, organizace a e-mailu.
- Vyzvat uživatele k potvrzení nebo nahrazení různých podrobností o účtu.
- Průvodce uživateli provede další kroky po aktivaci. Například dostanete uvítací e-mail, spravujte předplatné, Získejte podporu nebo si přečtěte dokumentaci.

Následující části tohoto článku vás provedou procesem vytvoření cílové stránky:

1. [Vytvořte registraci aplikace Azure AD](#create-an-azure-ad-app-registration) pro cílovou stránku.
2. [Jako výchozí bod pro aplikaci použijte ukázku kódu](#use-a-code-sample-as-a-starting-point) .
3. [Přečtěte si informace z deklarací identity kódovaných v tokenu ID](#read-information-from-claims-encoded-in-the-id-token), které se po přihlášení přijaly v Azure AD, které se odeslaly spolu s požadavkem.
4. [Použijte rozhraní Microsoft Graph API](#use-the-microsoft-graph-api) k získání dalších informací, podle potřeby.

## <a name="create-an-azure-ad-app-registration"></a>Vytvoření registrace aplikace Azure AD

Obchod na komerčním webu je plně integrovaný s Azure AD. Uživatelé dorazí na web Marketplace ověřený pomocí [účtu Azure AD nebo účet Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Po získání bezplatného nebo bezplatného zkušebního předplatného prostřednictvím nabídky jenom se seznamem přejde uživatel z komerčního tržiště na adresu URL vaší cílové stránky, kde můžete aktivovat a spravovat svoje předplatné s vaší aplikací SaaS. Je nutné, aby se uživatel přihlásil k aplikaci pomocí jednotného přihlašování služby Azure AD. (Adresa URL cílové stránky je uvedena na stránce [technické konfigurace](plan-saas-offer.md#technical-information) nabídky.

Prvním krokem k použití identity je, abyste se ujistili, že je vaše cílová stránka registrovaná jako aplikace Azure AD. Registrace aplikace vám umožní pomocí Azure AD ověřovat uživatele a žádat o přístup k prostředkům uživatele. Může být považována za definici aplikace, která umožňuje službě zjistit, jak vydávat tokeny aplikaci na základě nastavení aplikace.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí portálu Azure Portal

Chcete-li začít, postupujte podle pokynů pro [registraci nové aplikace](../active-directory/develop/quickstart-register-app.md). Pokud chcete umožnit uživatelům z jiných firem, aby si aplikaci navštívili, musíte zvolit **účty v libovolné organizační složce (libovolný adresář Azure AD – víceklientské) a osobní účty Microsoft (jako Skype nebo Xbox)** , když se zobrazí dotaz, kdo může aplikaci používat.

Pokud se chystáte zadat dotaz na rozhraní Microsoft Graph API, [nakonfigurujte novou aplikaci pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Když vyberete oprávnění rozhraní API pro tuto aplikaci, výchozí nastavení **uživatel. čtení** je dostatečné pro shromáždění základních informací o uživateli, aby bylo možné proces připojování hladce a automatickým způsobem vytvořit. Nevyžadovat žádná oprávnění API s popiskem **vyžaduje souhlas správce** , protože to zabrání všem uživatelům bez oprávnění správce na návštěvě cílové stránky.

Pokud v rámci procesu připojování nebo zřizování požadujete zvýšená oprávnění, zvažte použití funkcí [přírůstkového souhlasu](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) Azure AD, aby všichni uživatelé, kteří odesílají z webu Marketplace, mohli s cílovou stránkou interaktivně pracovat.

## <a name="use-a-code-sample-as-a-starting-point"></a>Použití ukázky kódu jako počátečního bodu

Microsoft nabízí několik ukázkových aplikací, které implementují jednoduchý web s povoleným přihlášením Azure AD. Po registraci aplikace v Azure AD okno **rychlý Start** nabízí seznam běžných typů aplikací a vývojových zásobníků (obrázek 1). Vyberte ten, který odpovídá vašemu prostředí, a postupujte podle pokynů ke stažení a instalaci.

**_Obrázek 1: okno rychlý Start v Azure Portal_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Znázorňuje okno rychlý Start v Azure Portal.":::

Po stažení kódu a nastavení vývojového prostředí změňte nastavení konfigurace v aplikaci tak, aby odráželo ID aplikace, ID tenanta a tajný klíč klienta, které jste si poznamenali v předchozím postupu. Všimněte si, že přesný postup se liší v závislosti na tom, kterou ukázku používáte.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Načíst informace z deklarací identity kódovaných v tokenu ID

V rámci toku [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) Azure AD přidá do žádosti [token ID](../active-directory/develop/id-tokens.md) , když se uživatel pošle na cílovou stránku. Tento token obsahuje několik částí základních informací, které by mohly být užitečné v procesu aktivace, včetně informací zobrazených v této tabulce.

| Hodnota | Popis |
| ------------ | ------------- |
| aud | Zamýšlená cílová skupina pro tento token. V takovém případě by se měl shodovat s ID aplikace a ověřit. |
| preferred_username | Primární uživatelské jméno navštíveného uživatele Může to být e-mailová adresa, telefonní číslo nebo jiný identifikátor. |
| e-mail | E-mailová adresa uživatele Všimněte si, že toto pole může být prázdné. |
| name | Uživatelsky čitelné hodnoty, které identifikují předmět tokenu. V takovém případě se jedná o uživatelské jméno. |
| identifikátor | Identifikátor v systému Microsoft identity, který jednoznačně identifikuje uživatele napříč aplikacemi. Microsoft Graph vrátí tuto hodnotu jako vlastnost ID pro daný uživatelský účet. |
| identifikátory | Identifikátor, který představuje tenanta Azure AD, ze kterého je uživatel. V případě MSA identity to bude vždycky `9188040d-6c67-4c5b-b112-36a304b66dad` . Další informace najdete v poznámce v následující části: použití rozhraní Microsoft Graph API. |
| jednotk | Identifikátor, který jedinečně identifikuje uživatele v této konkrétní aplikaci. |
|||

## <a name="use-the-microsoft-graph-api"></a>Použití rozhraní Microsoft Graph API

Token ID obsahuje základní informace pro identifikaci uživatele, ale proces aktivace může vyžadovat další podrobnosti, jako je například společnost uživatele – k dokončení procesu připojování. Použijte [rozhraní Microsoft Graph API](/graph/use-the-api) k vyžádání těchto informací, abyste zabránili vynucení zadání těchto podrobností uživatelem. Ve výchozím nastavení zahrnují oprávnění Standard _ *User. Read* * následující informace:

| Hodnota | Popis |
| ------------ | ------------- |
| displayName | Název zobrazený v adresáři pro uživatele |
| givenName | Křestní jméno uživatele |
| jobTitle | Pracovní pozice uživatele |
| pošta | Adresa SMTP pro uživatele |
| mobilePhone | Primární mobilní telefonní číslo uživatele |
| preferredLanguage | Kód ISO 639-1 pro preferovaný jazyk uživatele. |
| surname | Příjmení uživatele |
|||

Další vlastnosti, například název společnosti uživatele nebo umístění uživatele (země), lze vybrat k zařazení do žádosti. Další podrobnosti najdete v tématu [vlastnosti pro typ prostředku uživatele](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true#properties).

Většina aplikací zaregistrovaných ve službě Azure AD uděluje delegovaná oprávnění ke čtení informací o uživateli z tenanta Azure AD společnosti. Každá žádost o Microsoft Graph pro tyto informace musí být doprovázena přístupovým tokenem jako ověřování. Konkrétní kroky pro vygenerování přístupového tokenu budou záviset na použitém zásobníku technologie, ale vzorový kód bude obsahovat příklad. Další informace najdete v tématu [získání přístupu jménem uživatele](/graph/auth-v2-user).

> [!NOTE]
> Účty z klienta MSA (s ID tenanta `9188040d-6c67-4c5b-b112-36a304b66dad` ) nebudou vracet více informací, než již byly shromážděny pomocí tokenu ID. Toto volání Graph API pro tyto účty můžete přeskočit.

## <a name="next-steps"></a>Další kroky
- [Jak vytvořit nabídku SaaS na komerčním webu Marketplace](create-new-saas-offer.md)