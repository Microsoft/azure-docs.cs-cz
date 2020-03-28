---
title: Centrum nasazení pro Azure Kubernetes
description: Centrum nasazení v Azure DevOps zjednodušuje nastavení robustního kanálu Azure DevOps pro vaši aplikaci
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048115"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centrum nasazení pro Azure Kubernetes

Centrum nasazení v Azure DevOps zjednodušuje nastavení robustního kanálu Azure DevOps pro vaši aplikaci. Ve výchozím nastavení Nakonfiguruje Centrum nasazení kanál Azure DevOps pro nasazení aktualizací aplikací do clusteru Kubernetes. Můžete rozšířit výchozí nakonfigurovaný kanál Azure DevOps a také přidat bohatší možnosti: možnost získat schválení před nasazením, zřídit další prostředky Azure, spustit skripty, upgradovat aplikaci a dokonce spustit další ověřovací testy.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Nakonfigurujte kanál Azure DevOps pro nasazení aktualizací aplikací do clusteru Kubernetes.
> * Prozkoumejte kanál průběžné integrace (CI).
> * Zkontrolujte kanál průběžného doručování (CD).
> * Vyčistěte prostředky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Cluster služby Azure Kubernetes (AKS).

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

1. Přihlaste se na svůj [portál Azure](https://portal.azure.com/).

1. Vyberte možnost [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) na pravé straně panelu nabídek na portálu Azure.

1. Chcete-li vytvořit cluster AKS, spusťte následující příkazy:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Nasazení aktualizací aplikací do clusteru Kubernetes

1. Přejděte do skupiny prostředků, kterou jste vytvořili v předchozí části.

1. Vyberte cluster AKS a pak v levém noži vyberte **Centrum nasazení (náhled).** Vyberte **Možnost Začínáme**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Zvolte umístění kódu a vyberte **Další**. Potom vyberte jeden z aktuálně podporovaných úložišť: **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** nebo **GitHub**.

    Azure Repos je sada nástrojů pro správu verzí, které vám pomůžou spravovat váš kód. Ať už je váš softwarový projekt velký nebo malý, je vhodné používat co nejdříve správu verzí.

    - **Azure Repos**: Zvolte úložiště z existujícího projektu a organizace.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorizujte a vyberte úložiště pro svůj účet GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Centrum nasazení analyzuje úložiště a detekuje váš soubor Dockerfile. Pokud chcete aktualizovat dockerfile, můžete upravit identifikované číslo portu.

    ![Nastavení aplikace](media/deployment-center-launcher/application-settings.png)

    Pokud úložiště neobsahuje Dockerfile, systém zobrazí zprávu k potvrzení jednoho.

    ![Soubor dockeru](media/deployment-center-launcher/dockerfile.png)

1. Vyberte existující registr kontejnerů nebo ho vytvořte a pak vyberte **Dokončit**. Kanál se vytvoří automaticky a zařadí sestavení do fronty v [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines je cloudová služba, kterou můžete použít k automatickému sestavení a testování projektu kódu a jeho zpřístupnění ostatním uživatelům. Azure Pipelines kombinuje průběžnou integraci a průběžné doručování, aby neustále a konzistentně testovala a vytvářela svůj kód a doručovala ho libovolnému cíli.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Vyberte odkaz pro zobrazení probíhajícího kanálu.

1. Po dokončení nasazení se zobrazí úspěšné protokoly.

    ![Protokoly](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Centrum nasazení automaticky konfiguruje kanál CI/CD organizace Azure DevOps. Kanál lze prozkoumat a přizpůsobit.

1. Přejděte na řídicí panel Centra nasazení.  

1. Vyberte číslo sestavení ze seznamu úspěšných protokolů pro zobrazení kanálu sestavení pro váš projekt.

1. V pravém horním rohu vyberte tři tečky (...). Nabídka zobrazuje několik možností, jako je například řazení do fronty na nové sestavení, zachování sestavení a úpravy kanálu sestavení. Vyberte **Upravit kanál**. 

1. V tomto podokně můžete prozkoumat různé úkoly pro kanál sestavení. Sestavení provádí různé úkoly, jako je například shromažďování zdrojů z úložiště Git, vytvoření bitové kopie, odeslání bitové kopie do registru kontejneru a publikování výstupů, které se používají pro nasazení.

1. Vyberte název kanálu sestavení v horní části kanálu.

1. Změňte název kanálu sestavení na něco popisnějšího, vyberte **Uložit & fronty**a pak vyberte **Uložit**.

1. V části kanálu sestavení vyberte **Historie**. Toto podokno zobrazuje záznam auditu nedávných změn sestavení. Azure DevOps monitoruje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **možnost Aktivační události**. Můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**. Můžete určit zásady zachovat nebo odebrat počet sestavení, v závislosti na scénáři.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

Centrum nasazení automaticky vytvoří a nakonfiguruje vztah mezi vaší organizací Azure DevOps a vaším předplatným Azure. Mezi kroky patří nastavení připojení služby Azure k ověření předplatného Azure pomocí Azure DevOps. Automatizovaný proces také vytvoří kanál vydání, který poskytuje průběžné doručování do Azure.

1. Vyberte **Možnost Kanály**a pak **vyberte Verze**.

1. Chcete-li upravit kanál vydání, vyberte **Upravit**.

1. Ze seznamu **Artefakty** vyberte **Přetažení.** V předchozích krocích stavební kanál, který jste zkoumali, vytvoří výstup použitý pro artefakt. 

1. Vyberte aktivační událost **nepřetržitého nasazení** na pravé straně možnosti **Přetažení.** Tento kanál vydání má povolenou aktivační událost CD, která spouští nasazení vždy, když je k dispozici nový artefakt sestavení. Můžete také zakázat aktivační událost vyžadovat ruční spuštění pro vaše nasazení.

1. Chcete-li prozkoumat všechny úkoly pro váš kanál, vyberte **úkoly**. Verze nastaví prostředí kultivátoru, nakonfiguruje `imagePullSecrets` parametr, nainstaluje nástroje Helm a nasadí helmgrafy do clusteru Kubernetes.

1. Chcete-li zobrazit historii verzí, vyberte **možnost Zobrazit vydání**.

1. Chcete-li zobrazit souhrn, vyberte **možnost Uvolnit**. Vyberte některou z fází, chcete-li prozkoumat více nabídek, jako je například souhrn vydání, přidružené pracovní položky a testy. 

1. Vyberte **Potvrzení**. Toto zobrazení zobrazuje potvrzení kódu související s tímto nasazením. Porovnáním verzí zobrazíte rozdíly v potvrzení mezi nasazeními.

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o nasazení, které můžete zobrazit během a po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, můžete odstranit, když je již nepotřebujete. Použijte funkci odstranění na řídicím panelu Projekty DevOps.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Nebo můžete použít tento model CI/CD jako šablonu pro ostatní kanály.
