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
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: a5ba50b65cea573b8dbfdb17f671caee37cba459
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731492"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Rychlý Start: registrace aplikace s platformou Microsoft identity

V tomto rychlém startu zaregistrujete aplikaci do Azure Portal, aby platforma Microsoft Identity Service mohla poskytovat služby ověřování a autorizace pro vaši aplikaci a její uživatele.

Microsoft Identity Platform provádí správu identit a přístupu (IAM) pouze u registrovaných aplikací. Bez ohledu na to, jestli se jedná o klientskou aplikaci, jako je webová nebo mobilní aplikace, nebo se jedná o webové rozhraní API, které zálohuje klientskou aplikaci, registrace vytvoří vztah důvěryhodnosti mezi vaší aplikací a poskytovatelem identity a platformou Microsoft identity.

## <a name="prerequisites"></a>Požadavky

* Účet Azure, který má aktivní předplatné. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dokončení nastavení rychlého startu [tenanta](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registrace aplikace

Při registraci aplikace se vytvoří vztah důvěryhodnosti mezi vaší aplikací a platformou Microsoft identity. Vztah důvěryhodnosti je jednosměrný: vaše aplikace důvěřuje platformě identity Microsoftu, a ne jiným způsobem.

Při vytváření registrace aplikace postupujte podle těchto kroků:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte v horní nabídce možnost **adresář + filtr předplatného** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte zobrazovaný **název** vaší aplikace. Uživatelé vaší aplikace se můžou při používání aplikace zobrazit zobrazované jméno, například během přihlašování.
    Zobrazovaný název můžete kdykoli změnit a několik registrací aplikací může sdílet stejný název. Automatické vygenerované ID aplikace (klienta) registrace aplikace, ne jeho zobrazované jméno, jednoznačně identifikuje vaši aplikaci v rámci platformy identity.
1. Určete, kdo může používat aplikaci, někdy označovanou jako *přihlášená cílová skupina*.

    | Podporované typy účtu | Description |
    |-------------------------|-------------|
    | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte aplikaci pro použití jenom pro uživatele (nebo hosty) ve *vašem* tenantovi.<br><br>Často se říká *obchodní* aplikace (LOB), tato aplikace je *jediná klientská* aplikace na platformě Microsoft identity. |
    | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete, aby uživatelé v *jakémkoli* Azure Active Directoryovém Tenantovi (Azure AD) mohli aplikaci používat. Tato možnost je vhodná, pokud například vytváříte aplikaci typu software jako služba (SaaS), kterou máte v úmyslu poskytnout více organizacím.<br><br>Tento typ aplikace se označuje jako *víceklientské* aplikace na platformě Microsoft identity. |
    | **Účty v libovolném organizačním adresáři a osobní účty Microsoft** | Tuto možnost vyberte, pokud chcete cílit na co nejširší okruh zákazníků.<br><br>Výběrem této možnosti zaregistrujete *víceklientské* aplikace, která může podporovat také uživatele, kteří mají osobní *účty Microsoft*. |
    | **Osobní účty Microsoft** | Tuto možnost vyberte, pokud vytváříte aplikaci jenom pro uživatele, kteří mají osobní účty Microsoft. Osobní účty Microsoft zahrnují účty Skype, Xbox, Live a Hotmail. |

1. Nezadávejte nic pro **identifikátor URI přesměrování (volitelné)**. V další části nakonfigurujete identifikátor URI pro přesměrování.
1. Kliknutím na **Registrovat** dokončíte prvotní registraci aplikace.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Snímek obrazovky Azure Portal ve webovém prohlížeči, v němž se zobrazuje podokno Registrovat aplikaci":::

Po dokončení registrace se v Azure Portal zobrazí podokno s **přehledem** registrace aplikace. Zobrazí se **ID aplikace (klienta)**. Označuje se taky jako *ID klienta*. Tato hodnota jednoznačně identifikuje vaši aplikaci na platformě Microsoft identity. 

> [!IMPORTANT]
> Nové registrace aplikací jsou ve výchozím nastavení skryty uživatelům. Až budete připraveni na to, aby uživatelé aplikaci viděli na své [stránce Moje aplikace](../user-help/my-apps-portal-end-user-access.md) , můžete ji povolit. Pokud chcete aplikaci povolit, přejděte v Azure Portal na **Azure Active Directory**  >  **podnikové aplikace** a vyberte aplikaci. Pak na stránce **vlastnosti** přepínací tlačítko **Zobrazit uživatelům?** na Ano.

Kód vaší aplikace, nebo častěji knihovna ověřování použitá ve vaší aplikaci, používá také ID klienta. ID se používá jako součást ověřování tokenů zabezpečení, které přijímá z platformy identity.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Snímek obrazovky Azure Portal ve webovém prohlížeči, který zobrazuje podokno s přehledem registrace aplikace.":::

## <a name="add-a-redirect-uri"></a>Přidat identifikátor URI pro přesměrování

*Identifikátor URI přesměrování* je umístění, kde rozhraní Microsoft Identity Platform přesměruje klienta uživatele a odesílá tokeny zabezpečení po ověření.

V produkční webové aplikaci je například identifikátor URI přesměrování často veřejným koncovým bodem, ve kterém je vaše aplikace spuštěná, třeba `https://contoso.com/auth-response` . Během vývoje je běžné také přidat koncový bod, ve kterém aplikaci spouštíte místně, například `https://127.0.0.1/auth-response` nebo `http://localhost/auth-response` .

Můžete přidat a upravit identifikátory URI pro přesměrování registrovaných aplikací konfigurací jejich [Nastavení platformy](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Konfigurovat nastavení platformy

Nastavení pro každý typ aplikace, včetně identifikátorů URI přesměrování, se konfigurují v **konfiguracích platforem** v Azure Portal. Některé platformy, jako jsou **webové** a **jednostránkové aplikace**, vyžadují ruční určení identifikátoru URI přesměrování. Pro jiné platformy, jako je Mobile a Desktop, můžete při konfiguraci jejich dalších nastavení vybrat možnost z identifikátorů URI pro přesměrování vygenerovaných za vás.

Konfigurace nastavení aplikace na základě platformy nebo zařízení, na které cílíte:

1. V Azure Portal v části **Registrace aplikací** vyberte svou aplikaci.
1. V části **Spravovat** vyberte **ověřování**.
1. V části **konfigurace platformy** vyberte **Přidat platformu**.
1. V části **Konfigurovat platformy** vyberte dlaždici pro typ aplikace (platforma) a nakonfigurujte její nastavení.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Snímek obrazovky s podoknem konfigurace platformy v Azure Portal." border="false":::

    | Platforma | Nastavení konfigurace |
    | -------- | ---------------------- |
    | **Web** | Zadejte **identifikátor URI pro přesměrování** vaší aplikace. Tento identifikátor URI je umístění, kde Microsoft Identity Platform přesměruje klienta uživatele a odesílá tokeny zabezpečení po ověření.<br/><br/>Vyberte tuto platformu pro standardní webové aplikace, které běží na serveru. |
    | **Jednostránková aplikace** | Zadejte **identifikátor URI pro přesměrování** vaší aplikace. Tento identifikátor URI je umístění, kde Microsoft Identity Platform přesměruje klienta uživatele a odesílá tokeny zabezpečení po ověření.<br/><br/>Tuto platformu vyberte, pokud vytváříte webovou aplikaci na straně klienta pomocí JavaScriptu nebo architektury, jako je například úhlová, Vue.js, React.js nebo Blazor WebAssembly. |
    | **iOS/macOS** | Zadejte **ID sady prostředků** aplikace. Najdete ho v **nastavení sestavení** nebo v Xcode v souboru *info. plist*.<br/><br/>Identifikátor URI přesměrování se vygeneruje při zadání **ID sady prostředků**. |
    | **Android** | Zadejte **název balíčku** aplikace. Najdete ho v souboru *AndroidManifest.xml* . Také vygenerujte a zadejte **hodnotu hash podpisu**.<br/><br/>Identifikátor URI přesměrování se vygeneruje při zadání těchto nastavení. |
    | **Mobilní a desktopové aplikace** | Vyberte jeden z **navrhovaných identifikátorů URI pro přesměrování**. Nebo zadejte **vlastní identifikátor URI pro přesměrování**.<br/><br/>U aplikací klasické pracovní plochy pomocí vloženého prohlížeče doporučujeme<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Pro desktopové aplikace, které používají systémový prohlížeč, doporučujeme<br/>`http://localhost`<br/><br/>Vyberte tuto platformu pro mobilní aplikace, které nepoužívají nejnovější knihovnu Microsoft Authentication Library (MSAL), nebo nepoužívají zprostředkovatele. Vyberte taky tuto platformu pro desktopové aplikace. |
1. Vyberte **Konfigurovat** a dokončete konfiguraci platformy.

### <a name="redirect-uri-restrictions"></a>Omezení identifikátoru URI přesměrování

Existují určitá omezení formátu identifikátorů URI přesměrování přidaných do registrace aplikace. Podrobnosti o těchto omezeních najdete v článku o [omezeních a omezeních identifikátoru URI přesměrování (adresa URL odpovědi)](reply-url.md).

## <a name="add-credentials"></a>Přidat přihlašovací údaje

Přihlašovací údaje jsou používány [důvěrnými klientskými aplikacemi](msal-client-applications.md) , které přistupují k webovému rozhraní API. Příklady důvěrných klientů jsou [webové aplikace](scenario-web-app-call-api-overview.md), jiná [webová rozhraní API](scenario-protected-web-api-overview.md)nebo [aplikace typu Service-Type a démon](scenario-daemon-overview.md). Přihlašovací údaje umožňují, aby se vaše aplikace ověřovala jako samotná a nevyžadovala žádné interakce od uživatele za běhu. 

K registraci důvěrných klientských aplikací můžete přidat jak certifikáty, tak i klientské tajné klíče (řetězce) jako přihlašovací údaje.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Snímek obrazovky Azure Portal, který zobrazuje podokno certifikátů a tajných klíčů v registraci aplikace.":::

### <a name="add-a-certificate"></a>Přidat certifikát

Někdy se označuje jako *veřejný klíč*, certifikát je doporučeným typem přihlašovacích údajů. Poskytuje větší záruku než tajný klíč klienta. Další informace o použití certifikátu jako metody ověřování v aplikaci najdete v tématu [přihlašovací údaje certifikátu pro ověřování aplikací platformy Microsoft Identity Platform](active-directory-certificate-credentials.md).

1. V Azure Portal v části **Registrace aplikací** vyberte svou aplikaci.
1. Vyberte **certifikáty & tajných klíčů**  >  **nahrát certifikát**.
1. Vyberte soubor, který chcete odeslat. Musí to být jeden z následujících typů souborů: *. cer*, *. pem*, *. CRT*.
1. Vyberte **Přidat**.

### <a name="add-a-client-secret"></a>Přidat tajný klíč klienta

Tajný kód klienta je také označován jako *heslo aplikace*. Je to hodnota řetězce, kterou může aplikace použít místo certifikátu k samotné identitě. Tajný kód klienta je snazší pro dva typy přihlašovacích údajů, které se mají použít. Často se používá během vývoje, ale je považováno za méně bezpečné než certifikát. Používejte certifikáty ve svých aplikacích, které běží v produkčním prostředí. 

Další informace o doporučeních pro zabezpečení aplikací najdete v tématu [osvědčené postupy a doporučení pro Microsoft Identity Platform](identity-platform-integration-checklist.md#security).

1. V Azure Portal v části **Registrace aplikací** vyberte svou aplikaci.
1. Vyberte **certifikáty & tajných klíčů**  >   **nový tajný klíč klienta**.
1. Přidejte popis tajného klíče klienta.
1. Vyberte dobu trvání.
1. Vyberte **Přidat**.
1. *Poznamenejte si hodnotu tajného klíče* pro použití v kódu klientské aplikace. Tato hodnota tajného klíče se po opuštění této stránky už *znovu nezobrazí* .


## <a name="next-steps"></a>Další kroky

Klientské aplikace obvykle potřebují přístup k prostředkům ve webovém rozhraní API. Klientské aplikace můžete chránit pomocí platformy Microsoft identity. Můžete také použít platformu pro autorizaci v oboru, přístup na základě oprávnění k webovému rozhraní API.

Pokud chcete vytvořit další registraci aplikace pro vaše webové rozhraní API a vystavit jeho obory, přečtěte si další rychlý Start v řadě.

> [!div class="nextstepaction"]
> [Konfigurace aplikace k vystavení webového rozhraní API](quickstart-configure-app-expose-web-apis.md)
