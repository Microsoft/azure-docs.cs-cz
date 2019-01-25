---
title: Kanál CI/CD pomocí projektů Azure DevOps – Azure IoT Edge | Dokumentace Microsoftu
description: Azure DevOps Projects umožňuje snadno začít používat Azure. Pomůže vám spustit aplikaci Azure IoT Edge podle vašeho výběru v několika rychlých krocích.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 57279a4e92b1f42505003f12d41c8203aa5603d4
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54881865"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Vytvoření kanálu CI/CD pro IoT Edge s projekty Azure DevOps (Preview)

Konfigurace kontinuální integrace (CI) a průběžné doručování (CD) pro aplikace IoT Edge s projekty DevOps. Projekty DevOps zjednodušuje počáteční konfiguraci sestavení a vydaná verze kanálu v kanálech Azure.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Projekty DevOps vytvoří kanál CI/CD v Azure DevOps. Můžete vytvořit novou organizaci Azure DevOps nebo použít existující organizace. Prostředky Azure DevOps Projects také vytvoří v rámci předplatného Azure podle vašeho výběru.

1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**a poté vyhledejte **DevOps Projects**.  

1.  Vyberte **Vytvořit**.

## <a name="create-a-new-application-pipeline"></a>Vytvořit nový kanál aplikací 

1. Vaše modulů Azure IoT Edge je možné psát v [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) a [Java](tutorial-java-module.md). Vyberte upřednostňovaný jazyk a spusťte novou aplikaci: **.NET**, **Node.js**, **Python**, **C**, nebo **Java**. Pokračujte výběrem tlačítka **Next** (Další).

   ![Vyberte jazyk, který chcete vytvořit novou aplikaci](./media/how-to-devops-project/select-language.png)

2. Vyberte **jednoduché IoT (Preview)** jako aplikační platformu a pak vyberte **Další**.

   ![Vyberte architekturu jednoduché IoT](media/how-to-devops-project/select-iot.png)

3. Vyberte **IoT Edge** se službou Azure, která nasadí vaši aplikaci a pak vyberte **Další**.

   ![Vyberte službu IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Vytvořte novou organizaci bezplatné Azure DevOps nebo zvolte existující organizace.

   1. Zadejte název pro váš projekt. 

   2. Vyberte svoji organizaci Azure DevOps. Pokud nemáte existující organizace, vyberte **další nastavení** vytvořit nový certifikát. 

   3. Vyberte své předplatné Azure.

   4. Použijte název služby IoT Hub vygenerovaný název projektu nebo zadejte vlastní.

   5. Vyberte **další nastavení** ke konfiguraci prostředků Azure, které za vás vytvoří projekty DevOps.

   6. Vyberte **provádí** dokončit vytváření projektu. 

   ![Název a vytvořte aplikaci](media/how-to-devops-project/select-devops.png)

Po několika minutách se zobrazí řídicí panel projekty DevOps na webu Azure Portal. Vyberte název vašeho projektu zobrazíte průběh. Budete muset aktualizovat stránku. Ukázková aplikace IoT Edge je nastavena v úložišti ve vaší organizaci Azure DevOps, sestavení je spuštěno a vaše aplikace bude nasazena do zařízení IoT Edge. Tento řídicí panel poskytuje vhled do vašeho úložiště kódu, kanál CI/CD a vaši aplikaci v Azure.

   ![Zobrazení aplikace na portálu DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Potvrzení změn kódu a spuštění CI/CD

Projekty DevOps vytvoří úložiště Git pro svůj projekt v úložišti Azure. V této části zobrazení úložiště a proveďte změny kódu aplikace.

1. Chcete-li přejít k úložišti pro váš projekt vytvoří, vyberte **úložišť** v nabídce řídicího panelu Projekt.  

   ![Zobrazení úložiště vygenerované v úložišti Azure](./media/how-to-devops-project/view-repositories.png)

2. Následující kroky se provedou pomocí webového prohlížeče provádět změny kódu. Pokud chcete místo toho klonování úložiště místně, vyberte **klonování** shora napravo od okna. Pomocí adresy URL klonování úložiště Git ve Visual Studio Code nebo svůj upřednostňovaný vývojový nástroj. 

3. Úložiště již obsahuje kód pro modul s názvem **SampleModule** podle jazyka aplikace, kterou jste zvolili v procesu vytváření. Otevřít **modules/SampleModule/module.json** souboru.

   ![Otevřít module.json souboru v úložišti Azure](./media/how-to-devops-project/open-module-json.png)

4. Vyberte **upravit**a pak proveďte změnu `"version"` pod `"tag"`. Například můžete aktualizovat tak `"version": "${BUILD_BUILDID}"` používat [Azure DevOps vytvářet proměnné](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) jako součást vaší značky obrázku modulu Azure IoT Edge.

   ![Upravit verzi tak, aby přijímal proměnné sestavení](media/how-to-devops-project/update-module-json.png)

5. Vyberte **potvrzení**a pak uložte provedené změny.

6. V prohlížeči se vraťte na řídicí panel projekty DevOps na webu Azure Portal. Teď by se mělo zobrazit probíhající sestavení. Provedené změny se automaticky vytvořené a nasazené prostřednictvím kanálu CI/CD.

    ![Zobrazit stav probíhající](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Prozkoumejte kanálu CI/CD

Projekty Azure DevOps v předchozích částech, automaticky nakonfiguruje úplný kanál CI/CD pro vaši aplikaci IoT Edge. Potom můžete testované sestavení kanálu potvrzení změn na jeden ze souborů. Nyní prozkoumejte a podle potřeby upravte kanál. Proveďte následující kroky a seznamte se s Azure DevOps sestavení a vydávání kanálů.

1. Chcete-li zobrazit kanály pro sestavování ve vašem projektu DevOps, vyberte **vytvářet kanály** v nabídce řídicího panelu Projekt. Tento odkaz otevře na kartě prohlížeče a vytvoření kanálu pro nový projekt Azure DevOps.

   ![Zobrazit sestavení kanály ve službě Azure kanály](./media/how-to-devops-project/view-build-pipelines.png)

2. Vyberte **Upravit**.

    ![Upravit sestavení kanálu](media/how-to-devops-project/click-edit-button.png)

3. V panelu, které se otevře můžete zkontrolovat úlohy, ke kterým dochází při spuštění vašeho kanálu sestavení. Kanál sestavení provádí různé úlohy, jako je například načítání zdrojů z úložiště Git, vytváření bitové kopie modulu IoT Edge, doručením (push) moduly IoT Edge do registru kontejneru a publikování výstupy, které se používají pro nasazení. Další informace o úlohách Azure IoT Edge v Azure DevOps, naleznete v tématu [nakonfigurovat kanály Azure pro průběžnou integraci](how-to-ci-cd.md#configure-continuous-integration).

4. Vyberte **kanálu** záhlaví v horní části kanálu sestavení, otevřete podrobnosti kanálu. Změňte název vašeho kanálu sestavení výstižněji.

   ![Upravit podrobnosti kanálu](./media/how-to-devops-project/edit-build-pipeline.png)

5. Vyberte **Uložit & frontu**a pak vyberte **Uložit**.

6. V nabídce vašeho kanálu sestavení, vyberte **triggery** v nabídce. Projekty DevOps automaticky vytvoří aktivační událost CI, a každé potvrzení do úložiště spustí nové sestavení.  Volitelně můžete zvolit, které větve se do procesu CI zahrnou nebo se z něj vyloučí.

7. Vyberte **Uchování**. V závislosti na vašem scénáři můžete zadat zásady, které chcete zachovat nebo odebrat počet sestavení.

8. Vyberte **historie**. Panel Historie obsahuje záznam auditovaných nedávné změny sestavení. Kanály Azure uchovává informace o změnách, které se provedly kanálu sestavení, a umožňuje porovnání verzí.

9. Když skončíte zkoumání kanálu sestavení, přejděte na odpovídající kanál pro vydávání verzí. Vyberte **verze** pod **kanály**a pak vyberte **upravit** zobrazíte podrobnosti o kanálu.

    ![Kanál pro vydávání verzí zobrazení](media/how-to-devops-project/release-pipeline.png)

10. V části **Artefakty** vyberte **Zahodit**. Zdroj, který sleduje tento artefakt je výstup kanálu sestavení, kterou můžete prozkoumat v předchozích krocích. 

11. Vedle položky **vyřadit** ikonu, vyberte **trigger průběžného nasazování** vypadá podobně jako blesk. Tento kanál pro vydávání verzí aktivoval aktivační událost, která spustí nasazení pokaždé, když je k dispozici nové artefakt sestavení. Volitelně můžete zakázat aktivační událost tak, aby vaše nasazení vyžaduje ruční spuštění.  

12. V nabídce pro kanál pro vydávání verzí, vyberte **úlohy** klikněte na tlačítko **dev** fáze z rozevíracího seznamu. Projekty DevOps vytvoří fázi vydání verze pro, který vytvoří Centrum IoT, vytvoří zařízení IoT Edge v daném rozbočovači, vzorový modul z kanálu sestavení nasazuje a zřizuje virtuální počítače ke spuštění jako zařízení IoT Edge. Další informace o úlohách Azure IoT Edge pro disk CD, najdete v článku [nakonfigurovat kanály Azure pro průběžné nasazování](how-to-ci-cd.md#configure-continuous-deployment).

   ![Zobrazit úlohy průběžné nasazování](media/how-to-devops-project/dev-release.png)

13. Na pravé straně vyberte **zobrazit verze**. Toto zobrazení ukazuje historii vydaných verzí.

14. Vyberte název vydání zobrazíte další informace o něm.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure App Service a další související prostředky, které jste vytvořili, když už nepotřebujete, můžete odstranit. Použití **odstranit** funkce na řídicím panelu projekty DevOps.

## <a name="next-steps"></a>Další postup
* Další informace o úlohách pro Azure IoT Edge na Azure DevOps v [průběžnou integraci a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md)
* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
