---
title: Operační systém a opravy chyb za běhu v Azure App Service | Microsoft Docs
description: Popisuje, jak Azure App Service aktualizace operačního systému a moduly runtime a jak můžete získat aktualizovat oznámení.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.openlocfilehash: 0626b958a9b822569f4d3b6d27f3395bed853174
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030049"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Operační systém a opravy chyb za běhu v Azure App Service

Tento článek ukazuje, jak získat určité verze informace týkající se operačního systému nebo software v [služby App Service](app-service-web-overview.md). 

App Service je – jako služba platformy, což znamená, že operační systém a aplikace zásobníku jsou pro vás spravuje služba Azure; můžete spravovat jenom aplikace a jeho data. Větší kontrolu nad operačním systémem a aplikace je k dispozici zásobníku v [virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/). Si uvědomit je však pro vás užitečné jako uživatel s App Service vědět, další informace, jako například:

-   Jak a kdy jsou aktualizace operačního systému použít?
-   Jak je důležité zranitelná (například den nasazení) opravit služby App Service?
-   Jaké verze operačního systému a runtime se spouští vaše aplikace?

Z bezpečnostních důvodů nejsou publikovány určité podrobné informace o zabezpečení. Však článek cílem je zmírnit riziko z hlediska podle maximální využití průhlednost na proces a jak můžete vždy aktuální na oznámení související se zabezpečením nebo aktualizace modulu runtime.

## <a name="how-and-when-are-os-updates-applied"></a>Jak a kdy jsou aktualizace operačního systému použít?

Spravuje Azure opravy operačního systému na dvě úrovně, fyzických serverů a hostované virtuální počítače (VM) používající prostředky služby App Service. Obě jsou každý měsíc, aktualizovat, který nastavuje zarovnání měsíční [Patch Tuesday](https://technet.microsoft.com/security/bulletins.aspx) plán. Tyto aktualizace se použijí automaticky, tak, aby zaručuje vysokou dostupnost služby smlouvy SLA systému Azure. 

Podrobné informace o tom, jak se aktualizace, najdete v části [Demystifying magic za aktualizacím operačního systému aplikace služby](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak Azure nakládat s významné ohrožení zabezpečení?

Při závažné ohrožení zabezpečení vyžadují okamžitou opravy, jako například [ohrožení zabezpečení den nasazení](https://wikipedia.org/wiki/Zero-day_(computing)), důležité aktualizace jsou zpracovávány případ od případu.

Udržujte aktuální stav s oznámení kritické zabezpečení v Azure, navštivte stránky [Azure Security Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Když jsou podporované jazykové moduly runtime aktualizovat, přidat nebo zastaralé?

Nové stabilní verze podporované jazykové moduly runtime (hlavní, malé nebo opravy) jsou pravidelně přidané do instancí služby App Service. Některé aktualizace přepsat existující instalaci, zatímco jiné jsou nainstalovány node souběžně s existující verze. Přepsat instalace znamená, že vaše aplikace automaticky běží na aktualizované modulu runtime. Souběžně sdílená instalace znamená, že je nutné ručně migrovat aplikace, abyste mohli využívat nové verze modulu runtime. Další informace najdete v jednom z témata.

Tady jsou oznámeno runtime aktualizace a vyřazení:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informace v tomto poli se vztahují na moduly runtime jazyka, které jsou součástí aplikace služby App Service. Vlastní modul runtime, který nahrajete do služby App Service, například zůstává beze změny, není-li ručně upgradovat.
>
>

### <a name="new-patch-updates"></a>Nové aktualizace oprava

Oprava aktualizací na rozhraní .NET, PHP, Java SDK nebo verzi Tomcat nebo Jetty automaticky použity tak, že přepíše stávající instalace s novou verzí. Instalace aktualizací Oprava Node.js node souběžně s existující verze (podobně jako hlavní verze a podverze v další části). Nové Python oprava verze lze nainstalovat ručně prostřednictvím [lokality rozšíření](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), s integrovanou instalací Python vedle sebe.

### <a name="new-major-and-minor-versions"></a>Nový hlavní verze a podverze

Při přidání nové hlavní a vedlejší verzi je nainstalovaná node souběžně s existující verze. Aplikace můžete ručně upgradovat na novou verzi. Pokud jste nakonfigurovali verzi modulu runtime v konfiguračním souboru (například `web.config` a `package.json`), je třeba upgradovat stejným způsobem. Pokud jste použili aplikační službu nastavení konfigurace vaší verzi modulu runtime, můžete změnit v [portál Azure](https://portal.azure.com) nebo spuštěním [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) v [cloudové prostředí](../cloud-shell/overview.md), jako zobrazeno v následujících příkladech:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Nepoužívané verze  

Když je zastaralý starší verze, datum odebrání je oznámeno tak, aby vám podle toho naplánovat upgrade verze modulu runtime. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak můžete dotazovat stav aktualizace operačního systému a za běhu na instance Moje?  

Při důležitých informací operačního systému je uzamčené před přístupem (najdete v části [funkce operačního systému v Azure App Service](web-sites-available-operating-system-functionality.md)), [Kudu konzoly](https://github.com/projectkudu/kudu/wiki/Kudu-console) umožňuje dotazování vaše instance služby App Service týkající se operačního systému verze a verze modulu runtime. 

Následující tabulka ukazuje postup verzí systému Windows a language runtime, které se spouští vaše aplikace:

| Informace | Kde je najít | 
|-|-|
| Verze systému Windows | V tématu `https://<appname>.scm.azurewebsites.net/Env.cshtml` (v části informace systému) |
| Verze rozhraní .NET | V `https://<appname>.scm.azurewebsites.net/DebugConsole`, v příkazovém řádku spusťte následující příkaz: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Verze .NET core | V `https://<appname>.scm.azurewebsites.net/DebugConsole`, v příkazovém řádku spusťte následující příkaz: <br> `dotnet --version` |
| Verze PHP | V `https://<appname>.scm.azurewebsites.net/DebugConsole`, v příkazovém řádku spusťte následující příkaz: <br> `php --version` |
| Výchozí verze Node.js | V [cloudové prostředí](../cloud-shell/overview.md), spusťte následující příkaz: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Verze Pythonu | V `https://<appname>.scm.azurewebsites.net/DebugConsole`, v příkazovém řádku spusťte následující příkaz: <br> `python --version` |  

> [!NOTE]  
> Přístup k umístění v registru `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, kde informace o ["KB" opravy]((https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins)) je uložen, je uzamčené.
>
>

## <a name="more-resources"></a>Další zdroje informací

[Centrum zabezpečení: zabezpečení](https://www.microsoft.com/en-us/trustcenter/security)  
[64bitový ASP.NET Core v Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
