---
title: 'Rychlý Start: registrace aplikace na platformě Microsoft identity | Azure'
description: V tomto rychlém startu se dozvíte, jak zaregistrovat aplikaci s platformou Microsoft identity.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 9cd59d6bf5b9bf6e17cba0786bfac27ed12d7638
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258128"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Rychlý Start: registrace aplikace s platformou Microsoft identity

V tomto rychlém startu zaregistrujete aplikaci do Azure Portal, aby platforma Microsoft Identity Service mohla poskytovat služby ověřování a autorizace pro vaši aplikaci a její uživatele.

Každá aplikace, pro kterou má platforma Microsoft Identity provádět správu identit a přístupu (IAM), musí být zaregistrovaná. Bez ohledu na to, jestli se jedná o klientskou aplikaci, jako je webová nebo mobilní aplikace, nebo se jedná o webové rozhraní API, které zálohuje klientskou aplikaci, registrace vytvoří vztah důvěryhodnosti mezi vaší aplikací a poskytovatelem identity a platformou Microsoft identity.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným – [vytvořit účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Dokončení [rychlého startu: nastavení tenanta](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registrace aplikace

Při registraci aplikace se vytvoří vztah důvěryhodnosti mezi vaší aplikací a platformou Microsoft identity. Vztah důvěryhodnosti je jednosměrný: vaše aplikace důvěřuje platformě identity Microsoftu, a ne jiným způsobem.

Při vytváření registrace aplikace postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/quickstart-register-app/portal-01-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat**vyberte **Registrace aplikací**a pak **Nová registrace**.
1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. Určete, kdo může používat aplikaci, někdy označovanou jako *skupina pro přihlašování*.

    | Podporované typy účtu | Popis |
    |-------------------------|-------------|
    | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte aplikaci pro použití jenom pro uživatele (nebo hosty) ve *vašem* tenantovi.<br><br>Je často označována jako *obchodní* aplikace (LOB), což je **jediná klientská** aplikace na platformě Microsoft identity. |
    | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete, aby se vaše aplikace mohla používat pro uživatele v *jakémkoli* tenantovi služby Azure AD. Tato možnost je vhodná, pokud například vytváříte aplikaci typu software jako služba (SaaS), kterou máte v úmyslu poskytnout více organizacím.<br><br>Tato aplikace se označuje jako **víceklientská** aplikace na platformě Microsoft identity. |
    | **Účty v libovolném organizačním adresáři a osobní účty Microsoft** | Tuto možnost vyberte, pokud chcete cílit na co nejširší okruh zákazníků.<br><br>Výběrem této možnosti zaregistrujete **víceklientské** aplikace, která může podporovat i uživatele s osobními **účty Microsoft** (MSA). |
    | **Osobní účty Microsoft** | Tuto možnost vyberte, pokud vytváříte aplikaci pro použití jenom pro uživatele s osobními účty Microsoft. Osobní účty Microsoft zahrnují účty Skype, Xbox, Live a Hotmail. |

1. Nezadávejte nic pro **identifikátor URI přesměrování (volitelné)**, nakonfigurujete ho v další části.
1. Kliknutím na **Registrovat** dokončíte prvotní registraci aplikace.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Snímek obrazovky Azure Portal ve webovém prohlížeči, který zobrazuje podokno Registrovat aplikaci":::

Po dokončení registrace Azure Portal zobrazí podokno **Přehled** registrace aplikace, které zahrnuje **ID aplikace (klienta)**. Tato hodnota je také označována jako *ID klienta*. Tato hodnota jednoznačně identifikuje vaši aplikaci na platformě Microsoft identity.

Kód vaší aplikace, nebo častěji knihovny ověřování používané v aplikaci, používá také ID klienta jako jeden aspekt při ověřování tokenů zabezpečení, které obdrží od platformy identity.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Snímek obrazovky Azure Portal ve webovém prohlížeči, který zobrazuje podokno s přehledem registrace aplikace.":::

## <a name="add-a-redirect-uri"></a>Přidat identifikátor URI pro přesměrování

Identifikátor URI přesměrování je umístění, kde rozhraní Microsoft Identity Platform přesměruje klienta uživatele a odesílá tokeny zabezpečení po ověření.

V produkční webové aplikaci je například identifikátor URI přesměrování často veřejným koncovým bodem, ve kterém je vaše aplikace spuštěná, třeba `https://contoso.com/auth-response` . Během vývoje je běžné také přidat koncový bod, ve kterém aplikaci spouštíte místně, například `https://127.0.0.1/auth-response` nebo `http://localhost/auth-response` .

Můžete přidat a upravit identifikátory URI pro přesměrování registrovaných aplikací konfigurací jejich [Nastavení platformy](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Konfigurovat nastavení platformy

Nastavení pro každý typ aplikace, včetně identifikátorů URI přesměrování, se konfigurují v **konfiguracích platforem** v Azure Portal. Některé platformy, jako jsou **webové** a **jednostránkové aplikace**, vyžadují ruční určení identifikátoru URI přesměrování. Pro jiné platformy, jako je Mobile a Desktop, můžete při konfiguraci jejich dalších nastavení vybrat možnost z identifikátorů URI pro přesměrování vygenerovaných za vás.

Konfigurace nastavení aplikace na základě platformy nebo zařízení, na které cílíte:

1. V Azure Portal vyberte svou aplikaci v **Registrace aplikací** .
1. V části **Spravovat**vyberte **ověřování**.
1. V části **konfigurace platformy**vyberte **Přidat platformu**.
1. V části **Konfigurovat platformy**vyberte dlaždici pro typ aplikace (platforma) a nakonfigurujte její nastavení.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Snímek obrazovky s podoknem konfigurace platformy v Azure Portal" border="false":::

    | Platforma | Nastavení konfigurace |
    | -------- | ---------------------- |
    | **Web** | Zadejte **identifikátor URI pro přesměrování** vaší aplikace, umístění, kde Microsoft Identity Platform přesměruje klienta uživatele a pošle tokeny zabezpečení po ověření.<br/><br/>Vyberte tuto platformu pro standardní webové aplikace, které běží na serveru. |
    | **Jednostránková aplikace** | Zadejte **identifikátor URI pro přesměrování** vaší aplikace, umístění, kde Microsoft Identity Platform přesměruje klienta uživatele a pošle tokeny zabezpečení po ověření.<br/><br/>Tuto platformu vyberte, pokud vytváříte webovou aplikaci na straně klienta v jazyce JavaScript nebo pomocí architektury, jako je například úhlová, Vue.js, React.js nebo Blazor WebAssembly. |
    | **iOS/macOS** | Zadejte **ID sady prostředků**aplikace, které najdete v Xcode v souboru *info. plist* nebo nastavení sestavení.<br/><br/>Identifikátor URI přesměrování se vygeneruje při zadání ID sady prostředků. |
    | **Android** | Zadejte **název balíčku**aplikace, který můžete najít v souboru *AndroidManifest.xml* a vygenerujte a zadejte **hodnotu hash podpisu**.<br/><br/>Identifikátor URI přesměrování se vygeneruje při zadání těchto nastavení. |
    | **Mobilní a desktopové aplikace** | Vyberte jeden z **navrhovaných identifikátorů URI pro přesměrování** nebo zadejte **vlastní identifikátor URI pro přesměrování**.<br/>U desktopových aplikací doporučujeme:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Vyberte tuto platformu pro mobilní aplikace, které nepoužívají nejnovější knihovnu Microsoft Authentication Library (MSAL) nebo nepoužívají zprostředkovatele. Vyberte taky tuto platformu pro desktopové aplikace. |
1. Vyberte **Konfigurovat** a dokončete konfiguraci platformy.

### <a name="redirect-uri-restrictions"></a>Omezení identifikátoru URI přesměrování

Existují určitá omezení formátu identifikátorů URI přesměrování přidaných do registrace aplikace. Podrobnosti o těchto omezeních najdete v článku o [omezeních a omezeních identifikátoru URI přesměrování (adresa URL odpovědi)](reply-url.md).

## <a name="add-credentials"></a>Přidat přihlašovací údaje

Přihlašovací údaje jsou používány důvěrnými klientskými aplikacemi, které přistupují k webovému rozhraní API. Příklady důvěrných klientů jsou webové aplikace, jiná webová rozhraní API nebo aplikace typu démon a Service-Type. Přihlašovací údaje umožňují, aby se vaše aplikace ověřovala jako samotná a nevyžadovala žádné interakce od uživatele za běhu.

K registraci důvěrných klientských aplikací můžete přidat jak certifikáty, tak i klientské tajné klíče (řetězce) jako přihlašovací údaje.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Snímek obrazovky Azure Portal, který zobrazuje podokno certifikátů a tajných klíčů v registraci aplikace":::

### <a name="add-a-certificate"></a>Přidat certifikát

V případě, že se někdy označuje jako *veřejný klíč*, jsou certifikáty doporučeným typem přihlašovacích údajů, protože poskytují vyšší úroveň zabezpečení než tajný klíč klienta.

1. V Azure Portal vyberte svou aplikaci v **Registrace aplikací** .
1. Vyberte **certifikáty & tajných klíčů**  >  **nahrát certifikát**.
1. Vyberte soubor, který chcete nahrát. Musí jít o jeden z následujících typů souboru: .cer, .pem nebo .crt.
1. Vyberte **Přidat**.

### <a name="add-a-client-secret"></a>Přidat tajný klíč klienta

Tajný kód klienta, označovaný také jako *heslo aplikace*, je hodnota řetězce, kterou může aplikace použít místo certifikátu k samotné identitě. To je snazší pro použití těchto dvou typů přihlašovacích údajů, které se často používají během vývoje, ale považuje se za méně bezpečné než certifikát. Ve svých aplikacích běžících v produkčním prostředí byste měli používat certifikáty.

1. V Azure Portal vyberte svou aplikaci v **Registrace aplikací** .
1. Vyberte **certifikáty & tajných klíčů**  >   **nový tajný klíč klienta**.
1. Přidejte popis tajného klíče klienta.
1. Vyberte dobu trvání.
1. Vyberte **Přidat**.
1. **Poznamenejte si hodnotu tajného klíče** pro použití v kódu klientské aplikace – po opuštění této stránky se už *nezobrazí* .

## <a name="next-steps"></a>Další kroky

Klientské aplikace obvykle potřebují přístup k prostředkům ve webovém rozhraní API. Kromě ochrany klientských aplikací s platformou Microsoft Identity můžete použít platformu pro autorizaci v oboru, přístup na základě oprávnění k webovému rozhraní API.

Přejděte k dalšímu rychlému startu v řadě, abyste vytvořili další registraci aplikace pro vaše webové rozhraní API a vystavili své obory.

> [!div class="nextstepaction"]
> [Konfigurace aplikace k vystavení webového rozhraní API](quickstart-configure-app-expose-web-apis.md)
