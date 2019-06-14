---
title: Technologie nasazení v Azure Functions | Dokumentace Microsoftu
description: Další informace a nevýhodami různé způsoby, jak kód můžete nasadit do služby Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 689e54877a5b0a405fe7b3e3981d98ba6b42edb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062943"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie nasazení ve službě Azure Functions

Existuje několik různých technologií, které vám pomůže se nasadí kód projektu funkce Azure do Azure. Tento článek obsahuje úplný seznam těchto technologií ještě používáte, informuje o tom, které technologie jsou k dispozici pro jaké typy funkcí, vysvětluje, co se skutečně děje při každé metody se používá a poskytuje doporučení pro osvědčené metody pro použití v různé scénáře. Různé nástroje, které podporují nasazení do služby Azure Functions je vyladěná pro správnou technologii podle jejich kontextu.

## <a name="deployment-technology-availability"></a>Dostupnost technologie nasazení

> [!IMPORTANT]
> Azure Functions podporuje pro různé platformy místnímu vývoji a hostování na dva operační systémy: Windows a Linux. Existují tři plány hostování aktuálně k dispozici, každá má jiné chování - [spotřeby](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), a [dedicated (App Service)](functions-scale.md#app-service-plan). Ne všechna nasazení technologie jsou k dispozici pro každý flavor Azure Functions.

| Technologie nasazení | Windows, spotřebě | Windows premium (preview) | Windows dedicated  | Spotřeba Linux (preview) | Linux dedicated |
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
<sup>2</sup> úpravy portál je povolená pouze protokol HTTP a časovač aktivačních událostí pro službu Functions v Linuxu pomocí vyhrazených plánů.

## <a name="key-concepts"></a>Klíčové koncepty

Než budete pokračovat, je potřeba další některých klíčových koncepcí, které budou důležité Princip fungování nasazení ve službě Azure Functions.

### <a name="trigger-syncing"></a>Aktivační událost synchronizace

Pokud změníte některé z vašich triggery, funkce infrastruktura musí být tyto změny. Tuto synchronizaci dojde automaticky pro řadu technologií nasazení. Ale v některých případech je nutné ručně synchronizovat vaše aktivační události. Když nasazujete aktualizace pomocí adresy URL externího balíčku, místního Gitu, cloudové synchronizace nebo FTP, musí být nutné ručně synchronizovat vaše aktivační události. Můžete synchronizovat aktivační události v jednom ze tří způsobů:

* Restartujte aplikaci function app na webu Azure Portal
* Odeslání požadavku HTTP POST do `https://www.{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` pomocí [hlavní klíč](functions-bindings-http-webhook.md#authorization-keys).
* Odeslání požadavku HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Zástupné symboly nahraďte ID vašeho předplatného, název skupiny prostředků a název vaší aplikace function App.

## <a name="deployment-technology-details"></a>Podrobnosti o nasazení technologie  

### <a name="external-package-url"></a>Adresy URL externího balíčku

__Co to dělá:__ Umožňuje odkazovat na soubor vzdálené balíček (.zip), který obsahuje vaši aplikaci function app. Soubor se stáhne ze zadané adresy URL a spuštění aplikace [spustit z balíčku](run-functions-from-deployment-package.md) režimu.

__Jak jej používat:__ Přidat `WEBSITE_RUN_FROM_PACKAGE` do nastavení aplikace. Hodnota tohoto nastavení by měla být adresa URL – umístění souboru konkrétní balíček, který chcete spustit. Můžete přidat nastavení buď [na portálu](functions-how-to-use-azure-function-app-settings.md#settings) nebo [pomocí rozhraní příkazového řádku Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Pokud pomocí úložiště objektů blob v Azure, měli byste použít privátní kontejneru s [sdíleného přístupového podpisu (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) poskytnout funkcí přístup k balíčku. Kdykoli se aplikace restartuje ho načte kopii tohoto obsahu, což znamená, že váš odkaz musí být platný po dobu životnosti aplikace.

__Kdy ji použít:__ Toto je metoda nasazení jenom podporována pro službu Azure Functions v Linuxu spuštěnou v plánu Consumption (Preview). Při aktualizaci souboru balíčku, který odkazuje na aplikaci function app, je nutné [ruční synchronizaci triggery](#trigger-syncing) Azure říct, že došlo ke změně vaší aplikace.

### <a name="zip-deploy"></a>Nasazení ZIP

__Co to dělá:__ Umožňuje vložit soubor zip obsahující aplikaci function app do Azure. Volitelně můžete také zadat k vaší aplikaci spustit v [spustit z balíčku](run-functions-from-deployment-package.md) režimu.

__Jak jej používat:__ Nasazení pomocí vašich oblíbených nástrojů klient - [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [sady Visual Studio](functions-develop-vs.md#publish-to-azure), nebo [rozhraní příkazového řádku Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). K ručnímu nasazení souboru zip do vaší aplikace function app, postupujte podle pokynů uvedených v [nasazení ze souboru zip nebo adresy url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

Kromě toho při nasazení pomocí zip nasazení, uživatelé mohou zadat své aplikace spouštět z v [spustit z balíčku](run-functions-from-deployment-package.md) režimu tak, že nastavíte `WEBSITE_RUN_FROM_PACKAGE` hodnoty nastavení aplikace jako `1`. Tato možnost doporučuje a poskytuje rychlejší načítání pro vaše aplikace. To je ve výchozím nastavení pro klienta nástroje výše.

__Kdy ji použít:__ Toto je doporučené nasazení technologie pro běžící na Windows Azure Functions a Azure Functions v Linuxu spuštěnou v vyhrazený plán.

### <a name="docker-container"></a>Kontejner dockeru

__Co to dělá:__ Zadejte image kontejneru, který o přijetí změn a spuštění Azure Functions.

__Jak jej používat:__ Vytvoření aplikace function app s Linuxem vyhrazený plán a zadejte image kontejneru pro spuštění z. Můžete to provést dvěma způsoby:

* Vytvoření aplikace funkcí Linux na plán služby App Service na webu Azure Portal. Vyberte **Image Dockeru** pro **publikovat**a konfigurovat kontejner, poskytuje umístění, kde se hostuje image.
* Vytvoření aplikace funkcí Linux na plán služby App Service přes rozhraní příkazového řádku Azure. Zjistěte, jak kontrolou [vytvoření funkce v Linuxu pomocí vlastní image](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).

Chcete-li nasadit do existující aplikace pomocí vlastní kontejner, použijte [ `func deploy` ](functions-run-local.md#publish) příkaz [nástrojů Azure Functions Core](functions-run-local.md).

__Kdy ji použít:__ Tuto možnost použijte, když potřebujete větší kontrolu nad prostředím Linux kde běží vaše aplikace function app. Tento mechanismus pro nasazení dostupná jenom pro funkce běžící na Linuxu v plánu služby App Service.

### <a name="web-deploy-msdeploy"></a>Nasazení webu (MSDeploy)

__Co to dělá:__ Balíčky a nasazuje aplikace Windows k jakémukoli serveru služby IIS – včetně vaše aplikace funkcí Azure a systémem Windows.

__Jak jej používat:__ Použití [Visual Studio tools pro službu Azure Functions](functions-create-your-first-function-visual-studio.md), a nikoli značek `Run from package file (recommended)` zaškrtávací políčko.

Můžete také volat `MSDeploy.exe` přímo po stažení [webové nasazení 3.6](https://www.iis.net/downloads/microsoft/web-deploy).

__Kdy ji použít:__ Tato technologie nasazení je podporováno a nemá žádné problémy, ale o upřednostňovaný postup je nyní [Zip nasazení s využitím spustit z balíčku povolené](#zip-deploy). Další informace najdete [Příručka pro vývojáře v sadě Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Správy zdrojového kódu

__Co to dělá:__ Umožňuje připojit vaši aplikaci function app do úložiště git tak, že všechny aktualizace kódu v tomto úložišti aktivuje nasazení. Další informace, podívejte se na [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

__Jak jej používat:__ Pomocí centra pro nasazení na portálu Azure Functions k nastavení publikování ze správy zdrojového kódu. Další informace najdete v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md).

__Kdy ji použít:__ Pomocí správy zdrojového kódu je nejvhodnějším postupem pro týmy spolupracovat na jejich aplikace function App, a to je skvělá možnost, která umožňuje složitější kanály nasazení.

### <a name="local-git"></a>Místní git

__Co to dělá:__ Umožňuje vložit kódu z místního počítače do služby Azure Functions pomocí Gitu.

__Jak jej používat:__ Postupujte podle pokynů na adrese [místní nasazení z Gitu do služby Azure App Service](../app-service/deploy-local-git.md).

__Kdy ji použít:__ Obecně se doporučuje, jiné metody nasazení. Při publikování z místního gitu, je nutné [ruční synchronizaci triggery](#trigger-syncing).

### <a name="cloud-sync"></a>Cloudové synchronizace

__Co to dělá:__ Umožňuje synchronizovat obsah z Dropboxu a Onedrivu do služby Azure Functions.

__Jak jej používat:__ Postupujte podle pokynů v [synchronizuje obsah z cloudové složky](../app-service/deploy-content-sync.md).

__Kdy ji použít:__ Obecně se doporučuje, jiné metody nasazení. Při publikování pomocí synchronizace cloudu, je nutné [ruční synchronizaci triggery](#trigger-syncing).

### <a name="ftp"></a>FTP

__Co to dělá:__ Umožňuje přímo přenášet soubory do služby Azure Functions.

__Jak jej používat:__ Postupujte podle pokynů v [nasadit obsah pomocí FTP/s](../app-service/deploy-ftp.md).

__Kdy ji použít:__ Obecně se doporučuje, jiné metody nasazení. Při publikování s využitím protokolu FTP, je nutné [ruční synchronizaci triggery](#trigger-syncing).

### <a name="portal-editing"></a>Úpravy portálu

__Co to dělá:__ Pomocí editoru založené na portálu můžete přímo upravovat soubory na vaši aplikaci function app (nasazení v podstatě kdykoliv při kliknutí na **Uložit**).

__Jak jej používat:__ Aby bylo možné upravit funkce na webu Azure Portal, musí mít [vytvořená na portálu funkcí](functions-create-first-azure-function.md). Pomocí jiné metody nasazení je funkce jen pro čtení a zabrání v pokračování portálu úpravy, chcete-li zachovat jediný zdroj pravdivých informací. Pokud chcete vrátit do stavu, ve kterém můžete upravit soubory pomocí webu Azure portal, můžete ručně vypnout režim úprav zpět `Read/Write` a odebrat všechna nastavení souvisejících s nasazením aplikace (jako je `WEBSITE_RUN_FROM_PACKAGE`).

#### <a name="portal-editing-availability"></a>Portál úpravy dostupnosti

| | Windows, spotřebě | Windows Premium (Preview) | Windows Dedicated | Spotřeba Linux (Preview) | Linux Dedicated |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>1</sup>|
| TypeScript (Node.js) | | | | | |
| C# | | | | | |
| C#Skript |✔|✔|✔| |✔<sup>1</sup>|
| F# | | | | | |
| Java | | | | | |
| Python (Preview) | | | | | |
| PowerShell (Preview) |✔|✔|✔| | |

<sup>1</sup> úpravy portál je povolená pouze protokol HTTP a časovač aktivačních událostí pro službu Functions v Linuxu pomocí vyhrazených plánů.

__Kdy ji použít:__ Na portálu je skvělým způsobem, jak začít s využitím Azure Functions, ale pro všechny intenzivnější vývojové práce pomocí klienta nástroje doporučujeme:

* [Začínáme s využitím VS Code](functions-create-first-function-vs-code.md)
* [Začínáme s využitím Azure Functions Core Tools](functions-run-local.md)
* [Začínáme s využitím sady Visual Studio](functions-create-your-first-function-visual-studio.md)

## <a name="other-relevant-information"></a>Další důležité informace

### <a name="deployment-slots"></a>Nasazovací sloty

Když nasadíte aplikaci function app do Azure, můžete nasadit do slotu namísto samostatného nasazení přímo do produkčního prostředí. Další informace o sloty nasazení, najdete v části [dokumentace ke službě Azure App Service sloty](../app-service/deploy-staging-slots.md).

#### <a name="deployment-slots-levels-of-support"></a>Úrovně podpory pro sloty nasazení

Existují dvě úrovně podpory:

* _Všeobecně dostupná (GA)_ – plně podporované a schválena pro použití v produkčním prostředí.
* _Ve verzi Preview_ – ještě není podporované, ale očekávala se pro dosažení všeobecné dostupnosti stav v budoucnu.

| Plán hostování/OS | Úroveň podpory |
| --------------- | ------ |
| Windows, spotřebě | Náhled |
| Windows Premium (Preview) | Náhled |
| Windows Dedicated | Obecně dostupná |
| Využití systému Linux | Nepodporovaný |
| Linux Dedicated | Obecně dostupná |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md)
> [nepřetržité doručování pomocí Azure DevOps](functions-how-to-azure-devops.md)
> [Zip nasazení pro službu Azure Functions](deployment-zip-push.md) 
>  [Spouštění Azure Functions ze souboru balíčku](run-functions-from-deployment-package.md)
> [automatizace nasazování prostředků pro vaši aplikaci funkcí ve službě Azure Functions](functions-infrastructure-as-code.md)
