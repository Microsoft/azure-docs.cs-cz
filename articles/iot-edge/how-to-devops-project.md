---
title: Kanál CI/CD s projekty Azure DevOps – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Azure DevOps Projects usnadňuje schůdky v Azure. Pomůže vám spustit aplikaci Azure IoT Edge podle vašeho výběru v několika rychlých krocích.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66401678f03ee0043345208eb32560f589829226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510307"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro IoT Edge s azure devops projekty

Nakonfigurujte průběžnou integraci (CI) a průběžné doručování (CD) pro vaši aplikaci IoT Edge pomocí devOps Projects. DevOps Projects zjednodušuje počáteční konfiguraci kanálu sestavení a vydání v Azure Pipelines.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projekty vytvoří kanál CI/CD v Azure DevOps. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure dle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit zdroj**a vyhledejte **položku DevOps Projects**.  

1. Vyberte **Vytvořit**.

## <a name="create-a-new-application-pipeline"></a>Vytvoření nového kanálu aplikace

1. Vaše moduly Azure IoT Edge lze napsané v [jazycích C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) a [Java](tutorial-java-module.md). Vyberte preferovaný jazyk pro spuštění nové aplikace: **.NET**, **Node.js**, **Python**, **C**nebo **Java**. Pokračujte výběrem tlačítka **Next** (Další).

   ![Vyberte jazyk pro vytvoření nové aplikace.](./media/how-to-devops-project/select-language.png)

2. Jako architekturu aplikace vyberte **Jednoduché IoT** a pak vyberte **Další**.

   ![Vybrat jednoduchý rámec IoT](media/how-to-devops-project/select-iot.png)

3. Vyberte **IoT Edge** jako službu Azure, která nasazuje vaši aplikaci, a pak vyberte **Další**.

   ![Vybrat službu IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Vytvořte novou bezplatnou organizaci Azure DevOps nebo zvolte existující organizaci.

   1. Zadejte název projektu.

   2. Vyberte organizaci Azure DevOps. Pokud nemáte existující organizaci, vyberte **Další nastavení** a vytvořte novou.

   3. Vyberte své předplatné Azure.

   4. Použijte název ioT hubu generovaný názvem projektu nebo zadejte svůj vlastní.

   5. Přijměte výchozí umístění nebo zvolte jedno blízké.

   6. Vyberte **Další nastavení** a nakonfigurujte prostředky Azure, které devOps Projects vytvoří vaším jménem.

   7. Chcete-li dokončit vytváření projektu, vyberte **Hotovo.**

   ![Pojmenování a vytvoření aplikace](media/how-to-devops-project/select-devops.png)

Po několika minutách se na webu Azure Portal zobrazí řídicí panel DevOps Projects. Chcete-li zobrazit průběh, vyberte název projektu. Možná budete muset aktualizovat stránku. Ukázková aplikace IoT Edge se nastaví v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí na zařízení IoT Edge. Tento řídicí panel poskytuje přehled o úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.

   ![Zobrazení aplikace na webu Azure Portal](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Projects vytvořili úložiště Git pro váš projekt v Azure Repos. V této části zobrazíte úložiště a provedete změny kódu v aplikaci.

1. Chcete-li přejít na úložiště vytvořené pro váš projekt, vyberte **úložiště** v nabídce řídicího panelu projektu.  

   ![Zobrazit úložiště generované v azure úložišti](./media/how-to-devops-project/view-repositories.png)

2. Následující kroky procházet pomocí webového prohlížeče k provedení změn kódu. Pokud chcete klonovat úložiště místně místo, vyberte **Klonování** v pravém horním rohu okna. Pomocí poskytnuté adresy URL můžete naklonovat úložiště Git v kódu Sady Visual Studio nebo v upřednostňovaném vývojovém nástroji.

3. Úložiště již obsahuje kód pro modul s názvem **FilterModule** na základě jazyka aplikace, který jste zvolili v procesu vytváření. Otevřete soubor **modules/FilterModule/module.json.**

   ![Otevření souboru module.json v Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Všimněte si, že tento soubor používá [Azure DevOps sestavení proměnné](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) v parametru **verze.** Tato konfigurace zajišťuje, že nová verze modulu bude vytvořena při každém spuštění nového sestavení.

## <a name="examine-the-cicd-pipeline"></a>Zkontrolujte kanál CI/CD

V předchozích částech Azure DevOps Projekty automaticky nakonfiguroval úplný kanál CI/CD pro vaši aplikaci IoT Edge. Nyní prozkoumejte a přizpůsobte kanál podle potřeby. Pomocí následujících kroků se seznamte s kanály sestavení a vydání Azure DevOps.

1. Chcete-li zobrazit kanály sestavení v projektu DevOps, vyberte **sestavení kanálů** v nabídce řídicího panelu projektu. Tento odkaz otevře kartu prohlížeče a Azure DevOps sestavení kanálu pro váš nový projekt.

   ![Zobrazení kanálů sestavení v Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Vyberte **Upravit**.

    ![Upravit kanál sestavení](media/how-to-devops-project/click-edit-button.png)

3. V panelu, který se otevře, můžete zkontrolovat úkoly, ke kterým dochází při spuštění kanálu sestavení. Kanál sestavení provádí různé úlohy, jako je načítání zdrojů z úložiště Git, vytváření iniciálních modulů IoT Edge, odesílání modulů IoT Edge do registru kontejnerů a publikování výstupů, které se používají pro nasazení. Další informace o úlohách Azure IoT Edge v Azure DevOps najdete v [tématu Konfigurace kanálů Azure pro průběžnou integraci](how-to-ci-cd.md#configure-continuous-integration).

4. Vyberte záhlaví **kanálu** v horní části kanálu sestavení otevřít podrobnosti kanálu. Změňte název kanálu buildu na něco výstižnějšího.

   ![Úprava podrobností kanálu](./media/how-to-devops-project/edit-build-pipeline.png)

5. Vyberte **Uložit & frontu**a pak vyberte **Uložit**.

6. Z nabídky kanálu sestavení vyberte **Aktivační události.** DevOps Projekty automaticky vytvořili aktivační událost CI a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

7. Vyberte **Uchování**. V závislosti na scénáři můžete určit zásady zachovat nebo odebrat určitý počet sestavení.

8. Vyberte **historie**. Panel historie obsahuje záznam auditu nedávných změn sestavení. Azure Pipelines sleduje všechny změny, které jsou provedeny v kanálu sestavení a umožňuje porovnat verze.

9. Až budete hotovi zkoumání kanálu sestavení, přejděte na odpovídající verze kanálu. V části **Kanály**vyberte **Zprávy** a pak vyberte **Upravit,** chcete-li zobrazit podrobnosti kanálu.

    ![Zobrazit kanál vydání](media/how-to-devops-project/release-pipeline.png)

10. V části **Artefakty** vyberte **Zahodit**. Zdroj, který tento artefakt sleduje je výstup kanálu sestavení, který jste zkoumali v předchozích krocích.

11. Vedle ikony **Přetažení** vyberte **aktivační událost nepřetržitého nasazení,** která vypadá jako blesk. Tento kanál vydání povolil aktivační událost, která spustí nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžadují ruční spuštění.  

12. V nabídce kanálu vydání vyberte **Úkoly a** pak z rozevíracího seznamu zvolte fázi **pro zpracování.** DevOps Projects pro vás vytvořili fázi vydání, která vytvoří centrum IoT hub, vytvoří zařízení IoT Edge v tomto rozbočovači, nasadí ukázkový modul z kanálu sestavení a zřídí virtuální počítač, který se má spustit jako vaše zařízení IoT Edge. Další informace o úlohách Azure IoT Edge pro disk CD najdete [v tématu Konfigurace kanálů Azure pro průběžné nasazení](how-to-ci-cd.md#configure-continuous-deployment).

    ![Zobrazení úloh průběžného nasazení](media/how-to-devops-project/dev-release.png)

13. Na pravé straně vyberte **Zobrazit vydání**. Toto zobrazení ukazuje historii vydaných verzí.

14. Vyberte název vydání, chcete-li o ní zobrazit další informace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už je nepotřebujete. Funkce **Odstranit** na řídicím panelu Projekty devOps.

## <a name="next-steps"></a>Další kroky

* Další informace o úkolech pro Azure IoT Edge na Azure DevOps v [průběžné integraci a průběžném nasazování do Azure IoT Edge](how-to-ci-cd.md)
* Seznamte se s nasazením IoT Edge v [nasazení chodicích IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Projděte si kroky k vytvoření, aktualizaci nebo odstranění nasazení v [modulech Deploy and Monitor IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
