---
title: Přihlašovací údaje nasazení služby Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak použít přihlašovací údaje pro nasazení služby Azure App Service.
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
ms.openlocfilehash: 79841887ce8413970cb7dff0ca0099d4e0745a68
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259305"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Nakonfigurujte přihlašovací údaje nasazení pro službu Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) podporuje dva typy přihlašovacích údajů pro [místní nasazení přes Git](app-service-deploy-local-git.md) a [nasazení FTP/S](app-service-deploy-ftp.md). Se nejedná stejná jako přihlašovacích údajů Azure Active Directory.

* **Přihlašovací údaje na úrovni uživatele**: jednu sadu přihlašovacích údajů pro celý účet Azure. Slouží k nasazení do služby App Service pro libovolnou aplikaci, v libovolné předplatné, který má oprávnění pro přístup k účtu Azure. Jedná se o výchozí sadu přihlašovacích údajů, které nakonfigurujete v **App Services** > **&lt;app_name >** > **přihlašovacíúdajenasazení**. Je také výchozí sadu, která se zobrazí v grafickém uživatelském rozhraní portálu (například **přehled** a **vlastnosti** vaší aplikace [stránka s materiály pro](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Při delegování přístupu k prostředkům Azure na základě řízení přístupu Role (RBAC) nebo spolupracujícího správce oprávnění každému Azure, která přijímá přístup k aplikaci můžete použít jeho osobní přihlašovací údaje na úrovni uživatele, dokud přístup odvolat. Tyto přihlašovací údaje pro nasazení by neměly sdílet s ostatními uživateli Azure.
    >
    >

* **Přihlašovací údaje na úrovni aplikace**: jednu sadu přihlašovacích údajů pro každou aplikaci. Slouží k nasazení do této aplikace jenom. Přihlašovací údaje pro každé aplikaci, která se automaticky vygeneruje při vytváření aplikací a je součástí aplikace profil publikování. Nelze ručně nakonfigurovat přihlašovací údaje, ale můžete je obnovit pro aplikaci můžete kdykoli.

    > [!NOTE]
    > Pokud chcete dát někomu přístup k tyto přihlašovací údaje prostřednictvím na základě řízení přístupu Role (RBAC), je třeba provést je Přispěvatel nebo vyšší na webovou aplikaci. Čtenáři není povoleno publikovat a proto nelze získat přístup k přihlašovací údaje.
    >
    >

## <a name="userscope"></a>Nastavení a resetovat přihlašovací údaje na úrovni uživatele

Nakonfigurujete své přihlašovací údaje uživatele ve všech aplikacích [stránka s materiály pro](../azure-resource-manager/resource-group-portal.md#manage-resources). Bez ohledu na to v kterou aplikaci konfigurujete tyto přihlašovací údaje, se vztahuje na všechny aplikace a pro všechna předplatná v účtu Azure. 

Postup konfigurace vaše přihlašovací údaje na úrovni uživatele:

1. V [webu Azure portal](https://portal.azure.com), klikněte na službu App Service >  **&lt;any_app >** > **přihlašovací údaje pro nasazení**.

    > [!NOTE]
    > Na portálu musíte mít aspoň jednu aplikaci mohli získat přístup ke stránce přihlašovací údaje nasazení. Nicméně s [rozhraní příkazového řádku Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), můžete nakonfigurovat přihlašovací údaje na úrovni uživatele bez existující aplikace.

2. Konfigurace uživatelského jména a hesla a potom klikněte na tlačítko **Uložit**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Jakmile jednou nastavíte přihlašovací údaje pro nasazení, můžete najít *Git* uživatelské jméno pro nasazení vaší aplikace **přehled**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

a *FTP* uživatelské jméno pro nasazení vaší aplikace **vlastnosti**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nezobrazuje heslo nasazení na úrovni uživatele. Pokud zapomenete heslo, nejde ho načíst. Ale můžou resetovat svoje přihlašovací údaje podle pokynů v této části.
>
>  

## <a name="appscope"></a>Získat a obnovit přihlašovací údaje na úrovni aplikace
Pro každou aplikaci ve službě App Service jsou uložené jeho přihlašovací údaje na úrovni aplikace v souboru XML profilu publikování.

Pokud chcete získat přihlašovací údaje úrovni aplikace:

1. V [webu Azure portal](https://portal.azure.com), klikněte na službu App Service >  **&lt;any_app >** > **přehled**.

2. Klikněte na tlačítko **... Další** > **získat profil publikování**, a spustí stahování pro. Soubor PublishSettings.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Otevřít. Soubor PublishSettings a najít `<publishProfile>` značku s atributem `publishMethod="FTP"`. Potom získejte její `userName` a `password` atributy.
Toto jsou přihlašovací údaje úrovni aplikace.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Podobně jako přihlašovací údaje na úrovni uživatele, uživatelské jméno pro nasazení FTP je ve formátu `<app_name>\<username>`, a uživatelské jméno pro nasazení Git je právě `<username>` bez předchozí `<app_name>\`.

Resetovat přihlašovací údaje úrovni aplikace:

1. V [webu Azure portal](https://portal.azure.com), klikněte na službu App Service >  **&lt;any_app >** > **přehled**.

2. Klikněte na tlačítko **... Další** > **obnovit profil publikování**. Klikněte na tlačítko **Ano** potvrďte obnovení.

    Akce reset zneplatní všechny dříve staženy. Soubory PublishSettings.

## <a name="next-steps"></a>Další postup

Zjistěte, jak pomocí těchto přihlašovacích údajů nasadíte aplikaci z [místního Gitu](app-service-deploy-local-git.md) nebo pomocí [FTP/S](app-service-deploy-ftp.md).
