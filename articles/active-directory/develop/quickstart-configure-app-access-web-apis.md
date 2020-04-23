---
title: 'Úvodní příručka: Přístupová webová rozhraní API pro aplikace – platforma identit Microsoftu | Azure'
description: V tomto rychlém startu nakonfigurujte aplikaci zaregistrovanou na platformě identit microsoftu tak, aby zahrnovala identifikátory URI přesměrování, přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082318"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Rychlý start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API

V tomto rychlém startu přidáte identifikátory URI přesměrování, přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API pro vaši aplikaci. Webová nebo důvěrná klientská aplikace musí vytvořit zabezpečená pověření pro účast v toku udělení autorizace, který vyžaduje ověření. Výchozí metodou ověřování podporovanou webem Azure Portal je ID klienta a tajný klíč. Aplikace získá přístupový token během tohoto procesu.

Před klient může přistupovat k webové rozhraní API vystavené aplikace prostředků, jako je například rozhraní Microsoft Graph API, rozhraní pro souhlas zajišťuje, že klient získá oprávnění požadované pro požadovaná oprávnění. Ve výchozím nastavení mohou všechny aplikace požadovat oprávnění z rozhraní Microsoft Graph API.

## <a name="prerequisites"></a>Požadované součásti

* Dokončení [rychlého startu: Registrace aplikace s platformou identit microsoftu](quickstart-register-app.md).
* Kontrola [oprávnění a souhlasu v koncovém bodě platformy identit microsoftu](v2-permissions-and-consent.md).
* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu. Nastavte relaci portálu na tenanta Azure AD, který chcete.
1. Vyhledejte a vyberte **Azure Active Directory**. V části **Spravovat** vyberte **Registrace aplikací**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Po výběru aplikace se zobrazí **přehled** aplikace nebo hlavní registrační stránka.

Pomocí následujících postupů nakonfigurujte aplikaci pro přístup k webovým rozhraním API.

## <a name="add-redirect-uris-to-your-application"></a>Přidání identifikátorů URI pro přesměrování do aplikace

Můžete přidat vlastní identifikátory URI přesměrování a navrhované identifikátory URI přesměrování do aplikace. Přidání vlastního identifikátoru URI přesměrování pro webové a veřejné klientské aplikace:

1. Na stránce **Přehled** aplikace vyberte **Ověřování**.
1. Vyhledejte **identifikátory URI přesměrování**. Možná budete muset vybrat **Přepnout na staré prostředí**.
1. Vyberte typ vytvářené aplikace: **Web** nebo **Veřejný klient/nativní (mobilní & plochy).**
1. Zadejte URI pro přesměrování pro vaši aplikaci.

   * V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `http://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
   * V případě veřejných aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro `https://MyFirstApp`vaši aplikaci, například: .
1. Vyberte **Uložit**.

Chcete-li si vybrat z navržených identifikátorů URI přesměrování pro veřejné klienty, postupujte takto:

1. Na stránce **Přehled** aplikace vyberte **Ověřování**.
1. Vyhledejte **identifikátory URI navrhované přesměrování pro veřejné klienty (mobilní zařízení, počítače).** Možná budete muset vybrat **Přepnout na staré prostředí**.
1. Vyberte jednu nebo více identifikátorů URI přesměrování pro vaši aplikaci. Můžete také zadat vlastní identifikátor URI přesměrování. Pokud si nejste jisti, co použít, přečtěte si dokumentaci ke knihovně.
1. Vyberte **Uložit**.

Pro přesměrování identifikátorů URI platí určitá omezení. Další informace naleznete v tématu [Přesměrování omezení a omezení adresy URL identifikátoru URI/odpovědi](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Vyzkoušejte nové prostředí **nastavení ověřování,** kde můžete nakonfigurovat nastavení aplikace na základě platformy nebo zařízení, na které chcete cílit.
>
> Chcete-li zobrazit toto zobrazení, vyberte **Vyzkoušet nové prostředí** na stránce **Ověřování.**
>
> ![Klikněte na "Vyzkoušet nové prostředí" zobrazíte zobrazení konfigurace platformy](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Tím přejdete na [novou stránku **konfigurace platformy** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurace upřesňujících nastavení aplikace

V závislosti na aplikaci, kterou registrujete, je třeba nakonfigurovat některá další nastavení, například:

* **Adresa URL pro odhlášení**.
* Pro jednostránkové aplikace můžete povolit **implicitní udělení** a vybrat tokeny, které chcete vystavit koncový bod autorizace.
* U aplikací klasické pracovní plochy, které získávají tokeny pomocí integrovaného ověřování systému Windows, toku kódu zařízení nebo uživatelského jména/hesla v části **Výchozí typ klienta,** nastavte nastavení **Považovat aplikaci jako veřejný klient** na **hodnotu Ano**.
* U starších aplikací, které používaly sadu Live SDK k integraci se službou účtu Microsoft, nakonfigurujte **podporu sady Live SDK**. Nové aplikace toto nastavení nepotřebují.
* **Výchozí typ klienta**.
* **Podporované typy účtů**.

### <a name="modify-supported-account-types"></a>Úprava podporovaných typů účtů

**Podporované typy účtů** určují, kdo může aplikaci používat nebo získat přístup k rozhraní API.

Pokud jste při registraci aplikace nakonfigurovali podporované typy účtů, můžete toto nastavení změnit pouze pomocí editoru manifestů aplikace, pokud:

* Změníte typy účtů z **AzureADMyOrg** nebo **AzureADMultipleOrgs** na **AzureADandPersonalMicrosoftAccount**nebo naopak, nebo
* Můžete změnit typy účtů z **AzureADMyOrg** na **AzureADMultipleOrgs**, nebo naopak.

Pokud chcete změnit podporované typy účtů pro `signInAudience` existující registraci aplikace, aktualizujte klíč. Další informace naleznete [v tématu Konfigurace manifestu aplikace](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Konfigurace nastavení platformy pro vaši aplikaci

![Konfigurace nastavení aplikace na základě platformy nebo zařízení](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Chcete-li konfigurovat nastavení aplikace na základě platformy nebo zařízení, cílíte na:

1. Na stránce **Konfigurace platformy** vyberte **Přidat platformu** a vyberte z dostupných možností.

   ![Zobrazuje stránku Konfigurovat platformy.](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Zadejte informace o nastavení na základě platformy, kterou jste vybrali.

   | Platforma                | Nastavení konfigurace            |
   |-------------------------|-----------------------------------|
   | **Web**              | Zadejte **identifikátor URI přesměrování** pro vaši aplikaci. |
   | **iOS / macOS**              | Zadejte **ID sady prostředků**aplikace , které najdete v XCode v Info.plist nebo Nastavení sestavení. Přidání ID sady automaticky vytvoří identifikátor URI přesměrování pro aplikaci. |
   | **Android**          | Zadejte **název balíčku**aplikace , který najdete v souboru AndroidManifest.xml.<br/>Generovat a zadávat **hodnotu hash podpisu**. Přidání maše hash podpisu automaticky vytvoří identifikátor URI přesměrování pro aplikaci.  |
   | **Mobilní a desktopové aplikace**  | Nepovinný parametr. Pokud vytváříte aplikace pro stolní počítače a zařízení, vyberte jednu z **doporučených identifikátorů URI** pro navrhované přesměrování.<br/>Nepovinný parametr. Zadejte **identifikátor URI vlastního přesměrování**, který se používá jako umístění, kde služba Azure AD přesměruje uživatele v reakci na požadavky na ověření. Například pro aplikace .NET Core, kde `http://localhost`chcete interakci, použijte . |

   > [!NOTE]
   > Ve službě AD FS (AD FS) a Azure AD B2C je také nutné zadat číslo portu.  Například: `http://localhost:1234`. 

   > [!IMPORTANT]
   > U mobilních aplikací, které nepoužívají nejnovější knihovnu Microsoft Authentication Library (MSAL) nebo nepoužívají zprostředkovatele, je nutné nakonfigurovat identifikátory URI přesměrování pro tyto aplikace v **počítačích + zařízeních**.

V závislosti na platformě, kterou jste zvolili, může být další nastavení, které můžete nakonfigurovat. U **webových** aplikací můžete:

* Přidání dalších identifikátorů URI přesměrování
* Nakonfigurujte **implicitní udělení** pro výběr tokenů, které chcete vydat koncovým bodem autorizace:

  * U jednostránkových aplikací vyberte **tokeny accessu** i **tokeny ID.**
  * U webových aplikací vyberte **Tokeny ID.**

## <a name="add-credentials-to-your-web-application"></a>Přidání přihlašovacích údajů do webové aplikace

Chcete-li do webové aplikace přidat pověření, přidejte certifikát nebo vytvořte tajný klíč klienta. Přidání certifikátu:

1. Na stránce **Přehled** aplikace vyberte oddíl **Certifikáty & tajné kódy.**
1. Vyberte **Nahrát certifikát**.
1. Vyberte soubor, který chcete nahrát. Musí jít o jeden z následujících typů souboru: .cer, .pem nebo .crt.
1. Vyberte **Přidat**.

Přidání tajného klíče klienta:

1. Na stránce **Přehled** aplikace vyberte oddíl **Certifikáty & tajné kódy.**
1. Vyberte **Nový tajný klíč klienta**.
1. Přidejte popis tajného klíče klienta.
1. Vyberte dobu trvání.
1. Vyberte **Přidat**.

> [!NOTE]
> Po uložení změn konfigurace bude krajní pravý sloupec obsahovat hodnotu tajného klíče klienta. **Nezapomeňte si tuto hodnotu zkopírovat**, abyste ji mohli použít v kódu klientské aplikace, protože jinak se k ní už nedostanete, když tuto stránku opustíte.

## <a name="add-permissions-to-access-web-apis"></a>Přidání oprávnění pro přístup k webovým rozhraním API

Ve výchozím nastavení je [vybráno oprávnění k přihlášení a čtení uživatelského profilu rozhraní Graph API.](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) Můžete vybrat ze [dvou typů oprávnění](developer-glossary.md#permissions) pro každé webové rozhraní API:

* **Oprávnění aplikace**. Vaše klientská aplikace potřebuje přístup k webovému rozhraní API přímo jako sebe sama, bez kontextu uživatele. Tento typ oprávnění vyžaduje souhlas správce. Toto oprávnění není k dispozici pro desktopové a mobilní klientské aplikace.
* **Delegovaná oprávnění**. Klientská aplikace potřebuje přístup k webovému rozhraní API jako přihlášený uživatel, ale přístup je omezen vybraným oprávněním. Pokud tento typ oprávnění nevyžaduje souhlas správce, může ho udělit uživatel.

  > [!NOTE]
  > Přidání delegovaného oprávnění do aplikace neuděluje uživatelům v tenantovi souhlas automaticky. Pokud správce neudělí souhlas jménem všech uživatelů, musí uživatelé stále souhlas pro delegovaná oprávnění přidaná za běhu udělit ručně.

Přidání oprávnění k přístupu k souborům API prostředků z vašeho klienta:

1. Na stránce **Přehled** aplikace vyberte **oprávnění rozhraní API**.
1. V části **Nakonfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Ve výchozím nastavení si v tomto zobrazení můžete vybrat z **rozhraní API Microsoftu**. Vyberte sekci rozhraní API, která vás zajímají:

    * **Rozhraní API společnosti Microsoft**. Umožňuje vybrat oprávnění pro rozhraní MICROSOFT API, jako je Microsoft Graph.
    * **Api moje organizace používá**. Umožňuje vybrat oprávnění pro api, která vaše organizace zveřejňuje, nebo api, se kterými je vaše organizace integrována.
    * **Moje API**. Umožňuje vybrat oprávnění pro informace o možnostech API, která zveřejňujete.

1. Jakmile vyberete rozhraní API, zobrazí se stránka **Požádat o oprávnění rozhraní API**. Pokud rozhraní API zveřejňuje delegovaná oprávnění i oprávnění aplikace, vyberte, který typ oprávnění vaše aplikace potřebuje.
1. Až budete hotovi, vyberte **Přidat oprávnění**.

Vrátíte se na stránku **oprávnění rozhraní API.** Oprávnění byla uložena a přidána do tabulky.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Principy oprávnění rozhraní API a uživatelského rozhraní souhlasu správce

### <a name="configured-permissions"></a>Nakonfigurovaná oprávnění

Tato část zobrazuje oprávnění, která byla explicitně nakonfigurována v objektu aplikace. Tato oprávnění jsou součástí seznamu požadovaných prostředků aplikace. Z této tabulky můžete přidat nebo odebrat oprávnění. Jako správce můžete také udělit nebo odvolat souhlas správce pro sadu oprávnění rozhraní API nebo individuálních oprávnění.

### <a name="other-permissions-granted"></a>Jiná udělená oprávnění

Pokud je vaše aplikace registrována v tenantovi, může se zobrazit další oddíl s názvem **Další oprávnění udělená pro tenanta**. Tato část zobrazuje oprávnění udělená klientovi, která nebyla explicitně nakonfigurována na aplikačním objektu. Tato oprávnění byla dynamicky požadována a souhlasně. Tato část se zobrazí pouze v případě, že existuje alespoň jedno oprávnění, které se vztahuje.

Sadu oprávnění rozhraní API nebo jednotlivých oprávnění, která se zobrazí v této části, můžete přidat do části **Nakonfigurovaná oprávnění.** Jako správce můžete také odvolat souhlas správce pro jednotlivá rozhraní API nebo oprávnění v této části.

### <a name="admin-consent-button"></a>Tlačítko Souhlas správce

Pokud je vaše aplikace registrovaná v tenantovi, zobrazí se tlačítko **Udělit souhlas správce pro tenanta.** Je zakázána, pokud nejste správce, nebo pokud pro aplikaci nebyla nakonfigurována žádná oprávnění.
Toto tlačítko umožňuje správci udělit souhlas správce s oprávněními nakonfigurovanými pro aplikaci. Kliknutím na tlačítko souhlas správce se spustí nové okno s výzvou k udělení souhlasu, která zobrazuje všechna nakonfigurovaná oprávnění.

> [!NOTE]
> Mezi oprávněními nakonfigurovanými pro aplikaci a jejich zobrazením v řádku s souhlasem je prodleva. Pokud v výzvě k udělení souhlasu nevidíte všechna nakonfigurovaná oprávnění, zavřete je a znovu spusťte.

Pokud máte oprávnění, která byla udělena, ale není nakonfigurována, tlačítko souhlassprávce vás vyzve ke zpracování těchto oprávnění. Můžete je přidat do nakonfigurovaných oprávnění nebo je můžete odebrat.

Výzva k souhlasu poskytuje možnost **Přijmout** nebo **Zrušit**. Chcete-li udělit souhlas správce, vyberte **Přijmout.** Pokud vyberete **Zrušit**, souhlas správce se neudělí. Chybová zpráva uvádí, že souhlas byl odmítnut.

> [!NOTE]
> Mezi udělením souhlasu správce je prodleva, když v řádku s souhlasem **vyberete Přijmout** a na portálu se projeví stav souhlasu správce.

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a dozvíte se, jak vystavit webová api.
> [!div class="nextstepaction"]
> [Rychlý start: Konfigurace aplikace pro vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md)

* Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

* Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).

* [Úvodní příručka: Registrace aplikace s platformou microsoftu pro identity](quickstart-register-app.md)

* [Úvodní příručka: Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)

* [Úvodní příručka: Odebrání aplikace registrované na platformě identit Microsoftu](quickstart-remove-app.md)
