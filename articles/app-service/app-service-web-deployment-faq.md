---
title: Nejčastější dotazy k nasazení pro Azure web apps | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy týkající se nasazení pro funkci Web Apps služby Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: b38291df576d1dbd180f741659dbc379ca006d8e
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748438"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy k nasazení pro službu Web Apps v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o problémech nasazení pro [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Můžu jsem právě začínáte s App Service web apps. Jak můžu publikovat vlastní kód?

Tady jsou některé možnosti publikování kódu webové aplikace:

*   Nasazení pomocí sady Visual Studio. Pokud máte řešení sady Visual Studio, klikněte pravým tlačítkem na projekt webové aplikace a pak vyberte **publikovat**.
*   Nasazení pomocí klienta FTP. Na portálu Azure stáhnete profil publikování pro webovou aplikaci, kterou chcete nasadit váš kód. Nakonec odešlete soubory \site\wwwroot pomocí stejných přihlašovacích údajů Publikovat profil FTP.

Další informace najdete v tématu [nasazení aplikace do služby App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Při pokusu nasadit ze sady Visual Studio se zobrazit chybová zpráva. Jak to můžu vyřešit?

Pokud se zobrazí následující zpráva, pravděpodobně používáte starší verzi sady SDK: "při nasazení pro prostředek"YourResourceName"ve skupině prostředků 'YourResourceGroup' došlo k chybě: MissingRegistrationForLocation: předplatné není zaregistrované pro Typ prostředku "součástí" umístění "USA". Přeregistrujte prosím tohoto zprostředkovatele. Pokud chcete mít přístup do tohoto umístění." 

Pokud chcete tuto chybu vyřešit, upgradujte [nejnovější sadu SDK](https://azure.microsoft.com/downloads/). Pokud se zobrazí tato zpráva a máte nejnovější sadu SDK, odešlete žádost o podporu.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Nasazení aplikace ASP.NET ze sady Visual Studio do služby App Service
<a id="deployasp"></a>

Tento kurz [vytvoření vaší první webové aplikace ASP.NET v Azure během pěti minut](app-service-web-get-started-dotnet.md) ukazuje, jak nasadit webovou aplikaci ASP.NET do webové aplikace ve službě App Service pomocí sady Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jaké jsou různé druhy přihlašovací údaje pro nasazení?

App Service podporuje dva typy přihlašovacích údajů pro místní nasazení přes Git a FTP/S nasazení. Další informace o tom, jak nakonfigurovat přihlašovací údaje pro nasazení najdete v tématu [nakonfigurovat přihlašovací údaje nasazení pro službu App Service](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Co je soubor nebo adresář strukturu Moje webová aplikace App Service?

Informace o struktuře souborů z aplikace app Service najdete v tématu [struktura souboru v Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak můžu vyřešit, "FTP chyba 550 – tady není dostatek místa na disku" při pokusu serveru FTP soubory?

Pokud se zobrazí tato zpráva, je pravděpodobné, že používáte do disková kvóta v plánu služby pro vaši webovou aplikaci. Možná budete muset vertikálně navýšit kapacitu na vyšší úroveň služby na základě vašich potřeb místa na disku. Další informace o cenách plány a omezení prostředků, najdete v části [ceny služeb App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak nastavit průběžné nasazování pro svou webovou aplikaci služby App Service?

Můžete nastavit průběžné nasazování z několika zdrojů, včetně Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox a další úložiště Git. Tyto možnosti jsou dostupné na portálu. [Průběžné nasazování do služby App Service](app-service-continuous-deployment.md) je užitečný kurz, který vysvětluje, jak nastavit průběžné nasazování.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak se řešení potíží s průběžným nasazováním z Githubu nebo Bitbucketu?

Pomoc při zjišťování problémů s průběžným nasazováním z Githubu nebo Bitbucketu, naleznete v tématu [zkoumání průběžné nasazování](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Můžu nelze FTP na svém webu a publikování vlastní kód. Jak to můžu vyřešit?

Chcete-li vyřešit problémy s FTP:

1. Ověřte, že zadáváte správný název hostitele a přihlašovací údaje. Podrobné informace o různých typů přihlašovacích údajů a jejich použití naleznete v tématu [přihlašovací údaje pro nasazení](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Ověřte, že FTP porty nejsou blokované bránou firewall. Porty by měl mít tato nastavení:
    * Port připojení řízení FTP: 21
    * Port připojení dat FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak můžu publikovat svůj kód do služby App Service?

Rychlý start Azure usnadňuje nasazení vaší aplikace pomocí balíčku nasazení a metody podle vašeho výběru. Pokud chcete pomocí rychlého startu, na webu Azure Portal, přejděte na službu app service, v části **nasazení**vyberte **rychlý Start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Proč Moje aplikace někdy restartování po nasazení do služby App Service?

Další informace o okolnostech, za kterých nasazení aplikace může vést k restartování, najdete v článku [nasazení vs. problémy za běhu](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Tento článek popisuje, jak služby App Service nasadí soubory do složky wwwroot. Nikdy přímo restartování vaší aplikace.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Jak integrovat Azure DevOps kódu pomocí služby App Service?

Máte dvě možnosti pro průběžné nasazování pomocí Azure DevOps:

*   Použití projektu Git. Připojení pomocí služby App Service s použitím možnosti nasazení pro tohoto úložiště.
*   Použití projektu Team Foundation verze ovládacího prvku (TFVC). Nasazení pomocí agenta sestavení pro službu App Service.

Nasazení průběžné kódu pro obě tyto možnosti závisí na stávajících pracovních postupů pro vývojáře a postupy k vrácení se změnami. Další informace najdete v těchto článcích: 

*   [Implementaci nepřetržité nasazení vaší aplikace na web Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Nastavení Azure DevOps organizace, můžete nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak použít FTP a FTPS nasadit aplikaci do služby App Service?

Informace o nasazení webové aplikace do služby App Service pomocí FTP a FTPS, naleznete v tématu [nasazení aplikace do služby App Service pomocí FTP/S](app-service-deploy-ftp.md).
