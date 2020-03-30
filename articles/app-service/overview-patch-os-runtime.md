---
title: Kadence oprav operačního a runtime
description: Zjistěte, jak služba Azure App Service aktualizuje operační systém a běhové časy, jaké časy běhu a úroveň oprav vaše aplikace mají a jak můžete získat oznámení o aktualizacích.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 597964914f4022899ab027b735ec6932105497b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273626"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Opravy operačního a runtime ve službě Azure App Service

Tento článek ukazuje, jak získat určité informace o verzi týkající se operačního systému nebo softwaru ve [službě App Service](overview.md). 

Služba App Service je platforma jako služba, což znamená, že operační ho sah a zásobník aplikací spravuje za vás Azure; spravujete pouze aplikaci a její data. Větší kontrola nad os a zásobníkaplikací je k dispozici ve [virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/). S ohledem na to je pro vás jako uživatel app service užitečné znát další informace, například:

-   Jak a kdy se používají aktualizace operačního systému?
-   Jak je služba App Service opravena s významnými chybami zabezpečení (například nulový den)?
-   Které verze operačního systému a runtime spouštějí vaše aplikace?

Z bezpečnostních důvodů nejsou zveřejňovány určité specifika informací o zabezpečení. Cílem článku je však zmírnit obavy maximalizací transparentnosti procesu a jak můžete zůstat aktuální v oznámeních souvisejících se zabezpečením nebo aktualizacích za běhu.

## <a name="how-and-when-are-os-updates-applied"></a>Jak a kdy se používají aktualizace operačního systému?

Azure spravuje opravy operačního serveru na dvou úrovních, fyzické servery a hostované virtuální počítače (VM), které spouštějí prostředky služby App Service. Oba jsou aktualizovány měsíčně, což odpovídá měsíčnímu plánu [Patch Tuesday.](https://technet.microsoft.com/security/bulletins.aspx) Tyto aktualizace se použijí automaticky, a to způsobem, který zaručuje vysokou dostupnost sla služeb Azure. 

Podrobné informace o tom, jak se aktualizace používají, najdete [v tématu Demystifikace kouzlo aktualizace operačního systému Služby App Service](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak Azure řeší významná zranitelná místa?

Pokud závažné chyby zabezpečení vyžadují okamžité opravy, například [chyby zabezpečení nultého dne](https://wikipedia.org/wiki/Zero-day_(computing)), jsou aktualizace s vysokou prioritou zpracovávány případ od případu.

Na blogu [azure security](https://azure.microsoft.com/blog/topics/security/)blog můžete zůstat v obraze díky důležitým oznámením o zabezpečení v Azure. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Kdy jsou podporované moduly runtimes jazyka aktualizovány, přidány nebo zastaralé?

Do instancí služby App Service se pravidelně přidávají nové stabilní verze podporovaných modulů runtimes jazyka (hlavní, vedlejší nebo oprava). Některé aktualizace přepíší existující instalaci, zatímco jiné jsou nainstalovány vedle stávajících verzí. Přepsání instalace znamená, že vaše aplikace automaticky spustí na aktualizovaném běhu. Instalace vedle sebe znamená, že je nutné ručně migrovat aplikaci, abyste využili výhod nové verze runtime. Další informace naleznete v jedné z podsekcí.

Aktualizace a vyřazení za běhu jsou oznámeny zde:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informace zde platí pro zaběhu jazyka, které jsou integrovány do aplikace App Service. Vlastní runtime, který nahrajete do služby App Service, například zůstane nezměněn, pokud ji ručně neupgradujete.
>
>

### <a name="new-patch-updates"></a>Nové aktualizace oprav

Aktualizace oprav na verzi .NET, PHP, Java SDK nebo Tomcat/Jetty se použijí automaticky přepsáním stávající instalace novou verzí. Aktualizace oprav node.js jsou nainstalovány vedle stávajících verzí (podobně jako hlavní a dílčí verze v další části). Nové verze patchů pythonu lze instalovat ručně prostřednictvím [rozšíření lokality](https://azure.microsoft.com/blog/azure-web-sites-extensions/), bok po boku s vestavěnými instalacemi Pythonu.

### <a name="new-major-and-minor-versions"></a>Nové hlavní a dílčí verze

Po přidání nové hlavní nebo dílčí verze je nainstalována vedle stávajících verzí. Aplikaci můžete upgradovat ručně na novou verzi. Pokud jste nakonfigurovali verzi runtime `web.config` `package.json`v konfiguračním souboru (například a ), je třeba provést upgrade stejnou metodou. Pokud jste ke konfiguraci runtime verze použili nastavení služby App Service, můžete ji změnit na [webu Azure Portal](https://portal.azure.com) nebo spuštěním příkazu Azure [CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) v [cloudovém prostředí](../cloud-shell/overview.md), jak je znázorněno na následujících příkladech:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Zastaralé verze  

Pokud je starší verze zastaralá, datum odebrání je oznámeno, takže můžete odpovídajícím způsobem naplánovat upgrade runtime verze. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak se můžu dotazovat na stav aktualizace operačního systému a běhu v instancích?  

Zatímco důležité informace o operačním systému je uzamčen přístup (viz [Funkce operačního systému ve službě Azure App Service),](operating-system-functionality.md) [konzole Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) umožňuje dotaz na instanci služby App Service týkající se verze operačního systému a runtime verze. 

Následující tabulka ukazuje, jak se verze systému Windows a běhu jazyka, které jsou spuštěny vaše aplikace:

| Informace | Kde ji najít | 
|-|-|
| Verze systému Windows | Viz `https://<appname>.scm.azurewebsites.net/Env.cshtml` (v části Informace o systému) |
| Verze rozhraní .NET | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spusťte v příkazovém řádku následující příkaz: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| Verze jádra rozhraní .NET | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spusťte v příkazovém řádku následující příkaz: <br> `dotnet --version` |
| PHP verze | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spusťte v příkazovém řádku následující příkaz: <br> `php --version` |
| Výchozí verze souboru Node.js | V [prostředí cloudu](../cloud-shell/overview.md)spusťte následující příkaz: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Verze Pythonu | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spusťte v příkazovém řádku následující příkaz: <br> `python --version` |  
| Java verze | Na `https://<appname>.scm.azurewebsites.net/DebugConsole`, spusťte v příkazovém řádku následující příkaz: <br> `java -version` |  

> [!NOTE]  
> Přístup k `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`umístění registru , kde jsou uloženy informace o [opravách "KB",](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) je uzamčen.
>
>

## <a name="more-resources"></a>Další zdroje informací

[Centrum zabezpečení: Zabezpečení](https://www.microsoft.com/en-us/trustcenter/security)  
[64bitová ASP.NET core ve službě Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
