---
title: Vytvoření kanálu CI/CD pro aplikaci PWA pomocí GatsbyJS a Azure DevOps Starter
description: Naučte se vytvářet NodeJS progresivní webovou aplikaci (PWA) s využitím GatsbyJS a zjednodušeným prostředím pro vytváření Azure DevOps Starter.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: angrobe
ms.custom: mvc, devx-track-js
ms.openlocfilehash: fa6d0f78f984c5e71ccdbcada45ea1bc50be9e95
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318615"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Vytvoření kanálu CI/CD v Azure Pipelines pro Node.js pomocí Azure DevOps Starter

V tomto rychlém startu vytvoříte NodeJS progresivní webovou aplikaci (PWA) pomocí [GatsbyJS](https://www.gatsbyjs.org/) a zjednodušeným prostředím pro vytváření Azure DevOps Starter. Po dokončení budete mít pro aplikaci PWA v Azure Pipelines kanál průběžné integrace (CI) a průběžného doručování (CD). Azure DevOps Starter nastavuje, co potřebujete pro vývoj, nasazování a monitorování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Organizace [Azure DevOps](https://azure.microsoft.com/services/devops/) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Starter vytvoří kanál CI/CD v Azure Pipelines. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Starter také vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a v levém podokně vyberte **vytvořit prostředek**. 

   ![Vytvoření prostředku Azure v Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Do vyhledávacího pole zadejte **DevOps Starter**a pak vyberte. Kliknutím na **Přidat** vytvořte nový.

    ![Řídicí panel Starter DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vyberte ukázkovou aplikaci v Node.js.   

    ![Výběr Node.js ukázka](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Výchozí architektura ukázky je **Express.js**. Změňte výběr na **jednoduchá Node.js aplikace** a pak vyberte **Další**. 

    ![Výběr jednoduché aplikace Node.js](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Cíle nasazení, které jsou k dispozici v tomto kroku, jsou vydány rozhraním Application Framework vybraným v kroku 2. V tomto příkladu je **Webová aplikace Windows** výchozím cílem nasazení. Nechejte **Web App for Containers** sadu a vyberte **Další**.

    ![Vybrat cíl nasazení](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Konfigurace názvu projektu a předplatného Azure

1. V posledním kroku pracovního postupu pro vytváření DevOps Starter přiřadíte název projektu, vyberete předplatné Azure a vyberete **Hotovo**.  

    ![Přiřaďte název projektu a vyberte předplatné.](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Stránka souhrnu se zobrazí během sestavení projektu a aplikace je nasazena do Azure. Po krátké době se projekt vytvoří ve vaší [organizaci Azure DevOps](https://dev.azure.com/) , která obsahuje úložiště Git, desku Kanban, kanál nasazení, testovací plány a artefakty, které vaše aplikace vyžaduje.  

## <a name="managing-your-project"></a>Správa projektu

1. Přejděte na **všechny prostředky** a vyhledejte DevOps Starter. Vyberte **DevOps Starter**.

    ![Řídicí panel Azure DevOps v seznamu prostředků](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Jste přesměrováni na řídicí panel, který poskytuje přehled o vaší domovské stránce projektu, úložišti kódu, kanálu CI/CD a odkaz na spuštěnou aplikaci. Vyberte **domovskou stránku projektu** pro zobrazení vaší aplikace v **Azure DevOps** a na další kartě prohlížeče vyberte **koncový bod aplikace** a zobrazte si živou ukázkovou aplikaci. Tuto ukázku jsme později změnili na použití GatsbyJS vygenerovaného v aplikaci PWA.

    ![Řídicí panel Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Z projektu Azure DevOps můžete pozvat členy týmu, aby spolupracovali a navázali kanbanovou desku, abyste mohli začít sledovat vaši práci. Další informace najdete [tady](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Přehled služby Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Naklonujte úložiště a nainstalujte si Gatsby PWA.

DevOps Starter vytvoří úložiště Git v Azure Repos nebo GitHubu. Tento příklad vytvořil úložiště Azure. Dalším krokem je klonování úložiště a provádění změn.

1. V **projektu DevOps** **Vyberte úložiště** a pak klikněte na **klonovat**.  Existují různé mechanismy pro klonování úložiště Git na plochu.  Vyberte si ten, který odpovídá vašemu prostředí pro vývoj.  

    ![Klonování úložiště](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Po naklonování úložiště na plochu proveďte některé změny šablony Starter. Začněte tím, že z terminálu nainstalujete rozhraní příkazového řádku GatsbyJS.

   ```powershell
    npm install -g gatsby
   ```

1. Z terminálu přejděte do kořenového adresáře úložiště. Měl by obsahovat tři složky, které vypadají takto:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Nechceme, aby se všechny soubory ve složce aplikace nahradily Gatsby Starter. Chcete-li zkrátit, spusťte následující příkazy v sekvenci.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. K vygenerování ukázkové aplikace PWA použijte rozhraní příkazového řádku Gatsby. Spusťte `gatsby new` z terminálu a spusťte Průvodce PWA a vyberte `gatsby-starter-blog` pro úvodní šablonu. Měl by vypadat podobně jako tato ukázka:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Teď máte složku s názvem `my-gatsby-project` . Přejmenujte ji na `Application` a zkopírujte `Dockerfile` do ní.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. V oblíbeném editoru otevřete souboru Dockerfile a změňte první řádek z `FROM node:8` na `FROM node:12` . Tato změna zajistí, že váš kontejner používá Node.js verze 12. x namísto verze 8. x. GatsbyJS vyžaduje moderní verze Node.js.

1. V dalším kroku otevřete package.jsv souboru ve složce aplikace a upravte [pole skripty](https://docs.npmjs.com/files/package.json#scripts) , aby se zajistilo, že vývojové a provozní servery budou sledovat všechna dostupná síťová rozhraní (například 0.0.0.0) a port 80. Bez těchto nastavení nemůže služba Container App Service směrovat provoz do vaší aplikace Node.js spuštěné v rámci vašeho kontejneru. `scripts`Pole by se mělo podobat následujícímu. Konkrétně je třeba změnit `develop` `serve` cíle, a `start` z jejich výchozího nastavení.

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

1. Před potvrzením kódu v předchozí části udělejte změny v kanálech sestavení a vydání. Upravte kanál sestavení a aktualizujte úlohu uzlu tak, aby používala Node.js verze 12. x. V poli **verze úlohy** nastavte hodnotu 1. x a pole **verze** na hodnotu 12. x.

    ![Aktualizovat Node.js na 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. V tomto rychlém startu Nevytváříme testy jednotek a tyto kroky v našem kanálu sestavení zakážeme. Při psaní testů můžete tyto kroky znovu povolit. Kliknutím pravým tlačítkem myši vyberte úkoly s popiskem **instalovat testovací závislosti** a **Spusťte testy jednotek** a zakažte je.

    ![Zakázat testy sestavení](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Upravte svůj kanál pro vydávání verzí.

    ![Úprava kanálu pro vydávání verzí](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Stejně jako u kanálu sestavení změňte úlohu uzlu tak, aby používala 12. x, a zakažte tyto dvě testovací úlohy. Vaše verze by měla vypadat podobně jako na tomto snímku obrazovky.

    ![Dokončený kanál vydání](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Na levé straně prohlížeče otevřete soubor **views/index. pug** .

1. Vyberte **Upravit**a pak proveďte změnu nadpisu H2.  Například zadejte Začínáme hned **s Azure DevOps Starter** nebo Udělejte nějakou jinou změnu.

1. Vyberte **Potvrdit**a potom změny uložte.

1. V prohlížeči přejdete na řídicí panel DevOps Starter.   
Nyní by se mělo zobrazit sestavení probíhá. Změny, které jste provedli, jsou automaticky vytvořeny a nasazeny prostřednictvím kanálu CI/CD.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Potvrďte provedené změny a Projděte si kanál Azure CI/CD.

V předchozích dvou krocích jste do úložiště Git přidali Gatsby vygenerovanou aplikaci PWA a upravili jste kanály pro sestavení a nasazení kódu. Kód můžeme potvrdit a sledovat jeho průběh prostřednictvím kanálu sestavení a vydání.

1. Z kořenového adresáře úložiště Git vašeho projektu v terminálu spusťte následující příkazy, které Nahrajte svůj kód do projektu Azure DevOps:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Sestavení se spustí hned po `git push` dokončení. Průběh můžete sledovat na **řídicím panelu Azure DevOps**.

3. Po několika minutách by se měly dokončit vaše kanály sestavení a vydání a aplikace PWA by se měla nasadit do kontejneru. Klikněte na odkaz **koncový bod aplikace** z řídicího panelu výše a měli byste vidět projekt Gatsby Starter pro blogy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete odstranit Azure App Service a další související prostředky, které jste vytvořili, když už tyto prostředky nepotřebujete. Použijte funkci **Odstranit** na řídicím panelu DevOps Starter.

## <a name="next-steps"></a>Další kroky

Při konfiguraci procesu CI/CD se automaticky vytvoří kanály sestavení a vydání. Tyto kanály sestavení a vydání můžete změnit tak, aby vyhovovaly potřebám vašeho týmu. Další informace o kanálu CI/CD najdete tady:

> [!div class="nextstepaction"]
> [Přizpůsobení procesu CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

