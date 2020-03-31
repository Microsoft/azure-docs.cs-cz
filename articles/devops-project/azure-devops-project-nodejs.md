---
title: Vytvoření kanálu CI/CD pro pwa s projekty GatsbyJS a Azure DevOps
description: DevOps Projekty usnadňuje začínáme v Azure. Pomůže vám v několika rychlých krocích spustit aplikaci v libovolné službě Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: angrobe
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 508a61d6bbb00692855e09601aed67ab3be9cc8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78208945"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Úvodní příručka: Vytvoření kanálu CI/CD v Azure Pipelines pro Node.js s azure devops projekty
V tomto rychlém startu vytvoříte progresivní webovou aplikaci NodeJS (PWA) pomocí [GatsbyJS](https://www.gatsbyjs.org/) a zjednodušeného prostředí pro vytváření projektů Azure DevOps. Po dokončení máte kanál průběžné integrace (CI) a průběžného doručování (CD) pro vaše PWA v Azure Pipelines. Azure DevOps Projects nastavuje, co potřebujete pro vývoj, nasazování a monitorování.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Organizace [Azure DevOps.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projekty vytvoří kanál CI/CD v Azure pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure dle vašeho výběru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a v levém podokně vyberte **Vytvořit prostředek**. 

   ![Vytvoření prostředku Azure na webu Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Vyhledejte a vyberte **DevOps Projects**a pak vyberte **Vytvořit**.

 ![Vytvoření projektu DevOps](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci v Node.js.   

 ![Výběr ukázky Node.js](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Výchozí architektura ukázky je **Express.js**. Změňte výběr na **aplikaci Simple Node.js** a pak vyberte **Další**. 

 ![Výběr aplikace Simple Node.js](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. Cíle nasazení, které jsou k dispozici v tomto kroku, jsou dány rozhraním aplikace vybraným v kroku 2.  V tomto příkladu je výchozí cíl nasazení **windows web appu.**  Ponechte **webovou aplikaci pro kontejnery nastavenou** a vyberte **Další**.

 ![Výběr cíle nasazení](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Konfigurace názvu projektu a předplatného Azure

1. V posledním kroku pracovního postupu vytváření projektu DevOps přiřadíte název projektu, vyberete předplatné Azure a vyberete **Hotovo**.  

 ![Přiřazení názvu projektu a výběr předplatného](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Souhrnná stránka se zobrazí, když je váš projekt vytvořený a vaše aplikace se nasadí do Azure. Po krátkém období se vytvoří projekt ve vaší [organizaci Azure DevOps,](https://dev.azure.com/) která zahrnuje úložiště git, kanbanovou desku, kanál nasazení, testovací plány a artefakty vyžadované vaší aplikací.  

## <a name="managing-your-project"></a>Správa projektu

1. Přejděte na **Všechny zdroje** a najděte svůj projekt DevOps. Vyberte **projekt DevOps**.

![Řídicí panel Azure DevOps v seznamu prostředků](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Budete přesměrováni na řídicí panel, který poskytuje přehled o domovské stránce projektu, úložišti kódu, kanálu CI/CD a odkazu na spuštěnou aplikaci. Vyberte **domovskou stránku projektu,** chcete-li zobrazit aplikaci v **Azure DevOps** a na jiné kartě prohlížeče vyberte **koncový bod aplikace,** chcete-li zobrazit živou ukázkovou aplikaci.  Později změníme tuto ukázku tak, aby chomáč Generovaný soubor PWA gatsbyjs.

![Řídicí panel Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. Z projektu Azure DevOps můžete pozvat členy týmu ke spolupráci a vytvořit kanbanovou nástěnku, která začne sledovat vaši práci.  Další informace naleznete [zde](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Azure DevOps – přehled](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klonujte repo a nainstalujte gatsby PWA

DevOps Projects vytvoří úložiště git v Azure Repos nebo GitHub. Tento příklad vytvořil Azure Repo.  Dalším krokem je klonovat repo a provést změny.

1. V aplikaci **DevOps Project** vyberte **možnost Repos** a klepněte na příkaz **Klonovat**.  Existují různé mechanismy pro klonování úložiště git na plochu.  Vyberte si ten, který vyhovuje vašim zkušenostem s vývojem.  

![Klonování úložiště](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Po naklonování repo na plochu proveďte některé změny ve startovací šabloně. Začněte instalací gatsbyjs CLI z vašeho terminálu.
```powershell
npm install -g gatsby
```

3. Z terminálu přejděte do kořenového adresáře repo. Měl by obsahovat tři složky, které vypadají takto:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Nechceme všechny soubory ve složce Aplikace, protože budeme nahradit startérgatsby. Spusťte následující příkazy v pořadí, abyste jej zkrátili.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Pomocí gatsbyho vykázaného vygenerování ukázkového souboru PWA. Spusťte `gatsby new` z terminálu a spusťte průvodce PWA a vyberte `gatsby-starter-blog` pro svou počáteční šablonu. Měl by se podobat této ukázce:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Nyní máte složku `my-gatsby-project`s názvem . Přejmenujte `Application` jej na `Dockerfile` a zkopírujte do něj.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. V oblíbeném editoru otevřete soubor Dockerfile `FROM node:8` `FROM node:12`a změňte první řádek z na . Tato změna zajistí, že váš kontejner používá Node.js verze 12.x namísto verze 8.x. GatsbyJS vyžaduje modernější verze Node.js.

8. Dále otevřete soubor package.json ve složce Aplikace a upravte [pole skriptů,](https://docs.npmjs.com/files/package.json#scripts) abyste zajistili, že vývojové a produkční servery budou naslouchat na všech dostupných síťových rozhraních (například 0.0.0.0) a portu 80. Bez těchto nastavení aplikace kontejneru služba nemůže směrovat provoz do aplikace Node.js spuštěné uvnitř kontejneru. Pole `scripts` by se mělo podobat tomu, co je níže. Konkrétně chcete změnit `develop`, `serve`a `start` cíle z jejich výchozích hodnot.
```json
  "scripts": {
    "build": "gatsby build",
    "develop": "gatsby develop  -H 0.0.0.0 -p 80",
    "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
    "start": "npm run serve",
    "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
    "clean": "gatsby clean",
    "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
  }
```

## <a name="edit-your-cicd-pipelines"></a>Úprava kanálů CI/CD

1. Před potvrzením kódu v předchozí části proveďte některé změny v kanálech sestavení a vydání. Upravte kanál sestavení a aktualizujte úlohu uzlu tak, aby používala soubor Node.js verze 12.x. Nastavte pole **Verze úkolu** na 1.x a pole **Verze** na 12.x.
![Aktualizovat soubor Node.js na 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. V tomto rychlém startu nevytváříme testy částí a tyto kroky v našem kanálu sestavení zakážeme. Při psaní testů můžete znovu povolit tyto kroky. Klepnutím pravým tlačítkem myši vyberte úlohy označené **Instalace testovacích závislostí** a **Spuštění testů částí** a jejich zakázání.

![Zakázat testy sestavení](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Upravte kanál vydání.
![Úprava kanálu vydání](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Stejně jako u kanálu sestavení změňte úlohu uzlu tak, aby používala soubor 12.x, a zakažte dvě testovací úlohy. Vaše verze by měla vypadat tento snímek obrazovky.

![Kanál dokončené verze](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Na levé straně prohlížeče přejděte do souboru **views/index.pug.**

1. Vyberte **Upravit**a proveďte změnu nadpisu h2.  
    Například zadejte **Začít hned s Azure DevOps Projekty** nebo provést některé další změny.

1. Vyberte **Potvrdit**a uložte změny.

1. V prohlížeči přejděte na řídicí panel Projekty DevOps.   
Nyní byste měli vidět probíhající sestavení. Provedené změny jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Potvrzení změn a prozkoumání kanálu Azure CI/CD

V předchozích dvou krocích jste do úložiště git přidali gatsby generované rozhraní PWA a upravili vaše kanály pro sestavení a nasazení kódu. Můžeme potvrdit kód a sledovat jeho průběh prostřednictvím kanálu sestavení a vydání.

1. Z kořenového adresáře úložiště git vašeho projektu v terminálu spusťte následující příkazy a přesměrujte kód do projektu Azure DevOps:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. Sestavení je spuštěno, `git push` jakmile je dokončeno. Průběh můžete sledovat z **řídicího panelu Azure DevOps**.

![Řídicí panel Azure DevOps v seznamu prostředků](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Po několika minutách by měly být dokončeny kanály sestavení a vydání a vaše aplikace PWA by měla být nasazena do kontejneru. Klikněte na odkaz **koncový bod aplikace** z řídicího panelu výše a měli byste vidět Gatsby startovací projekt pro blogy.



## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už nepotřebujete prostředky. Funkce **Odstranit** na řídicím panelu Projekty devOps.


## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD se automaticky vytvoří kanály sestavení a vydání. Tyto kanály sestavení a vydání můžete změnit tak, aby vyhovovaly potřebám vašeho týmu. Další informace o kanálu CI/CD najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

