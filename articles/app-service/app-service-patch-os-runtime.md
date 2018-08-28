---
title: Operační systém a používání dílčích oprav modulu runtime ve službě Azure App Service | Dokumentace Microsoftu
description: Popisuje, jak aktualizovat službu Azure App Service aktualizace operačního systému a moduly runtime a jak můžete získat oznámení.
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
ms.openlocfilehash: 9855becd7c047788ed310dff4317a5df87cc9b61
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047624"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Operační systém a používání dílčích oprav modulu runtime ve službě Azure App Service

V tomto článku se dozvíte, jak získat některé informace o verzi operačního systému nebo softwaru v [služby App Service](app-service-web-overview.md). 

App Service je typu platforma jako služba, což znamená, že operační systém a aplikace zásobníku jsou pro vás spravuje služba Azure; můžete spravovat jenom aplikace a její data. Větší kontrolu nad operačním systémem a aplikace je k dispozici zásobníku v [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/). To na paměti je však užitečné pro vás jako uživatel služby App Service znát další informace, jako například:

-   Jak a kdy jsou aktualizace operačního systému použít?
-   Jak je opravit služby App Service před významné ohrožení zabezpečení (například nultého dne)?
-   Které verze operačního systému a modulu runtime se spouští vaše aplikace?

Z bezpečnostních důvodů nejsou publikovány určitá specifika informace o zabezpečení. Ale článek, zaměřuje na zmírnění této nepříjemné otázky tak maximální využití průhlednost na procesu, a jak je můžete dostávat aktuální informace o oznámení týkajících se zabezpečení nebo aktualizace modulu runtime.

## <a name="how-and-when-are-os-updates-applied"></a>Jak a kdy jsou aktualizace operačního systému použít?

Azure slouží ke správě opravy operačního systému na dvou úrovních, fyzických serverů a hostované virtuální počítače (VM), na kterých běží prostředky App Service. Obě se aktualizuje každý měsíc, které odpovídá měsíční [Patch Tuesday](https://technet.microsoft.com/security/bulletins.aspx) plánu. Tyto aktualizace se použijí automaticky, tak, aby zaručuje vysokou dostupnost služby smlouvy SLA Azure. 

Podrobné informace o tom, jak se aktualizace najdete v tématu [uvedení magic za aktualizace operačního systému aplikace služby](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak Azure zacházet s významnou ohrožení zabezpečení?

Při závažné ohrožení zabezpečení vyžadují okamžitou používání dílčích oprav, jako například [ohrožení zabezpečení nultého dne](https://wikipedia.org/wiki/Zero-day_(computing)), vysoce prioritní aktualizace jsou zpracovány v případ od případu.

Zůstaňte v obraze díky oznámení týkající se zabezpečení v Azure najdete [blog o zabezpečení Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Když jsou podporované jazykové moduly runtime aktualizován, přidá nebo zastaralé?

Nová stabilní verze podporované jazykové moduly runtime (hlavní verze, podverze nebo oprava) jsou pravidelně přidané do instancí služby App Service. Některé aktualizace přepsat existující instalaci, zatímco jiné jsou nainstalovány souběžně s existující verzí. Přepsání instalace znamená, že vaše aplikace automaticky běží na aktualizovaný modul runtime. Vedle sebe instalace znamená, že musíte ručně provést migraci vaší aplikace, abyste mohli využívat nové verze modulu runtime. Další informace naleznete podčásti.

Aktualizace modulu runtime a posíláme upozornění jsou novou funkcí je tady

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informace v tomto poli se vztahují na jazykové moduly runtime, která jsou integrovaná do aplikace služby App Service. Vlastního modulu runtime, který nahrajete do služby App Service, například zůstane beze změny, pokud provedete ruční upgrade.
>
>

### <a name="new-patch-updates"></a>Nové aktualizace

Oprava aktualizací pro .NET, PHP, Java SDK nebo verzi Tomcat nebo Jetty se použijí automaticky přepsáním stávající instalace nové verze. Node.js aktualizace jsou nainstalovány souběžně s existující verzí (podobně jako hlavní verze a podverze v další části). Nové Python verze opravy nainstalujete ručně prostřednictvím [rozšířením webu](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), souběžně s integrovanou instalace Pythonu.

### <a name="new-major-and-minor-versions"></a>Nové hlavní verze a podverze

Když se přidá nový hlavních nebo vedlejších verzí, je nainstalovaná souběžně s existující verze. Aplikaci můžete ručně upgradovat na novou verzi. Pokud jste nakonfigurovali verze modulu runtime v konfiguračním souboru (například `web.config` a `package.json`), budete muset upgradovat stejným způsobem. Pokud jste použili službu App Service na nastavení konfigurace vaší verze modulu runtime, můžete ji v změnit [webu Azure portal](https://portal.azure.com) nebo spuštěním [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) v [Cloud Shell](../cloud-shell/overview.md), jako můžete vidět v následujícím příkladu:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Zastaralá verze  

Když ze starší verze je zastaralá, datum odebrání je oznámili tak, aby mohli plánovat upgrade modulu runtime verze odpovídajícím způsobem. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak můžete dotazovat stav aktualizace operačního systému a modul runtime na Moje instance?  

Zatímco důležité informace o operačním systému je uzamčen před přístupem (naleznete v tématu [funkce operačního systému ve službě Azure App Service](web-sites-available-operating-system-functionality.md)), [konzola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) umožňuje dotazování vaší instance služby App Service týkající se operačního systému verze a verze modulu runtime. 

Následující tabulka ukazuje jak verzí systému Windows a modulu runtime jazyka, na kterých běží vaše aplikace:

| Informace | Kde ho najít | 
|-|-|
| Verze systému Windows | Zobrazit `https://<appname>.scm.azurewebsites.net/Env.cshtml` (v části Systémové informace) |
| Verze rozhraní .NET | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spuštěním následujícího příkazu na příkazovém řádku: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Verze .NET core | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spuštěním následujícího příkazu na příkazovém řádku: <br> `dotnet --version` |
| Verze PHP | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spuštěním následujícího příkazu na příkazovém řádku: <br> `php --version` |
| Výchozí verze Node.js | V [Cloud Shell](../cloud-shell/overview.md), spusťte následující příkaz: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Verze Pythonu | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spuštěním následujícího příkazu na příkazovém řádku: <br> `python --version` |  

> [!NOTE]  
> Přístup k umístění v registru `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, kde informace o ["KB" opravy](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) je uložena, je uzamčen.
>
>

## <a name="more-resources"></a>Další zdroje informací

[Centrum zabezpečení: zabezpečení](https://www.microsoft.com/en-us/trustcenter/security)  
[64bitový ASP.NET Core ve službě Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
