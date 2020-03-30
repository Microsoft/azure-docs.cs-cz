---
title: Vytvoření & instančního objektu aplikace Azure AD na portálu
titleSuffix: Microsoft identity platform
description: Vytvořte novou aplikaci Azure Active Directory & instanční objekt pro správu přístupu k prostředkům pomocí řízení přístupu na základě rolí ve Správci prostředků Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: c5f65adfe401f2f6e99234d08b8e8dabeff7d5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264112"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Postup: Použití portálu k vytvoření aplikace azure a instančního objektu Azure AD, který má přístup k prostředkům

Tento článek ukazuje, jak vytvořit novou aplikaci Azure Active Directory (Azure AD) a instanční objekt, který lze použít s řízením přístupu na základě rolí. Pokud máte kód, který potřebuje přístup nebo upravit prostředky, můžete vytvořit identitu pro aplikaci. Tato identita se označuje jako instanční objekt. Potom můžete přiřadit požadovaná oprávnění instančnímu objektu. Tento článek ukazuje, jak pomocí portálu vytvořit instanční objekt. Zaměřuje se na aplikaci s jedním tenantem, kde je aplikace určena ke spuštění pouze v rámci jedné organizace. Aplikace s jedním tenantem se obvykle používají pro obchodní aplikace, které běží v rámci vaší organizace.

> [!IMPORTANT]
> Místo vytváření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službě, která podporuje spravované identity a přistupuje k prostředkům, které podporují ověřování Azure AD, spravované identity jsou lepší volbou pro vás. Další informace o spravovaných identitách pro prostředky Azure, včetně služeb, které je aktuálně podporují, najdete v [tématu Co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Pojďme rovnou do vytváření identity. Pokud narazíte na problém, zkontrolujte [požadovaná oprávnění](#required-permissions) a ujistěte se, že váš účet může vytvořit identitu.

1. Přihlaste se ke svému účtu Azure prostřednictvím [portálu Azure](https://portal.azure.com).
1. Vyberte **Azure Active Directory**.
1. Vyberte **Registrace aplikací**.
1. Vyberte **možnost Nová registrace**.
1. Pojmenujte aplikaci. Vyberte typ podporovaného účtu, který určuje, kdo může aplikaci používat. V části **Přesměrování identifikátoru URI**vyberte **pro** typ aplikace, kterou chcete vytvořit, web. Zadejte identifikátor URI, do kterého je přístupový token odeslán. Nelze vytvořit pověření pro [nativní aplikaci](../manage-apps/application-proxy-configure-native-client-application.md). Tento typ nelze použít pro automatickou aplikaci. Po nastavení hodnot vyberte **Registrovat**.

   ![Zadejte název aplikace.](./media/howto-create-service-principal-portal/create-app.png)

Vytvořili jste instanční objekt aplikace azure a služby.

## <a name="assign-a-role-to-the-application"></a>Přiřazení role k aplikaci

Chcete-li získat přístup k prostředkům v rámci předplatného, musíte aplikaci přiřadit roli. Rozhodněte, která role nabízí správná oprávnění pro aplikaci. Informace o dostupných rolích najdete v tématu [RBAC: Built in Roles](../../role-based-access-control/built-in-roles.md).

Obor můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou zděděna do nižších úrovní oboru. Například přidání aplikace do role Čtečka pro skupinu prostředků znamená, že může číst skupinu prostředků a všechny prostředky, které obsahuje.

1. Na webu Azure Portal vyberte úroveň oboru, ke které chcete aplikaci přiřadit. Chcete-li například přiřadit roli v oboru předplatného, vyhledejte a vyberte **předplatná**nebo na **domovské** stránce vyberte **Předplatná.**

   ![Můžete například přiřadit roli v oboru předplatného.](./media/howto-create-service-principal-portal/select-subscription.png)

1. Vyberte konkrétní předplatné, ke komu chcete aplikaci přiřadit.

   ![Vybrat předplatné pro přiřazení](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Pokud hledací předplatné nevidíte, vyberte **filtr globálních předplatných**. Ujistěte se, že předplatné, které chcete, je vybránpro portál.

1. Vyberte **řízení přístupu (IAM)**.
1. Vyberte **Přidat přiřazení role**.
1. Vyberte roli, kterou chcete aplikaci přiřadit. Chcete-li například aplikaci povolit provádění akcí, jako je **restartování**, **spuštění** a **zastavení** instancí, vyberte roli **přispěvatele.**  Další informace o [dostupných rolích](../../role-based-access-control/built-in-roles.md) Ve výchozím nastavení se aplikace Azure AD nezobrazují v dostupných možnostech. Chcete-li najít aplikaci, vyhledejte název a vyberte ji.

   ![Vyberte roli, kterou chcete přiřadit k aplikaci.](./media/howto-create-service-principal-portal/select-role.png)

1. Chcete-li dokončit přiřazení role, vyberte **uložit.** Zobrazí se aplikace v seznamu uživatelů s rolí pro tento obor.

Váš instanční objekt je nastaven. Můžete ji začít používat ke spuštění skriptů nebo aplikací. V další části se zobrazí, jak získat hodnoty, které jsou potřebné při podepisování programově.

## <a name="get-values-for-signing-in"></a>Získání hodnot pro přihlášení

Při programovém přihlášení musíte předat ID klienta s požadavkem na ověření. Potřebujete také ID pro vaši aplikaci a ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. Vyberte **Azure Active Directory**.
1. V **registraci aplikací** ve službě Azure AD vyberte svou aplikaci.
1. Zkopírujte ID adresáře (klienta) a uložte ho do kódu aplikace.

    ![Zkopírujte adresář (ID klienta) a uložte ho do kódu aplikace](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace.

   ![Zkopírovat ID aplikace (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certifikáty a tajné klíče
Daemon aplikace můžete použít dvě formy přihlašovacích údajů k ověření pomocí Azure AD: certifikáty a tajné klíče aplikací.  Doporučujeme použít certifikát, ale můžete také vytvořit nový tajný klíč aplikace.

### <a name="upload-a-certificate"></a>Nahrání certifikátu

Pokud jej máte, můžete použít existující certifikát.  Volitelně můžete vytvořit certifikát podepsaný svým držitelem pouze pro *účely testování*. Otevřete PowerShell a [spusťte New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) s následujícími parametry a vytvořte certifikát podepsaný svým držitelem v úložišti uživatelských certifikátů v počítači: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportujte tento certifikát do souboru pomocí modulu snap-in [Spravovat uživatelský certifikát](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC přístupného z Ovládacích panelů systému Windows.

1. Z nabídky **Start** vyberte **Spustit** a zadejte **certmgr.msc**.

   Zobrazí se nástroj Správce certifikátů pro aktuálního uživatele.

1. Chcete-li zobrazit certifikáty, rozbalte v části **Certifikáty – aktuální uživatel** v levém podokně **osobní** adresář.
1. Klikněte pravým tlačítkem myši na certifikát, který jste vytvořili, a vyberte **všechny úkoly >export .**
1. Postupujte podle průvodce exportem certifikátu.  Exportujte soukromý klíč, zadejte heslo pro soubor certifikátu a exportujte do souboru.

Chcete-li certifikát nahrát,

1. Vyberte **Azure Active Directory**.
1. V **registraci aplikací** ve službě Azure AD vyberte svou aplikaci.
1. Vyberte **možnost Certifikáty & tajných kódů**.
1. Vyberte **Nahrát certifikát** a vyberte certifikát (existující certifikát nebo certifikát podepsaný svým držitelem, který jste exportovali).

    ![Vyberte Nahrát certifikát a vyberte ten, který chcete přidat.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Vyberte **Přidat**.

Po registraci certifikátu u aplikace na portálu pro registraci aplikací je třeba povolit použití certifikátu kódem klientské aplikace.

### <a name="create-a-new-application-secret"></a>Vytvoření nového tajného klíče aplikace

Pokud se rozhodnete certifikát nepoužívat, můžete vytvořit nový tajný klíč aplikace.

1. Vyberte **možnost Certifikáty & tajných kódů**.
1. Vyberte **tajný chutin klienta -> Nový tajný klíč klienta**.
1. Zadejte popis tajného klíče a dobu trvání. Až bude hotovo, vyberte **Přidat**.

   Po uložení tajného klíče klienta se zobrazí hodnota tajného klíče klienta. Zkopírujte tuto hodnotu, protože klíč nebudete moci později načíst. Zadáte hodnotu klíče s ID aplikace pro přihlášení jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![Zkopírujte tajnou hodnotu, protože ji nemůžete později načíst](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurace zásad přístupu k prostředkům
Mějte na paměti, že možná budete muset nakonfigurovat oprávnění pro přidání prostředků, ke kterým vaše aplikace potřebuje přístup. Například je nutné také [aktualizovat zásady přístupu trezoru klíčů,](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) aby aplikace přístup ke klíčům, tajným kódům nebo certifikátům.  

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do trezoru klíčů a vyberte **zásady přístupu**.  
1. Vyberte **Přidat zásady přístupu**, vyberte oprávnění klíče, tajného klíče a certifikátu, kterým chcete aplikaci udělit.  Vyberte instanční objekt, který jste vytvořili dříve.
1. Vyberte **Přidat,** chcete-li přidat zásady přístupu, pak **uložit,** abyste změny potvrzlili.
    ![Přidat zásady přístupu](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Požadovaná oprávnění

Musíte mít dostatečná oprávnění k registraci aplikace s tenantem Azure AD a přiřadit k aplikaci roli ve vašem předplatném Azure.

### <a name="check-azure-ad-permissions"></a>Kontrola oprávnění Azure AD

1. Vyberte **Azure Active Directory**.
1. Všimněte si vaší role. Pokud máte roli **Uživatele,** musíte se ujistit, že nesprávci mohou zaregistrovat aplikace.

   ![Najděte svou roli. Pokud jste uživatel, ujistěte se, že aplikace mohou zaregistrovat uživatelé, kteří nejsou správci.](./media/howto-create-service-principal-portal/view-user-info.png)

1. V levém podokně vyberte **Uživatelská nastavení**.
1. Zkontrolujte nastavení **registrace aplikací.** Tuto hodnotu může nastavit pouze správce. Pokud je nastavena na **Ano**, může aplikaci zaregistrovat každý uživatel v tenantovi Azure AD.

Pokud je nastavení registrace aplikací nastaveno na **hodnotu Ne**, mohou tyto typy aplikací zaregistrovat pouze uživatelé s rolí správce. Informace o dostupných rolích a rolích najdete [v](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) [tématu Dostupné role](../users-groups-roles/directory-assign-admin-roles.md#available-roles) a oprávnění, která jsou udělena každé roli. Pokud je vašemu účtu přiřazena role Uživatel, ale nastavení registrace aplikace je omezeno na uživatele správce, požádejte správce, aby vám buď přiřadil jednu z rolí správce, která může vytvářet a spravovat všechny aspekty registrací aplikací, nebo aby uživatelům umožnil zaregistrovat se. Apps.

### <a name="check-azure-subscription-permissions"></a>Kontrola oprávnění předplatného Azure

Ve vašem předplatném Azure `Microsoft.Authorization/*/Write` musí mít váš účet přístup k přiřazení role k aplikaci AD. Tato akce se povoluje prostřednictvím role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../../role-based-access-control/built-in-roles.md#user-access-administrator). Pokud je vašemu účtu přiřazena role **přispěvatele,** nemáte dostatečná oprávnění. Při pokusu o přiřazení role instančního objektu se zobrazí chyba.

Kontrola oprávnění k předplatnému:

1. Vyhledejte a vyberte **Předplatná**nebo na **domovské** stránce vyberte **Předplatná.**

   ![Search](./media/howto-create-service-principal-portal/select-subscription.png)

1. Vyberte předplatné, ve kterém chcete vytvořit instanční objekt.

   ![Vybrat předplatné pro přiřazení](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Pokud hledací předplatné nevidíte, vyberte **filtr globálních předplatných**. Ujistěte se, že předplatné, které chcete, je vybránpro portál.

1. Vyberte **možnost Moje oprávnění**. Potom vyberte **Klepnutím sem zobrazíte úplné podrobnosti o přístupu k tomuto předplatnému**.

   ![Vyberte předplatné, ve kterém chcete vytvořit instanční objekt.](./media/howto-create-service-principal-portal/view-details.png)

1. Vyberte **Zobrazení** v **přiřazení rolí,** chcete-li zobrazit přiřazené role a určit, zda máte dostatečná oprávnění k přiřazení role aplikaci AD. Pokud ne, požádejte správce předplatného, aby vás přidal do role Správce přístupu uživatelů. Na následujícím obrázku je uživateli přiřazena role vlastníka, což znamená, že uživatel má odpovídající oprávnění.

   ![Tento příklad ukazuje, že uživateli je přiřazena role Vlastník.](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Další kroky

* Další informace o určení zásad zabezpečení najdete v [tématu Řízení přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md).  
* Seznam dostupných akcí, které mohou být uživatelům uděleny nebo odepřeny, naleznete v [tématu operace zprostředkovatele prostředků Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
