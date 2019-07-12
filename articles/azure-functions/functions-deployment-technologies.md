---
title: Technologie nasazení ve službě Azure Functions | Dokumentace Microsoftu
description: Přečtěte si různé způsoby, jak kód můžete nasadit do služby Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594398"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie nasazení ve službě Azure Functions

Nasaďte kód projektu funkce Azure do Azure můžete použít několik různých technologií. Tento článek obsahuje úplný seznam těchto technologií ještě používáte, popisuje technologie, které jsou k dispozici pro jaké typy funkcí, vysvětluje, co se stane při použití každé metody a poskytuje doporučení pro osvědčené metody pro použití v různých scénářích . Různé nástroje, které podporují nasazení do služby Azure Functions je vyladěná pro správnou technologii podle jejich kontextu.

## <a name="deployment-technology-availability"></a>Dostupnost technologie nasazení

> [!IMPORTANT]
> Služba Azure Functions podporuje místní vývoj pro různé platformy a hostování ve Windows a Linuxu. V současné době tři plány hostování jsou dostupné: [Spotřeba](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), a [Dedicated (Azure App Service)](functions-scale.md#app-service-plan). Každý plán obsahuje různé chování. Ne všechna nasazení technologie jsou k dispozici pro každý flavor Azure Functions.

| Technologie nasazení | Windows, spotřebě | Windows Premium (preview) | Windows Dedicated  | Spotřeba Linux (preview) | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| Adresy URL externího balíčku<sup>1</sup> |✔|✔|✔|✔|✔|
| Nasazení ZIP |✔|✔|✔| |✔|
| Kontejner dockeru | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Správy zdrojového kódu |✔|✔|✔| |✔|
| Místní Git<sup>1</sup> |✔|✔|✔| |✔|
| Cloudové synchronizace<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Úpravy portálu |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> technologie nasazení, která vyžaduje [ruční aktivační událost synchronizace](#trigger-syncing).  
<sup>2</sup> úpravy portálu je povolena pouze pro aktivační události HTTP a časovač pro službu Functions v Linuxu pomocí vyhrazené plánu.

## <a name="key-concepts"></a>Klíčové koncepty

Některé klíčové koncepty jsou důležité k porozumění fungování nasazení ve službě Azure Functions.

### <a name="trigger-syncing"></a>Aktivační událost synchronizace

Pokud změníte některé z vašich aktivační události, musí být funkce infrastruktury počítat se změnami. Synchronizace dojde automaticky pro řadu technologií nasazení. V některých případech musí však ručně synchronizovat vaše aktivační události. Když nasazujete aktualizace odkazem adresu URL externího balíčku, místní Git, cloudové synchronizace nebo FTP, musí ručně synchronizovat vaše aktivační události. Můžete synchronizovat aktivační události v jednom ze tří způsobů:

* Restartujte aplikaci function app na webu Azure Portal
* Odeslání požadavku HTTP POST do `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` pomocí [hlavní klíč](functions-bindings-http-webhook.md#authorization-keys).
* Odeslání požadavku HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Zástupné symboly nahraďte ID vašeho předplatného, název skupiny prostředků a název vaší aplikace function App.

## <a name="deployment-technology-details"></a>Podrobnosti o nasazení technologie 

Následující metody nasazení jsou k dispozici ve službě Azure Functions.

### <a name="external-package-url"></a>Adresy URL externího balíčku

Adresy URL externího balíčku můžete odkazovat na soubor vzdálené balíček (.zip), který obsahuje vaši aplikaci function app. Soubor se stáhne ze zadané adresy URL a spuštění aplikace [spustit z balíčku](run-functions-from-deployment-package.md) režimu.

>__Jak jej používat:__ Přidat `WEBSITE_RUN_FROM_PACKAGE` do nastavení aplikace. Hodnota tohoto nastavení by měla být adresa URL (umístění, které chcete spustit konkrétní balíček souboru). Můžete přidat nastavení buď [na portálu](functions-how-to-use-azure-function-app-settings.md#settings) nebo [pomocí rozhraní příkazového řádku Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Pokud používáte úložiště objektů Blob v Azure, použijte privátní kontejneru s [sdílený přístupový podpis (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) poskytnout funkcí přístup k balíčku. Pokaždé, když se aplikace restartuje, načte kopii tohoto obsahu. Váš odkaz musí být platný po dobu životnosti aplikace.

>__Kdy ji použít:__ Adresa URL externího balíčku je pouze podporované metody nasazení pro službu Azure Functions v Linuxu spuštěnou v plánu Consumption (Preview). Při aktualizaci souboru balíčku, který odkazuje na aplikaci function app, je nutné [ruční synchronizaci triggery](#trigger-syncing) Azure říct, že došlo ke změně vaší aplikace.

### <a name="zip-deploy"></a>Nasazení ZIP

Použití zip nasazení tak, aby nabízel souboru ZIP, který obsahuje vaši aplikací funkcí do Azure. Volitelně můžete nastavit aplikaci spustíte [spustit z balíčku](run-functions-from-deployment-package.md) režimu.

>__Jak jej používat:__ Nasaďte pomocí oblíbených klientských nástrojů: [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [sady Visual Studio](functions-develop-vs.md#publish-to-azure), nebo [rozhraní příkazového řádku Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). K ručnímu nasazení souboru ZIP do vaší aplikace function app, postupujte podle pokynů v [nasazení ze souboru ZIP nebo adresy URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Když nasadíte pomocí zip nasazení, můžete nastavit svoji aplikaci spouštět [spustit z balíčku](run-functions-from-deployment-package.md) režimu. Chcete-li nastavit režim spuštění z balíčku, nastavte `WEBSITE_RUN_FROM_PACKAGE` hodnoty nastavení aplikace pro `1`. Doporučujeme, abyste nasazení zip. Poskytuje rychlejší načítání pro vaše aplikace a je výchozí nastavení pro VS Code, Visual Studio a rozhraní příkazového řádku Azure.

>__Kdy ji použít:__ Nasazení ZIP je technologie, doporučené nasazení pro službu Azure Functions a systémem Windows a pro službu Azure Functions v Linuxu spuštěnou v vyhrazený plán.

### <a name="docker-container"></a>Kontejner dockeru

Můžete nasadit image kontejneru s Linuxem, který obsahuje vaši aplikaci function app.

>__Jak jej používat:__ Vytvoření aplikace function app s Linuxem vyhrazený plán a zadejte image kontejneru pro spuštění z. Můžete to provést dvěma způsoby:
>
>* Vytvoření aplikace funkcí Linux v plánu služby Azure App Service na webu Azure Portal. Pro **publikovat**vyberte **Image Dockeru**a pak proveďte konfiguraci kontejneru. Zadejte umístění, kde se hostuje image.
>* Vytvoření aplikace funkcí Linux na plán služby App Service pomocí Azure CLI. Další informace o postupu [vytvoření funkce v Linuxu pomocí vlastní image](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>K nasazení do stávající aplikace s použitím vlastní kontejner, ve [nástrojů Azure Functions Core](functions-run-local.md), použijte [ `func deploy` ](functions-run-local.md#publish) příkazu.

>__Kdy ji použít:__ Použijte možnost kontejner Dockeru když potřebujete větší kontrolu nad prostředím Linux kde běží vaše aplikace function app. Tento mechanismus pro nasazení je dostupná jenom pro funkce běžící na Linuxu v plánu služby App Service.

### <a name="web-deploy-msdeploy"></a>Nasazení webu (MSDeploy)

Nástroj nasazení webu balíčky a aplikace Windows k jakémukoli serveru služby IIS, včetně vaší aplikace function App a systémem Windows v Azure nasadí.

>__Jak jej používat:__ Použití [Visual Studio tools pro službu Azure Functions](functions-create-your-first-function-visual-studio.md). Zrušte **spustit ze souboru balíčku (doporučeno)** zaškrtávací políčko.
>
>Můžete také stáhnout [webové nasazení 3.6](https://www.iis.net/downloads/microsoft/web-deploy) a volat `MSDeploy.exe` přímo.

>__Kdy ji použít:__ Nástroj nasazení webu je podporován a nemá žádné problémy, ale je upřednostňovaný postup [zip nasazení s využitím spustit z balíčku povolené](#zip-deploy). Další informace najdete v tématu [Příručka pro vývojáře v sadě Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Správy zdrojového kódu

Použití správy zdrojového kódu pro vaši aplikaci function app připojení k úložišti Git. Aktualizace kódu v tomto úložišti aktivuje nasazení. Další informace najdete v tématu [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak jej používat:__ Pomocí centra pro nasazení na portálu Azure Functions k nastavení publikování ze správy zdrojového kódu. Další informace najdete v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md).

>__Kdy ji použít:__ Pomocí správy zdrojového kódu je nejvhodnější pro týmy, které spolupracovat na jejich aplikace function App. Správy zdrojového kódu je možnost dobré nasazení, která umožňuje složitější kanály nasazení.

### <a name="local-git"></a>Místní Git

Vám pomůže místní Git push kódu z místního počítače do služby Azure Functions pomocí Gitu.

>__Jak jej používat:__ Postupujte podle pokynů v [místní nasazení z Gitu do služby Azure App Service](../app-service/deploy-local-git.md).

>__Kdy ji použít:__ Obecně doporučujeme používat metodu jiného nasazení. Při publikování z místního Gitu, je nutné [ruční synchronizaci triggery](#trigger-syncing).

### <a name="cloud-sync"></a>Cloudové synchronizace

Použití cloudové synchronizace pro synchronizaci svůj obsah od Dropbox nebo OneDrive do služby Azure Functions.

>__Jak jej používat:__ Postupujte podle pokynů v [synchronizuje obsah z cloudové složky](../app-service/deploy-content-sync.md).

>__Kdy ji použít:__ Obecně doporučujeme jiné metody nasazení. Když publikujete pomocí synchronizace cloudu, je nutné [ruční synchronizaci triggery](#trigger-syncing).

### <a name="ftp"></a>FTP

Pomocí funkce FTP přímo přenos souborů do služby Azure Functions.

>__Jak jej používat:__ Postupujte podle pokynů v [nasadit obsah pomocí FTP/s](../app-service/deploy-ftp.md).

>__Kdy ji použít:__ Obecně doporučujeme jiné metody nasazení. Když publikujete pomocí protokolu FTP, je nutné [ruční synchronizaci triggery](#trigger-syncing).

### <a name="portal-editing"></a>Úpravy portálu

V editoru založené na portálu může přímo upravit soubory, které jsou ve své aplikaci function app (nasazení v podstatě pokaždé, když se uložit změny).

>__Jak jej používat:__ Aby bylo možné upravit funkce na webu Azure Portal, musí mít [vytvořená na portálu funkcí](functions-create-first-azure-function.md). Pokud chcete zachovat jediný zdroj pravdivých informací, pomocí jiné metody nasazení je funkce jen pro čtení a zabrání v pokračování portálu úpravy. Pokud chcete vrátit do stavu, ve kterém můžete upravit soubory na webu Azure Portal, můžete ručně vypnout režim úprav zpět `Read/Write` a odebrat všechna nastavení souvisejících s nasazením aplikace (jako je `WEBSITE_RUN_FROM_PACKAGE`). 

>__Kdy ji použít:__ Na portálu je dobrým způsobem, jak začít pracovat s využitím Azure Functions. Při vývojových pracích intenzivnější doporučujeme použít klienta nástroje:
>
>* [Začínáme s využitím VS Code](functions-create-first-function-vs-code.md)
>* [Začínáme s využitím Azure Functions Core Tools](functions-run-local.md)
>* [Začínáme s využitím sady Visual Studio](functions-create-your-first-function-visual-studio.md)

Následující tabulka uvádí operační systémy a jazyky, tuto podporu portálu úpravy:

| | Windows, spotřebě | Windows Premium (Preview) | Windows Dedicated | Spotřeba Linux (Preview) | Linux Dedicated |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C#Skript |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (Preview) | | | | | |
| PowerShell (Preview) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> Úpravy portálu je povolena pouze pro aktivační události HTTP a časovač pro službu Functions v Linuxu pomocí vyhrazené plánu.

## <a name="deployment-slots"></a>Nasazovací sloty

Když nasadíte aplikaci function app do Azure, můžete nasadit do samostatného nasazení slotu namísto nasazení přímo do produkčního prostředí. Další informace o sloty nasazení najdete v tématu [sloty Azure App Service](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Úrovně podpory pro sloty nasazení

Existují dvě úrovně podpory pro sloty nasazení:

* **Obecné dostupnosti (GA)** : Plně podporované a schválené pro použití v produkčním prostředí.
* **Preview**: Ještě není podporované, ale není očekáváno GA stav v budoucnu.

| Plán hostování/OS | Úroveň podpory |
| --------------- | ------ |
| Windows, spotřebě | Náhled |
| Windows Premium (Preview) | Náhled |
| Windows Dedicated | Všeobecná dostupnost |
| Využití systému Linux | Nepodporuje se |
| Linux Dedicated | Všeobecná dostupnost |

## <a name="next-steps"></a>Další postup

Přečtěte si tyto články a další informace o nasazení vaší aplikace function App: 

+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)
+ [Průběžné doručování s využitím Azure DevOps](functions-how-to-azure-devops.md)
+ [Nasazení ZIP pro službu Azure Functions](deployment-zip-push.md)
+ [Spouštění Azure Functions ze souboru balíčku](run-functions-from-deployment-package.md)
+ [Automatizace nasazování prostředků pro vaši aplikaci funkcí ve službě Azure Functions](functions-infrastructure-as-code.md)
