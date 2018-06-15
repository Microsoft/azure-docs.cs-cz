---
title: Přihlašovací údaje nasazení služby Azure App Service | Microsoft Docs
description: Naučte se používat přihlašovací údaje pro nasazení služby Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "27778543"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurovat přihlašovací údaje nasazení pro službu Azure App Service
[Aplikační služba Azure](http://go.microsoft.com/fwlink/?LinkId=529714) podporuje dva typy přihlašovací údaje pro [místní nasazení Git](app-service-deploy-local-git.md) a [nasazení FTP/S](app-service-deploy-ftp.md). To však nejsou stejné, jako přihlašovacích údajů Azure Active Directory.

* **Přihlašovací údaje individuální**: jednu sadu přihlašovacích údajů pro celý účet Azure. Slouží k nasazení do služby App Service pro libovolnou aplikaci v žádné předplatné, který má oprávnění k přístupu k účtu Azure. Jedná se o výchozí sadu přihlašovacích údajů, které nakonfigurujete v **App Services** > **&lt;app_name >** > **přihlašovacíúdajenasazení**. Je také výchozí sada, která se zobrazí v grafickém uživatelském rozhraní portálu (například **přehled** a **vlastnosti** vaší aplikace [prostředků stránky](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Při delegování přístupu k prostředkům Azure prostřednictvím na základě řízení přístupu Role (RBAC) nebo spolusprávce oprávnění každého Azure uživatele, který obdrží přístup k aplikaci můžete použít jejich osobní údaje individuální, dokud je odvolat přístup. Tyto přihlašovací údaje nasazení by neměly sdílet s jinými uživateli Azure.
    >
    >

* **Přihlašovací údaje úrovni aplikace**: jednu sadu přihlašovacích údajů pro každou aplikaci. Slouží k nasazení této aplikace jenom. Přihlašovací údaje pro každou aplikaci je generován automaticky při vytváření aplikace a je nalezena v aplikace profilu publikování. Nelze ručně nakonfigurujte pověření, ale můžete je obnovit pro aplikaci, kdykoli.

    > [!NOTE]
    > Aby bylo možné poskytnout někdo přístup pro tyto přihlašovací údaje prostřednictvím na základě řízení přístupu Role (RBAC), budete muset udělat, je Přispěvatel nebo vyšší na webové aplikace. Čtečky není povoleno publikovat a proto nemá přístup k jejich přihlašovacích údajů.
    >
    >

## <a name="userscope"></a>Nastavit a resetovat přihlašovací údaje uživatele

Přihlašovací údaje uživatele můžete nakonfigurovat v jakékoli aplikaci [prostředků stránky](../azure-resource-manager/resource-group-portal.md#manage-resources). Bez ohledu na to v aplikaci, kterou konfigurujete tato pověření, platí pro všechny aplikace a pro všechna předplatná v účtu Azure. 

Konfigurace přihlašovacích údajů uživatele:

1. V [portál Azure](https://portal.azure.com), klikněte na aplikační služby >  **&lt;any_app >** > **přihlašovací údaje nasazení**.

    > [!NOTE]
    > Na portálu musí mít aspoň jednu aplikaci, než se dostanete k stránce přihlašovací údaje nasazení. Nicméně s [rozhraní příkazového řádku Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set), můžete nakonfigurovat přihlašovací údaje uživatele bez stávající aplikace.

2. Nakonfigurujte uživatelské jméno a heslo a potom klikněte na **Uložit**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Jakmile jednou nastavíte přihlašovací údaje nasazení, můžete najít *Git* nasazení uživatelské jméno ve vaší aplikaci **přehled**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

a *FTP* nasazení uživatelské jméno ve vaší aplikaci **vlastnosti**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nezobrazuje heslo individuální nasazení. Pokud zapomenete heslo, nelze jej načíst. Však mohou obnovit svoje přihlašovací údaje podle kroků v této části.
>
>  

## <a name="appscope"></a>Získání a resetovat přihlašovací údaje úrovni aplikace
Pro každou aplikaci ve službě App Service svoje přihlašovací údaje na úrovni aplikace jsou uložené v souboru XML profilu publikování.

Pokud chcete získat přihlašovací údaje úrovni aplikace:

1. V [portál Azure](https://portal.azure.com), klikněte na aplikační služby >  **&lt;any_app >** > **přehled**.

2. Klikněte na tlačítko **... Další** > **profilu publikování Get**, a stažení spuštění. Soubor PublishSettings.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Otevřete. Soubor PublishSettings a najděte `<publishProfile>` značky s atributem `publishMethod="FTP"`. Poté, získat přístup k jeho `userName` a `password` atributy.
Jedná se o pověření úrovni aplikace.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Podobně jako přihlašovací údaje uživatele, uživatelské jméno FTP nasazení je ve formátu `<app_name>\<username>`, a uživatelským jménem nasazení Git je právě `<username>` bez předchozím `<app_name>\`.

Se resetovat přihlašovací údaje úrovni aplikace:

1. V [portál Azure](https://portal.azure.com), klikněte na aplikační služby >  **&lt;any_app >** > **přehled**.

2. Klikněte na tlačítko **... Další** > **profil publikování se resetování**. Klikněte na tlačítko **Ano** potvrďte vynulování.

    Resetování akce zruší všechny dříve staženy. Soubory PublishSettings.

## <a name="next-steps"></a>Další postup

Zjistěte, jak používat tyto přihlašovací údaje k nasazení aplikace z [místní Git](app-service-deploy-local-git.md) nebo pomocí [FTP/S](app-service-deploy-ftp.md).
