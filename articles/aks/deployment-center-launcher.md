---
title: Centrum nasazení pro Azure Kubernetes
description: Centrum nasazení v Azure DevOps zjednodušuje nastavení robustního kanálu Azure DevOps pro vaši aplikaci.
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932213"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centrum nasazení pro Azure Kubernetes

Centrum nasazení v Azure DevOps zjednodušuje nastavení robustního kanálu Azure DevOps pro vaši aplikaci. Ve výchozím nastavení centrum nasazení nakonfiguruje kanál Azure DevOps, aby nasadil aktualizace vaší aplikace do clusteru Kubernetes. Můžete rozšíření výchozího nakonfigurovaného kanálu Azure DevOps a také přidat bohatší možnosti: možnost získat před nasazením, zřídit další prostředky Azure, spouštět skripty, upgradovat aplikaci a dokonce i spustit více ověřovacích testů.

V tomto kurzu:

> [!div class="checklist"]
> * Nakonfigurujte kanál Azure DevOps pro nasazení aktualizací vaší aplikace do clusteru Kubernetes.
> * Projděte si kanál průběžné integrace (CI).
> * Projděte si kanál průběžného doručování (CD).
> * Vyčistěte prostředky.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Můžete ho získat zdarma prostřednictvím programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Cluster služby Azure Kubernetes (AKS).

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

1. Přihlaste se ke svému [Azure Portal](https://portal.azure.com/).

1. Vyberte možnost [Cloud Shell](../cloud-shell/overview.md) na pravé straně řádku nabídek v Azure Portal.

1. Pokud chcete vytvořit cluster AKS, spusťte následující příkazy:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Nasazení aktualizací aplikace do clusteru Kubernetes

1. Přejít do skupiny prostředků, kterou jste vytvořili v předchozí části.

1. Vyberte cluster AKS a v levém okně vyberte **centrum nasazení (Preview)** . Vyberte **Začínáme**.

   ![Snímek obrazovky ukazuje Azure Portal se šipkou ukazující na centrum nasazení.](media/deployment-center-launcher/settings.png)

1. Zvolte umístění kódu a vyberte **Další**. Pak vyberte jedno z aktuálně podporovaných úložišť: **[Azure Repos](/azure/devops/repos/index)** nebo **GitHub**.

    Azure Repos je sada nástrojů pro správu verzí, které vám pomůžou spravovat kód. Bez ohledu na to, jestli je váš softwarový projekt velký nebo malý, je dobrý nápad pomocí správy verzí co nejdříve.

    - **Azure Repos**: vyberte úložiště ze stávajícího projektu a organizace.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: autorizovat a vyberte úložiště pro váš účet GitHub.

        ![Animace znázorňuje proces v GitHubu výběru GitHubu jako zdroje a následného výběru úložiště.](media/deployment-center-launcher/github.gif)


1. Centrum nasazení analyzuje úložiště a detekuje vaše souboru Dockerfile. Pokud chcete aktualizovat souboru Dockerfile, můžete označené číslo portu upravit.

    ![Nastavení aplikace](media/deployment-center-launcher/application-settings.png)

    Pokud úložiště neobsahuje souboru Dockerfile, zobrazí systém zprávu pro potvrzení jednoho.

    ![Snímek obrazovky se zobrazením centra nasazení se zprávou nebylo možné najít souboru Dockerfile v úložišti.](media/deployment-center-launcher/dockerfile.png)

1. Vyberte existující registr kontejnerů nebo ho vytvořte a pak vyberte **Dokončit**. Kanál se vytvoří automaticky a zařadí do fronty sestavení v [Azure Pipelines](/azure/devops/pipelines/index).

    Azure Pipelines je cloudová služba, kterou můžete použít k automatickému sestavení a otestování projektu kódu a zpřístupnění pro jiné uživatele. Azure Pipelines kombinuje průběžnou integraci a průběžné doručování pro neustálé a konzistentní testování a sestavování kódu a dodávání do libovolného cíle.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Kliknutím na odkaz zobrazíte probíhající kanál.

1. Po dokončení nasazení se zobrazí úspěšné protokoly.

    ![Snímek obrazovky ukazuje centrum nasazení s verzí-1 označenou zeleným znakem zaškrtnutí.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Prozkoumání kanálu CI

Centrum nasazení automaticky nakonfiguruje kanál CI/CD vaší organizace Azure DevOps. Kanál se dá prozkoumat a přizpůsobit.

1. Přejít na řídicí panel centra nasazení.  

1. Vyberte číslo sestavení ze seznamu úspěšných protokolů pro zobrazení kanálu sestavení pro váš projekt.

1. Vyberte tři tečky (...) v pravém horním rohu. V nabídce se zobrazuje několik možností, jako je například zařazování nového sestavení do fronty, uchování sestavení a úprava kanálu sestavení. Vyberte **Upravit kanál**. 

1. V tomto podokně si můžete prohlédnout různé úlohy pro svůj kanál sestavení. Sestavení provádí různé úkoly, jako je například shromažďování zdrojů z úložiště Git, vytvoření image, vložení image do registru kontejneru a publikování výstupů, které se používají pro nasazení.

1. Vyberte název kanálu sestavení v horní části kanálu.

1. Změňte název kanálu sestavení na výstižnější, vyberte **uložit & fronty** a pak vyberte **Uložit**.

1. V rámci kanálu sestavení vyberte **Historie**. V tomto podokně se zobrazuje záznam pro audit vašich poslední změny sestavení. Azure DevOps sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

1. Vyberte **triggery**. Můžete zahrnout nebo vyloučit větve z procesu CI.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou několik sestavení.

## <a name="examine-the-cd-pipeline"></a>Prozkoumání kanálu CD

Centrum nasazení automaticky vytvoří a nakonfiguruje vztah mezi vaší organizací Azure DevOps a vaším předplatným Azure. Mezi zahrnuté kroky patří nastavení připojení služby Azure pro ověření předplatného Azure pomocí Azure DevOps. Automatizovaný proces také vytvoří kanál pro vydávání verzí, který poskytuje průběžné doručování do Azure.

1. Vyberte **kanály** a pak vybrat **verze**.

1. Chcete-li upravit kanál verze, vyberte možnost **Upravit**.

1. V seznamu **artefaktů** vyberte možnost **drop** . V předchozích krocích vytváří kanál konstrukce, který jste prozkoumali, výstup použitý pro artefakt. 

1. Na pravé straně možnosti **drop** vyberte Trigger **průběžného nasazování** . Tento kanál verze má povolený Trigger CD, který spouští nasazení, kdykoli je k dispozici nový artefakt sestavení. Aktivační událost taky můžete zakázat, aby se vyžadovalo ruční spuštění nasazení.

1. Pokud chcete kontrolovat všechny úlohy vašeho kanálu, vyberte **úlohy**. Vydaná verze nastaví prostředí do, nakonfiguruje `imagePullSecrets` parametr, nainstaluje nástroje Helm a nasadí grafy Helm do clusteru Kubernetes.

1. Chcete-li zobrazit historii vydání, vyberte možnost **Zobrazit vydané verze**.

1. Chcete-li zobrazit souhrn, vyberte možnost **verze**. Výběrem libovolné fáze můžete prozkoumat několik nabídek, jako je souhrn vydaných verzí, přidružené pracovní položky a testy. 

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení kódu související s tímto nasazením. Porovnejte verze, abyste viděli rozdíly v potvrzení mezi nasazeními.

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o nasazení, které můžete zobrazit během a po nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Související prostředky, které jste vytvořili, můžete odstranit, když už je nepotřebujete. Použijte funkci Odstranit na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

Tyto kanály buildu a verze můžete upravit tak, aby splňovaly požadavky vašeho týmu. Nebo můžete použít tento model CI/CD jako šablonu pro vaše jiné kanály.
