---
title: Konfigurace aplikace pro přístup k webovým rozhraním API – Microsoft Identity Platform | Azure
description: Zjistěte, jak nakonfigurovat aplikaci zaregistrovanou na platformě Microsoft Identity Platform tak, aby zahrnovala identifikátory URI pro přesměrování, přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 32691892ccae31541855f47bd8274aa28b6dc185
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704285"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API

Aby se webová nebo důvěrná klientská aplikace mohla účastnit toku udělení autorizace, který vyžaduje ověření (a získání přístupového tokenu), musí mít zabezpečené přihlašovací údaje. Výchozí metodou ověřování podporovanou webem Azure Portal je ID klienta a tajný klíč.

Kromě toho, než klient získá přístup k webovému rozhraní API zveřejněnému aplikací prostředků (jako je rozhraní Microsoft Graph API), rozhraní pro udělování souhlasu zajišťuje, že klient musí nejprve na základě vyžádání oprávnění získat udělení oprávnění. Ve výchozím nastavení si můžou všechny aplikace zvolit oprávnění z rozhraní Microsoft Graph API. Ve výchozím nastavení je vybrané [oprávnění rozhraní Graph API pro „přihlášení a čtení uživatelského profilu“](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions). U každého požadovaného webového rozhraní API si můžete vybrat ze [dvou typů oprávnění](developer-glossary.md#permissions):

* **Oprávnění aplikace** – Klientská aplikace vyžaduje přímý přístup k webovému rozhraní API sama za sebe (bez kontextu uživatele). Tento typ oprávnění vyžaduje souhlas správce a navíc není k dispozici pro veřejné (desktopové a mobilní) klientské aplikace.
* **Delegovaná oprávnění** – Klientská aplikace vyžaduje přístup k webovému rozhraní API jako přihlášený uživatel, ale s přístupem omezeným vybraným oprávněním. Pokud tento typ oprávnění nevyžaduje souhlas správce, může ho udělit uživatel.

  > [!NOTE]
  > Přidání delegovaného oprávnění do aplikace neuděluje uživatelům v tenantovi souhlas automaticky. Pokud správce neudělí souhlas jménem všech uživatelů, musí uživatelé stále souhlas pro delegovaná oprávnění přidaná za běhu udělit ručně.

V tomto rychlém startu si ukážeme, jak nakonfigurovat aplikaci následujícím způsobem:

* [Přidání identifikátorů URI pro přesměrování do aplikace](#add-redirect-uris-to-your-application)
* [Konfigurace upřesňujících nastavení pro vaši aplikaci](#configure-advanced-settings-for-your-application)
* [Upravit podporované typy účtů](#modify-supported-account-types)
* [Přidání přihlašovacích údajů do webové aplikace](#add-credentials-to-your-web-application)
* [Přidání oprávnění pro přístup k webovým rozhraním API](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* Přečtěte si o podporovaných [oprávněních a souhlasu](v2-permissions-and-consent.md), kterým je důležité rozumět při vytváření aplikací, které budou používat jiní uživatelé či jiné aplikace.
* Máte tenanta, ke kterému jsou zaregistrované aplikace.
  * Pokud nemáte žádné zaregistrované aplikace, [přečtěte si o registraci aplikací na platformě Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

Než budete moct nakonfigurovat aplikaci, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Vyhledejte a vyberte **Azure Active Directory**. 
1. V levém podokně vyberte **Registrace aplikací**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** neboli hlavní stránka registrace dané aplikace.
1. Podle následujícího postupu nakonfigurujte aplikaci pro přístup k webovým rozhraním API:
    * [Přidání identifikátorů URI pro přesměrování do aplikace](#add-redirect-uris-to-your-application)
    * [Konfigurace upřesňujících nastavení pro vaši aplikaci](#configure-advanced-settings-for-your-application)
    * [Upravit podporované typy účtů](#modify-supported-account-types)
    * [Přidání přihlašovacích údajů do webové aplikace](#add-credentials-to-your-web-application)
    * [Přidání oprávnění pro přístup k webovým rozhraním API](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Přidání identifikátorů URI pro přesměrování do aplikace

Přidání identifikátorů URI pro přesměrování do aplikace:

1. Na stránce **Přehled** aplikace vyberte část **Ověřování**.
1. Pokud chcete přidat vlastní identifikátor URI pro přesměrování pro webové a veřejné klientské aplikace, postupujte následovně:
   1. Vyhledejte část **Identifikátor URI pro přesměrování**.
   1. Vyberte typ aplikace, kterou vytváříte: **Web** nebo **Veřejný klient (mobilní a desktopová zařízení)** .
   1. Zadejte URI pro přesměrování pro vaši aplikaci.
      * V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `http://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
      * V případě veřejných aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, například: `https://MyFirstApp`.

1. Pokud si chcete vybrat z navrhovaných identifikátorů URI pro veřejné klienty (mobilní, desktopové), postupujte následovně:
    1. Vyhledejte část **Navrhované identifikátory URI pro přesměrování pro veřejné klienty (mobilní, desktopové)** .
    1. Pomocí zaškrtávacích políček vyberte odpovídající identifikátory URI pro přesměrování pro vaši aplikaci. Můžete také zadat vlastní identifikátor URI pro přesměrování. Pokud si nejste jisti, co použít, podívejte se do dokumentace ke knihovně.

Existují určitá omezení, která platí pro přesměrování identifikátorů URI. Přečtěte si další informace o [omezeních a omezeních identifikátoru URI přesměrování](https://docs.microsoft.com/azure/active-directory/develop/reply-url).
> [!NOTE]
> Vyzkoušejte si nové prostředí nastavení **ověřování** , kde můžete nakonfigurovat nastavení pro svou aplikaci na základě platformy nebo zařízení, na které chcete cílit.
>
> Chcete-li zobrazit toto zobrazení, vyberte možnost **vyzkoušet nové prostředí** z výchozího zobrazení stránky **ověřování** .
>
> ![Kliknutím na vyzkoušet nové prostředí zobrazíte zobrazení konfigurace platformy.](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Tím přejdete na [stránku nové **konfigurace platformy** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurace upřesňujících nastavení pro vaši aplikaci

V závislosti na aplikaci, kterou zaregistrujete, existuje několik dalších nastavení, která budete možná muset nakonfigurovat, třeba:

* **Odhlašovací adresa URL**
* U aplikací s jednou stránkou můžete povolit **implicitní udělení** a vybrat tokeny, které chcete, aby koncový bod autorizace vydával.
* U aplikací klasické pracovní plochy, které získávají tokeny s integrovaným ověřováním systému Windows, tokem kódu zařízení nebo uživatelským jménem/heslem v části **výchozí typ klienta** , nakonfigurujte nastavení **považovat aplikaci jako veřejného klienta** na **Ano**.
* Pro starší aplikace, které používaly sadu Live SDK pro integraci se službou účet Microsoft, nakonfigurujte **podporu sady Live SDK**. Nové aplikace toto nastavení nepotřebují.
* **Výchozí typ klienta**

### <a name="modify-supported-account-types"></a>Upravit podporované typy účtů

**Podporované typy účtů** určují, kdo může použít aplikaci nebo získat přístup k rozhraní API.

Po [nakonfigurování podporovaných typů účtu](quickstart-register-app.md) při prvotní registraci aplikace lze toto nastavení změnit pouze pomocí editoru manifestu aplikace, pokud:

* Můžete změnit typy účtů z **AzureADMyOrg** nebo **AzureADMultipleOrgs** na **AzureADandPersonalMicrosoftAccount**, nebo naopak.
* Můžete změnit typy účtů z **AzureADMyOrg** na **AzureADMultipleOrgs**nebo naopak.

Změna podporovaných typů účtů pro existující registraci aplikace:

* Viz téma [Konfigurace manifestu aplikace](reference-app-manifest.md) a aktualizace klíče `signInAudience`.

## <a name="configure-platform-settings-for-your-application"></a>Konfigurace nastavení platformy pro vaši aplikaci

[![nakonfigurovat nastavení vaší aplikace na základě platformy nebo zařízení](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

Při konfiguraci nastavení aplikace na základě platformy nebo zařízení cílíte na tyto možnosti:

1. Na stránce **konfigurace platformy** vyberte možnost **Přidat platformu** a zvolte z dostupných možností.

   ![Zobrazí stránku konfigurace platforem.](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Zadejte informace o nastavení na základě vybrané platformy.

   | Platforma                | Choices              | Nastavení konfigurace            |
   |-------------------------|----------------------|-----------------------------------|
   | **Webové aplikace**    | **Web**              | Zadejte **identifikátor URI pro přesměrování** vaší aplikace. |
   | **Mobilní aplikace** | **iOS**              | Zadejte **ID sady prostředků**aplikace, které najdete v Xcode v souboru info. plist nebo nastavení sestavení. Přidání ID sady prostředků automaticky vytvoří identifikátor URI přesměrování pro aplikaci. |
   |                         | **Android**          | * Zadejte **název balíčku**aplikace, který můžete najít v souboru souboru AndroidManifest. XML.<br/>* Vygenerujte a zadejte **hodnotu hash podpisu**. Přidání hodnoty hash signatury automaticky vytvoří identifikátor URI přesměrování pro aplikaci.  |
   | **Plocha a zařízení**   | **Plocha a zařízení** | Volitelné. Pokud vytváříte aplikace pro stolní počítače a zařízení, vyberte jeden z doporučených **identifikátorů URI pro přesměrování** .<br/>Volitelné. Zadejte **vlastní identifikátor URI pro přesměrování**, který se používá jako umístění, kde Azure AD bude uživatele přesměrovat v reakci na žádosti o ověření. Například pro aplikace .NET Core, kde chcete interakce, použijte `https://localhost`. |

   > [!IMPORTANT]
   > Pro mobilní aplikace, které nepoužívají nejnovější knihovnu MSAL nebo nepoužívají zprostředkovatele, je nutné nakonfigurovat identifikátory URI přesměrování pro tyto aplikace v **desktopových a mobilních zařízeních**.

1. V závislosti na platformě, kterou jste zvolili, můžou existovat další nastavení, která můžete konfigurovat. Pro **Web** Apps můžete:
    * Přidat další identifikátory URI pro přesměrování
    * Pokud chcete vybrat tokeny, které byste chtěli vystavit pomocí koncového bodu autorizace, nakonfigurujte **implicitní udělení** :
        * U jednostránkovéch aplikací vyberte **přístupové tokeny** a **tokeny ID** .
        * Pro Web Apps vyberte **tokeny ID** .

## <a name="add-credentials-to-your-web-application"></a>Přidání přihlašovacích údajů do webové aplikace

Přidání přihlašovacích údajů do webové aplikace:

1. Na stránce **Přehled** aplikace vyberte část **Certifikáty a tajné klíče**.

1. Pokud chcete přidat certifikát, postupujte následovně:

    1. Vyberte **Nahrát certifikát**.
    1. Vyberte soubor, který chcete nahrát. Musí jít o jeden z následujících typů souboru: .cer, .pem nebo .crt.
    1. Vyberte **Přidat**.

1. Pokud chcete přidat tajný klíč klienta, postupujte následovně:

    1. Vyberte **Nový tajný klíč klienta**.
    1. Přidejte popis tajného klíče klienta.
    1. Vyberte dobu trvání.
    1. Vyberte **Přidat**.

> [!NOTE]
> Po uložení změn konfigurace bude krajní pravý sloupec obsahovat hodnotu tajného klíče klienta. **Nezapomeňte si tuto hodnotu zkopírovat**, abyste ji mohli použít v kódu klientské aplikace, protože jinak se k ní už nedostanete, když tuto stránku opustíte.

## <a name="add-permissions-to-access-web-apis"></a>Přidání oprávnění pro přístup k webovým rozhraním API

Přidání oprávnění pro přístup k rozhraní API prostředků z klienta:

1. Na stránce **Přehled** aplikace vyberte **Oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění** vyberte tlačítko **Přidat oprávnění** .
1. Ve výchozím nastavení si v tomto zobrazení můžete vybrat z **rozhraní API Microsoftu**. Vyberte sekci rozhraní API, která vás zajímají:
    * **Rozhraní API Microsoftu** – Umožňuje vybrat oprávnění pro rozhraní API Microsoftu, jako je Microsoft Graph.
    * **Rozhraní API, která používá moje organizace** – Umožňuje vybrat oprávnění pro rozhraní API zveřejněná vaší organizací nebo rozhraní API, která vaše organizace integrovala.
    * **Moje rozhraní API** – Umožňuje vybrat oprávnění pro rozhraní API, která jste zveřejnili.
1. Jakmile vyberete rozhraní API, zobrazí se stránka **Požádat o oprávnění rozhraní API**. Pokud rozhraní API zveřejňuje delegovaná oprávnění i oprávnění aplikace, vyberte, který typ oprávnění vaše aplikace potřebuje.
1. Až budete hotovi, vyberte **Přidat oprávnění**. Vrátíte se na stránku **Oprávnění rozhraní API**, kde se v tabulce zobrazí uložená a přidaná oprávnění.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Principy oprávnění k rozhraní API a uživatelského rozhraní pro vyjádření souhlasu správce

### <a name="configured-permissions"></a>Nakonfigurovaná oprávnění

V této části se zobrazují oprávnění, která jsou explicitně nakonfigurovaná u objektu aplikace (\the oprávnění, která jsou součástí seznamu požadavků na přístup k požadovaným prostředkům v aplikaci). V této tabulce můžete přidat nebo odebrat oprávnění. Jako správce můžete taky udělit nebo odvolat souhlas správce pro sadu oprávnění rozhraní API nebo individuálních oprávnění v této části.

### <a name="other-permissions-granted"></a>Další udělená oprávnění

Pokud je vaše aplikace registrovaná v tenantovi, může se zobrazit další oddíl s názvem **Další oprávnění udělená pro tenanta**. V této části se zobrazují oprávnění udělená pro tenanta, ale nebyla explicitně nakonfigurovaná u objektu aplikace (např. oprávnění, která byla dynamicky vyžádána a poslána). Tato část se zobrazí jenom v případě, že existuje aspoň jedno oprávnění, které platí.

Do oddílu **konfigurovaná oprávnění** můžete přidat sadu oprávnění rozhraní API nebo jednotlivá oprávnění, která se zobrazí v této části. Jako správce můžete také odvolat souhlas správce pro jednotlivá rozhraní API nebo oprávnění v této části.

### <a name="admin-consent-button"></a>Tlačítko pro souhlas správce

Pokud je vaše aplikace registrovaná v tenantovi, zobrazí se tlačítko **udělení souhlasu správce pro tenanta** . Pokud nejste správcem, nebo pokud pro aplikaci nejsou nakonfigurovaná žádná oprávnění, bude zakázaná.
Toto tlačítko umožňuje správci snadno udělit souhlas správce k oprávněním nakonfigurovaným pro aplikaci. Kliknutím na tlačítko souhlasu správce se otevře nové okno s výzvou, kde se zobrazí všechna nakonfigurovaná oprávnění.

> [!NOTE]
> Mezi oprávněními nakonfigurovanými pro aplikaci a jejich zobrazením na výzvu k vyjádření souhlasu dochází ke zpoždění. Pokud nevidíte všechna nakonfigurovaná oprávnění v příkazovém řádku, zavřete ji a znovu ji spusťte.

Pokud máte oprávnění, která byla udělena, ale nejsou nakonfigurovaná, po kliknutí na tlačítko souhlasu správce se zobrazí výzva k rozhodnutí, jak tato oprávnění zpracovat. Můžete je přidat do konfigurovaných oprávnění nebo je můžete odebrat.

Výzva k vyjádření souhlasu nabízí možnost **přijmout** nebo **Zrušit**. Pokud vyberete **přijmout**, udělí se souhlas správce. Pokud vyberete **Zrušit**, není uděleno oprávnění správce a zobrazí se chyba s oznámením, že souhlas byl odmítnut.

> [!NOTE]
> Existuje zpoždění mezi udělením souhlasu správce (vyberte **přijmout** na výzvu k vyjádření souhlasu) a stav souhlasu správce se odrazí v uživatelském rozhraní.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto rychlých startech souvisejících se správou aplikací:

* [Registrace aplikace na platformě Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurace aplikace pro zveřejnění webových rozhraní API](quickstart-configure-app-expose-web-apis.md)
* [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
* [Odebrání aplikace zaregistrované na platformě Microsoft Identity Platform](quickstart-remove-app.md)

Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
