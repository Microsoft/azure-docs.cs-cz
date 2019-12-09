---
title: Vytvoření identity pro aplikaci Azure na portálu
titleSuffix: Microsoft identity platform
description: V této části najdete popis postupu vytvoření nové aplikace Azure Active Directory a instančního objektu, který se dá použít s řízením přístupu na základě role v Azure Resource Manager ke správě přístupu k prostředkům.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c883dc1f9743e4318db5fe21b0e52ea92d60dc5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917858"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům

V tomto článku se dozvíte, jak vytvořit novou aplikaci Azure Active Directory (Azure AD) a instančního objektu, který se dá použít s řízením přístupu na základě role. Pokud máte kód, který potřebuje přístup k prostředkům nebo jejich úpravu, můžete pro aplikaci vytvořit identitu. Tato identita se označuje jako instanční objekt. Pak můžete objektům služby přiřadit požadovaná oprávnění. V tomto článku se dozvíte, jak použít portál k vytvoření instančního objektu. Zaměřuje se na aplikaci s jedním tenantů, kde má aplikace běžet jenom v jedné organizaci. Pro obchodní aplikace, které běží v rámci vaší organizace, obvykle používáte aplikace pro jednoho tenanta.

> [!IMPORTANT]
> Místo Vytvoření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službě, která podporuje spravované identity a přistupuje k prostředkům, které podporují ověřování Azure AD, jsou spravované identity lepší volbou pro vás. Další informace o spravovaných identitách pro prostředky Azure, včetně toho, které služby v současnosti podporují, najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Pojďme přejít přímo na vytvoření identity. Pokud narazíte na problém, zkontrolujte [požadovaná oprávnění](#required-permissions) a ujistěte se, že váš účet může vytvořit identitu.

1. Přihlaste se ke svému účtu Azure prostřednictvím [Azure Portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory**.
1. Vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Pojmenujte aplikaci. Vyberte podporovaný typ účtu, který určuje, kdo může aplikaci používat. V části **identifikátor URI pro přesměrování**vyberte **Web** pro typ aplikace, kterou chcete vytvořit. Zadejte identifikátor URI, na který se přístupový token pošle. Nemůžete vytvořit pověření pro [nativní aplikaci](../manage-apps/application-proxy-configure-native-client-application.md). Tento typ nelze použít pro automatizovanou aplikaci. Po nastavení hodnot vyberte **Registrovat**.

   ![Zadejte název vaší aplikace.](./media/howto-create-service-principal-portal/create-app.png)

Vytvořili jste aplikaci a instanční objekt služby Azure AD.

## <a name="assign-the-application-to-a-role"></a>Přiřazení aplikace k roli

Pokud chcete získat přístup k prostředkům ve vašem předplatném, musíte aplikaci přiřadit k roli. Rozhodněte, která role nabízí správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: vestavěné role](../../role-based-access-control/built-in-roles.md).

Rozsah můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění se dědí do oboru na nižších úrovních. Například přidání aplikace do role čtenář pro skupinu prostředků znamená, že může číst skupinu prostředků a všechny prostředky, které obsahuje.

1. V Azure Portal vyberte úroveň rozsahu, do kterého chcete aplikaci přiřadit. Pokud například chcete přiřadit roli v oboru předplatného, vyhledejte a vyberte **odběry**nebo vyberte **předplatná** na **domovské** stránce.

   ![Přiřaďte například roli v oboru předplatného.](./media/howto-create-service-principal-portal/select-subscription.png)

1. Vyberte konkrétní předplatné, ke kterému chcete aplikaci přiřadit.

   ![Výběr předplatného pro přiřazení](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Pokud nevidíte předplatné, které hledáte, vyberte **globální filtr předplatných**. Ujistěte se, že je vybráno požadované předplatné portálu.

1. Vyberte **Řízení přístupu (IAM)** .
1. Vyberte **přidat přiřazení role**.
1. Vyberte roli, kterou chcete aplikaci přiřadit. Pokud například chcete, aby aplikace mohla provádět akce, jako je **restartování**, **spuštění** a **zastavení** instancí, vyberte roli **Přispěvatel** .  Další informace o [dostupných rolích](../../role-based-access-control/built-in-roles.md) ve výchozím nastavení se v dostupných možnostech nezobrazí v aplikacích Azure AD. Chcete-li najít aplikaci, vyhledejte její název a vyberte ji.

   ![Vyberte roli, kterou chcete přiřadit k aplikaci.](./media/howto-create-service-principal-portal/select-role.png)

1. Vyberte **Uložit** k dokončení přiřazení role. Vaše aplikace se zobrazí v seznamu uživatelů přiřazených k roli pro tento obor.

Vaše instanční objekt je nastavený. Můžete ji začít používat ke spouštění skriptů nebo aplikací. V další části se dozvíte, jak získat hodnoty, které jsou potřeba při přihlašování prostřednictvím kódu programu.

## <a name="get-values-for-signing-in"></a>Získat hodnoty pro přihlášení

Při programovém přihlášení musíte předat ID tenanta s vaší žádostí o ověření. Také potřebujete ID aplikace a ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. Vyberte **Azure Active Directory**.
1. V **Registrace aplikací** ve službě Azure AD vyberte svou aplikaci.
1. Zkopírujte ID adresáře (tenant) a uložte ho do kódu aplikace.

    ![Zkopírujte adresář (ID tenanta) a uložte ho do kódu vaší aplikace.](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace.

   ![Kopírovat ID aplikace (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certifikáty a tajné klíče
Aplikace démona můžou pomocí dvou forem přihlašovacích údajů ověřit pomocí Azure AD: certifikáty a tajné klíče aplikace.  Doporučujeme použít certifikát, ale můžete také vytvořit nový tajný klíč aplikace.

### <a name="upload-a-certificate"></a>Odeslat certifikát

Pokud nějaký máte, můžete použít existující certifikát.  Volitelně můžete pro účely testování vytvořit certifikát podepsaný svým držitelem. Otevřete PowerShell a spusťte rutinu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) s následujícími parametry k vytvoření certifikátu podepsaného svým držitelem v úložišti certifikátů uživatele v počítači: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportujte tento certifikát do souboru pomocí modulu snap-in [Spravovat certifikát uživatele](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) konzoly MMC přístupný z ovládacích panelů systému Windows.

Postup nahrání certifikátu:

1. Vyberte **certifikáty & tajných**kódů.
1. Vyberte **nahrát certifikát** a vyberte certifikát (existující certifikát nebo certifikát podepsaný svým držitelem).

    ![Vyberte nahrát certifikát a vyberte ten, který chcete přidat.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Vyberte **Přidat**.

Po registraci certifikátu ve vaší aplikaci na portálu pro registraci aplikací je nutné povolit klientský kód aplikace pro použití certifikátu.

### <a name="create-a-new-application-secret"></a>Vytvořit nový tajný klíč aplikace

Pokud se rozhodnete nepoužívat certifikát, můžete vytvořit nový tajný klíč aplikace.

1. Vyberte **certifikáty & tajných**kódů.
1. Vyberte **Client tajných klíčů – > nový tajný klíč klienta**.
1. Zadejte popis tajného kódu a dobu trvání. Až budete hotovi, vyberte **Přidat**.

   Po uložení tajného klíče klienta se zobrazí hodnota tajného klíče klienta. Zkopírujte tuto hodnotu, protože nemůžete získat klíč později. Zadejte hodnotu klíče s ID aplikace, abyste se mohli přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![Zkopírujte tajnou hodnotu, protože ji nemůžete později načíst.](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurace zásad přístupu pro prostředky
Mějte na paměti, že možná budete muset pro prostředky, ke kterým aplikace potřebuje přístup, nakonfigurovat oprávnění k přidání. Například je třeba [aktualizovat zásady přístupu trezoru klíčů](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) , aby vaše aplikace měla přístup k klíčům, tajným klíčům nebo certifikátům.  

1. V [Azure Portal](https://portal.azure.com)přejděte do svého trezoru klíčů a vyberte **zásady přístupu**.  
1. Vyberte **Přidat zásady přístupu**a pak vyberte klíčová, tajná a oprávnění certifikátů, která chcete aplikaci udělit.  Vyberte objekt služby, který jste předtím vytvořili.
1. Vyberte **Přidat** a přidejte zásadu přístupu a potom **uložte** změny, které chcete potvrdit.
    ![přidat zásady přístupu](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Požadovaná oprávnění

Musíte mít dostatečná oprávnění k registraci aplikace ve vašem tenantovi Azure AD a přiřazení aplikace k roli v předplatném Azure.

### <a name="check-azure-ad-permissions"></a>Ověřit oprávnění služby Azure AD

1. Vyberte **Azure Active Directory**.
1. Poznamenejte si svoji roli. Máte-li roli **uživatele** , je nutné zajistit, aby aplikace bez oprávnění správce mohli registrovat aplikace.

   ![Najděte svoji roli. Pokud jste uživatel, ujistěte se, že nesprávci můžou registrovat aplikace.](./media/howto-create-service-principal-portal/view-user-info.png)

1. V levém podokně vyberte **nastavení uživatele**.
1. Ověřte nastavení **Registrace aplikací** . Tuto hodnotu může nastavit jenom správce. Pokud je nastaveno na **Ano**, každý uživatel v TENANTOVI Azure AD může zaregistrovat aplikaci.

Pokud je nastavení registrace aplikací nastaveno na **ne**, můžou tyto typy aplikací registrovat jenom uživatelé s rolí správce. Další informace o dostupných rolích správců a konkrétních oprávněních v Azure AD, která jsou udělena jednotlivým rolím, najdete v tématu [dostupné role](../users-groups-roles/directory-assign-admin-roles.md#available-roles) a [oprávnění role](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) . Pokud je váš účet přiřazený k roli uživatele, ale nastavení registrace aplikace je omezené na uživatele s právy pro správu, požádejte správce, aby vám přidělil jednu z rolí správce, která může vytvářet a spravovat všechny aspekty registrací aplikací, nebo umožnit uživatelům registrovat aplikace.

### <a name="check-azure-subscription-permissions"></a>Ověřit oprávnění pro předplatné Azure

Ve vašem předplatném Azure musí mít váš účet `Microsoft.Authorization/*/Write` přístup k přiřazení aplikace AD k roli. Tato akce se povoluje prostřednictvím role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../../role-based-access-control/built-in-roles.md#user-access-administrator). Pokud je váš účet přiřazen k roli **Přispěvatel** , nemáte příslušná oprávnění. Při pokusu o přiřazení instančního objektu k roli se zobrazí chyba.

Ověření oprávnění k předplatnému:

1. V pravém horním rohu vyberte svůj účet a vyberte **...-> moje oprávnění**.

   ![Vyberte svůj účet a oprávnění uživatele.](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. V rozevíracím seznamu vyberte předplatné, ve kterém chcete vytvořit instanční objekt. Pak vyberte **kliknutím sem zobrazíte informace o úplných přístupech k tomuto předplatnému**.

   ![Vyberte předplatné, ve kterém chcete vytvořit instanční objekt.](./media/howto-create-service-principal-portal/view-details.png)

1. Výběrem **přiřazení rolí** zobrazíte přiřazené role a určíte, jestli máte adekvátní oprávnění k přiřazení aplikace AD k roli. Pokud ne, požádejte správce předplatného, aby vás přidal do role správce přístupu uživatele. Na následujícím obrázku je uživatel přiřazen k roli vlastníka, což znamená, že uživatel má odpovídající oprávnění.

   ![Tento příklad ukazuje, že uživatel je přiřazen k roli vlastníka.](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Další kroky

* Další informace o zadávání zásad zabezpečení najdete v tématu [Access Control na základě rolí v Azure](../../role-based-access-control/role-assignments-portal.md).  
* Seznam dostupných akcí, které mohou uživatelé udělit nebo odepřít, najdete v tématu [Azure Resource Manager operací poskytovatele prostředků](../../role-based-access-control/resource-provider-operations.md).
