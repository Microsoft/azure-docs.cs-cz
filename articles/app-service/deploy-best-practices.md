---
title: Osvědčené postupy pro nasazení
description: Přečtěte si o klíčových mechanismech nasazení na Azure App Service. Vyhledání doporučení specifických pro jazyk a dalších aspektů.
keywords: Azure App Service, Webová aplikace, nasazení, nasazení, kanály, sestavení
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 74bd7bc159f7f5974452adf6b2f51148d869b4ed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589232"
---
# <a name="deployment-best-practices"></a>Osvědčené postupy nasazení

Každý vývojový tým má jedinečné požadavky, které můžou zajistit obtížné implementaci kanálu efektivního nasazení v jakékoli cloudové službě. Tento článek představuje tři hlavní komponenty nasazení do App Service: zdroje nasazení, kanály sestavení a mechanismy nasazení. Tento článek také popisuje některé osvědčené postupy a tipy pro konkrétní jazykové zásobníky.

## <a name="deployment-components"></a>Součásti nasazení

### <a name="deployment-source"></a>Zdroj nasazení

Zdroj nasazení je umístění vašeho kódu aplikace. U produkčních aplikací je zdrojem nasazení obvykle úložiště hostované softwarem pro řízení verzí, jako je [GitHub, Bitbucket nebo Azure Repos](deploy-continuous-deployment.md). Ve scénářích vývoje a testování může být zdrojem nasazení [projekt na vašem místním počítači](deploy-local-git.md). App Service také podporuje [OneDrive a složky Dropboxu](deploy-content-sync.md) jako zdroje nasazení. I když cloudové složky můžou snadno začít pracovat s App Service, obvykle se tento zdroj nedoporučuje používat pro produkční aplikace na podnikové úrovni. 

### <a name="build-pipeline"></a>Kanál buildu

Jakmile se rozhodnete pro zdroj nasazení, je dalším krokem výběr kanálu sestavení. Kanál sestavení přečte zdrojový kód ze zdroje nasazení a provede řadu kroků (například kompilování kódu, minifikace HTML a JavaScriptu, spuštění testů a balení komponent) k získání aplikace ve stavu spustitelný. Konkrétní příkazy spouštěné kanálem sestavení závisí na vašem jazykovém zásobníku. Tyto operace lze spustit na serveru sestavení, například Azure Pipelines, nebo v místním prostředí.

### <a name="deployment-mechanism"></a>Mechanismus nasazení

Mechanismus nasazení je akce, která se používá k umístění vaší sestavené aplikace do adresáře */Home/site/wwwroot* vaší webové aplikace. Adresář */wwwroot* je připojené umístění úložiště sdílené všemi instancemi vaší webové aplikace. Když mechanismus nasazení umístí aplikaci do tohoto adresáře, obdrží vaše instance oznámení o synchronizaci nových souborů. App Service podporuje následující mechanismy nasazení:

- Kudu koncové body: [Kudu](https://github.com/projectkudu/kudu/wiki) je open source nástroj pro zvýšení produktivity vývojářů, který běží jako samostatný proces ve Windows App Service a jako druhý kontejner v App Service systému Linux. Kudu zpracovává nepřetržitá nasazení a poskytuje koncové body HTTP pro nasazení, jako je například zipdeploy.
- FTP a WebDeploy: pomocí svého [webového serveru nebo přihlašovacích údajů uživatele](deploy-configure-credentials.md)můžete soubory nahrávat [přes FTP](deploy-ftp.md) nebo WebDeploy. Tyto mechanismy nejdou přes Kudu.  

Nástroje pro nasazení, jako jsou Azure Pipelines, Jenkinse a moduly plug-in, používají jeden z těchto mechanismů nasazení.

## <a name="use-deployment-slots"></a>Použití slotů nasazení

Kdykoli je to možné, použijte [nasazovací sloty](deploy-staging-slots.md) při nasazení nového výrobního buildu. Pokud používáte úroveň plánu úrovně Standard App Service nebo lepší, můžete aplikaci nasadit do přípravného prostředí, ověřit své změny a provádět testy kouře. Až budete připraveni, můžete vyměnit pracovní a produkční sloty. Operace prohození zahřívá potřebné pracovní instance, aby odpovídaly vašemu produkčnímu měřítku, a tím eliminuje prostoje.

### <a name="continuously-deploy-code"></a>Průběžně nasazovat kód

Pokud váš projekt obsahuje určené větve pro testování, kontrolu a přípravu, pak je třeba každou z těchto větví trvale nasadit do přípravného slotu. (To se označuje jako [Návrh Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) To umožňuje zúčastněným stranám snadno posoudit a otestovat nasazenou větev. 

Průběžné nasazování by nikdy nemělo být povolené pro produkční slot. Místo toho by měla být vaše produkční větev (často hlavní) nasazená do neprodukčního slotu. Až budete připraveni k vydání základní větve, Proměňte ji do produkčního slotu. Přepnutí do produkčního prostředí – místo nasazení do produkčního prostředí zabraňuje výpadkům a umožňuje vrátit zpět změny tím, že se znovu odsadí. 

![Diagram znázorňující tok mezi vývojem, přípravnou a hlavní větví a sloty, na které jsou nasazeny.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Průběžné nasazování kontejnerů

Pro vlastní kontejnery z Docker nebo jiných registrů kontejnerů nasaďte image do přípravného slotu a Proměňte ji do produkčního prostředí, aby nedocházelo k výpadkům. Automatizace je složitější než nasazení kódu, protože je nutné vložit image do registru kontejneru a aktualizovat značku image na WebApp.

Pro každou větev, kterou chcete nasadit do slotu, nastavte automatizaci tak, aby při každém potvrzení na větev provede následující:

1. **Sestavte a označte obrázek**. Jako součást kanálu sestavení označte obrázek pomocí ID potvrzení Git, časového razítka nebo jiných identifikovatelných informací. Doporučujeme nepoužívat výchozí značku "poslední". V opačném případě je obtížné sledovat, jaký kód je aktuálně nasazený, takže ladění bylo mnohem obtížnější.
1. Nahrajte **tagovaný obrázek**. Jakmile je obrázek sestavený a označený, kanál ho nahraje do našeho registru kontejneru. V dalším kroku slot nasazení načte tagovaný obraz z registru kontejneru.
1. **Aktualizujte slot nasazení novou značkou image**. Když se tato vlastnost aktualizuje, lokalita se automaticky restartuje a vyžádá si novou image kontejneru.

![Vizuál využití slotu](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Níže jsou uvedeny příklady pro běžné rozhraní automatizace.

### <a name="use-azure-devops"></a>Použití Azure DevOps

App Service má [integrované průběžné doručování](deploy-continuous-deployment.md) pro kontejnery prostřednictvím centra nasazení. Přejděte do aplikace v [Azure Portal](https://portal.azure.com/) a v části **nasazení** vyberte **centrum nasazení** . Podle pokynů vyberte úložiště a větev. Tím se nakonfiguruje kanál sestavení a verze DevOps pro automatické sestavení, označení a nasazení kontejneru při vložení nových potvrzení do vybrané větve.

### <a name="use-github-actions"></a>Použití akcí GitHubu

Nasazení kontejnerů můžete také automatizovat [pomocí akcí GitHubu](deploy-container-github-action.md).  Níže uvedený soubor pracovního postupu sestaví a označí kontejner s ID potvrzení, nabídne ho do registru kontejneru a aktualizuje zadaný slot webu novou značkou image.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Použití dalších zprostředkovatelů automatizace

Výše uvedené kroky se vztahují na další automatizační nástroje, jako je CircleCI nebo Travis CI. K aktualizaci slotů nasazení pomocí nových značek obrázků v posledním kroku ale musíte použít rozhraní příkazového řádku Azure CLI. Pokud chcete ve svém skriptu Automation použít Azure CLI, vygenerujte instanční objekt pomocí následujícího příkazu.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

V rámci vašeho skriptu se přihlaste pomocí `az login --service-principal` a poskytněte informace o objektu zabezpečení. Pak můžete použít `az webapp config container set` k nastavení názvu kontejneru, značky, adresy URL registru a hesla k registru. Níže najdete několik užitečných odkazů, které vám pomůžou vytvořit kontejner CI.

- [Jak se přihlásit k rozhraní příkazového řádku Azure CLI v kruhu CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Language-Specific hlediska

### <a name="java"></a>Java

Použijte Kudu [zipdeploy/](deploy-zip.md) rozhraní API pro nasazení aplikací jar a [wardeploy/](deploy-zip.md#deploy-war-file) pro aplikace War. Pokud používáte Jenkinse, můžete tato rozhraní API používat přímo ve fázi nasazení. Další informace najdete v [tomto článku](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli).

### <a name="node"></a>Uzel

Ve výchozím nastavení Kudu provádí kroky sestavení pro vaši aplikaci Node ( `npm install` ). Pokud používáte sestavovací službu, jako je například Azure DevOps, sestavení Kudu není nutné. Chcete-li zakázat sestavení Kudu, vytvořte nastavení aplikace `SCM_DO_BUILD_DURING_DEPLOYMENT` s hodnotou `false` .

### <a name="net"></a>.NET 

Ve výchozím nastavení Kudu provádí kroky sestavení pro vaši aplikaci .NET ( `dotnet build` ). Pokud používáte sestavovací službu, jako je například Azure DevOps, sestavení Kudu není nutné. Chcete-li zakázat sestavení Kudu, vytvořte nastavení aplikace `SCM_DO_BUILD_DURING_DEPLOYMENT` s hodnotou `false` .

## <a name="other-deployment-considerations"></a>Další požadavky na nasazení

### <a name="local-cache"></a>Místní mezipaměť

Obsah Azure App Service je uložený v Azure Storage a je vytvořen jako trvalý způsob sdílení obsahu. Některé aplikace ale potřebují jenom vysoce výkonné úložiště obsahu jen pro čtení, které můžou běžet s vysokou dostupností. Tyto aplikace můžou využívat používání [místní mezipaměti](overview-local-cache.md). Pro weby správy obsahu, jako je WordPress, se nedoporučuje místní mezipaměť.

V kombinaci s [sloty nasazení](deploy-staging-slots.md) vždy používejte místní mezipaměť, aby nedocházelo k výpadkům. V [této části](overview-local-cache.md#best-practices-for-using-app-service-local-cache) najdete informace o tom, jak tyto funkce používat dohromady.

### <a name="high-cpu-or-memory"></a>Vysoký procesor nebo paměť

Pokud váš App Service plán používá více než 90% dostupného procesoru nebo paměti, může mít základní virtuální počítač potíže se zpracováním nasazení. Pokud k tomu dojde, dočasná velikost počtu instancí proveďte v případě nasazení. Po dokončení nasazení můžete počet instancí vrátit do předchozí hodnoty.

Další informace o osvědčených postupech najdete v [App Service Diagnostics](./overview-diagnostics.md) , kde najdete osvědčené postupy, které jsou specifické pro váš prostředek.

- Přejděte do webové aplikace v [Azure Portal](https://portal.azure.com).
- Klikněte na **Diagnostika a řešení problémů** v levém navigačním panelu, který otevře diagnostiku App Service.
- Vyberte dlaždici domovské stránky s **doporučenými postupy** .
- Pokud chcete zobrazit aktuální stav aplikace v souvislosti s těmito osvědčenými postupy, klikněte na **osvědčené postupy pro dostupnost & výkon** nebo **osvědčené postupy pro optimální konfiguraci** .

Tento odkaz můžete použít také k přímému otevření App Service diagnostiky pro váš prostředek: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .