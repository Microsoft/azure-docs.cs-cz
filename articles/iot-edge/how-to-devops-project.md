---
title: Kanál CI/CD s Azure DevOps Projects-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects usnadňuje začátek práce s Azure. Pomůže vám spustit Azure IoT Edge aplikaci podle vašeho výběru v několika rychlých krocích.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c7598aafea85e91f28233645d3d71bf85bf45425
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130981"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Vytvoření kanálu CI/CD pro IoT Edge s využitím Azure DevOps Projects

Nakonfigurujte průběžnou integraci (CI) a průběžné doručování (CD) pro vaši aplikaci IoT Edge s DevOps Projects. DevOps Projects zjednodušuje počáteční konfiguraci kanálu sestavení a vydání v Azure Pipelines.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

DevOps Projects vytvoří kanál CI/CD ve službě Azure DevOps. Můžete vytvořit novou organizaci Azure DevOps nebo použít stávající organizaci. DevOps Projects taky vytvoří prostředky Azure v předplatném Azure podle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**a pak vyhledejte **DevOps Projects**.  

1. Vyberte **Vytvořit**.

## <a name="create-a-new-application-pipeline"></a>Vytvořit nový kanál aplikace

1. Vaše moduly Azure IoT Edge můžou být napsané v [jazycích C#](tutorial-csharp-module.md), [Node. js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) a [Java](tutorial-java-module.md). Vyberte preferovaný jazyk pro spuštění nové aplikace: **.NET**, **Node. js**, **Python**, **C**nebo **Java**. Pokračujte výběrem tlačítka **Next** (Další).

   ![Vyberte jazyk pro vytvoření nové aplikace.](./media/how-to-devops-project/select-language.png)

2. Jako aplikační rozhraní vyberte **jednoduché IoT** a pak vyberte **Další**.

   ![Výběr jednoduchého rozhraní IoT Framework](media/how-to-devops-project/select-iot.png)

3. Jako službu Azure, která nasadí vaši aplikaci, vyberte **IoT Edge** a pak vyberte **Další**.

   ![Výběr služby IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Vytvořte novou bezplatnou organizaci Azure DevOps nebo vyberte existující organizaci.

   1. Zadejte název projektu.

   2. Vyberte svou organizaci Azure DevOps. Pokud nemáte existující organizaci, vyberte **Další nastavení** a vytvořte novou.

   3. Vyberte své předplatné Azure.

   4. Použijte název IoT Hub generovaný vaším názvem projektu, nebo zadejte vlastní.

   5. Přijměte výchozí umístění nebo vyberte jednu z nich.

   6. Vyberte **Další nastavení** a nakonfigurujte prostředky Azure, které DevOps Projects vytvoří vaším jménem.

   7. Chcete-li dokončit vytváření projektu, vyberte **Hotovo** .

   ![Název a vytvoření aplikace](media/how-to-devops-project/select-devops.png)

Po několika minutách se řídicí panel DevOps Projects zobrazí v Azure Portal. Vyberte název projektu, abyste viděli průběh. Možná budete muset aktualizovat stránku. Ukázková IoT Edge Aplikace je nastavená v úložišti ve vaší organizaci Azure DevOps, spustí se sestavení a vaše aplikace se nasadí do IoT Edge zařízení. Tento řídicí panel poskytuje přehled o vašem úložišti kódu, kanálu CI/CD a vaší aplikaci v Azure.

   ![Zobrazit aplikaci v Azure Portal](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

DevOps Projects vytvořit úložiště Git pro váš projekt v Azure Repos. V této části si zobrazíte úložiště a provedete změny kódu v aplikaci.

1. Chcete-li přejít k úložišti vytvořenému pro váš projekt, vyberte **úložiště** v nabídce řídicího panelu projektu.  

   ![Zobrazit úložiště vygenerované v Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. Následující kroky vás provedou použitím webového prohlížeče k provedení změn kódu. Pokud chcete úložiště klonovat místně, vyberte v pravém horním rohu okna **klonovat** . K naklonování úložiště Git v Visual Studio Code nebo preferovaném vývojovém nástroji použijte poskytnutou adresu URL.

3. Úložiště již obsahuje kód pro modul s názvem **FilterModule** na základě jazyka aplikace, který jste zvolili v procesu vytváření. Otevřete soubor **modules/FilterModule/Module. JSON** .

   ![Otevřete soubor Module. JSON v Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Všimněte si, že tento soubor používá v parametru **Version** [proměnné buildu Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) . Tato konfigurace zajišťuje, že se nová verze modulu vytvoří při každém spuštění nového buildu.

## <a name="examine-the-cicd-pipeline"></a>Kontrola kanálu CI/CD

V předchozích částech Azure DevOps Projects pro IoT Edge aplikaci automaticky nakonfigurovali úplný kanál CI/CD. Nyní Prozkoumejte a přizpůsobte kanál podle potřeby. Následující postup použijte k seznámení s kanály pro sestavování a vydávání Azure DevOps.

1. Pokud chcete zobrazit kanály sestavení v projektu DevOps, v nabídce řídicího panelu projektu vyberte **vytvořit kanály** . Tento odkaz otevře kartu prohlížeče a kanál sestavení Azure DevOps pro váš nový projekt.

   ![Zobrazit kanály sestavení v Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Vyberte **Upravit**.

    ![Upravit kanál sestavení](media/how-to-devops-project/click-edit-button.png)

3. Na panelu, který se otevře, můžete kontrolovat úkoly, ke kterým dochází při spuštění kanálu sestavení. Kanál sestavení provádí různé úkoly, jako je například načítání zdrojů z úložiště Git, vytváření imagí IoT Edge modulu, vkládání IoT Edge modulů do registru kontejneru a publikování výstupů, které se používají pro nasazení. Další informace o úlohách Azure IoT Edge v Azure DevOps najdete v tématu [konfigurace Azure Pipelines pro průběžnou integraci](how-to-ci-cd.md#configure-continuous-integration).

4. Vyberte hlavičku **kanálu** v horní části kanálu sestavení a otevřete podrobnosti kanálu. Změňte název kanálu buildu na něco výstižnějšího.

   ![Úprava podrobností kanálu](./media/how-to-devops-project/edit-build-pipeline.png)

5. Vyberte **uložit & frontu**a potom vyberte **Uložit**.

6. V nabídce kanálu sestavení vyberte **triggery** . DevOps Projects automaticky vytvořil Trigger CI a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

7. Vyberte **Uchování**. V závislosti na vašem scénáři můžete určit zásady, které zachovají nebo odeberou určitý počet sestavení.

8. Vyberte **Historie**. Panel Historie obsahuje záznam o auditu nedávných změn v sestavení. Azure Pipelines sleduje všechny změny provedené v kanálu sestavení a umožňuje porovnat verze.

9. Až budete s prozkoumáním kanálu sestavení hotovi, přejděte na odpovídající kanál verze. V části **kanály**vyberte **releases (verze** ) a pak vyberte **Upravit** . zobrazí se podrobnosti o kanálu.

    ![Zobrazit kanál verze](media/how-to-devops-project/release-pipeline.png)

10. V části **Artefakty** vyberte **Zahodit**. Zdroj, který sleduje tento artefakt, je výstupem kanálu sestavení, který jste prozkoumali v předchozích krocích.

11. Vedle ikony **odkládacího umístění** vyberte **Trigger průběžného nasazování** , který vypadá jako blesk. Tento kanál vydaných verzí povolil Trigger, který spouští nasazení pokaždé, když je k dispozici nový artefakt sestavení. Volitelně můžete aktivační událost zakázat, aby vaše nasazení vyžadovalo ruční spuštění.  

12. V nabídce pro svůj kanál pro vydávání verzí vyberte **úlohy** a pak zvolte fázi **vývoje** z rozevíracího seznamu. DevOps Projects vytvořili fázi vydaných verzí, která vytvoří centrum IoT, vytvoří v tomto centru IoT Edge zařízení, nasadí vzorový modul z kanálu sestavení a zřídí virtuální počítač, který se spustí jako zařízení IoT Edge. Další informace o úlohách Azure IoT Edge pro CD najdete v tématu [konfigurace Azure Pipelines pro průběžné nasazování](how-to-ci-cd.md#configure-continuous-deployment).

    ![Zobrazit úlohy průběžného nasazování](media/how-to-devops-project/dev-release.png)

13. Na pravé straně vyberte **Zobrazit vydané verze**. Toto zobrazení ukazuje historii vydaných verzí.

14. Vyberte název verze, pro který chcete zobrazit další informace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete odstranit Azure App Service a další související prostředky, které jste vytvořili, když už je nepotřebujete. Použijte funkci **Odstranit** na řídicím panelu DevOps Projects.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o úlohách Azure IoT Edge v Azure DevOps v části [průběžná integrace a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md)
* Pochopení nasazení IoT Edge v části [Principy nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Projděte si postup vytvoření, aktualizace nebo odstranění nasazení v části [nasazení a monitorování IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).
