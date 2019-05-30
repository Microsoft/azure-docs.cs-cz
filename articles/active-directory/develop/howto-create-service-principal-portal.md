---
title: Vytvoření identity pro aplikace Azure na portálu | Dokumentace Microsoftu
description: Popisuje, jak vytvořit novou aplikaci Azure Active Directory a instančního objektu, který lze použít s řízením přístupu na základě role v Azure Resource Manageru pro správu přístupu k prostředkům.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5a16e2d5e3ac723675ebdb536a51d20412681f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235395"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Postup: Použití portálu k vytvoření aplikace Azure AD a instanční objekt, který má přístup k prostředkům

V tomto článku se dozvíte, jak vytvořit novou aplikaci Azure Active Directory (Azure AD) a instanční objekt, který je možné s řízením přístupu na základě rolí. Když máte kód, který je potřeba přistupovat nebo změnit prostředky, můžete vytvořit identitu pro aplikace. Tato identita se označuje jako instanční objekt. Pak můžete přiřadit požadovaná oprávnění pro instanční objekt. Tento článek ukazuje, jak používat portál k vytvoření instančního objektu služby. Zaměřuje se na jednoho tenanta aplikaci, kde je aplikace určena pro spuštění v rámci organizace jenom jeden. Obvykle používají aplikace s jedním tenantem pro řádek podnikovým aplikacím, které běží v rámci vaší organizace.

> [!IMPORTANT]
> Místo vytvoření instančního objektu, zvažte použití spravované identity pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službu, která podporuje spravované identity a přístupy prostředky, které podporují ověřování Azure AD, spravované identity jsou vhodnější pro vás. Další informace o spravovaných identit pro prostředky Azure, včetně služby, které aktuálně podporují, najdete v článku [co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Přejděme rovnou k vytvoření identity. Pokud narazíte na problém, zkontrolujte, [požadovaná oprávnění](#required-permissions) k Ujistěte se, že váš účet můžete vytvořit identitu.

1. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory**.
1. Vyberte **Registrace aplikací**.

   ![Výběr registrace aplikací](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Vyberte **registrace nové**.

   ![Přidat aplikaci](./media/howto-create-service-principal-portal/select-add-app.png)

1. Zadejte název aplikace. Vyberte účet podporovaný typ, který určuje, kdo může používat aplikace. V části **identifikátor URI pro přesměrování**vyberte **webové** pro typ aplikace, kterou chcete vytvořit. Zadejte identifikátor URI, kde bude zaslána přístupový token.  Nelze vytvořit přihlašovací údaje [nativní aplikace](../manage-apps/application-proxy-configure-native-client-application.md). Tento typ nelze použít pro automatické aplikaci. Po nastavení hodnot, vyberte **zaregistrovat**.

   ![název aplikace](./media/howto-create-service-principal-portal/create-app.png)

Vytvořili jste aplikaci Azure AD a instanční objekt služby.

## <a name="assign-the-application-to-a-role"></a>Přiřazení aplikace k roli

Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli nabízí správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: Vestavěné role](../../role-based-access-control/built-in-roles.md).

Nastavit obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků znamená, že můžete přečíst, skupinu prostředků a všechny prostředky, které obsahuje.

1. Přejděte na úrovni oboru, který chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **všechny služby** a **předplatná**.

   ![Výběr předplatného](./media/howto-create-service-principal-portal/select-subscription.png)

1. Vyberte konkrétní předplatné, které chcete přiřadit aplikaci.

   ![Vyberte předplatné pro přiřazení](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Pokud nevidíte předplatné, které hledáte, vyberte **filtr globálních předplatných**. Ujistěte se, že předplatné, které chcete, aby je vybrán pro portál. 

1. Vyberte **řízení přístupu (IAM)** .
1. Vyberte **přidat přiřazení role**.

   ![Výběr možnosti Přidat přiřazení role](./media/howto-create-service-principal-portal/select-add.png)

1. Vyberte roli, kterou chcete přiřadit k aplikaci. Aby aplikace mohla provést akce, jako je **restartovat**, **start** a **Zastavit** instance, vyberte **Přispěvatel** role. Ve výchozím nastavení nejsou zobrazeny aplikacím služby Azure AD v dostupných možnostech. Najít aplikace, vyhledejte název a vyberte ji.

   ![Výběr role](./media/howto-create-service-principal-portal/select-role.png)

1. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

Je nastavený instančního objektu služby. Chcete-li začít, používat ke spouštění skriptů nebo aplikací. V další části ukazuje, jak získat hodnoty, které jsou potřeba při přihlašování prostřednictvím kódu programu.

## <a name="get-values-for-signing-in"></a>Získání hodnot pro přihlášení

Při programově přihlášení, je potřeba předat ID tenanta žádosti o ověření. Taky potřebujete ID aplikace a ověřovacího klíče. K získání těchto hodnot použijte následující postup:

1. Vyberte **Azure Active Directory**.

1. Z **registrace aplikací** ve službě Azure AD, vyberte svou aplikaci.

   ![Vybrat aplikaci](./media/howto-create-service-principal-portal/select-app.png)

1. Zkopírujte ID adresáře (tenant) a uložte ho v kódu aplikace.

    ![ID tenanta](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace.

   ![ID klienta](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certifikáty a tajné kódy
Deamon aplikací můžete používat dvě formy přihlašovacích údajů k ověření ve službě Azure AD: certifikátů a tajných klíčů aplikací.  Doporučujeme používat certifikát, ale můžete také vytvořit nový tajný klíč aplikace.

### <a name="upload-a-certificate"></a>Nahrání certifikátu

Pokud nemáte, můžete použít existující certifikát.  Volitelně můžete vytvořit certifikát podepsaný svým držitelem pro testovací účely. Otevřete PowerShell a spuštěním [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) s následujícími parametry k vytvoření certifikátu podepsaného svým držitelem do úložiště certifikátů uživatele ve vašem počítači: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  Export tohoto certifikátu pomocí [spravovat uživatelský certifikát](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) modul snap-in konzoly MMC přístupné z ovládacího panelu Windows.

Nahrání certifikátu:
1. Vyberte **certifikáty a tajné kódy**.

   ![Vyberte nastavení](./media/howto-create-service-principal-portal/select-certs-secrets.png)
1. Klikněte na **nahrát certifikát** a vyberte certifikát (existující certifikát nebo certifikát podepsaný svým držitelem certifikáty můžete exportovat).
    ![Nahrání certifikátu](./media/howto-create-service-principal-portal/upload-cert.png)
1. Klikněte na tlačítko **Add** (Přidat).

Po registraci certifikátu s vaší aplikací v portálu pro registraci aplikace, je potřeba povolit kódem klientské aplikace pro použití certifikátu.

### <a name="create-a-new-application-secret"></a>Vytvořit nový tajný klíč aplikace
Pokud se rozhodnete použít certifikát, můžete vytvořit nový tajný klíč aplikace.
1. Vyberte **certifikáty a tajné kódy**.

   ![Vyberte nastavení](./media/howto-create-service-principal-portal/select-certs-secrets.png)

1. Vyberte **tajné klíče klienta -> Nový tajný kód klienta**.
1. Zadejte popis tajný klíč a hodnotu duration. Až budete hotovi, vyberte **přidat**.

   ![Uložte tajný klíč](./media/howto-create-service-principal-portal/save-secret.png)

   Po uložení tajný kód klienta, se zobrazí hodnota tajného kódu klienta. Zkopírujte tuto hodnotu, protože nejste schopni načíst klíč později. Hodnotu klíče uveďte s ID aplikace se přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![Zkopírovat tajný klíč](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Požadovaná oprávnění

Musíte mít dostatečná oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném Azure.

### <a name="check-azure-ad-permissions"></a>Zkontrolujte oprávnění Azure AD

1. Vyberte **Azure Active Directory**.
1. Všimněte si vaši roli. Pokud máte **uživatele** role, ujistěte se, že nejsou správci, můžou registrovat aplikace.

   ![Najít uživatele](./media/howto-create-service-principal-portal/view-user-info.png)

1. Vyberte **uživatelská nastavení**.

   ![Vyberte uživatelská nastavení.](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Zkontrolujte, **registrace aplikací** nastavení. Tuto hodnotu lze nastavit pouze správce. Pokud hodnotu **Ano**, každý uživatel v tenantovi Azure AD můžete zaregistrovat aplikaci.

   ![Zobrazit registrace aplikace](./media/howto-create-service-principal-portal/view-app-registrations.png)

Pokud je nastavení registrace aplikací nastaveno **ne**, pouze uživatelé s rolí správce může registrovat tyto typy aplikací. Zobrazit [dostupných rolí](../users-groups-roles/directory-assign-admin-roles.md#available-roles) a [oprávnění role](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) Další informace o rolích správce a zvláštní oprávnění ve službě Azure AD, kterým jsou přiřazena k jednotlivým rolím. Pokud je váš účet přiřazenou roli uživatele, ale registrace nastavení aplikace, které je omezená na uživatele správce, požádejte svého správce, aby buď můžete přiřadit do jedné z rolí správce, které můžete vytvářet a spravovat všechny aspekty registrací aplikací nebo uživatelům umožnit Registrace aplikací.

### <a name="check-azure-subscription-permissions"></a>Zkontrolujte oprávnění předplatného Azure

Ve vašem předplatném Azure, musí mít váš účet `Microsoft.Authorization/*/Write` přístup k aplikaci AD přiřadit k roli. Tato akce se povoluje prostřednictvím role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../../role-based-access-control/built-in-roles.md#user-access-administrator). Pokud váš účet je přiřazena k **Přispěvatel** role, nemáte dostatečná oprávnění. Zobrazí chybová zpráva při pokusu o přiřazení instančního objektu k roli.

Ke kontrole oprávnění pro předplatné:

1. V pravém horním rohu vyberte svůj účet a vyberte **... -> Moje oprávnění**.

   ![Vyberte oprávnění pro uživatele](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. V rozevíracím seznamu vyberte předplatné, které chcete vytvořit instanční objekt ve službě. Vyberte **kliknutím zde zobrazíte úplný přístup podrobnosti pro toto předplatné**.

   ![Najít uživatele](./media/howto-create-service-principal-portal/view-details.png)

1. Vyberte **přiřazení rolí** zobrazení přiřazených rolí a zjistit, pokud máte odpovídající oprávnění k přiřazení AD aplikace k roli. Pokud ne, požádejte správce předplatného, aby vás přidal do role správce přístupu uživatelů. Na následujícím obrázku že uživatel přiřazený k roli vlastník, což znamená, že má uživatel dostatečná oprávnění.

   ![Zobrazit oprávnění](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Další postup

* Pokud chcete nastavit aplikaci s více tenanty, přečtěte si [Příručka pro vývojáře k ověřování pomocí rozhraní API Azure Resource Manageru](../../azure-resource-manager/resource-manager-api-authentication.md).
* Další informace o určení zásad pro zabezpečení najdete v tématu [řízení přístupu na základě Role v Azure](../../role-based-access-control/role-assignments-portal.md).  
* Seznam dostupných akcí, které může být povolen nebo odepřen pro uživatele najdete v tématu [operace poskytovatele prostředků Azure Resource Manageru](../../role-based-access-control/resource-provider-operations.md).
