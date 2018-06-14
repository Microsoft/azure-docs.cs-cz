---
title: Nejčastější dotazy k nasazení pro webové aplikace Azure | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se nasazení pro funkce Web Apps služby Azure App Service.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 54c99cc9dde6a9706c75401dd75b98f8f5de7cec
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068066"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy k nasazení pro webové aplikace v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o problémy při nasazení pro [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>I se právě Začínáme s webovými aplikacemi App Service. Jak lze publikovat vlastní kód?

Zde jsou některé možnosti pro publikování kódu webové aplikace:

*   Nasazení pomocí sady Visual Studio. Pokud máte řešení sady Visual Studio, klikněte pravým tlačítkem na projekt webové aplikace a pak vyberte **publikovat**.
*   Nasazení pomocí klienta FTP. Na portálu Azure stáhnete profil publikování pro webové aplikace, kterou chcete nasadit svůj kód. Pak odešlete soubory do \site\wwwroot pomocí stejných přihlašovacích údajů Publikovat profil FTP.

Další informace najdete v tématu [nasazení vaší aplikace do služby App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>I při pokusu o nasazení ze sady Visual Studio zobrazí chybová zpráva. Jak to lze vyřešit?

Pokud se zobrazí následující zprávu, pravděpodobně používáte starší verze sady SDK: "při nasazení pro prostředek"YourResourceName"ve skupině prostředků 'YourResourceGroup' došlo k chybě: MissingRegistrationForLocation: předplatné není zaregistrované pro typ prostředku"součástmi"v umístění, střed USA". Zkuste se znovu zaregistrovat pro tohoto zprostředkovatele pro přístup do tohoto umístění." 

Chcete-li tuto chybu vyřešit, upgradujte na [nejnovější SDK](https://azure.microsoft.com/downloads/). Pokud se zobrazí tato zpráva a máte na nejnovější SDK, odešlete žádost o podporu.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Nasazení aplikace ASP.NET v sadě Visual Studio do služby App Service
<a id="deployasp"></a>

Tento kurz [vytvořte první webové aplikace ASP.NET v Azure v pěti minutách](app-service-web-get-started-dotnet.md) ukazuje, jak nasadit webovou aplikaci ASP.NET do webové aplikace ve službě App Service pomocí Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jaké jsou různé typy přihlašovací údaje pro nasazení?

Služby App Service podporuje dva typy přihlašovací údaje pro místní nasazením Git a FTP/S. Další informace o tom, jak nakonfigurovat přihlašovací údaje pro nasazení najdete v tématu [nakonfigurovat přihlašovací údaje nasazení pro službu App Service](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Co je soubor nebo adresář strukturu webová aplikace služby App Service?

Informace o struktuře souborů aplikace služby App Service najdete v tématu [struktura souboru v Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak lze vyřešit, "Chyba FTP 550 - zde není dostatek místa na disku" při Moje soubory FTP?

Pokud se zobrazí tato zpráva, je pravděpodobné, že používáte do kvóty disku v plánu služby pro webovou aplikaci. Možná budete muset vertikálně navýšit kapacitu na vyšší úroveň služby, na základě potřeb místa na disku. Další informace o cenách plány a omezení prostředků najdete v tématu [služby App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak nastavit průběžné nasazování pro webovou aplikaci my služby App Service?

Můžete nastavit průběžné nasazování z několika zdrojů, včetně Visual Studio Team Services, OneDrive, Githubu, Bitbucket, Dropbox a další úložiště Git. Tyto možnosti jsou dostupné na portálu. [Průběžné nasazování do služby App Service](app-service-continuous-deployment.md) je užitečné kurz, který vysvětluje, jak nastavit průběžné nasazování.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak odstranit problémy s průběžné nasazování z Githubu a Bitbucket?

Pomoc příčin problémů s průběžné nasazování z webu GitHub nebo Bitbucket, najdete v tématu [příčin průběžné nasazování](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>I nelze FTP na mé lokality a publikování vlastní kód. Jak to lze vyřešit?

Chcete-li vyřešit problémy FTP:

1. Ověřte, že jste zadali správný hostitele název a pověření. Podrobné informace o různých typech přihlašovací údaje a jejich použití najdete v tématu [přihlašovací údaje nasazení](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Ověřte, že porty serveru FTP nejsou blokována bránou firewall. Porty musí mít tato nastavení:
    * Port připojení řízení FTP: 21
    * Port pro připojení FTP data: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak publikovat vlastní kód do služby App Service?

Rychlý start Azure slouží k vám pomůžou nasadit aplikace pomocí nasazení zásobníku a metoda podle svého výběru. Chcete-li použít startu na portálu Azure, přejděte na **nastavení** > **nasazení aplikace**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Proč aplikace my někdy restartovat po nasazení do služby App Service?

Další informace o podmínek, za kterých nasazení aplikace může způsobit restart najdete v tématu [nasazení oproti runtime problémy](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Jak popisuje článek, služby App Service nasadí soubory do složky wwwroot. Nikdy přímo restartuje vaší aplikace.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Jak integrovat kódu Visual Studio Team Services službou App Service?

Máte dvě možnosti pro průběžné nasazování pomocí Visual Studio Team Services:

*   Pomocí Git projektu. Připojte prostřednictvím služby App Service pomocí možnosti nasazení pro tento úložišti.
*   Pomocí projektu Team Foundation verze ovládacího prvku (TFVC). Nasazení pomocí agenta sestavení pro službu App Service.

Nasazení průběžné kód pro obě tyto možnosti závisí na stávajících pracovními postupy developer a postupy vrácení se změnami. Další informace najdete v těchto článcích: 

*   [Implementace průběžné nasazování vaší aplikace na web Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Nastavit účet Visual Studio Team Services, můžete nasadit do webové aplikace](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak používat protokol FTP nebo FTPS k nasazení aplikace do služby App Service?

Informace o použití FTP a FTPS k nasazení vaší webové aplikace do služby App Service najdete v tématu [nasazení vaší aplikace do služby App Service pomocí FTP nebo S](app-service-deploy-ftp.md).
