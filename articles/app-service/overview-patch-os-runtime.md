---
title: Tempo opravy operačního systému a modulu runtime
description: Přečtěte si, jak Azure App Service aktualizovat operační systém a moduly runtime, jaké moduly runtime a úroveň oprav mají vaše aplikace a jak můžete dostávat oznámení o aktualizacích.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b52223aea0f0bdfecf58906ac192e893da3b47d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558483"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Opravy operačního systému a modulu runtime v Azure App Service

V tomto článku se dozvíte, jak získat určité informace o verzi pro operační systém nebo software v [App Service](overview.md). 

App Service je platforma jako služba, což znamená, že pro vás Azure spravuje operační systém a zásobník aplikací. Spravujte jenom svou aplikaci a její data. Větší kontrola nad operačním systémem a zásobníkem aplikací je k dispozici v [Azure Virtual Machines](../virtual-machines/index.yml). V takovém případě je užitečné, abyste byli App Servicei uživateli, který vám pomůže získat další informace, například:

-   Jak a kdy se používají aktualizace operačního systému?
-   Jak se App Serviceují opravy s významnými ohroženími zabezpečení (například Zero-Day)?
-   Které operační systémy a verze modulu runtime spouští vaše aplikace?

Z bezpečnostních důvodů nejsou publikované určité konkrétní informace o zabezpečení. Článek se ale zaměřuje na zmírnění obav tím, že maximalizuje transparentnost procesu a jak můžete udržovat oznámení týkající se zabezpečení nebo aktualizace za běhu.

## <a name="how-and-when-are-os-updates-applied"></a>Jak a kdy se používají aktualizace operačního systému?

Azure spravuje opravy operačního systému na dvou úrovních, fyzických serverech a hostovaných virtuálních počítačích, které spouštějí App Service prostředky. Obě aktualizace se aktualizují měsíčně, což se zarovnává s plánem úterý v rámci měsíčních [oprav](/security-updates/) . Tyto aktualizace se aplikují automaticky tak, že garantuje smlouvu SLA pro vysokou dostupnost služeb Azure. 

Podrobné informace o tom, jak se aktualizace aplikují, najdete v tématu [Demystifying The magic App Service Updates for OS](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak Azure pracuje s významnými ohroženími zabezpečení?

Pokud závažné chyby zabezpečení vyžadují okamžitou opravu, jako jsou například [slabá místa](https://wikipedia.org/wiki/Zero-day_(computing)), jsou aktualizace s vysokou prioritou zpracovávány na základě velkých a malých písmen.

Informujte se o důležitých oznámeních o zabezpečení v Azure na [blogu zabezpečení Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Kdy jsou podporované jazykové moduly runtime, které se aktualizovaly, přidávají nebo zastaralá?

Nové stabilní verze podporovaných jazykových modulů runtime (hlavní, vedlejší nebo opravné) se pravidelně přidávají do instancí App Service. Některé aktualizace přepisují existující instalaci, zatímco ostatní jsou nainstalovány souběžně se stávajícími verzemi. Instalace přepsání znamená, že se vaše aplikace automaticky spustí v aktualizovaném modulu runtime. Souběžná instalace znamená, že musíte aplikaci ručně migrovat, aby bylo možné využít novou verzi modulu runtime. Další informace najdete v jednom z dílčích sekcí.

Aktualizace a zastaralosti za běhu jsou oznámeny zde:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Zde uvedené informace platí pro jazykové moduly runtime, které jsou integrovány do aplikace App Service. Vlastní modul runtime, který nahrajete do App Service například zůstane beze změny, pokud jej ručně neprovedete.
>
>

### <a name="new-patch-updates"></a>Nové aktualizace oprav

Aktualizace aktualizací .NET, PHP, Java SDK nebo verze Tomcat se aplikují automaticky přepsáním stávající instalace nejnovější verzí. Aktualizace Node.js opravy se instalují souběžně se stávajícími verzemi (podobně jako hlavní a dílčí verze v další části). Nové verze oprav Pythonu se dají nainstalovat ručně prostřednictvím [rozšíření lokality](https://azure.microsoft.com/blog/azure-web-sites-extensions/)vedle sebe s integrovanými instalacemi v Pythonu.

### <a name="new-major-and-minor-versions"></a>Nové hlavní a dílčí verze

Když se přidá nová hlavní nebo dílčí verze, nainstaluje se souběžně s existujícími verzemi. Aplikaci můžete ručně upgradovat na novou verzi. Pokud jste nakonfigurovali verzi modulu runtime v konfiguračním souboru (například `web.config` a `package.json` ), musíte upgradovat se stejnou metodou. Pokud jste pro konfiguraci běhové verze použili App Service nastavení, můžete ji změnit v [Azure Portal](https://portal.azure.com) nebo spuštěním příkazu rozhraní příkazového [řádku Azure CLI](/cli/azure/get-started-with-azure-cli) v [Cloud Shell](../cloud-shell/overview.md), jak je znázorněno v následujících příkladech:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Zastaralé verze  

Je-li starší verze zastaralá, je datum odebrání oznámeno, takže můžete podle potřeby naplánovat upgrade verze modulu runtime. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak se můžu dotazovat na stav aktualizace operačního systému a běhu u mých instancí?  

I když jsou důležité informace o operačním systému uzamčené od přístupu (viz [funkce operačního systému na Azure App Service](operating-system-functionality.md)), [Konzola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) vám umožní dotazovat se na vaši instanci App Service v souvislosti s verzí operačního systému a verzemi modulu runtime. 

Následující tabulka ukazuje, jak verze Windows, tak jazykového modulu runtime, ve kterém běží vaše aplikace:

| Informace | Kde ji najít | 
|-|-|
| Verze systému Windows | Viz `https://<appname>.scm.azurewebsites.net/Env.cshtml` (informace o systému) |
| Verze .NET | V `https://<appname>.scm.azurewebsites.net/DebugConsole` Spusťte na příkazovém řádku následující příkaz: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| Verze .NET Core | V `https://<appname>.scm.azurewebsites.net/DebugConsole` Spusťte na příkazovém řádku následující příkaz: <br> `dotnet --version` |
| Verze PHP | V `https://<appname>.scm.azurewebsites.net/DebugConsole` Spusťte na příkazovém řádku následující příkaz: <br> `php --version` |
| Výchozí verze Node.js | V [Cloud Shell](../cloud-shell/overview.md)spusťte následující příkaz: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python version (Verze Pythonu) | V `https://<appname>.scm.azurewebsites.net/DebugConsole` Spusťte na příkazovém řádku následující příkaz: <br> `python --version` |  
| Verze Java | V `https://<appname>.scm.azurewebsites.net/DebugConsole` Spusťte na příkazovém řádku následující příkaz: <br> `java -version` |  

> [!NOTE]  
> Přístup k umístění registru `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` , kde jsou uložené informace o [opravách KB](/security-updates/SecurityBulletins/securitybulletins) , jsou uzamčené.
>
>

## <a name="more-resources"></a>Další zdroje informací

[Centrum zabezpečení: zabezpečení](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bitová ASP.NET Core na Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
