---
title: 'Rychlý Start: přístup k webovým rozhraním API pro aplikaci – Microsoft Identity Platform | Azure'
description: V tomto rychlém startu nakonfigurujte aplikaci registrovanou na platformě Microsoft identity, aby zahrnovala identifikátory URI přesměrování, přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 5e628626f2db49ff67d6d7ab425a3a19870b1ebd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240895"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API

V tomto rychlém startu přidáte identifikátory URI přesměrování, přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API vaší aplikace. Webová nebo důvěrná klientská aplikace musí vytvořit zabezpečené přihlašovací údaje, aby se účastnily autorizačního toku, který vyžaduje ověření. Výchozí metodou ověřování podporovanou webem Azure Portal je ID klienta a tajný klíč. Během tohoto procesu aplikace získá přístupový token.

Aby mohl klient získat přístup k webovému rozhraní API vystavenému pomocí aplikace prostředků, jako je Microsoft Graph API, rozhraní pro vyjádření souhlasu zajišťuje, že klient získá udělení oprávnění požadované pro požadovaná oprávnění. Ve výchozím nastavení můžou všechny aplikace požádat o oprávnění z rozhraní Microsoft Graph API.

## <a name="prerequisites"></a>Požadavky

* Dokončení [rychlého startu: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md)
* Kontrola [oprávnění a souhlasu v koncovém bodě Microsoft Identity Platform](v2-permissions-and-consent.md).
* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu. Nastavte relaci portálu na klienta služby Azure AD, kterého chcete.
1. Vyhledejte a vyberte **Azure Active Directory**. V části **Spravovat**vyberte **Registrace aplikací**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Po výběru aplikace se zobrazí stránka s **přehledem** nebo hlavní registrační stránkou aplikace.

Pomocí následujících postupů můžete nakonfigurovat aplikaci pro přístup k webovým rozhraním API.

## <a name="add-redirect-uris-to-your-application"></a>Přidání identifikátorů URI pro přesměrování do aplikace

Do své aplikace můžete přidat vlastní identifikátory URI pro přesměrování a doporučené identifikátory URI pro přesměrování. Přidání vlastního identifikátoru URI pro přesměrování pro webové a veřejné klientské aplikace:

1. Na stránce **Přehled** aplikace vyberte **ověřování**.
1. Vyhledejte **identifikátory URI pro přesměrování**. Možná budete muset vybrat možnost **Přepnout na původní prostředí**.
1. Vyberte typ aplikace, kterou vytváříte: **Web** nebo **veřejný klient/nativní (mobilní & Desktop)** .
1. Zadejte URI pro přesměrování pro vaši aplikaci.

   * V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `http://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
   * V případě veřejných aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, například: `https://MyFirstApp`.
1. Vyberte **Save** (Uložit).

Pokud si chcete vybrat z navrhovaných identifikátorů URI pro přesměrování pro veřejné klienty, postupujte takto:

1. Na stránce **Přehled** aplikace vyberte **ověřování**.
1. Najděte **navrhovaný identifikátor URI pro přesměrování pro veřejné klienty (mobilní zařízení, Desktop)** . Možná budete muset vybrat možnost **Přepnout na původní prostředí**.
1. Vyberte jeden nebo více identifikátorů URI pro přesměrování vaší aplikace. Můžete také zadat vlastní identifikátor URI pro přesměrování. Pokud si nejste jisti, co je třeba použít, přečtěte si dokumentaci ke knihovně.
1. Vyberte **Save** (Uložit).

Určitá omezení se vztahují na identifikátory URI přesměrování. Další informace najdete v tématu [omezení a omezení adresy URL pro přesměrování](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Vyzkoušejte si nové prostředí nastavení **ověřování** , kde můžete nakonfigurovat nastavení pro svou aplikaci na základě platformy nebo zařízení, na které chcete cílit.
>
> Chcete-li zobrazit toto zobrazení, vyberte možnost **vyzkoušet nové prostředí** ze stránky **ověřování** .
>
> ![Kliknutím na vyzkoušet nové prostředí zobrazíte zobrazení konfigurace platformy.](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Tím přejdete na [stránku nové **konfigurace platformy** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurace upřesňujících nastavení pro vaši aplikaci

V závislosti na aplikaci, kterou zaregistrujete, existuje několik dalších nastavení, která budete možná muset nakonfigurovat, třeba:

* **Odhlašovací adresa URL**
* U aplikací s jednou stránkou můžete povolit **implicitní udělení** a vybrat tokeny, které chcete, aby koncový bod autorizace vydával.
* U aplikací klasické pracovní plochy, které získávají tokeny pomocí integrovaného ověřování systému Windows, toku kódu zařízení nebo uživatelského jména a hesla v části **výchozí typ klienta** , nastavte nastavení **považovat aplikaci jako veřejného klienta** na **Ano**.
* Pro starší aplikace, které používaly sadu Live SDK pro integraci se službou účet Microsoft, nakonfigurujte **podporu sady Live SDK**. Nové aplikace toto nastavení nepotřebují.
* **Výchozí typ klienta**.
* **Podporované typy účtů**.

### <a name="modify-supported-account-types"></a>Upravit podporované typy účtů

**Podporované typy účtů** určují, kdo může použít aplikaci nebo získat přístup k rozhraní API.

Pokud jste nakonfigurovali podporované typy účtů při registraci aplikace, můžete toto nastavení změnit pouze pomocí editoru manifestu aplikace, pokud:

* Můžete změnit typy účtů z **AzureADMyOrg** nebo **AzureADMultipleOrgs** na **AzureADandPersonalMicrosoftAccount**nebo jiný způsob, nebo
* Můžete změnit typy účtů z **AzureADMyOrg** na **AzureADMultipleOrgs**nebo jiný způsob, jak se pohybovat.

Pokud chcete změnit podporované typy účtů pro existující registraci aplikace, aktualizujte `signInAudience` klíč. Další informace najdete v tématu [Konfigurace manifestu aplikace](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Konfigurace nastavení platformy pro vaši aplikaci

![Konfigurace nastavení aplikace na základě platformy nebo zařízení](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Při konfiguraci nastavení aplikace na základě platformy nebo zařízení cílíte na tyto možnosti:

1. Na stránce **konfigurace platformy** vyberte možnost **Přidat platformu** a zvolte z dostupných možností.

   ![Zobrazí stránku konfigurace platforem.](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Zadejte informace o nastavení na základě vybrané platformy.

   | Platforma                | Nastavení konfigurace            |
   |-------------------------|-----------------------------------|
   | **Web**              | Zadejte **identifikátor URI pro přesměrování** vaší aplikace. |
   | **iOS/macOS**              | Zadejte **ID sady prostředků**aplikace, které najdete v Xcode v souboru info. plist nebo nastavení sestavení. Přidání ID sady prostředků automaticky vytvoří identifikátor URI přesměrování pro aplikaci. |
   | **Android**          | Zadejte **název balíčku**aplikace, který můžete najít v souboru souboru AndroidManifest. XML.<br/>Vygenerujte a zadejte **hodnotu hash podpisu**. Přidání hodnoty hash signatury automaticky vytvoří identifikátor URI přesměrování pro aplikaci.  |
   | **Mobilní a desktopové aplikace**  | Volitelná. Pokud vytváříte aplikace pro stolní počítače a zařízení, vyberte jeden z doporučených **identifikátorů URI pro přesměrování** .<br/>Volitelná. Zadejte **vlastní identifikátor URI pro přesměrování**, který se používá jako umístění, kde Azure AD bude uživatele přesměrovat v reakci na žádosti o ověření. Například pro aplikace .NET Core, kde chcete interakce, použijte `https://localhost`. |

   > [!IMPORTANT]
   > Pro mobilní aplikace, které nepoužívají nejnovější knihovnu Microsoft Authentication Library (MSAL) nebo nepoužívají zprostředkovatele, je nutné nakonfigurovat identifikátory URI přesměrování pro tyto aplikace v **desktopových a mobilních zařízeních**.

V závislosti na platformě, kterou jste zvolili, můžou existovat další nastavení, která můžete konfigurovat. Pro **Web** Apps můžete:

* Přidat další identifikátory URI pro přesměrování
* Pokud chcete vybrat tokeny, které byste chtěli vystavit pomocí koncového bodu autorizace, nakonfigurujte **implicitní udělení** :

  * U jednostránkovéch aplikací vyberte **přístupové tokeny** a **tokeny ID** .
  * Pro Web Apps vyberte **tokeny ID** .

## <a name="add-credentials-to-your-web-application"></a>Přidání přihlašovacích údajů do webové aplikace

Chcete-li do webové aplikace přidat pověření, přidejte certifikát nebo vytvořte tajný klíč klienta. Přidání certifikátu:

1. Na stránce **Přehled** aplikace vyberte část **certifikáty & tajných** kódů.
1. Vyberte **Nahrát certifikát**.
1. Vyberte soubor, který chcete nahrát. Musí jít o jeden z následujících typů souboru: .cer, .pem nebo .crt.
1. Vyberte **Přidat**.

Postup přidání tajného klíče klienta:

1. Na stránce **Přehled** aplikace vyberte část **certifikáty & tajných** kódů.
1. Vyberte **Nový tajný klíč klienta**.
1. Přidejte popis tajného klíče klienta.
1. Vyberte dobu trvání.
1. Vyberte **Přidat**.

> [!NOTE]
> Po uložení změn konfigurace bude krajní pravý sloupec obsahovat hodnotu tajného klíče klienta. **Nezapomeňte si tuto hodnotu zkopírovat**, abyste ji mohli použít v kódu klientské aplikace, protože jinak se k ní už nedostanete, když tuto stránku opustíte.

## <a name="add-permissions-to-access-web-apis"></a>Přidání oprávnění pro přístup k webovým rozhraním API

Ve výchozím nastavení je vybrané [oprávnění pro přihlášení a čtení profilu uživatele Graph API](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) . Pro každé webové rozhraní API můžete vybrat ze [dvou typů oprávnění](developer-glossary.md#permissions) :

* **Oprávnění aplikace**. Vaše klientská aplikace musí přistupovat přímo k webovému rozhraní API, a to bez kontextu uživatele. Tento typ oprávnění vyžaduje souhlas správce. Toto oprávnění není k dispozici pro klientské a mobilní aplikace v klientských počítačích.
* **Delegovaná oprávnění**. Klientská aplikace musí mít přístup k webovému rozhraní API jako přihlášený uživatel, ale s přístupem omezeným zvoleným oprávněním. Pokud tento typ oprávnění nevyžaduje souhlas správce, může ho udělit uživatel.

  > [!NOTE]
  > Přidání delegovaného oprávnění do aplikace neuděluje uživatelům v tenantovi souhlas automaticky. Pokud správce neudělí souhlas jménem všech uživatelů, musí uživatelé stále souhlas pro delegovaná oprávnění přidaná za běhu udělit ručně.

Přidání oprávnění pro přístup k rozhraním API prostředků z klienta:

1. Na stránce **Přehled** aplikace vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Ve výchozím nastavení si v tomto zobrazení můžete vybrat z **rozhraní API Microsoftu**. Vyberte sekci rozhraní API, která vás zajímají:

    * **Rozhraní Microsoft API**. Umožňuje vybrat oprávnění pro rozhraní Microsoft API, například Microsoft Graph.
    * **Rozhraní API moje organizace používá**. Umožňuje vybrat oprávnění pro rozhraní API, která vaše organizace zpřístupňuje, nebo rozhraní API, se kterými je vaše organizace integrovaná.
    * **Moje rozhraní API**. Umožňuje vybrat oprávnění pro vystavovaná rozhraní API.

1. Jakmile vyberete rozhraní API, zobrazí se stránka **Požádat o oprávnění rozhraní API**. Pokud rozhraní API zveřejňuje delegovaná oprávnění i oprávnění aplikace, vyberte, který typ oprávnění vaše aplikace potřebuje.
1. Až budete hotovi, vyberte **Přidat oprávnění**.

Vrátíte se na stránku **oprávnění rozhraní API** . Oprávnění byla uložena a přidána do tabulky.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Principy oprávnění k rozhraní API a uživatelského rozhraní pro vyjádření souhlasu správce

### <a name="configured-permissions"></a>Nakonfigurovaná oprávnění

V této části jsou uvedena oprávnění, která byla explicitně konfigurována u objektu aplikace. Tato oprávnění jsou součástí seznamu požadovaných prostředků přístupu k prostředkům aplikace. V této tabulce můžete přidat nebo odebrat oprávnění. Jako správce můžete taky udělit nebo odvolat souhlas správce pro sadu oprávnění rozhraní API nebo individuálních oprávnění.

### <a name="other-permissions-granted"></a>Další udělená oprávnění

Pokud je vaše aplikace registrovaná v tenantovi, může se zobrazit další oddíl s názvem **Další oprávnění udělená pro tenanta**. Tato část zobrazuje oprávnění udělená pro tenanta, který není explicitně nakonfigurovaný pro objekt aplikace. Tato oprávnění byla dynamicky vyžádána a poslána. Tato část se zobrazí jenom v případě, že existuje aspoň jedno oprávnění, které platí.

Do oddílu **konfigurovaná oprávnění** můžete přidat sadu oprávnění rozhraní API nebo jednotlivá oprávnění, která se zobrazí v této části. Jako správce můžete také odvolat souhlas správce pro jednotlivá rozhraní API nebo oprávnění v této části.

### <a name="admin-consent-button"></a>Tlačítko pro souhlas správce

Pokud je vaše aplikace registrovaná v tenantovi, zobrazí se tlačítko **udělení souhlasu správce pro tenanta** . Je zakázaná, pokud nejste správcem, nebo pokud pro aplikaci nejsou nakonfigurovaná žádná oprávnění.
Toto tlačítko umožňuje správci udělit souhlas správce k oprávněním nakonfigurovaným pro aplikaci. Kliknutím na tlačítko souhlasu správce se otevře nové okno s výzvou, kde se zobrazí všechna nakonfigurovaná oprávnění.

> [!NOTE]
> Mezi oprávněními nakonfigurovanými pro aplikaci a jejich zobrazením na výzvu k vyjádření souhlasu dochází ke zpoždění. Pokud nevidíte všechna nakonfigurovaná oprávnění v příkazovém řádku, zavřete ji a znovu ji spusťte.

Máte-li udělená oprávnění, která byla udělena, ale nejsou nakonfigurována, klikněte na tlačítko pro souhlas správce, abyste mohli tato oprávnění zpracovat. Můžete je přidat do konfigurovaných oprávnění nebo je můžete odebrat.

Výzva k vyjádření souhlasu nabízí možnost **přijmout** nebo **Zrušit**. Vyberte **přijmout** pro udělení souhlasu správce. Pokud vyberete **Zrušit**, neudělí se souhlas správce. Chybová zpráva uvádí, že souhlas byl odmítnut.

> [!NOTE]
> Udělení souhlasu správce je zpožděné tím, že na příkazovém řádku na základě souhlasu zvolíte **přijmout** a na portálu se projeví stav souhlasu správce.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak vystavit webová rozhraní API.
> [!div class="nextstepaction"]
> [Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md)

* Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

* Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).

* [Rychlý Start: registrace aplikace s platformou Microsoft identity](quickstart-register-app.md)

* [Rychlý Start: Změna účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)

* [Rychlý Start: odebrání aplikace registrované s platformou Microsoft identity](quickstart-remove-app.md)
