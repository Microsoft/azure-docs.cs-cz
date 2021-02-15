---
title: Vytvoření instančního objektu & aplikace služby Azure AD na portálu
titleSuffix: Microsoft identity platform
description: Vytvořte nový Azure Active Directory App & instančního objektu pro správu přístupu k prostředkům pomocí řízení přístupu na základě role v Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 8a4a4153261b93b3b17641e8561962c274570bd0
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104208"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Postup: Vytvoření aplikace Azure AD a instančního objektu s přístupem k prostředkům pomocí portálu

V tomto článku se dozvíte, jak vytvořit novou aplikaci Azure Active Directory (Azure AD) a instančního objektu, který se dá použít s řízením přístupu na základě role. Pokud máte aplikace, hostované služby nebo automatizované nástroje, které potřebují přístup k prostředkům nebo jejich úpravu, můžete pro aplikaci vytvořit identitu. Tato identita se označuje jako instanční objekt. Přístup k prostředkům je omezen rolemi přiřazenými k instančnímu objektu a poskytuje vám kontrolu nad tím, k jakým prostředkům se dá získat přístup a na jaké úrovni. Z bezpečnostních důvodů se v automatizovaných nástrojích vždy doporučuje používat instanční objekty, a neumožňovat jim připojení pomocí identity uživatele.

V tomto článku se dozvíte, jak pomocí portálu vytvořit instanční objekt v Azure Portal. Zaměřuje se na aplikaci s jedním tenantů, kde má aplikace běžet jenom v jedné organizaci. Pro obchodní aplikace, které běží v rámci vaší organizace, obvykle používáte aplikace pro jednoho tenanta.  [K vytvoření instančního objektu můžete použít taky Azure PowerShell](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Místo Vytvoření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službě, která podporuje spravované identity a přistupuje k prostředkům, které podporují ověřování Azure AD, jsou spravované identity lepší volbou pro vás. Další informace o spravovaných identitách pro prostředky Azure, včetně toho, které služby v současnosti podporují, najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Registrace aplikace, objekty aplikace a instanční objekty
Neexistuje žádný způsob, jak přímo vytvořit instanční objekt pomocí Azure Portal.  Když zaregistrujete aplikaci prostřednictvím Azure Portal, objekt aplikace a instanční objekt se automaticky vytvoří v domovském adresáři nebo tenantovi.  Další informace o vztahu mezi registrací aplikace, objekty aplikace a instančními objekty najdete v tématu o tom, jak číst [aplikace a objekty zabezpečení služby v Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Oprávnění vyžadovaná k registraci aplikace

Musíte mít dostatečná oprávnění k registraci aplikace ve vašem tenantovi Azure AD a přiřazení k aplikaci roli v předplatném Azure.

### <a name="check-azure-ad-permissions"></a>Ověřit oprávnění služby Azure AD

1. Vyberte **Azure Active Directory**.
1. Poznamenejte si svoji roli. Máte-li roli **uživatele** , je nutné zajistit, aby aplikace bez oprávnění správce mohli registrovat aplikace.

   ![Najděte svoji roli. Pokud jste uživatel, ujistěte se, že nesprávci můžou registrovat aplikace.](./media/howto-create-service-principal-portal/view-user-info.png)

1. V levém podokně vyberte **nastavení uživatele**.
1. Ověřte nastavení **Registrace aplikací** . Tuto hodnotu může nastavit jenom správce. Pokud je nastaveno na **Ano**, každý uživatel v TENANTOVI Azure AD může zaregistrovat aplikaci.

Pokud je nastavení registrace aplikací nastaveno na **ne**, můžou tyto typy aplikací registrovat jenom uživatelé s rolí správce. Další informace o dostupných rolích správců a konkrétních oprávněních v Azure AD, která jsou udělena jednotlivým rolím, najdete v tématu [dostupné role](../roles/permissions-reference.md#available-roles) a [oprávnění role](../roles/permissions-reference.md#role-permissions) . Pokud má váš účet přiřazenou roli uživatele, ale nastavení registrace aplikace je omezené na uživatele s právy pro správu, požádejte správce, aby vám přidělil jednu z rolí správce, která může vytvářet a spravovat všechny aspekty registrací aplikací, nebo umožnit uživatelům registrovat aplikace.

### <a name="check-azure-subscription-permissions"></a>Ověřit oprávnění pro předplatné Azure

Ve vašem předplatném Azure musí mít váš účet `Microsoft.Authorization/*/Write` přístup k aplikaci AD, aby mohl přiřadit roli. Tato akce se povoluje prostřednictvím role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../../role-based-access-control/built-in-roles.md#user-access-administrator). Pokud má váš účet přiřazenou roli **Přispěvatel** , nemáte příslušná oprávnění. Při pokusu o přiřazení instančního objektu k roli se zobrazí chyba.

Ověření oprávnění k předplatnému:

1. Vyhledejte a vyberte **předplatná** nebo vyberte **předplatná** na **domovské** stránce.

   ![Hledat](./media/howto-create-service-principal-portal/select-subscription.png)

1. Vyberte předplatné, ve kterém chcete vytvořit instanční objekt.

   ![Výběr předplatného pro přiřazení](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Pokud nevidíte předplatné, které hledáte, vyberte **globální filtr předplatných**. Ujistěte se, že je vybráno požadované předplatné portálu.

1. Vyberte **Moje oprávnění**. Pak vyberte **kliknutím sem zobrazíte informace o úplných přístupech k tomuto předplatnému**.

   ![Vyberte předplatné, ve kterém chcete vytvořit instanční objekt.](./media/howto-create-service-principal-portal/view-details.png)

1. Výběrem **Zobrazit** v **přiřazeních rolí** zobrazíte přiřazené role a určíte, jestli máte adekvátní oprávnění k přiřazení role k aplikaci AD. Pokud ne, požádejte správce předplatného, aby vás přidal do role správce přístupu uživatele. Na následujícím obrázku je uživateli přiřazena role vlastníka, což znamená, že uživatel má odpovídající oprávnění.

   ![Tento příklad ukazuje, že uživatel má přiřazenou roli vlastníka.](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Registrace aplikace ve službě Azure AD a Vytvoření instančního objektu

Pojďme přejít přímo na vytvoření identity. Pokud narazíte na problém, zkontrolujte [požadovaná oprávnění](#permissions-required-for-registering-an-app) a ujistěte se, že váš účet může vytvořit identitu.

1. Přihlaste se ke svému účtu Azure prostřednictvím <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Vyberte **Azure Active Directory**.
1. Vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Pojmenujte aplikaci. Vyberte podporovaný typ účtu, který určuje, kdo může aplikaci používat. V části **identifikátor URI pro přesměrování** vyberte **Web** pro typ aplikace, kterou chcete vytvořit. Zadejte identifikátor URI, na který se přístupový token pošle. Nemůžete vytvořit pověření pro [nativní aplikaci](../manage-apps/application-proxy-configure-native-client-application.md). Tento typ nelze použít pro automatizovanou aplikaci. Po nastavení hodnot vyberte **Registrovat**.

   ![Zadejte název vaší aplikace.](./media/howto-create-service-principal-portal/create-app.png)

Vytvořili jste aplikaci a instanční objekt služby Azure AD.

## <a name="assign-a-role-to-the-application"></a>Přiřazení role k aplikaci

Pokud chcete získat přístup k prostředkům ve vašem předplatném, musíte aplikaci přiřadit roli. Rozhodněte, která role nabízí správná oprávnění pro aplikaci. Další informace o dostupných rolích najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

Rozsah můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou zděděna na nižší úrovně rozsahu. Například přidání aplikace do role *Čtenář* pro skupinu prostředků znamená, že může číst skupinu prostředků a všechny prostředky, které obsahuje.

1. V Azure Portal vyberte úroveň rozsahu, do kterého chcete aplikaci přiřadit. Pokud například chcete přiřadit roli v oboru předplatného, vyhledejte a vyberte **odběry** nebo vyberte **předplatná** na **domovské** stránce.

   ![Přiřaďte například roli v oboru předplatného.](./media/howto-create-service-principal-portal/select-subscription.png)

1. Vyberte konkrétní předplatné, ke kterému chcete aplikaci přiřadit.

   ![Výběr předplatného pro přiřazení](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Pokud nevidíte předplatné, které hledáte, vyberte **globální filtr předplatných**. Ujistěte se, že je vybráno požadované předplatné portálu.

1. Vyberte **Řízení přístupu (IAM)** .
1. Vyberte **Přidat přiřazení role**.
1. Vyberte roli, kterou chcete aplikaci přiřadit. Pokud například chcete, aby aplikace mohla provádět akce, jako je **restartování**, **spuštění** a **zastavení** instancí, vyberte roli **Přispěvatel** .  Další informace o [dostupných rolích](../../role-based-access-control/built-in-roles.md) ve výchozím nastavení se v dostupných možnostech nezobrazí v aplikacích Azure AD. Chcete-li najít aplikaci, vyhledejte její název a vyberte ji.

   ![Vyberte roli, kterou chcete přiřadit k aplikaci.](./media/howto-create-service-principal-portal/select-role.png)

1. Kliknutím na **Uložit** dokončete přiřazení role. Vaše aplikace se zobrazí v seznamu uživatelů s rolí pro tento obor.

Vaše instanční objekt je nastavený. Můžete ji začít používat ke spouštění skriptů nebo aplikací. Chcete-li spravovat instanční objekt (oprávnění, uživatelská oprávnění, přečtěte si, kteří uživatelé jsou souhlasi, zkontrolujte oprávnění, přečtěte si informace o přihlášení a další), přejděte na **podnikové aplikace**.

V další části se dozvíte, jak získat hodnoty, které jsou potřeba při přihlašování prostřednictvím kódu programu.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Získání hodnot ID tenanta a aplikace pro přihlášení

Když se programově přihlašujete, předejte ID klienta k žádosti o ověření a ID aplikace.  Budete také potřebovat certifikát nebo ověřovací klíč (popsaný v následující části). K získání těchto hodnot použijte následující postup:

1. Vyberte **Azure Active Directory**.
1. V **Registrace aplikací** ve službě Azure AD vyberte svou aplikaci.
1. Zkopírujte ID adresáře (tenant) a uložte ho do kódu aplikace.

    ![Zkopírujte adresář (ID tenanta) a uložte ho do kódu vaší aplikace.](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    ID adresáře (tenant) se taky dá najít na stránce s přehledem výchozích adresářů.

1. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace.

   ![Kopírovat ID aplikace (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Ověřování: dvě možnosti

K dispozici jsou dva typy ověřování pro instanční objekty: ověřování založené na heslech (tajný klíč aplikace) a ověřování na základě certifikátů. Doporučujeme *použít certifikát*, ale můžete také vytvořit tajný klíč aplikace.

### <a name="option-1-upload-a-certificate"></a>Možnost 1: nahrání certifikátu

Pokud nějaký máte, můžete použít existující certifikát.  Volitelně můžete vytvořit certifikát podepsaný svým držitelem pro *účely testování*. Chcete-li vytvořit certifikát podepsaný svým držitelem, otevřete PowerShell a spusťte rutinu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) s následujícími parametry k vytvoření certifikátu v úložišti certifikátů uživatele v počítači:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportujte tento certifikát do souboru pomocí modulu snap-in [Spravovat certifikát uživatele](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) konzoly MMC přístupný z ovládacích panelů systému Windows.

1. V nabídce **Start** vyberte **Run (spustit** ) a pak zadejte **certmgr. msc**.

   Zobrazí se nástroj Správce certifikátů pro aktuálního uživatele.

1. Pokud chcete zobrazit certifikáty, v části **Certifikáty – aktuální uživatel** v levém podokně rozbalte **osobní** adresář.
1. Klikněte pravým tlačítkem na certifikát, který jste vytvořili, vyberte **všechny úkoly – >exportovat**.
1. Postupujte podle pokynů Průvodce exportem certifikátu.  Neexportujte privátní klíč a exportujte ho do. Soubor CER.

Postup nahrání certifikátu:

1. Vyberte **Azure Active Directory**.
1. V **Registrace aplikací** ve službě Azure AD vyberte svou aplikaci.
1. Vyberte **certifikáty & tajných** kódů.
1. Vyberte **nahrát certifikát** a vyberte certifikát (existující certifikát nebo certifikát podepsaný svým držitelem).

    ![Vyberte nahrát certifikát a vyberte ten, který chcete přidat.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Vyberte **Přidat**.

Po registraci certifikátu s aplikací na portálu pro registraci aplikací povolte, aby kód klientské aplikace používal certifikát.

### <a name="option-2-create-a-new-application-secret"></a>Možnost 2: vytvoření nového tajného klíče aplikace

Pokud se rozhodnete nepoužívat certifikát, můžete vytvořit nový tajný klíč aplikace.

1. Vyberte **Azure Active Directory**.
1. V **Registrace aplikací** ve službě Azure AD vyberte svou aplikaci.
1. Vyberte **certifikáty & tajných** kódů.
1. Vyberte **Client tajných klíčů – > nový tajný klíč klienta**.
1. Zadejte popis tajného kódu a dobu trvání. Až budete hotovi, vyberte **Přidat**.

   Po uložení tajného klíče klienta se zobrazí hodnota tajného klíče klienta. Zkopírujte tuto hodnotu, protože už nebudete moct klíč načíst později. Zadáním hodnoty klíče s ID aplikace se přihlásíte jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![Zkopírujte tajnou hodnotu, protože ji nemůžete později načíst.](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurace zásad přístupu pro prostředky
Mějte na paměti, že možná budete muset nakonfigurovat další oprávnění k prostředkům, které vaše aplikace potřebuje k přístupu. Například je třeba [aktualizovat zásady přístupu trezoru klíčů](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) , aby vaše aplikace měla přístup k klíčům, tajným klíčům nebo certifikátům.

1. V <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>přejděte do svého trezoru klíčů a vyberte **zásady přístupu**.
1. Vyberte **Přidat zásady přístupu** a pak vyberte klíčová, tajná a oprávnění certifikátů, která chcete aplikaci udělit.  Vyberte objekt služby, který jste předtím vytvořili.
1. Vyberte **Přidat** a přidejte zásadu přístupu a potom **uložte** změny, které chcete potvrdit.
    ![Přidat zásady přístupu](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Azure PowerShell k vytvoření instančního objektu](howto-authenticate-service-principal-powershell.md).
* Další informace o zadávání zásad zabezpečení najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Seznam dostupných akcí, které mohou uživatelé udělit nebo odepřít, najdete v tématu [Azure Resource Manager operací poskytovatele prostředků](../../role-based-access-control/resource-provider-operations.md).
* Informace o práci s registracemi aplikací pomocí **Microsoft Graph** najdete v referenčních informacích k rozhraní API pro [aplikace](/graph/api/resources/application) .
