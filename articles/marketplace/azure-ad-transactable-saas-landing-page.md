---
title: Sestavení cílové stránky pro SaaS nabídku s podporou transakcí na komerčním webu Marketplace
description: Naučte se, jak vytvořit cílovou stránku pro SaaS nabídku s podporou transakcí.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: d4c23e6b213c102813758742b8d191735207d285
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124896"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Sestavení cílové stránky pro SaaS nabídku s podporou transakcí na komerčním webu Marketplace

Tento článek vás provede procesem vytvoření cílové stránky pro transakční SaaS aplikaci, která bude prodávána na komerčním webu Microsoft Marketplace.

## <a name="overview"></a>Přehled

Cílovou stránku si můžete představit jako "předsálí" pro nabídku software jako službu (SaaS). Po přihlášení nákupčího k odběru nabídky si komerční tržiště přesměruje na cílovou stránku, aby aktivovala a nakonfigurovala své předplatné vaší aplikace SaaS. Představte si to jako krok potvrzení objednávky, který umožňuje kupujícímu zjistit, co si koupili, a potvrdit podrobnosti o svém účtu. Pomocí Azure Active Directory (Azure AD) a Microsoft Graph povolíte pro kupující jednotné přihlašování (SSO) a získáte důležité informace o kupujícím, které můžete použít k potvrzení a aktivaci jejich předplatného, včetně jejich jména, e-mailové adresy a organizace.

Vzhledem k tomu, že informace potřebné k aktivaci předplatného jsou omezené a poskytované službou Azure AD a Microsoft Graph, neměli byste vyžadovat informace, které vyžadují více než základní souhlas. Pokud potřebujete informace o uživateli, které vyžadují další souhlas s vaší aplikací, měli byste tyto informace požádat po dokončení Aktivace předplatného. To umožňuje bezproblémovou aktivaci předplatného pro kupujícího a snižuje riziko jeho přenechání.

Cílová stránka obvykle obsahuje následující:

- Představte si název nabídky a plánovaného nákupu, jakož i fakturační smlouvy.
- Prezentace podrobností účtu kupujícího, včetně křestního jména a příjmení, organizace a e-mailu
- Vyzvat kupujícího, aby potvrdil nebo nahradil jiné podrobnosti účtu.
- Provedete si nákupčího s dalšími kroky po aktivaci. Například dostanete uvítací e-mail, spravujte předplatné, Získejte podporu nebo si přečtěte dokumentaci.

> [!NOTE]
> Kupující bude také směrován na cílovou stránku při správě svého předplatného po aktivaci. Po aktivaci předplatného nákupčího je nutné použít jednotné přihlašování, aby se uživatel mohl přihlásit. Doporučuje se uživateli nasměrovat na profil účtu nebo na stránku konfigurace.

Následující části vás provedou procesem vytvoření cílové stránky:

1. [Vytvořte registraci aplikace Azure AD](#create-an-azure-ad-app-registration) pro cílovou stránku.
1. [Jako výchozí bod pro aplikaci použijte ukázku kódu](#use-a-code-sample-as-a-starting-point) .
1. [Využijte dvě aplikace Azure AD ke zvýšení zabezpečení v produkčním](#use-two-azure-ad-apps-to-improve-security-in-production)prostředí.
1. Pomocí komerčního tržiště [vyřešte identifikační token nákupu pro Marketplace](#resolve-the-marketplace-purchase-identification-token) , který jste přidali k adrese URL.
1. [Přečtěte si informace z deklarací identity kódovaných v tokenu ID](#read-information-from-claims-encoded-in-the-id-token), který jste dostali z Azure AD po přihlášení, které se odeslaly s požadavkem.
1. [Použijte rozhraní Microsoft Graph API](#use-the-microsoft-graph-api) k získání dalších informací, podle potřeby.

## <a name="create-an-azure-ad-app-registration"></a>Vytvoření registrace aplikace Azure AD

Obchod na komerčním webu je plně integrovaný s Azure AD. Nákupčí obdrží na webu Marketplace ověření pomocí [účtu Azure AD nebo účet Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Po zakoupení kupující dostane z komerčního tržiště na adresu URL vaší cílové stránky, aby aktivoval a spravoval předplatné vaší aplikace SaaS. Je nutné, aby se nákupčí přihlásil do vaší aplikace pomocí jednotného přihlašování služby Azure AD. (Adresa URL cílové stránky je uvedena na stránce [technické konfigurace](plan-saas-offer.md#technical-information) nabídky.

Prvním krokem k použití identity je, abyste se ujistili, že je vaše cílová stránka registrovaná jako aplikace Azure AD. Registrace aplikace vám umožní pomocí Azure AD ověřovat uživatele a žádat o přístup k prostředkům uživatele. Může být považována za definici aplikace, která umožňuje službě zjistit, jak vydávat tokeny aplikaci na základě nastavení aplikace.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí portálu Azure Portal

Chcete-li začít, postupujte podle pokynů pro [registraci nové aplikace](../active-directory/develop/quickstart-register-app.md). Pokud chcete umožnit uživatelům z jiných firem, aby si aplikaci navštívili, musíte zvolit jednu z možností víceklientské architektury po zobrazení výzvy, která může aplikaci používat.

Pokud se chystáte zadat dotaz na rozhraní Microsoft Graph API, [nakonfigurujte novou aplikaci pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Když vyberete oprávnění rozhraní API pro tuto aplikaci, výchozí hodnota **User. Read** je dostatečná pro shromáždění základních informací o kupujícím, aby bylo možné proces zprovoznění hladký a automatický. Nevyžadovat žádná oprávnění API s popiskem **vyžaduje souhlas správce** , protože to zabrání všem uživatelům bez oprávnění správce na návštěvě cílové stránky.

Pokud požadujete zvýšená oprávnění jako součást procesu připojování nebo zřizování, zvažte použití funkce [přírůstkového souhlasu](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) Azure AD, aby všichni nákupčíé z webu Marketplace mohli nejprve interaktivně navzájem komunikovat s cílovou stránkou.

## <a name="use-a-code-sample-as-a-starting-point"></a>Použití ukázky kódu jako počátečního bodu

Nabízíme několik ukázkových aplikací, které implementují jednoduchý web s povoleným přihlášením Azure AD. Po registraci aplikace v Azure AD okno **rychlý Start** nabízí seznam běžných typů aplikací a vývojových zásobníků, jak je vidět na obrázku 1. Vyberte ten, který odpovídá vašemu prostředí, a postupujte podle pokynů ke stažení a instalaci.

**_Obrázek 1: okno rychlý Start v Azure Portal_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Znázorňuje okno rychlý Start v Azure Portal.":::

Po stažení kódu a nastavení vývojového prostředí změňte nastavení konfigurace v aplikaci tak, aby odráželo ID aplikace, ID tenanta a tajný klíč klienta, které jste si poznamenali v předchozím postupu. Všimněte si, že přesný postup se liší v závislosti na tom, kterou ukázku používáte.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Použití dvou aplikací Azure AD ke zvýšení zabezpečení v produkčním prostředí

Tento článek představuje zjednodušenou verzi architektury pro implementaci cílové stránky vaší nabídky SaaS pro komerční tržišti. Při spouštění stránky v produkčním prostředí doporučujeme zvýšit zabezpečení prostřednictvím komunikace s rozhraními API pro plnění SaaS prostřednictvím jiné zabezpečené aplikace. K tomu je potřeba vytvořit dvě nové aplikace:

- Nejprve aplikace s víceklientské cílovou stránkou, která je v tomto okamžiku popsána, s výjimkou funkce, která by kontaktovala rozhraní API pro plnění SaaS. Tato funkce bude převedena na jinou aplikaci, jak je popsáno níže.
- Za druhé, aplikace pro vlastní komunikaci s rozhraními API pro plnění SaaS. Tato aplikace by měla být jeden tenant, kterou používá vaše organizace, a seznam řízení přístupu se dá vytvořit tak, aby se omezil přístup k rozhraním API jenom z této aplikace.

To umožňuje řešení pracovat ve scénářích, které sledují princip [oddělení obav](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) . Například cílová stránka používá první registrovanou aplikaci Azure AD k přihlášení uživatele. Po přihlášení uživatele bude cílová stránka používat druhou službu Azure AD k vyžádání přístupového tokenu pro volání rozhraní API pro plnění SaaS a volání operace Resolve.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Vyřešit identifikační token nákupu pro Marketplace

Při odeslání kupujícího na cílovou stránku se do parametru URL přidá token. Tento token se liší od tokenu vydaného službou Azure AD a přístupového tokenu, který se používá pro ověřování služba-služba. používá se jako vstup pro volání [rozhraní API pro splnění SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) k získání podrobností o předplatném. Stejně jako u všech volání rozhraní API pro plnění SaaS se vaše žádost o služby na službu ověří pomocí přístupového tokenu, který je založený na uživateli ID aplikace Azure AD pro ověřování služba-služba.

> [!NOTE]
> Ve většině případů je vhodnější provést toto volání z druhé, jediné klientské aplikace. Viz [použití dvou aplikací Azure AD ke zvýšení zabezpečení v produkčním](#use-two-azure-ad-apps-to-improve-security-in-production) prostředí výše v tomto článku.

### <a name="request-an-access-token"></a>Vyžádání přístupového tokenu

K ověření aplikace pomocí rozhraní API pro plnění SaaS potřebujete přístupový token, který se dá vygenerovat voláním koncového bodu Azure AD OAuth. Podívejte se, [Jak získat autorizační token vydavatele](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>Zavolejte koncový bod vyřešení

Rozhraní API pro splnění SaaS implementují [koncový bod](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) , který lze volat k potvrzení platnosti tokenu Marketplace a vrácení informací o předplatném.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Načíst informace z deklarací identity kódovaných v tokenu ID

V rámci toku [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) Azure AD přidá do žádosti [token ID](../active-directory/develop/id-tokens.md) při odeslání kupujícího na cílovou stránku. Tento token obsahuje několik částí základních informací, které by mohly být užitečné v procesu aktivace, včetně informací zobrazených v této tabulce.

| Hodnota | Popis |
| ------------ | ------------- |
| aud | Zamýšlená cílová skupina pro tento token. V takovém případě by se měl shodovat s ID aplikace a ověřit. |
| preferred_username | Primární uživatelské jméno navštíveného uživatele Může to být e-mailová adresa, telefonní číslo nebo jiný identifikátor. |
| e-mail | E-mailová adresa uživatele Všimněte si, že toto pole může být prázdné. |
| name | Uživatelsky čitelné hodnoty, které identifikují předmět tokenu. V tomto případě se bude jednat o název kupujícího. |
| identifikátor | Identifikátor v systému Microsoft identity, který jednoznačně identifikuje uživatele napříč aplikacemi. Microsoft Graph vrátí tuto hodnotu jako vlastnost ID pro daný uživatelský účet. |
| identifikátory | Identifikátor, který představuje klienta služby Azure AD, ze kterého je nákupčí. V případě MSA identity to bude vždycky ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Další informace najdete v poznámce v následující části: použití rozhraní Microsoft Graph API. |
| jednotk | Identifikátor, který jedinečně identifikuje uživatele v této konkrétní aplikaci. |
|||

## <a name="use-the-microsoft-graph-api"></a>Použití rozhraní Microsoft Graph API

Token ID obsahuje základní informace pro identifikaci kupujícího, ale proces aktivace může vyžadovat další podrobnosti, jako je například společnost kupujícího, aby bylo možné dokončit proces připojování. Použijte [rozhraní Microsoft Graph API](/graph/use-the-api) k vyžádání těchto informací, abyste zabránili vynucení zadání těchto podrobností uživatelem. Ve výchozím nastavení zahrnují oprávnění Standard _ *User. Read* * následující informace.

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

Další vlastnosti, například název společnosti uživatele nebo umístění uživatele (země), lze vybrat k zařazení do žádosti. Další podrobnosti najdete v tématu [vlastnosti pro typ prostředku uživatele](/graph/api/resources/user?view=graph-rest-1.0#properties) .

Většina aplikací zaregistrovaných ve službě Azure AD uděluje delegovaná oprávnění ke čtení informací o uživateli z tenanta Azure AD společnosti. Každý požadavek na Microsoft Graph pro tyto informace musí být doprovázený přístupovým tokenem pro ověřování. Konkrétní kroky pro vygenerování přístupového tokenu budou záviset na použitém zásobníku technologie, ale vzorový kód bude obsahovat příklad. Další informace najdete v tématu [získání přístupu jménem uživatele](/graph/auth-v2-user).

> [!NOTE]
> Účty z klienta MSA (s ID tenanta ``9188040d-6c67-4c5b-b112-36a304b66dad`` ) nebudou vracet více informací, než již byly shromážděny pomocí tokenu ID. Toto volání Graph API pro tyto účty můžete přeskočit.

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit nabídku SaaS na komerčním webu Marketplace](create-new-saas-offer.md)