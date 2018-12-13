---
title: Kanál CI/CD pomocí projektů Azure DevOps – Azure IoT Edge | Dokumentace Microsoftu
description: Azure DevOps Projects umožňuje snadno začít používat Azure. Pomůže vám spustit aplikaci Azure IoT Edge podle vašeho výběru v několika rychlých krocích.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074210"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Vytvoření kanálu CI/CD pro IoT Edge s projekty Azure DevOps (Preview)

Konfigurace kontinuální integrace (CI) a průběžné doručování (CD) pro aplikace IoT Edge s projekty DevOps. Projekty DevOps zjednodušuje počáteční konfiguraci sestavení a vydaná verze kanálu v kanálech Azure.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Projekty DevOps vytvoří kanál CI/CD v Azure DevOps. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek** ikony v levém navigačním panelu a pak vyhledejte **DevOps Projects**.  

1.  Vyberte **Vytvořit**.

## <a name="select-a-sample-application-and-azure-service"></a>Výběr ukázkové aplikace a služby Azure

1. Vaše modulů Azure IoT Edge je možné psát v [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) a [Java](tutorial-java-module.md). Vyberte upřednostňovaný jazyk a spusťte novou aplikaci. Odpovídajícím způsobem, můžete vybrat **.NET**, **Node.js**, **Python**, **C**, nebo **Java**a potom klikněte na **Další**.

    ![Vyberte jazyk, který chcete vytvořit novou aplikaci](./media/how-to-devops-project/select-language.png)

2. Vyberte **jednoduché IoT (Preview)** a potom klikněte na tlačítko **Další**.

    ![Vyberte architekturu jednoduché IoT](media/how-to-devops-project/select-iot.png)

3. Vyberte **IoT Edge**a potom klikněte na tlačítko **Další**.

    ![Vyberte službu IoT Edge](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurace Azure DevOps a předplatné Azure

1. Vytvořte novou organizaci bezplatné Azure DevOps nebo zvolte existující organizace.

    a. Zvolte název pro váš projekt. 

    b. Vyberte předplatné Azure a umístění, zvolte název pro vaši aplikaci a pak vyberte **provádí**.  

    ![Název a vytvořte aplikaci](media/how-to-devops-project/select-devops.png)

1. Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Ukázková aplikace IoT Edge je nastavena v úložišti ve vaší organizaci Azure DevOps, sestavení je spuštěno a vaše aplikace bude nasazena do zařízení IoT Edge. Tento řídicí panel poskytuje vhled do vašeho úložiště kódu, kanál CI/CD a vaši aplikaci v Azure.

    ![Zobrazení aplikace na portálu DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

Projekty DevOps vytvoří úložiště Git v úložišti Azure nebo z Githubu. K zobrazení úložiště a proveďte změny kódu aplikace, proveďte následující kroky:

1. Na levé straně řídicího panelu DevOps Projects, vyberte odkaz pro vaše **hlavní** větve.  
Tento odkaz otevře zobrazení nově vytvořeného úložiště Git.

1. Pokud chcete zobrazit adresu URL klonu úložiště, v pravé horní části prohlížeče vyberte **Clone** (Klonovat). Můžete klonování úložiště Git v nástroji VS Code nebo dalších oblíbených nástrojů. V dalších několika krocích vytvořte pomocí webového prohlížeče a potvrzení kódu změní přímo do hlavní větve.

    ![Klonování úložiště git](media/how-to-devops-project/clone.png)

1. Na levé straně prohlížeče, přejděte **modules/FilterModule/module.json** souboru.

1. Vyberte **upravit**a pak proveďte změnu `"version"` pod `"tag"`. Například můžete aktualizovat tak `"version": "${BUILD_BUILDID}"` používat [Azure DevOps vytvářet proměnné](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) jako součást vaší značky obrázku modulu Azure IoT Edge.

    ![Upravit verzi tak, aby přijímal proměnné sestavení](media/how-to-devops-project/update-module-json.png)

1. Vyberte **potvrzení**a pak uložte provedené změny.

1. V prohlížeči přejdete na řídicí panel Projekt Azure DevOps.  Teď by se mělo zobrazit probíhající sestavení. Provedené změny se automaticky vytvořené a nasazené prostřednictvím kanálu CI/CD.

    ![Zobrazit stav probíhající](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Prozkoumejte kanálu CI/CD

Projekty Azure DevOps v předchozím kroku, automaticky nakonfiguruje úplný kanál CI/CD pro vaši aplikaci IoT Edge. Prozkoumejte kanál a podle potřeby ho upravte. Proveďte následující kroky a seznamte se s Azure DevOps sestavení a vydávání kanálů.

1. V horní části řídicího panelu DevOps Projects, vyberte **vytvářet kanály**.  
Tento odkaz otevře na kartě prohlížeče a vytvoření kanálu pro nový projekt Azure DevOps.

1. Vyberte **Upravit**.

    ![Upravit sestavení kanálu](media/how-to-devops-project/click-edit-button.png)

1. V tomto podokně můžete prozkoumat různé úlohy pro vašeho kanálu sestavení. Sestavení provádí různé úlohy, jako je například načítání zdrojů z úložiště Git, vytváření bitové kopie modulu IoT Edge, nasdílení moduly IoT Edge a publikování použít výstupy, které se používají pro nasazení. Další informace o úlohách Azure IoT Edge pro nepřetržitou Integraci, můžete navštívit [nakonfigurovat kanály Azure pro průběžnou integraci](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![Zobrazení úloh průběžné integrace](media/how-to-devops-project/ci.png)

1. V horní části kanálu sestavení vyberte název kanálu sestavení.

1. Změnit na něco více popisné, vyberte název vašeho kanálu sestavení **Uložit & frontu**a pak vyberte **Uložit**.

1. Pod názvem kanálu buildu vyberte **Historie**.   
V **historie** podokně se zobrazí záznam auditovaných nedávné změny pro sestavení.  Kanály Azure uchovává informace o změnách, které se provedly kanálu sestavení, a umožňuje porovnání verzí.

1. Vyberte **Triggery**. Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

1. Vyberte **Uchování**. V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

1. Vyberte **vydání** pod **kanály**. Projekty DevOps vytvoří kanál pro vydávání verzí Spravovat nasazení do Azure IoT Edge.

    ![Kanál pro vydávání verzí zobrazení](media/how-to-devops-project/release-pipeline.png)

1. Vyberte **Upravit**. Kanál pro vydávání verzí obsahuje kanál, který definuje procesu vydávání verzí.  

1. V části **Artefakty** vyberte **Zahodit**. Kanál buildu, který jste prozkoumali v předchozích krocích, vytvoří výstup pro artefakt. 

1. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování**.  
Tento kanál pro vydávání verzí má povoleno CD triggeru, který se spouští nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění.  

1. Na levé straně vyberte **úlohy**. Úkoly jsou aktivity, které proces nasazení provede. V tomto příkladu byl úkol vytvořen k nasazení bitové kopie modulu Azure IoT Edge. Další informace o úlohách Azure IoT Edge pro disk CD, můžete navštívit [nakonfigurovat kanály Azure pro průběžné nasazování](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![Zobrazit úlohy průběžné nasazování](media/how-to-devops-project/dev-release.png)

1. Na pravé straně vyberte **zobrazit verze**. Toto zobrazení ukazuje historii vydaných verzí.

1. Vyberte tři tečky (...) u jedné vydávání verzí a pak vyberte **otevřít**.  
Existuje několik nabídek, které chcete prozkoumat, jako je například souhrnu vydané verze, přidružené pracovní položky a testy.

1. Vyberte **Potvrzení**. Toto zobrazení ukazuje potvrzení změn kódu, které jsou spojeny s konkrétní nasazení. 

1. Vyberte **Protokoly**. Protokoly obsahují užitečné informace o procesu nasazení. Můžete je zobrazit během nasazení i po nich.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup
* Další informace o úlohách pro Azure IoT Edge na Azure DevOps v [průběžnou integraci a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md)
* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
