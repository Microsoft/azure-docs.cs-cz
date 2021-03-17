---
title: Nejčastější dotazy k nasazení – Azure App Service | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se nasazení Web Apps funkce Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080469"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy k Web Apps v Azure

V tomto článku najdete odpovědi na nejčastější dotazy týkající se problémů s nasazením [funkce Web Apps Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Jenom Začínáme s App Service Web Apps. Návody publikovat můj kód?

Tady je několik možností pro publikování kódu webové aplikace:

*   Nasazení pomocí sady Visual Studio. Máte-li řešení sady Visual Studio, klikněte pravým tlačítkem myši na projekt webové aplikace a vyberte možnost **publikovat**.
*   Nasazení pomocí klienta FTP. V Azure Portal stáhněte profil publikování pro webovou aplikaci, do které chcete kód nasadit. Pak nahrajte soubory do \site\wwwroot pomocí stejných přihlašovacích údajů k FTP profilu publikování.

Další informace najdete v tématu [nasazení aplikace do App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Při pokusu o nasazení ze sady Visual Studio se zobrazí chybová zpráva. Návody vyřešit tuto chybu?

Pokud se zobrazí následující zpráva, možná použijete starší verzi sady SDK: "Chyba při nasazení pro prostředek" YourResourceName "ve skupině prostředků" YourResourceGroup ": MissingRegistrationForLocation: předplatné není zaregistrované pro typ prostředku ' součásti ' v umístění ' Střed USA '. K tomuto poskytovateli se znovu zaregistrujte, aby měl přístup k tomuto umístění. " 

Chcete-li tuto chybu vyřešit, upgradujte na [nejnovější sadu SDK](https://azure.microsoft.com/downloads/). Pokud se zobrazí tato zpráva a máte nejnovější sadu SDK, odešlete žádost o podporu.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Návody nasadit aplikaci ASP.NET ze sady Visual Studio do App Service?
<a id="deployasp"></a>

Kurz [Vytvoření první webové aplikace v ASP.NET v Azure během pěti minut](quickstart-dotnetcore.md) vám ukáže, jak nasadit webovou aplikaci v ASP.NET do webové aplikace v App Service pomocí sady Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jaké jsou různé typy přihlašovacích údajů pro nasazení?

App Service podporuje dva typy přihlašovacích údajů pro nasazení místního úložiště Git a FTP/S. Další informace o tom, jak nakonfigurovat přihlašovací údaje pro nasazení, najdete v tématu [konfigurace přihlašovacích údajů nasazení pro App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Jaká je struktura souborů nebo adresářů webové aplikace App Service?

Informace o struktuře souborů aplikace App Service najdete v tématu [struktura souborů v Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Návody vyřešit "Chyba FTP 550-na disku není dostatek místa" při pokusu o uložení souborů na server FTP?

Pokud se zobrazí tato zpráva, je možné, že máte spuštěnou kvótu disku v plánu služby pro vaši webovou aplikaci. Možná budete muset škálovat až na vyšší úroveň služby na základě potřeb místa na disku. Další informace o cenových plánech a omezeních prostředků najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Návody nastavit průběžné nasazování pro moji App Service webovou aplikaci?

Průběžné nasazování můžete nastavit z několika prostředků, včetně Azure DevOps, OneDrivu, GitHubu, BitBucket, Dropboxu a dalších úložišť Git. Tyto možnosti jsou k dispozici na portálu. [Průběžné nasazování do App Service](deploy-continuous-deployment.md) je užitečný kurz, který vysvětluje, jak nastavit průběžné nasazování.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Návody řešení potíží s průběžným nasazováním z GitHubu a Bitbucket?

Nápovědu k prozkoumání problémů s průběžným nasazováním z GitHubu nebo Bitbucket najdete v tématu [prozkoumání průběžného nasazování](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Nedaří se mi FTP na můj web a publikovat můj kód. Návody vyřešit tento problém?

Řešení problémů FTP:

1. Ověřte, že zadáváte správný název a přihlašovací údaje hostitele. Podrobné informace o různých typech přihlašovacích údajů a způsobu jejich použití najdete v tématu [přihlašovací údaje pro nasazení](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Ověřte, že brána firewall neblokuje porty FTP. Porty by měly mít tato nastavení:
    * Port připojení řízení FTP: 21
    * Port pro připojení dat FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Návody publikovat můj kód do App Service?

Rychlý Start Azure je navržený tak, aby vám usnadnil nasazení aplikace pomocí zásobníku nasazení a metody podle vašeho výběru. Pokud chcete používat rychlý Start, klikněte v Azure Portal do služby App Service v části **nasazení**vyberte **rychlý Start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Proč se moje aplikace někdy po nasazení do App Service restartuje?

Další informace o okolnostech, za kterých může nasazení aplikace způsobit restart, najdete v tématu [problémy s nasazením a za běhu](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Jak popisuje článek, App Service nasadí soubory do složky Wwwroot. Nikdy přímo nerestartuje vaši aplikaci.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Jak integrovat kód Azure DevOps se službou App Service?

Pro použití průběžného nasazování s Azure DevOps máte dvě možnosti:

*   Použijte projekt Git. Připojení prostřednictvím App Service pomocí centra nasazení.
*   Použijte projekt Správa verzí Team Foundation (TFVC). Nasazení pomocí agenta sestavení pro App Service.

Průběžné nasazování kódu pro obě tyto možnosti závisí na stávajících pracovních postupech pro vývojáře a postupech vrácení se změnami. Další informace najdete v těchto článcích: 

*   [Implementace průběžného nasazování aplikace na web Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Nastavení organizace Azure DevOps, aby ji bylo možné nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Návody k nasazení aplikace do App Service použít FTP nebo FTPS?

Informace o tom, jak pomocí FTP nebo FTPS nasadit webovou aplikaci do App Service, najdete v tématu [nasazení aplikace pro App Service pomocí FTP/S](deploy-ftp.md).
