---
title: Časté otázky k nasazení – služba Azure App Service | Dokumenty společnosti Microsoft
description: Získejte odpovědi na nejčastější dotazy týkající se nasazení pro funkci Webové aplikace služby Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671692"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy k Web Apps v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se problémů s nasazením [funkce Webové aplikace služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Právě začínám s webovými aplikacemi App Service. Jak mohu publikovat svůj kód?

Tady jsou některé možnosti publikování kódu webové aplikace:

*   Nasazení pomocí sady Visual Studio. Pokud máte řešení Visual Studio, klikněte pravým tlačítkem myši na projekt webové aplikace a potom vyberte **publikovat**.
*   Nasazení pomocí klienta FTP. Na webu Azure Portal si stáhněte profil publikování pro webovou aplikaci, do které chcete nasadit svůj kód. Potom nahrajte soubory do \site\wwwroot pomocí stejných pověření FTP profilu publikování.

Další informace najdete [v tématu Nasazení aplikace do služby App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Při pokusu o nasazení z sady Visual Studio se zobrazí chybová zpráva. Jak lze tuto chybu vyřešit?

Pokud se zobrazí následující zpráva, je možné, že používáte starší verzi sady SDK: "Chyba během nasazení prostředku YourResourceName' ve skupině prostředků YourResourceGroup: MissingRegistrationForLocation: Předplatné není registrováno pro "komponenty" v lokaci "Střední USA". Znovu zaregistrujte tohoto zprostředkovatele, abyste měli přístup k tomuto umístění." 

Chcete-li tuto chybu vyřešit, upgradujte na [nejnovější sadu SDK](https://azure.microsoft.com/downloads/). Pokud se zobrazí tato zpráva a máte nejnovější sadu SDK, odešlete žádost o podporu.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Jak nasadím aplikaci ASP.NET z Visual Studia do služby App Service?
<a id="deployasp"></a>

Kurz [Vytvoření první ASP.NET webové aplikace v Azure za pět minut](app-service-web-get-started-dotnet.md) ukazuje, jak nasadit ASP.NET webovou aplikaci do webové aplikace ve službě App Service pomocí Visual Studia.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jaké jsou různé typy přihlašovacích údajů pro nasazení?

Služba App Service podporuje dva typy přihlašovacích údajů pro místní nasazení Gitu a nasazení FTP/S. Další informace o konfiguraci přihlašovacích údajů k nasazení naleznete v [tématu Konfigurace přihlašovacích údajů pro službu App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Jaká je struktura souborů nebo adresářů webové aplikace App Service?

Informace o struktuře souborů aplikace App Service najdete [v tématu Struktura souborů v Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak lze vyřešit "FTP Error 550 - Není dostatek místa na disku" při pokusu o FTP mé soubory?

Pokud se zobrazí tato zpráva, je pravděpodobné, že v plánu služeb pro webovou aplikaci narážíte na diskovou kvótu. Možná budete muset vertikálně navýšit kapacitu na vyšší úroveň služby na základě potřeb místa na disku. Další informace o cenových plánech a limitech prostředků najdete v [tématu Ceny služby App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak nastavím průběžné nasazování webové aplikace App Service?

Můžete nastavit průběžné nasazení z několika prostředků, včetně Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox a dalších úložišť Git. Tyto možnosti jsou k dispozici na portálu. [Průběžné nasazování do služby App Service](deploy-continuous-deployment.md) je užitečný kurz, který vysvětluje, jak nastavit průběžné nasazování.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak řeším problémy s nepřetržitým nasazením z GitHubu a Bitbucketu?

Pomoc s vyšetřováním problémů s nepřetržitým nasazením z GitHubu nebo Bitbucketu najdete [v tématu Zkoumání průběžného nasazení](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Nemohu FTP na mých stránkách a publikovat svůj kód. Jak tento problém vyřeším?

Řešení problémů s ftp:

1. Ověřte, zda zadáváte správný název hostitele a pověření. Podrobné informace o různých typech pověření a jejich použití naleznete v [tématu Deployment credentials](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Ověřte, zda porty FTP nejsou blokovány bránou firewall. Porty by měly mít tato nastavení:
    * Port připojení řízení FTP: 21
    * Datový port FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak můžu publikovat svůj kód do služby App Service?

Azure Quickstart je navržený tak, aby vám pomohl nasadit vaši aplikaci pomocí zásobníku nasazení a metody podle vašeho výběru. Pokud chcete použít úvodní příručku, přejděte na portál Azure na svou aplikační službu v části **Nasazení**vyberte **Rychlý start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Proč se moje aplikace někdy restartuje po nasazení do služby App Service?

Informace o okolnostech, za kterých může nasazení aplikace vést k restartování, naleznete v [tématu Nasazení vs. problémy s zaběhu .](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts") Jak popisuje článek, Služba App Service nasazuje soubory do složky wwwroot. Nikdy přímo nerestartuje aplikaci.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Jak integrovat kód Azure DevOps se službou App Service?

Máte dvě možnosti pro použití průběžného nasazení s Azure DevOps:

*   Použijte projekt Git. Připojte se prostřednictvím služby App Service pomocí Centra pro nasazení.
*   Použijte projekt Správy verzí teamfoundation (TFVC). Nasazení pomocí agenta sestavení pro službu App Service.

Průběžné nasazení kódu pro obě tyto možnosti závisí na existujících pracovních postupech pro vývojáře a postupech vrácení se změnami. Další informace najdete v těchto článcích: 

*   [Implementace průběžného nasazování aplikace na web Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Nastavení organizace Azure DevOps, aby se mohla nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak se pomocí FTP nebo FTPS nasadím do služby App Service?

Informace o nasazení webové aplikace do služby App Service pomocí FTP nebo FTPS najdete v tématu [Nasazení aplikace do služby App Service pomocí FTP/S](deploy-ftp.md).
