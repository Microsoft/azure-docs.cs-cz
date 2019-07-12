---
title: Použití nasazení softwaru ve službě Azure App Service
description: Nasazení System Center v Azure DevOps, zjednodušuje zřízení robustního kanálu Azure DevOps pro vaše aplikace
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854635"
---
# <a name="deployment-center-launcher"></a>Spouštěč nasazení System Center

Nasazení System center v Azure DevOps, zjednodušuje nahoru robustního kanálu DevOps pro vaše aplikace. Ve výchozím nastavení nakonfiguruje kanálu DevOps pro vaše aplikace aktualizace nasadit do clusteru kubernetes. Můžete rozšířit výchozí nakonfigurovanou DevOps kanálu a přidejte bohatší možnosti DevOps – schválení před nasazením, zřízení dalších prostředků Azure, spouštění skriptů, upgrade vaší aplikace nebo i další ověřovací testy spuštěné.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Konfigurace kanálu DevOps pro vaše aplikace aktualizace nasadit do clusteru k8s
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete jej získat bezplatná [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Cluster Azure Kubernetes Service (AKS)

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com/)

1. Vyberte [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) tlačítko v pravém horním rohu webu Azure portal v nabídce.

1. K vytvoření clusteru AKS, spusťte následující příkazy.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Nasazení aktualizací aplikací do clusteru K8s

1. Přejděte do výše vytvořili skupinu prostředků.

1. Vyberte AKS cluster a v levém okně nastavení klikněte na **Deployment Center (preview)** . Klikněte na **Začínáme**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Zvolte umístění kódu a klikněte na tlačítko **Další**. V současné době jsou podporované úložiště, **[úložišť Azure](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** a **Githubu**. Provedením následujících kroků na základě výběru v úložišti.

    Úložiště Azure je sada nástrojů správy verzí, které můžete použít ke správě vašeho kódu. Zda softwarového projektu je velké nebo malé, co nejdříve pomocí správy verzí je vhodné.

    - **Úložiště Azure**: Zvolte úložiště z existující projekt a organizaci.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorizace a vybrat úložiště pro váš účet GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. My budeme analýzy úložiště a zjišťování vašem souboru Dockerfile. Pokud chcete provést aktualizaci, můžete upravit počet identifikovaných port.

    ![Nastavení aplikace](media/deployment-center-launcher/application-settings.png)

    Pokud úložiště obsahuje soubor Dockerfile, systém zobrazí zprávu do jednoho potvrzení. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Vyberte nebo vytvořte existující registr kontejneru a klikněte na **Dokončit**. Kanálu se automaticky vytvoří a zařadit sestavení do fronty [kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Kanály Azure je Cloudová služba, která vám pomůže se automaticky sestavit a otestovat kód projektu a zpřístupnit ostatním uživatelům. Kanály Azure kombinuje kontinuální integrace (CI) a průběžné doručování (CD) na neustále a konzistentní testování a vytváření kódu a odešle ho do libovolného cílového.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Klikněte na odkaz zobrazíte probíhající kanálu.

1. Úspěšné protokoly se zobrazí, jak je znázorněno po dokončení nasazení.

    ![Logs](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Nasazení System center konfiguruje organizaci Azure DevOps CI / CD kanálu automaticky. Kanál můžete prozkoumat a přizpůsobit. 

1. Přejdete na řídicí panel centra pro nasazení.  

1. Klikněte na číslo sestavení ze seznamu úspěšné protokoly zobrazíte kanálu sestavení pro projekt. 

1. Klikněte na tlačítko ellipsis(...) v pravém horním rohu. Zobrazí se nabídka ukazuje několik možností, jako je například zařazování nového sestavení, zachování sestavení a úprava kanálu sestavení. Zvolte **upravit kanál**. 

1. U vašeho kanálu sestavení v tomto podokně můžete prozkoumat různé úlohy. Sestavení provádí různé úlohy, jako je například shromažďování zdroje z úložiště Git, vytvoření bitové kopie, nahráním image do registru kontejneru a publikování výstupy, které se používají pro nasazení.

1. Vyberte název sestavení kanálu v horní části kanálu sestavení.

1. Změnit název kanálu sestavení na něco více popisné, vyberte **Uložit & frontu**a pak vyberte **Uložit**.

1. Vyberte **historie** v rámci vašeho kanálu sestavení. Toto podokno zobrazuje záznam auditovaných nedávné změny sestavení. Azure DevOps sleduje změny provedené do kanálu sestavení a lze porovnávat verze.

1. Zvolte **triggery**. Volitelně můžete větví můžete zahrnuty nebo vyloučeny ze procesu CI.

1. Zvolte **uchování**. Můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení v závislosti na vašem scénáři.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

Nasazení System center vytvoří a nakonfiguruje automaticky nezbytných kroků k vašemu předplatnému Azure z vaší organizace Azure DevOps. Tyto kroky zahrnují nastavení připojení služby Azure k ověření vašeho předplatného Azure s Azure DevOps. Automatizace také vytvoří kanál pro vydávání verzí, který poskytuje CD do Azure.

1. Zvolte **kanály**a klikněte na tlačítko **vydání**.

1. Chcete-li upravit kanál pro vydávání verzí, klikněte na tlačítko **upravit** .

1. Vyberte **vyřadit** z **artefakty**. V předchozích krocích vytvoří kanál konstrukce, které můžete prozkoumat výstup pro artefakt. 

1. Vyberte **průběžné nasazování** aktivační události na pravé straně **vyřadit** ikonu. Tento kanál pro vydávání verzí má povoleno CD aktivační událost, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat pro vaše nasazení vyžaduje ruční spuštění aktivační události.

1. Pokud chcete prozkoumat všechny úlohy pro vašeho kanálu, klikněte na **úlohy**. Vydání nastaví tiller prostředí, nakonfiguruje imagePullSecrets, nainstaluje nástroje Helm a nasadí do clusteru K8s grafům helmu.

1. Pokud chcete zobrazit historii vydané verze, klikněte na **zobrazit verze**. 

1. Pokud chcete zobrazit souhrn, klikněte na **vydání**. Klikněte na některý z fáze k prozkoumání více nabídek, jako je například souhrn, vydané verze přidružené pracovní položky a testy. 

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu, které jsou spojené s jejím nasazením. Porovnání verzí zobrazíte potvrzení rozdíly mezi nasazeními.

1. Vyberte **Protokoly**. Protokoly obsahovat užitečné informace. Během a po nasazení můžete je zobrazit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Pomocí funkce Delete na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup

Tyto kanály buildu a verze můžete volitelně upravit tak, aby splňovaly požadavky vašeho týmu. Tento vzor CI/CD můžete také použít jako šablonu pro své další kanály. V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace kanálu DevOps pro vaše aplikace aktualizace nasadit do clusteru k8s
> * Prozkoumání kanálu CI
> * Prozkoumání kanálu CD
> * Vyčištění prostředků
