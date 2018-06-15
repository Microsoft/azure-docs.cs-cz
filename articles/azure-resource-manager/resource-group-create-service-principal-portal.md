---
title: Vytvoření identity pro aplikaci Azure portálu | Microsoft Docs
description: Popisuje, jak vytvořit novou aplikaci Azure Active Directory a objektu služby, které je možné pomocí řízení přístupu na základě rolí ve službě Správce prostředků Azure, které pokud chcete spravovat přístup k prostředkům.
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
ms.openlocfilehash: a8784a8e29e65d8abea566b5a2bf41a2ae6cadf0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359624"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Vytvoření aplikace Azure Active Directory a objektu služby, které mají přístup k prostředkům pomocí portálu

Když máte kód, který potřebuje přístup k nebo úpravám prostředků, musíte vytvořit aplikaci Azure Active Directory (AD). Přiřadíte požadovaná oprávnění k aplikaci AD. Tento postup je vhodnější spuštění aplikace vlastní oprávnění, protože můžete přiřadit oprávnění k identitě aplikace, která se liší od vlastní oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.

Tento článek ukazuje, jak provádět tyto kroky prostřednictvím portálu. Zaměřuje se na jednoho klienta aplikace, kde je záměrem spustit v rámci organizace jenom jedna aplikace. Obvykle používají aplikace jednoho klienta pro-obchodní aplikace, které běží v rámci vaší organizace.

> [!IMPORTANT]
> Místo vytvoření instančního objektu zvažte použití Identity spravované služby Azure AD (Azure AD MSI) pro identitu vaší aplikace. Azure AD MSI je funkce služby Azure Active Directory ve veřejné verzi Preview, která zjednodušuje vytváření identity pro kód. Pokud váš kód běží na službě, která podporuje Azure AD MSI a pracuje s prostředky, které podporují ověřování Azure Active Directory, je pro vás Azure AD MSI lepší volbou. Další informace o Azure AD MSI, včetně služeb, které ji aktuálně podporují, najdete v článku [Spravovaná identita služby pro prostředky Azure](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Požadovaná oprávnění

K dokončení tohoto článku, musíte mít dostatečná oprávnění k registraci aplikace ve službě klientovi Azure AD a přiřazení aplikace k roli ve vašem předplatném Azure. Ujistíme se, že máte správná oprávnění k provedení těchto kroků.

### <a name="check-azure-active-directory-permissions"></a>Zkontrolujte oprávnění služby Azure Active Directory

1. Vyberte **Azure Active Directory**.

   ![Výběr Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. V Azure Active Directory, vyberte **uživatelská nastavení**.

   ![Vyberte nastavení uživatele](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Zkontrolujte **registrace aplikace** nastavení. Pokud nastavena na **Ano**, uživatelé bez oprávnění správce můžete zaregistrovat aplikace AD. Toto nastavení znamená, že každý uživatel v klientovi Azure AD můžete zaregistrovat aplikaci. Můžete přejít k [oprávnění předplatné Azure zkontrolujte](#check-azure-subscription-permissions).

   ![Zobrazit registrace aplikace](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Pokud registrace aplikace, nastavení je nastavený na **ne**, pouze [globální správci](../active-directory/active-directory-assign-admin-roles-azure-portal.md) můžete zaregistrovat aplikace. Zkontrolujte, zda je váš účet správce pro tenanta Azure AD. Vyberte **přehled** a prohlédněte si informace o uživateli. Pokud je váš účet přiřazenou roli uživatele, ale nastavení registrace aplikace (z předchozího kroku) je omezený na správci, požádejte správce, aby buď přiřadit k roli globálního správce nebo umožňuje uživatelům registrovat aplikace.

   ![Najít uživatele](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Zkontrolujte oprávnění předplatného Azure

Ve vašem předplatném Azure, musí mít váš účet `Microsoft.Authorization/*/Write` přístup k aplikaci AD přiřadit roli. Tato akce je poskytována prostřednictvím [vlastníka](../role-based-access-control/built-in-roles.md#owner) role nebo [správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator) role. Pokud váš účet je přiřazen k **Přispěvatel** role, nemáte dostatečná oprávnění. Obdržíte chybu při pokusu o přiřazení objektu služby roli.

Zkontrolujte oprávnění svého předplatného:

1. Vyberte svůj účet v pravém horním rohu a vyberte **Moje oprávnění**.

   ![Vyberte oprávnění uživatele](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. V rozevíracím seznamu vyberte předplatné. Vyberte **kliknutím sem zobrazíte úplný přístup podrobnosti pro toto předplatné**.

   ![Najít uživatele](./media/resource-group-create-service-principal-portal/view-details.png)

1. Zobrazit přiřazené role a zjistit, pokud máte odpovídající oprávnění k aplikaci AD přiřadit roli. Pokud ne, požádejte správce předplatného můžete přidat do role správce přístupu uživatelů. Na následujícím obrázku je uživatele přiřadit k roli vlastníka, což znamená, že tento uživatel má odpovídající oprávnění.

   ![Zobrazit oprávnění](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

1. Přihlaste se k účtu Azure prostřednictvím [portál Azure](https://portal.azure.com).
1. Vyberte **Azure Active Directory**.

   ![Výběr Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Vyberte **Registrace aplikací**.

   ![Výběr Registrace aplikací](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Vyberte **Registrace nové aplikace**.

   ![Přidání aplikace](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Zadejte název a URL aplikace. V poli **Webová aplikace / webové rozhraní API** vyberte typ vytvářené aplikace. Nelze vytvořit přihlašovací údaje pro [nativní aplikace](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); proto, že typ nefunguje pro automatické aplikaci. Po nastavení hodnoty, vyberte **vytvořit**.

   ![Pojmenování aplikace](./media/resource-group-create-service-principal-portal/create-app.png)

Vytvoření vaší aplikace.

## <a name="get-application-id-and-authentication-key"></a>Získat klíč ID a ověřování aplikace

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

   Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte si ji, protože později už klíč nepůjde načíst. Hodnotu klíče uveďte s ID aplikace, aby bylo možné se přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

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

Pro přístup k prostředkům ve vašem předplatném, je nutné přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí najdete v tématu [RBAC: integrovaným rolím](../role-based-access-control/built-in-roles.md).

Rozsah můžete nastavit na úrovni předplatné, skupinu prostředků nebo prostředek. Na nižších úrovních oboru dědí oprávnění. Například přidání aplikace do role Čtenář pro skupinu prostředků znamená, že ho může číst skupině prostředků a všechny prostředky, které obsahuje.

1. Přejděte na úroveň oboru, který chcete přiřadit aplikaci. Například přiřazení role v oboru předplatné, vyberte **odběry**. Místo toho můžete třeba vybrat skupinu prostředků nebo prostředek.

   ![Vyberte předplatné](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Vyberte určitý odběr (skupinu prostředků nebo prostředek) aplikaci přiřadit.

   ![Vyberte předplatné pro přiřazení](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Vyberte **přístup k ovládacímu prvku (IAM)**.

   ![Vybrat přístupu](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Vyberte **Přidat**.

   ![Vyberte Přidat](./media/resource-group-create-service-principal-portal/select-add.png)

1. Vyberte roli, kterou chcete přiřadit k aplikaci. Na následujícím obrázku **čtečky** role.

   ![Vyberte roli](./media/resource-group-create-service-principal-portal/select-role.png)

1. Ve výchozím nastavení nejsou aplikace Azure Active Directory zobrazí v dostupných možností. K vyhledání aplikace, je nutné zadat název ho do pole hledání. Vyberte ho.

   ![Hledat aplikace](./media/resource-group-create-service-principal-portal/search-app.png)

1. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí aplikace v seznamu Uživatelé s přiřazenou rolí pro tento obor.

## <a name="next-steps"></a>Další postup
* Víceklientské aplikace, naleznete v tématu [Příručka pro vývojáře k autorizaci s rozhraním API pro Azure Resource Manager](resource-manager-api-authentication.md).
* Další informace o určení zásady zabezpečení najdete v tématu [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md).  
* Seznam dostupných akcí, které může být povolen nebo odepřen uživatelům najdete v tématu [poskytovatel prostředků Azure Resource Manager operations](../role-based-access-control/resource-provider-operations.md).
