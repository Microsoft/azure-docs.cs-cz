---
title: Kurz – přidání ověřování do webové aplikace v Azure App Service | Azure
description: V tomto kurzu se naučíte, jak povolit ověřování a autorizaci webové aplikace běžící na Azure App Service. Omezte přístup k webové aplikaci uživatelům ve vaší organizaci.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: b17cb6906a37d2cab4383fac18400b35dc8adb2f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223171"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Kurz: Přidání ověřování do webové aplikace běžící na Azure App Service

Naučte se, jak povolit ověřování pro webovou aplikaci běžící na Azure App Service a omezit přístup uživatelům ve vaší organizaci.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagram, který zobrazuje přihlašování uživatele" border="false":::

App Service poskytuje integrovanou podporu ověřování a autorizace, takže se můžete přihlašovat uživatelům a přistupovat k datům tak, že ve webové aplikaci napíšete minimální nebo žádný kód. Použití modulu ověřování/autorizace App Service není vyžadováno, ale pomáhá zjednodušit ověřování a autorizaci pro vaši aplikaci. Tento článek ukazuje, jak zabezpečit webovou aplikaci pomocí App Serviceho modulu ověřování/autorizace pomocí Azure Active Directory (Azure AD) jako poskytovatele identity.

Modul pro ověřování a autorizaci je povolený a nakonfigurovaný pomocí nastavení Azure Portal a aplikace. Nevyžadují se žádné sady SDK, konkrétní jazyky ani změny kódu aplikace. Podporuje se celá řada zprostředkovatelů identity, mezi které patří Azure AD, účet Microsoft, Facebook, Google a Twitter. Pokud je povolen modul ověřování/autorizace, předá před zpracováním kódem aplikace všechny příchozí požadavky HTTP. Další informace najdete v tématu [ověřování a autorizace v Azure App Service](overview-authentication-authorization.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Nakonfigurujte ověřování pro webovou aplikaci.
> * Omezte přístup k webové aplikaci uživatelům ve vaší organizaci.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Vytvoření a publikování webové aplikace v App Service

Pro tento kurz potřebujete webovou aplikaci nasazenou pro App Service. Můžete použít existující webovou aplikaci nebo můžete postupovat podle pokynů [ASP.NET Core rychlý Start](quickstart-dotnetcore.md) k vytvoření a publikování nové webové aplikace pro App Service.

Bez ohledu na to, jestli používáte existující webovou aplikaci, nebo vytvořte novou, poznamenejte si název webové aplikace a název skupiny prostředků, do které je webová aplikace nasazená. Tyto názvy budete potřebovat v rámci tohoto kurzu. 

## <a name="configure-authentication-and-authorization"></a>Konfigurace ověřování a autorizace

Nyní máte webovou aplikaci běžící na App Service. V dalším kroku povolíte ověřování a autorizaci pro webovou aplikaci. Službu Azure AD použijete jako zprostředkovatele identity. Další informace najdete v tématu [Konfigurace ověřování Azure AD pro vaši aplikaci App Service](configure-authentication-provider-aad.md).

V nabídce [Azure Portal](https://portal.azure.com) vyberte **skupiny prostředků**, nebo vyhledejte a vyberte **skupiny prostředků** z libovolné stránky.

V **skupiny prostředků** vyhledejte a vyberte skupinu prostředků. V **přehledu** vyberte stránku správy vaší aplikace.

:::image type="content" alt-text="Snímek obrazovky znázorňující výběr stránky správy vaší aplikace" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

V nabídce vlevo vaší aplikace vyberte **ověřování** a pak klikněte na **Přidat zprostředkovatele identity**.

Na stránce **Přidat poskytovatele identity** vyberte **Microsoft** jako **poskytovatele identity** pro přihlášení k identitám Microsoft a Azure AD.

V případě registračního  >  **typu aplikace** registrace aplikace **Vyberte vytvořit novou registraci aplikace**.

U   >  **podporovaných typů účtů** pro registraci aplikací vyberte **aktuální tenant – jeden tenant**.

V části **nastavení ověřování App Service** ponechte možnost **ověřování** nastavenou na **vyžadovat ověření** a **neověřené požadavky** nastavené na **http 302 Nalezeno přesměrování: doporučuje se pro weby**.

V dolní části stránky **Přidat poskytovatele identity** klikněte na **Přidat** , aby se povolilo ověřování pro webovou aplikaci.

:::image type="content" alt-text="Snímek obrazovky, který ukazuje konfiguraci ověřování." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Teď máte aplikaci, která je zabezpečená App Service ověřováním a autorizací.

## <a name="verify-limited-access-to-the-web-app"></a>Ověření omezeného přístupu k webové aplikaci

Když jste povolili modul ověřování a autorizace App Service, vytvořila se registrace aplikace v tenantovi Azure AD. Registrace aplikace má stejný zobrazovaný název jako vaše webová aplikace. Chcete-li kontrolovat nastavení, vyberte v nabídce portál možnost **Azure Active Directory** a vyberte možnost **Registrace aplikací**. Vyberte registraci aplikace, kterou jste vytvořili. V přehledu ověřte, zda je u **podporovaných typů účtů** nastavena **pouze moje organizace**.

:::image type="content" alt-text="Snímek obrazovky, který zobrazuje ověření přístupu" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Pokud chcete ověřit, jestli je přístup k aplikaci omezený na uživatele ve vaší organizaci, spusťte v anonymním nebo privátním režimu prohlížeč a přejděte na `https://<app-name>.azurewebsites.net` . Měli byste směřovat na zabezpečenou přihlašovací stránku a ověřit tak, že neověření uživatelé nemají povolený přístup k lokalitě. Přihlaste se jako uživatel ve vaší organizaci, abyste získali přístup k webu. Můžete také spustit nový prohlížeč a zkusit se přihlásit pomocí osobního účtu, abyste ověřili, že uživatelé mimo organizaci nemají přístup.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s tímto kurzem hotovi a už nepotřebujete webovou aplikaci ani související prostředky, [vyčistěte prostředky, které jste vytvořili](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Nakonfigurujte ověřování pro webovou aplikaci.
> * Omezte přístup k webové aplikaci uživatelům ve vaší organizaci.

> [!div class="nextstepaction"]
> [Služba App Service přistupuje k úložišti](scenario-secure-app-access-storage.md)
