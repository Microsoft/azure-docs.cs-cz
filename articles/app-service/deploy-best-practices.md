---
title: Osvědčené postupy pro nasazení
description: Přečtěte si o klíčových mechanismech nasazení do služby Azure App Service. Najděte doporučení specifická pro jazyk a další upozornění.
keywords: azure app service, webová aplikace, nasazení, nasazení, kanály, sestavení
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770697"
---
# <a name="deployment-best-practices"></a>Doporučené postupy nasazení

Každý vývojový tým má jedinečné požadavky, které mohou ztížit implementaci kanálu efektivního nasazení v jakékoli cloudové službě. Tento článek představuje tři hlavní součásti nasazení do služby App Service: zdroje nasazení, sestavení kanály a mechanismy nasazení. Tento článek také popisuje některé osvědčené postupy a tipy pro konkrétní jazykové zásobníky.

## <a name="deployment-components"></a>Součásti nasazení

### <a name="deployment-source"></a>Zdroj nasazení

Zdroj nasazení je umístění kódu aplikace. Pro produkční aplikace je zdroj nasazení obvykle úložiště hostované softwarem pro správu verzí, jako je [GitHub, BitBucket nebo Azure Repos](deploy-continuous-deployment.md). Pro scénáře vývoje a testování může být zdrojem nasazení [projekt v místním počítači](deploy-local-git.md). Služba App Service také podporuje [složky OneDrive a Dropbox](deploy-content-sync.md) jako zdroje nasazení. Zatímco cloudové složky mohou usnadnit zahájení používání služby App Service, obvykle se nedoporučuje používat tento zdroj pro produkční aplikace na podnikové úrovni. 

### <a name="build-pipeline"></a>Kanál buildu

Jakmile se rozhodnete pro zdroj nasazení, dalším krokem je výběr kanálu sestavení. Kanál sestavení přečte zdrojový kód ze zdroje nasazení a provede řadu kroků (například kompilaci kódu, minifying HTML a JavaScript, spuštěné testy a součásti balení), aby se aplikace dostala do spustitelného stavu. Konkrétní příkazy prováděné kanálem sestavení závisí na zásobníku jazyka. Tyto operace lze provést na serveru sestavení, jako je například Azure Pipelines, nebo spustit místně.

### <a name="deployment-mechanism"></a>Mechanismus nasazení

Mechanismus nasazení je akce, která se používá k vložíní vytvořené aplikace do adresáře */home/site/wwwroot* vaší webové aplikace. Adresář */wwwroot* je připojené úložiště sdílené všemi instancemi webové aplikace. Když mechanismus nasazení vloží vaši aplikaci do tohoto adresáře, vaše instance obdrží oznámení o synchronizaci nových souborů. Služba App Service podporuje následující mechanismy nasazení:

- Kudu koncové body: [Kudu](https://github.com/projectkudu/kudu/wiki) je open source vývojářský nástroj produktivity, který běží jako samostatný proces ve službě Windows App Service a jako druhý kontejner v Linux App Service. Kudu zpracovává průběžné nasazení a poskytuje koncové body HTTP pro nasazení, jako je například zipdeploy.
- FTP a WebDeploy: Pomocí [vašeho webu nebo pověření uživatele](deploy-configure-credentials.md)můžete nahrávat soubory přes [FTP](deploy-ftp.md) nebo WebDeploy. Tyto mechanismy neprocházejí Kudu.  

Nástroje pro nasazení, jako jsou moduly plug-in Azure Pipelines, Jenkins a editor, používají jeden z těchto mechanismů nasazení.

## <a name="use-deployment-slots"></a>Použití slotů pro nasazení

Kdykoli je to možné, použijte [sloty nasazení](deploy-staging-slots.md) při nasazování nového produkčního sestavení. Když používáte úroveň Standard App Service Plan nebo lepší, můžete nasadit aplikaci do pracovního prostředí, ověřit změny a provést kouřové testy. Až budete připraveni, můžete vyměnit pracovní a produkční sloty. Operace prohození zahřeje potřebné instance pracovníka tak, aby odpovídaly vašemu výrobnímu měřítku, čímž eliminuje prostoje.

### <a name="continuously-deploy-code"></a>Průběžně nasazovat kód

Pokud váš projekt určil větve pro testování, QA a pracovní, pak každá z těchto větví by měla být průběžně nasazena do pracovního slotu. (Tento návrh se označuje jako [návrh Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) To umožňuje zúčastněným stranám snadno posoudit a otestovat nasazenou větev. 

Průběžné nasazení by nikdy nemělo být povoleno pro produkční slot. Místo toho by vaše výrobní větev (často hlavní) měla být nasazena do neprodukčního slotu. Až budete připraveni uvolnit základní větev, vyměňte ji do produkčního slotu. Přepnutí do produkčního prostředí – namísto nasazení do produkčního prostředí – zabraňuje prostojům a umožňuje vrátit změny zpět dalším prohozením. 

![Vizuální použití slotu](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Průběžné nasazování kontejnerů

Pro vlastní kontejnery z Dockeru nebo jiných registrů kontejnerů nasaďte bitovou kopii do pracovního slotu a přeměňte na produkční, abyste zabránili prostojům. Automatizace je složitější než nasazení kódu, protože je nutné posunout bitovou kopii do registru kontejneru a aktualizovat značku bitové kopie ve webové aplikaci.

Pro každou větev, kterou chcete nasadit do patice, nastavte automatizaci tak, aby při každém potvrzení větve udělala následující kroky.

1. **Vytvořte a označte bit ovou bitovou kopii**. Jako součást kanálu sestavení označte bitovou kopii id potvrzení git, časovým razítkem nebo jinými identifikovatelnými informacemi. Je nejlepší nepoužívat výchozí "nejnovější" značku. V opačném případě je obtížné trasovat zpět, jaký kód je aktuálně nasazen, což ztěžuje ladění.
1. **Zatlačte na označený obrázek**. Jakmile je bitová kopie vytvořena a označena, kanál odešle bitovou kopii do našeho registru kontejnerů. V dalším kroku slot nasazení bude vyžádat tagované bitové kopie z registru kontejneru.
1. **Aktualizujte slot pro nasazení novou značkou bitové kopie**. Po aktualizaci této vlastnosti se web automaticky restartuje a vytáhne novou bitovou kopii kontejneru.

![Vizuální použití slotu](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Níže jsou uvedeny příklady pro běžné architektury automatizace.

### <a name="use-azure-devops"></a>Použití Azure DevOps

Služba App Service má [integrované průběžné doručování](deploy-continuous-deployment.md) kontejnerů prostřednictvím Centra nasazení. Přejděte do aplikace na [webu Azure Portal](https://portal.azure.com/) a v části **Nasazení**vyberte **Centrum nasazení** . Podle pokynů vyberte úložiště a pobočku. Tím nakonfigurujete kanál sestavení a vydání DevOps tak, aby automaticky vytvářel, označoval a nasazoval kontejner, když jsou nové revize odesílány do vybrané větve.

### <a name="use-github-actions"></a>Použití akcí GitHubu

Můžete také automatizovat nasazení kontejneru [pomocí akce GitHub](containers/deploy-container-github-action.md).  Níže uvedený soubor pracovního postupu vytvoří a označí kontejner s ID potvrzení, zasune jej do registru kontejnerů a aktualizuje zadanou patici webu novou značkou bitové kopie.

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
    - uses: actions/checkout@master

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

### <a name="use-other-automation-providers"></a>Použití jiných poskytovatelů automatizace

Kroky uvedené výše platí pro jiné automatizační nástroje, jako je CircleCI nebo Travis CI. Je však nutné použít Azure CLI k aktualizaci slotů nasazení s novými značkami bitové kopie v posledním kroku. Chcete-li použít rozhraní příkazového příkazu Azure ve skriptu automatizace, vygenerujte instanční objekt pomocí následujícího příkazu.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Ve skriptu se `az login --service-principal`přihlaste pomocí a zadejte informace o objektu zabezpečení. Potom můžete `az webapp config container set` nastavit název kontejneru, značku, adresu URL registru a heslo registru. Níže jsou uvedeny některé užitečné odkazy pro vás k vytvoření kontejneru CI procesu.

- [Jak se přihlásit do azure cli na Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Důležité informace týkající se jazyka

### <a name="java"></a>Java

Použijte Kudu [zipdeploy/](deploy-zip.md) API pro nasazení aplikací JAR a [wardeploy/](deploy-zip.md#deploy-war-file) pro aplikace WAR. Pokud používáte Jenkins, můžete použít tato api přímo ve fázi nasazení. Další informace najdete v [tomto článku](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Node

Ve výchozím nastavení Kudu provede kroky sestavení pro`npm install`aplikaci uzlu ( ). Pokud používáte službu sestavení, jako je Azure DevOps, pak sestavení Kudu je zbytečné. Chcete-li zakázat sestavení Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`vytvořte nastavení `false`aplikace , s hodnotou .

### <a name="net"></a>.NET 

Ve výchozím nastavení Kudu provede kroky sestavení pro`dotnet build`vaši aplikaci .NET ( ). Pokud používáte službu sestavení, jako je Azure DevOps, pak sestavení Kudu je zbytečné. Chcete-li zakázat sestavení Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`vytvořte nastavení `false`aplikace , s hodnotou .

## <a name="other-deployment-considerations"></a>Další důležité informace o nasazení

### <a name="local-cache"></a>Místní mezipaměť

Obsah služby Azure App Service se uložených ve službě Azure Storage a je vynořil a trvanlivým způsobem jako sdílené složky obsahu. Některé aplikace však potřebují pouze vysoce výkonné úložiště obsahu jen pro čtení, které mohou běžet s vysokou dostupností. Tyto aplikace mohou těžit z použití [místní mezipaměti](overview-local-cache.md). Místní mezipaměť se nedoporučuje pro weby pro správu obsahu, jako je WordPress.

Vždy používejte místní mezipaměť ve spojení s [sloty nasazení,](deploy-staging-slots.md) abyste zabránili prostojům. Informace o společném používání těchto funkcí naleznete v [této části.](overview-local-cache.md#best-practices-for-using-app-service-local-cache)

### <a name="high-cpu-or-memory"></a>Vysoký procesor nebo paměť

Pokud váš plán služby App Service používá více než 90 % dostupného procesoru nebo paměti, může mít základní virtuální počítač potíže se zpracováním vašeho nasazení. V takovém případě dočasně vertikálně navertit kapacitu počtu instancí k provedení nasazení. Po dokončení nasazení můžete vrátit počet instancí na jeho předchozí hodnotu.

Další informace o doporučených postupech najdete v centru [aplikace Diagnostika služby App Service](https://docs.microsoft.com/azure/app-service/overview-diagnostics) a vyhledejte užitečné postupy, které jsou specifické pro váš prostředek.

- Přejděte do webové aplikace na [webu Azure Portal](https://portal.azure.com).
- Klikněte na **Diagnostika a řešení problémů** v levém navigačním panelu, který otevře Diagnostika služby App Service.
- Zvolte dlaždice domovské stránky **doporučené postupy.**
- Chcete-li zobrazit aktuální stav aplikace s ohledem na tyto osvědčené postupy, klepněte na tlačítko **Doporučené postupy pro dostupnost & výkonu** nebo doporučené postupy pro optimální **konfiguraci.**

Pomocí tohoto odkazu můžete také přímo otevřít diagnostiku `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`služby App Service pro váš prostředek: .
