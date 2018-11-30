---
title: Nakonfigurujte přihlašovací údaje nasazení pro službu Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak použít přihlašovací údaje pro nasazení služby Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.openlocfilehash: a04057023308087adbf449b8f91af6a473c11c1e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312091"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Nakonfigurujte přihlašovací údaje nasazení pro službu Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) podporuje dva typy přihlašovacích údajů pro [místní nasazení přes Git](app-service-deploy-local-git.md) a [nasazení FTP/S](app-service-deploy-ftp.md). Se nejedná stejná jako přihlašovacích údajů Azure Active Directory.

* **Přihlašovací údaje na úrovni uživatele**: jednu sadu přihlašovacích údajů pro celý účet Azure. Slouží k nasazení do služby App Service pro libovolnou aplikaci, v libovolné předplatné, který má oprávnění pro přístup k účtu Azure. Je výchozí skupinu, která se zobrazí v grafickém uživatelském rozhraní portálu (například **přehled** a **vlastnosti** z aplikace [stránka s materiály pro](../azure-resource-manager/resource-group-portal.md#manage-resources)). Když uživateli je udělen přístup k aplikaci prostřednictvím řízení přístupu na základě Role (RBAC) nebo oprávnění coadmin, tohoto uživatele můžete používat své vlastní přihlašovací údaje na úrovni uživatele, dokud odvolat přístup. Nesdílejte tyto přihlašovací údaje s jinými uživateli Azure.

* **Přihlašovací údaje na úrovni aplikace**: jednu sadu přihlašovacích údajů pro každou aplikaci. Slouží k nasazení do této aplikace jenom. Přihlašovací údaje pro každé aplikaci, která jsou generovány při vytváření aplikací. Nejde nakonfigurovat ručně, ale může resetovat kdykoli. Tento uživatel musí být uživatel chce být udělen přístup k přihlašovací údaje na úrovni aplikace prostřednictvím (RBAC), Přispěvatel nebo vyšší na aplikaci. Čtenáři není povoleno publikovat a nelze získat přístup k přihlašovací údaje.

## <a name="userscope"></a>Nastavení a resetovat přihlašovací údaje na úrovni uživatele

Nakonfigurujete své přihlašovací údaje uživatele ve všech aplikacích [stránka s materiály pro](../azure-resource-manager/resource-group-portal.md#manage-resources). Bez ohledu na to v kterou aplikaci konfigurujete tyto přihlašovací údaje, se vztahuje na všechny aplikace a pro všechna předplatná v účtu Azure. 

Postup konfigurace vaše přihlašovací údaje na úrovni uživatele:

1. V [webu Azure portal](https://portal.azure.com), v levé nabídce klikněte na tlačítko **App Services** > **&lt;any_app >** > **nasazení System Center** > **přihlašovací údaje pro nasazení**.

    Na portálu musíte mít aspoň jednu aplikaci mohli získat přístup ke stránce přihlašovací údaje nasazení. Nicméně s [rozhraní příkazového řádku Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), můžete nakonfigurovat přihlašovací údaje na úrovni uživatele bez existující aplikace.

2. Klikněte na tlačítko **přihlašovací údaje uživatele**, konfiguraci, uživatelského jména a hesla a potom klikněte na tlačítko **přihlašovací údaje pro uložení**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Jakmile jednou nastavíte přihlašovací údaje pro nasazení, můžete najít *Git* uživatelské jméno pro nasazení vaší aplikace **přehled**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

a *FTP* uživatelské jméno pro nasazení vaší aplikace **vlastnosti**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nezobrazuje heslo nasazení na úrovni uživatele. Pokud zapomenete heslo, můžete resetovat svoje přihlašovací údaje podle pokynů v této části.
>
>  

## <a name="appscope"></a>Získat a obnovit přihlašovací údaje na úrovni aplikace
Pokud chcete získat přihlašovací údaje úrovni aplikace:

1. V [webu Azure portal](https://portal.azure.com), v levé nabídce klikněte na tlačítko **App Services** > **&lt;any_app >** > **nasazení System Center** > **přihlašovací údaje pro nasazení**.

2. Klikněte na tlačítko **aplikace pověření**a klikněte na tlačítko **kopírování** odkaz na kopírování uživatelské jméno nebo heslo.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Resetovat přihlašovací údaje úrovni aplikace, klikněte na tlačítko **resetovat přihlašovací údaje** v dialogovém okně stejné.

## <a name="next-steps"></a>Další postup

Zjistěte, jak pomocí těchto přihlašovacích údajů nasadíte aplikaci z [místního Gitu](app-service-deploy-local-git.md) nebo pomocí [FTP/S](app-service-deploy-ftp.md).
