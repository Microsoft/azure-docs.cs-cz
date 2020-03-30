---
title: Technologie nasazení ve funkcích Azure
description: Seznamte se s různými způsoby nasazení kódu do funkcí Azure.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277125"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie nasazení ve funkcích Azure

K nasazení kódu projektu Azure Functions do Azure můžete použít několik různých technologií. Tento článek obsahuje vyčerpávající seznam těchto technologií, popisuje, které technologie jsou k dispozici pro které varianty funkce, vysvětluje, co se stane při použití jednotlivých metod a poskytuje doporučení pro nejlepší metodu pro použití v různých scénářích . Různé nástroje, které podporují nasazení do funkcí Azure, jsou naladěny na správnou technologii na základě jejich kontextu. Obecně platí, že nasazení zip uvázla doporučovací technologie pro funkce Azure.

## <a name="deployment-technology-availability"></a>Dostupnost technologie nasazení

Azure Functions podporuje místní vývoj a hostování napříč platformami ve Windows a Linuxu. V současné době jsou k dispozici tři hostingové plány:

+ [Spotřeby](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Vyhrazeno (služba aplikace)](functions-scale.md#app-service-plan)

Každý plán má jiné chování. Ne všechny technologie nasazení jsou k dispozici pro každou variantu funkce Azure. Následující graf ukazuje, které technologie nasazení jsou podporovány pro každou kombinaci operačního systému a plánu hostování:

| Technologie nasazení | Spotřeba systému Windows | Windows Premium | Windows Vyhrazené  | Spotřeba Linuxu | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Adresa URL externího balíčku<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Nasazení zipu |✔|✔|✔|✔|✔|✔|
| Kontejner Dockeru | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Správa zdrojového kódu |✔|✔|✔| |✔|✔|
| Místní Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloudová synchronizace<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Úpravy portálu |✔|✔|✔| |<sup>✔ 2</sup>|<sup>✔ 2</sup>|

<sup>1</sup> Technologie nasazení, která vyžaduje [ruční synchronizaci aktivačních událostí](#trigger-syncing).  
<sup>2</sup> Úprava portálu je povolena pouze pro aktivační události HTTP a Časovač pro funkce v Linuxu pomocí prémiových a vyhrazených plánů.

## <a name="key-concepts"></a>Klíčové koncepty

Některé klíčové koncepty jsou důležité pro pochopení toho, jak nasazení fungují ve funkcích Azure.

### <a name="trigger-syncing"></a>Synchronizace aktivační události

Při změně některého z aktivačních událostí, funkce infrastruktury musí být vědomi změny. Synchronizace probíhá automaticky pro mnoho technologií nasazení. V některých případech je však nutné ručně synchronizovat aktivační události. Při nasazení aktualizací odkazem na adresu URL externího balíčku, místní Git, cloudovou synchronizaci nebo FTP je nutné ručně synchronizovat aktivační události. Aktivační události můžete synchronizovat jedním ze tří způsobů:

* Restartování aplikace funkcí na webu Azure Portal
* Odešlete požadavek `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` HTTP POST pomocí [hlavního klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Odešlete požadavek `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`HTTP POST společnosti . Nahraďte zástupné symboly ID předplatného, názvem skupiny prostředků a názvem aplikace pro funkci.

### <a name="remote-build"></a>Vzdálené sestavení

Funkce Azure můžete automaticky provádět sestavení na kód, který obdrží po nasazení ZIP. Tato sestavení se chovají mírně odlišně v závislosti na tom, zda vaše aplikace běží na Windows nebo Linuxu. Vzdálená sestavení se neprovádějí, pokud byla aplikace dříve nastavena na spuštění v režimu [Spustit z balíčku.](run-functions-from-deployment-package.md) Chcete-li se dozvědět, jak používat vzdálené sestavení, přejděte na [nasazení zip](#zip-deploy).

> [!NOTE]
> Pokud máte problémy se vzdáleným sestavením, může to být proto, že vaše aplikace byla vytvořena před zpřístupněním funkce (1. srpna 2019). Zkuste vytvořit novou aplikaci `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` funkcí nebo spuštěním aktualizace aplikace funkce. Tento příkaz může trvat dva pokusy k úspěchu.

#### <a name="remote-build-on-windows"></a>Vzdálené sestavení v systému Windows

Všechny aplikace funkcí spuštěné v systému Windows mají malou aplikaci pro správu, web SCM (nebo [Kudu).](https://github.com/projectkudu/kudu) Tento web zpracovává většinu logiky nasazení a sestavení pro funkce Azure.

Když je aplikace nasazená do Windows, spustí `dotnet restore` se příkazy `npm install` specifické pro jazyk, například (C#) nebo (JavaScript).

#### <a name="remote-build-on-linux"></a>Vzdálené sestavení na Linuxu

Chcete-li povolit vzdálené sestavení na Linuxu, je nutné nastavit následující [nastavení aplikace:](functions-how-to-use-azure-function-app-settings.md#settings)

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Ve výchozím nastavení nástroje [Azure Functions Core Tools](functions-run-local.md) a rozšíření Azure Functions Extension for Visual Studio [Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) provádějí vzdálená sestavení při nasazování do Linuxu. Z tohoto důvodu oba nástroje automaticky vytvořit tato nastavení pro vás v Azure. 

Když jsou aplikace postaveny vzdáleně na [Linuxu, běží z balíčku nasazení](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Plán Consumption

Linuxfunkce aplikace spuštěné v plánu spotřeba nemají SCM/Kudu stránky, což omezuje možnosti nasazení. Aplikace funkcí v Linuxu spuštěné v plánu Spotřeba však podporují vzdálená sestavení.

##### <a name="dedicated-and-premium-plans"></a>Vyhrazené a prémiové plány

Funkční aplikace běžící na Linuxu v [plánu Dedicated (App Service)](functions-scale.md#app-service-plan) a [plán Premium](functions-scale.md#premium-plan) mají také omezený web SCM / Kudu.

## <a name="deployment-technology-details"></a>Podrobnosti o technologii nasazení

Následující metody nasazení jsou k dispozici v Azure Functions.

### <a name="external-package-url"></a>Adresa URL externího balíčku

Adresu URL externího balíčku můžete použít k odkazování na soubor vzdáleného balíčku (.zip), který obsahuje vaši aplikaci funkce. Soubor se stáhne z poskytnuté adresy URL a aplikace se spustí v režimu [Spustit z balíčku.](run-functions-from-deployment-package.md)

>__Jak ji používat:__ Přidejte `WEBSITE_RUN_FROM_PACKAGE` do nastavení aplikace. Hodnota tohoto nastavení by měla být adresa URL (umístění konkrétního souboru balíčku, který chcete spustit). Nastavení můžete přidat [buď na portálu,](functions-how-to-use-azure-function-app-settings.md#settings) nebo [pomocí příkazového příkazového příkazu k webu Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Pokud používáte úložiště objektů blob Azure, použijte privátní kontejner se [sdíleným přístupovým podpisem (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) k poskytnutí přístupu functions k balíčku. Při každém restartování aplikace načte kopii obsahu. Váš odkaz musí být platný po dobu životnosti aplikace.

>__Kdy ji použít:__ Adresa URL externího balíčku je jedinou podporovanou metodou nasazení pro funkce Azure spuštěnou na Linuxu v plánu Spotřeba, pokud uživatel nechce, aby došlo ke [vzdálenému sestavení.](#remote-build) Když aktualizujete soubor balíčku, na který odkazuje aplikace funkce, musíte [ručně synchronizovat aktivační události,](#trigger-syncing) abyste azure informovali, že se vaše aplikace změnila.

### <a name="zip-deploy"></a>Nasazení zipu

Pomocí nasazení zip můžete do Azure vysunout soubor ZIP, který obsahuje vaši aplikaci funkcí. Volitelně můžete nastavit, aby aplikace začala [běžet z balíčku](run-functions-from-deployment-package.md), nebo určit, že dojde ke [vzdálenému sestavení.](#remote-build)

>__Jak ji používat:__ Nasazení pomocí oblíbeného klientského nástroje: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio](functions-develop-vs.md#publish-to-azure)nebo z příkazového řádku pomocí nástrojů Azure Functions [Core Tools](functions-run-local.md#project-file-deployment). Ve výchozím nastavení tyto nástroje používají nasazení zip a [spustit z balíčku](run-functions-from-deployment-package.md). Základní nástroje a rozšíření kódu Visual Studio umožňují [vzdálené sestavení](#remote-build) při nasazování do Linuxu. Chcete-li ručně nasadit soubor ZIP do aplikace pro funkce, postupujte podle pokynů v [části Nasazení ze souboru ZIP nebo adresy URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Při nasazení pomocí zip nasazení, můžete nastavit aplikaci [spustit z balíčku](run-functions-from-deployment-package.md). Chcete-li spustit z `WEBSITE_RUN_FROM_PACKAGE` balíčku, `1`nastavte hodnotu nastavení aplikace na . Doporučujeme nasazení zip. Poskytuje rychlejší načítání pro vaše aplikace a je výchozí pro VS Code, Visual Studio a Azure CLI. 

>__Kdy ji použít:__ Nasazení zipu je doporučená technologie nasazení pro funkce Azure.

### <a name="docker-container"></a>Kontejner Dockeru

Můžete nasadit bitovou kopii kontejneru Linux, která obsahuje vaši aplikaci funkce.

>__Jak ji používat:__ Vytvořte aplikaci funkcí Linuxu v plánu Premium nebo Dedicated a určete, ze které image kontejneru se má spustit. To můžete provést dvěma způsoby:
>
>* Vytvořte aplikaci funkcí Linuxu na plánu služby Azure App Service na webu Azure Portal. V **části Publikovat**vyberte image **Dockeru**a nakonfigurujte kontejner. Zadejte umístění, kde je obrázek hostován.
>* Vytvořte aplikaci funkcí Linuxu v plánu služby App Service pomocí azure cli. Postup najdete [v tématu Vytvoření funkce v Linuxu pomocí vlastního obrázku](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Chcete-li nasadit do existující aplikace pomocí vlastního kontejneru, použijte v [nástrojích Azure Functions Core Tools](functions-run-local.md) [`func deploy`](functions-run-local.md#publish) , použijte příkaz.

>__Kdy ji použít:__ Možnost kontejneru Dockeru použijte, když potřebujete větší kontrolu nad prostředím Linuxu, kde běží vaše aplikace funkce. Tento mechanismus nasazení je k dispozici pouze pro funkce spuštěné v Systému Linux.

### <a name="web-deploy-msdeploy"></a>Nasazení webu (MSDeploy)

Balíčky nasazení webu a nasazení aplikací pro Windows na libovolný server Služby IIS, včetně aplikací pro funkce spuštěných ve Windows v Azure.

>__Jak ji používat:__ Použijte [nástroje Visual Studia pro funkce Azure](functions-create-your-first-function-visual-studio.md). Zrušte zaškrtnutí políčka **Spustit ze souboru balíčku (doporučeno).**
>
>Můžete si také stáhnout Web Deploy `MSDeploy.exe` [3.6](https://www.iis.net/downloads/microsoft/web-deploy) a volat přímo.

>__Kdy ji použít:__ Nasazení webu je podporováno a nemá žádné problémy, ale upřednostňovaným mechanismem je [nasazení zip s povoleným balíčkem Spustit z](#zip-deploy). Další informace naleznete v [průvodci vývojem sady Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Správa zdrojového kódu

Pomocí ovládacího prvku zdroj ového kódu připojte aplikaci funkcí k úložišti Git. Aktualizace kódu v tomto úložišti aktivuje nasazení. Další informace naleznete na [webu Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak ji používat:__ Centrum nasazení v oblasti Funkce portálu slouží k nastavení publikování ze správy zdrojového kódu. Další informace najdete [v tématu Průběžné nasazení pro funkce Azure](functions-continuous-deployment.md).

>__Kdy ji použít:__ Použití správy zdrojového kódu je osvědčeným postupem pro týmy, které spolupracují na svých aplikacích funkcí. Správě zdrojového kódu je dobrá možnost nasazení, která umožňuje složitější nasazení kanály.

### <a name="local-git"></a>Místní Git

Místní Git můžete použít k nabízení kódu z místního počítače do Azure Functions pomocí Gitu.

>__Jak ji používat:__ Postupujte podle pokynů v [části Nasazení místního Gitu do služby Azure App Service](../app-service/deploy-local-git.md).

>__Kdy ji použít:__ Obecně doporučujeme použít jinou metodu nasazení. Při publikování z místního Gitu je nutné [ručně synchronizovat aktivační události](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizace cloudu

Pomocí cloudové synchronizace můžete synchronizovat obsah z Dropboxu a OneDrivu do Azure Functions.

>__Jak ji používat:__ Postupujte podle pokynů v [synchronizaci obsahu z cloudové složky](../app-service/deploy-content-sync.md).

>__Kdy ji použít:__ Obecně doporučujeme jiné metody nasazení. Při publikování pomocí synchronizace cloudu je nutné [ručně synchronizovat aktivační události](#trigger-syncing).

### <a name="ftp"></a>FTP

Pomocí protokolu FTP můžete přímo přenášet soubory do funkcí Azure.

>__Jak ji používat:__ Postupujte podle pokynů v [části Nasazení obsahu pomocí ftp/s](../app-service/deploy-ftp.md).

>__Kdy ji použít:__ Obecně doporučujeme jiné metody nasazení. Při publikování pomocí ftp je nutné [ručně synchronizovat aktivační události](#trigger-syncing).

### <a name="portal-editing"></a>Úpravy portálu

V editoru založeném na portálu můžete přímo upravovat soubory, které jsou ve vaší aplikaci funkcí (v podstatě nasazení při každém uložení změn).

>__Jak ji používat:__ Abyste mohli upravovat své funkce na webu Azure Portal, musíte [mít vytvořené funkce na portálu](functions-create-first-azure-function.md). Chcete-li zachovat jeden zdroj pravdy, pomocí jakékoli jiné metody nasazení je vaše funkce jen pro čtení a zabraňuje pokračování úpravy portálu. Chcete-li se vrátit do stavu, ve kterém můžete upravovat soubory na webu `Read/Write` Azure Portal, můžete ručně zapnout režim úprav zpět a odebrat všechna nastavení aplikací souvisejících s nasazením (například `WEBSITE_RUN_FROM_PACKAGE`). 

>__Kdy ji použít:__ Portál je dobrý způsob, jak začít pracovat s Funkcemi Azure. Pro intenzivnější vývojové práce doporučujeme použít jeden z následujících klientských nástrojů:
>
>* [Kód visual studia](functions-create-first-function-vs-code.md)
>* [Základní nástroje Azure Functions (příkazový řádek)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

V následující tabulce jsou uvedeny operační systémy a jazyky, které podporují úpravy portálu:

| | Spotřeba systému Windows | Windows Premium | Windows Vyhrazené | Spotřeba Linuxu | Linux Premium | Linux Dedicated |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Skript jazyka C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Preview) | | | | | | |
| PowerShell (Preview) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>Úpravy portálu jsou povoleny pouze pro aktivační události HTTP a Timer pro funkce v Linuxu pomocí plánů Premium a Dedicated.

## <a name="deployment-slots"></a>Nasazovací sloty

Když nasadíte aplikaci funkce do Azure, můžete nasadit do samostatného slotu pro nasazení namísto přímo do produkčního prostředí. Další informace o slotech nasazení najdete v dokumentaci k tématu [Azure Functions Deployment Slots.](../app-service/deploy-staging-slots.md)

## <a name="next-steps"></a>Další kroky

Další informace o nasazování funkčních aplikací najdete v těchto článcích: 

+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)
+ [Průběžné doručování pomocí Azure DevOps](functions-how-to-azure-devops.md)
+ [Zip nasazení pro funkce Azure](deployment-zip-push.md)
+ [Spuštění funkcí Azure ze souboru balíčku](run-functions-from-deployment-package.md)
+ [Automatizace nasazení prostředků pro vaši funkční aplikaci ve službě Azure Functions](functions-infrastructure-as-code.md)
