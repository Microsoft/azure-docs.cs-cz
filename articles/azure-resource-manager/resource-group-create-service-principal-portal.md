---
title: Vytvoření identity pro aplikace Azure na portálu | Dokumentace Microsoftu
description: Popisuje, jak vytvořit novou aplikaci Azure Active Directory a instančního objektu, který lze použít s řízením přístupu na základě role v Azure Resource Manageru pro správu přístupu k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 2f053f6dd98b9f4e97d69e51bce933a003633277
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497939"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům pomocí portálu

Až budete mít kód, který je potřeba přistupovat nebo změnit prostředky, musíte vytvořit aplikaci Azure Active Directory (AD). Pak můžete přiřadit požadovaná oprávnění k aplikaci AD. Tento přístup je vhodnější pro spuštění aplikace pod svými přihlašovacími údaji, protože můžete přiřadit oprávnění pro aplikace identitu, která se liší od své vlastní oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.

Tento článek ukazuje, jak tento postup na portálu. Zaměřuje se na jednoho tenanta aplikaci, kde je aplikace určena pro spuštění v rámci organizace jenom jeden. Obvykle používají aplikace s jedním tenantem pro řádek podnikovým aplikacím, které běží v rámci vaší organizace.

> [!IMPORTANT]
> Místo vytvoření instančního objektu, zvažte použití spravované identity pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službu, která podporuje spravované identity a přístupy prostředky, které podporují ověřování pomocí Azure Active Directory, spravované identity jsou vhodnější pro vás. Další informace o spravovaných identit pro prostředky Azure, včetně služby, které aktuálně podporují, najdete v článku [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="required-permissions"></a>Požadovaná oprávnění

K dokončení tohoto článku, musíte mít dostatečná oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném Azure. Ujistíme se, že máte správná oprávnění k provedení těchto kroků.

### <a name="check-azure-active-directory-permissions"></a>Kontrola oprávnění Azure Active Directory

1. Vyberte **Azure Active Directory**.

   ![Výběr Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. V Azure Active Directory vyberte **Uživatelská nastavení**.

   ![Vyberte uživatelská nastavení.](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Zkontrolujte, **registrace aplikací** nastavení. Pokud nastavit **Ano**, bez oprávnění správce. uživatelé můžou registrovat aplikace AD. V případě tohoto nastavení může aplikaci zaregistrovat kterýkoli uživatel v tenantovi Azure AD. Můžete přejít k [oprávnění pro předplatné Azure zkontrolujte](#check-azure-subscription-permissions).

   ![Zobrazit registrace aplikace](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Pokud je nastavení registrace aplikací nastaveno **ne**, pouze [globální správci](../active-directory/users-groups-roles/directory-assign-admin-roles.md) můžou registrovat aplikace. Zkontrolujte, zda je váš účet správce pro tenanta Azure AD. Vyberte **přehled** a podívejte se na informace o uživateli. Pokud je váš účet přiřazenou roli uživatele, ale nastavení registrace aplikace (z předchozího kroku) je omezená na uživatele správce, požádejte svého správce, aby buď přiřaďte roli globálního správce nebo povolit uživatelům registrovat aplikace.

   ![Najít uživatele](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Zkontrolujte oprávnění předplatného Azure

Ve vašem předplatném Azure, musí mít váš účet `Microsoft.Authorization/*/Write` přístup k aplikaci AD přiřadit k roli. Tato akce se povoluje prostřednictvím role [vlastníka](../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator). Pokud váš účet je přiřazena k **Přispěvatel** role, nemáte dostatečná oprávnění. Zobrazí chybová zpráva při pokusu o přiřazení instančního objektu k roli.

Ke kontrole oprávnění pro předplatné:

1. V pravém horním rohu vyberte svůj účet a vyberte **Moje oprávnění**.

   ![Vyberte oprávnění pro uživatele](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Z rozevíracího seznamu vyberte předplatné. Vyberte **kliknutím zde zobrazíte úplný přístup podrobnosti pro toto předplatné**.

   ![Najít uživatele](./media/resource-group-create-service-principal-portal/view-details.png)

1. Zobrazení přiřazených rolí a zjistit, pokud máte odpovídající oprávnění k přiřazení AD aplikace k roli. Pokud ne, požádejte správce předplatného, aby vás přidal do role správce přístupu uživatelů. Na následujícím obrázku že uživatel přiřazený k roli vlastník, což znamená, že má uživatel dostatečná oprávnění.

   ![Zobrazit oprávnění](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

1. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory**.

   ![Výběr Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Vyberte **Registrace aplikací**.

   ![Výběr Registrace aplikací](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Vyberte **Registrace nové aplikace**.

   ![Přidání aplikace](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Zadejte název a URL aplikace. V poli **Webová aplikace / webové rozhraní API** vyberte typ vytvářené aplikace. Nelze vytvořit přihlašovací údaje pro [nativní aplikace](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); proto, že typ nefunguje pro automatické aplikaci. Po nastavení hodnot, vyberte **vytvořit**.

   ![Pojmenování aplikace](./media/resource-group-create-service-principal-portal/create-app.png)

Vytvořili jste aplikaci.

## <a name="get-application-id-and-authentication-key"></a>Získejte ID a ověřovacího klíče aplikace

K programovému přihlášení potřebujete ID aplikace a ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. V Azure Active Directory vyberte z **Registrace aplikací** svou aplikaci.

   ![Výběr aplikace](./media/resource-group-create-service-principal-portal/select-app.png)

1. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. V některých [ukázkových aplikacích](#log-in-as-the-application) se tato hodnota označuje jako ID klienta.

   ![ID klienta](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Pokud chcete generovat ověřovací klíč, vyberte **Nastavení**.

   ![Volba Nastavení](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

   ![Volba Klíče](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

   ![Uložení klíče](./media/resource-group-create-service-principal-portal/save-key.png)

   Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte tuto hodnotu, protože nejste schopni načíst klíč později. Hodnotu klíče uveďte s ID aplikace se přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![Uložený klíč](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Získání ID tenanta

Při programovém přihlášení potřebujete kromě žádosti o ověření předat i ID tenanta.

1. Vyberte **Azure Active Directory**.

   ![Výběr Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. K získání ID tenanta vyberte v tenantovi Azure AD možnost **Vlastnosti**.

   ![Výběr položky Vlastnosti v Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Zkopírujte **ID adresáře**. Tato hodnota představuje ID tenanta.

   ![ID tenanta](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Přiřazení aplikace k roli

Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md).

Nastavit obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků znamená, že můžete přečíst, skupinu prostředků a všechny prostředky, které obsahuje.

1. Přejděte na úrovni oboru, který chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **předplatná**. Místo toho můžete třeba vybrat skupinu prostředků nebo prostředek.

   ![Vyberte předplatné](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Vyberte konkrétní předplatné (skupinu prostředků nebo prostředek), přiřazení aplikace.

   ![Vyberte předplatné pro přiřazení](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Vyberte **řízení přístupu (IAM)**.

   ![Vyberte přístup](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Vyberte **Přidat**.

   ![Výběr možnosti Přidat](./media/resource-group-create-service-principal-portal/select-add.png)

1. Vyberte roli, kterou chcete přiřadit k aplikaci. Aby bylo možné povolit aplikaci provést akce, jako je **restartovat**, **start** a **Zastavit** instancí, musíte mít roli vyberte **Přispěvatel**. Na následujícím obrázku **čtečky** role.

   ![Vyberte roli](./media/resource-group-create-service-principal-portal/select-role.png)

1. Ve výchozím nastavení aplikace Azure Active Directory nejsou zobrazeny v dostupných možnostech. Pokud chcete najít aplikace, je nutné zadat název ho do vyhledávacího pole. Vyberte ji.

   ![Hledat aplikace](./media/resource-group-create-service-principal-portal/search-app.png)

1. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

## <a name="next-steps"></a>Další postup
* Pokud chcete nastavit aplikaci s více tenanty, přečtěte si [Příručka pro vývojáře k ověřování pomocí rozhraní API Azure Resource Manageru](resource-manager-api-authentication.md).
* Další informace o určení zásad pro zabezpečení najdete v tématu [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md).  
* Seznam dostupných akcí, které může být povolen nebo odepřen pro uživatele najdete v tématu [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md).
