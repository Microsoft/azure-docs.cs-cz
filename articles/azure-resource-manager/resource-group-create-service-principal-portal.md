---
title: Vytvoření identity pro aplikace Azure na portálu | Dokumentace Microsoftu
description: Popisuje, jak vytvořit novou aplikaci Azure Active Directory a instančního objektu, který lze použít s řízením přístupu na základě role v Azure Resource Manageru pro správu přístupu k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2018
ms.author: tomfitz
ms.openlocfilehash: 5233cde48d52e34960e87d3362b8cfaf3a0722c0
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113729"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům pomocí portálu

Když máte kód, který je potřeba přistupovat nebo změnit prostředky, můžete vytvořit identitu pro aplikace. Tato identita se označuje jako instanční objekt. Pak můžete přiřadit požadovaná oprávnění pro instanční objekt. Tento článek ukazuje, jak používat portál k vytvoření instančního objektu služby. Zaměřuje se na jednoho tenanta aplikaci, kde je aplikace určena pro spuštění v rámci organizace jenom jeden. Obvykle používají aplikace s jedním tenantem pro řádek podnikovým aplikacím, které běží v rámci vaší organizace.

> [!IMPORTANT]
> Místo vytvoření instančního objektu, zvažte použití spravované identity pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službu, která podporuje spravované identity a přístupy prostředky, které podporují ověřování pomocí Azure Active Directory, spravované identity jsou vhodnější pro vás. Další informace o spravovaných identit pro prostředky Azure, včetně služby, které aktuálně podporují, najdete v článku [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Přejděme rovnou k vytvoření identity. Pokud narazíte na problém, zkontrolujte, [požadovaná oprávnění](#required-permissions) k Ujistěte se, že váš účet můžete vytvořit identitu.

1. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory**.

   ![Výběr azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Vyberte **Registrace aplikací**.

   ![Výběr registrace aplikací](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Vyberte **+ registrace nové aplikace**.

   ![Přidat aplikaci](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Zadejte název a URL aplikace. V poli **Webová aplikace / webové rozhraní API** vyberte typ vytvářené aplikace. Nelze vytvořit přihlašovací údaje [nativní aplikace](../active-directory/manage-apps/application-proxy-configure-native-client-application.md). Tento typ nelze použít pro automatické aplikaci. Po nastavení hodnot, vyberte **vytvořit**.

   ![název aplikace](./media/resource-group-create-service-principal-portal/create-app.png)

Vytvořili jste aplikaci Azure Active Directory a instančního objektu.

## <a name="assign-application-to-role"></a>Přiřazení aplikace k roli

Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli nabízí správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md).

Nastavit obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků znamená, že můžete přečíst, skupinu prostředků a všechny prostředky, které obsahuje.

1. Přejděte na úrovni oboru, který chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **všechny služby** a **předplatná**.

   ![Výběr předplatného](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Vyberte konkrétní předplatné, které chcete přiřadit aplikaci.

   ![Vyberte předplatné pro přiřazení](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

   Pokud nevidíte předplatné, které hledáte, vyberte **filtr globálních předplatných**. Ujistěte se, že předplatné, které chcete, aby je vybrán pro portál. 

1. Vyberte **řízení přístupu (IAM)**.

   ![Vyberte přístup](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Vyberte **+ Přidat**.

   ![Výběr možnosti Přidat](./media/resource-group-create-service-principal-portal/select-add.png)

1. Vyberte roli, kterou chcete přiřadit k aplikaci. Umožňuje aplikaci provést akce, jako je **restartovat**, **start** a **Zastavit** instance, vyberte **Přispěvatel** role. Ve výchozím nastavení aplikace Azure Active Directory nejsou zobrazeny v dostupných možnostech. Najít aplikace, vyhledejte název a vyberte ji.

   ![Výběr role](./media/resource-group-create-service-principal-portal/select-role.png)

1. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

Je nastavený instančního objektu služby. Chcete-li začít, používat ke spouštění skriptů nebo aplikací. V další části ukazuje, jak získat hodnoty, které jsou potřeba při přihlašování prostřednictvím kódu programu.

## <a name="get-values-for-signing-in"></a>Získání hodnot pro přihlášení

### <a name="get-tenant-id"></a>Získání ID tenanta

Při programově přihlášení, je potřeba předat ID tenanta žádosti o ověření.

1. Vyberte **Azure Active Directory**.

   ![Výběr azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Vyberte **vlastnosti**.

   ![Vyberte vlastnosti v Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Kopírovat **ID adresáře** k získání ID tenanta.

   ![ID tenanta](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Získejte ID a ověřovacího klíče aplikace

Taky potřebujete ID aplikace a ověřovacího klíče. K získání těchto hodnot použijte následující postup:

1. V Azure Active Directory vyberte z **Registrace aplikací** svou aplikaci.

   ![Vybrat aplikaci](./media/resource-group-create-service-principal-portal/select-app.png)

1. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace.

   ![ID klienta](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Vyberte **nastavení**.

   ![Vyberte nastavení](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Vyberte **Klíče**.

   ![Vyberte klíče](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

   ![Uložte klíč](./media/resource-group-create-service-principal-portal/save-key.png)

   Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte tuto hodnotu, protože nejste schopni načíst klíč později. Hodnotu klíče uveďte s ID aplikace se přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![uložený klíč](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Požadovaná oprávnění

Musíte mít dostatečná oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném Azure.

### <a name="check-azure-active-directory-permissions"></a>Kontrola oprávnění Azure Active Directory

1. Vyberte **Azure Active Directory**.

   ![Vyberte Azure Active Directory.](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Všimněte si, že vaši roli. Pokud máte **uživatele** role, ujistěte se, že nejsou správci, můžou registrovat aplikace.

   ![Najít uživatele](./media/resource-group-create-service-principal-portal/view-user-info.png)

1. Vyberte **uživatelská nastavení**.

   ![Vyberte uživatelská nastavení.](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Zkontrolujte, **registrace aplikací** nastavení. Tuto hodnotu lze nastavit pouze správce. Pokud hodnotu **Ano**, každý uživatel v tenantovi Azure AD můžete zaregistrovat aplikaci.

   ![Zobrazit registrace aplikace](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

Pokud je nastavení registrace aplikací nastaveno **ne**, pouze [globální správci](../active-directory/users-groups-roles/directory-assign-admin-roles.md) můžou registrovat aplikace. Pokud je váš účet přiřazenou roli uživatele, ale registrace nastavení aplikace, které je omezená na uživatele správce, požádejte svého správce, aby buď přiřaďte roli globálního správce nebo povolit uživatelům registrovat aplikace.

### <a name="check-azure-subscription-permissions"></a>Zkontrolujte oprávnění předplatného Azure

Ve vašem předplatném Azure, musí mít váš účet `Microsoft.Authorization/*/Write` přístup k aplikaci AD přiřadit k roli. Tato akce se povoluje prostřednictvím role [vlastníka](../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator). Pokud váš účet je přiřazena k **Přispěvatel** role, nemáte dostatečná oprávnění. Zobrazí chybová zpráva při pokusu o přiřazení instančního objektu k roli.

Ke kontrole oprávnění pro předplatné:

1. V pravém horním rohu vyberte svůj účet a vyberte **Moje oprávnění**.

   ![Vyberte oprávnění pro uživatele](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. V rozevíracím seznamu vyberte předplatné, které chcete vytvořit instanční objekt ve službě. Vyberte **kliknutím zde zobrazíte úplný přístup podrobnosti pro toto předplatné**.

   ![Najít uživatele](./media/resource-group-create-service-principal-portal/view-details.png)

1. Zobrazení přiřazených rolí a zjistit, pokud máte odpovídající oprávnění k přiřazení AD aplikace k roli. Pokud ne, požádejte správce předplatného, aby vás přidal do role správce přístupu uživatelů. Na následujícím obrázku že uživatel přiřazený k roli vlastník, což znamená, že má uživatel dostatečná oprávnění.

   ![Zobrazit oprávnění](./media/resource-group-create-service-principal-portal/view-user-role.png)


## <a name="next-steps"></a>Další postup
* Pokud chcete nastavit aplikaci s více tenanty, přečtěte si [Příručka pro vývojáře k ověřování pomocí rozhraní API Azure Resource Manageru](resource-manager-api-authentication.md).
* Další informace o určení zásad pro zabezpečení najdete v tématu [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md).  
* Seznam dostupných akcí, které může být povolen nebo odepřen pro uživatele najdete v tématu [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md).
