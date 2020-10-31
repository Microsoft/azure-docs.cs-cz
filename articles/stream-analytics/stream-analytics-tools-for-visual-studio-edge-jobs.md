---
title: Úlohy Azure Stream Analytics Edge v aplikaci Visual Studio
description: Tento článek popisuje, jak vytvářet, ladit a vytvářet Stream Analytics v úlohách IoT Edge pomocí nástrojů Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 55ff983169e15c74bf343993b66088932a538c36
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127514"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Vývoj úloh Stream Analytics Edge pomocí nástrojů sady Visual Studio

V tomto kurzu se naučíte používat nástroje pro Stream Analytics pro Visual Studio. Naučíte se, jak vytvářet, ladit a vytvářet úlohy Stream Analytics Edge. Až úlohu vytvoříte a otestujete, můžete přejít na Azure Portal a nasadit ji do svých zařízení. 

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující předpoklady:

* Nainstalujte [Visual studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual studio 2015](https://www.visualstudio.com/vs/older-downloads/)nebo [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express není podporovaná.  

* Postupujte podle [pokynů k instalaci](stream-analytics-tools-for-visual-studio-edge-jobs.md) nástroje Stream Analytics Tools for Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Vytvoření projektu Stream Analytics Edge 

V aplikaci Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt** . Přejděte do seznamu **šablony** v levém > rozbalte **Azure Stream Analytics**  >  **Stream Analytics Edge**  >  **Azure Stream Analytics aplikaci Edge** . Zadejte název, umístění a název řešení pro svůj projekt a vyberte **OK** .

![Nový projekt Stream Analytics Edge v aplikaci Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Po vytvoření projektu přejděte na **Průzkumník řešení** a zobrazte hierarchii složek.

![Zobrazení Průzkumníka řešení pro úlohu Stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Zvolit správné předplatné

1. V nabídce **zobrazení** v aplikaci Visual Studio vyberte možnost **Průzkumník serveru** .  

2. Klikněte pravým tlačítkem na **Azure** > vyberte **připojit k předplatnému Microsoft Azure** > a pak se přihlaste pomocí svého účtu Azure.

## <a name="define-inputs"></a>Definice vstupů

1. V **Průzkumník řešení** rozbalte uzel **vstupy** , ve kterém se zobrazí vstup s názvem **EdgeInput.js** . Dvojitým kliknutím zobrazíte její nastavení.  

2. Nastavte typ zdroje na **datový proud** . Potom nastavte zdroj na **Edge hub** , formát serializace události na **JSON** a kódování na **UTF8** . Volitelně můžete přejmenovat **vstupní alias** . Pojďme ho nechat jako v tomto příkladu. V případě, že přejmenujete vstupní alias, použijte název, který jste zadali při definování dotazu. Vyberte **Uložit** a nastavení se uloží.  
   ![Konfigurace vstupu úlohy Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definování výstupů

1. V **Průzkumník řešení** rozbalte uzel **výstupy** , ve kterém se zobrazí výstup s názvem **EdgeOutput.js** . Dvojitým kliknutím zobrazíte její nastavení.  

2. Nezapomeňte nastavit jímku pro výběr **hraničního centra** , nastavit formát serializace události na **JSON** , nastavit kódování na **UTF8** a nastavit **pole** formátu. Volitelně můžete **alias pro výstup** přejmenovat, takže ho ponecháme v tomto příkladu. Pokud chcete alias pro výstup přejmenovat, použijte název, který jste zadali při definování dotazu. Vyberte **Uložit** a nastavení se uloží. 
   ![Konfigurace výstupu úlohy Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

Stream Analytics úlohy nasazené v prostředích Stream Analytics IoT Edge podporují většinu [referenčních informací o dotazovacím jazyku Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Pro Stream Analytics hraničních úloh však zatím nejsou podporovány následující operace: 


|**Kategorie**  | **Příkaz**  |
|---------|---------|
|Jiné operátory | <ul><li>ROZDĚLIT PODLE</li><li>ČASOVÉ RAZÍTKO PODLE</li><li>Uživatelsky definovaná funkce v Javascriptu</li><li>Uživatelsky definované agregace (UDA)</li><li>GetMetadataPropertyValue</li><li>Použití více než 14 agregací v jednom kroku</li></ul>   |

Když na portálu vytvoříte úlohu Stream Analytics Edge, kompilátor vás automaticky upozorní, pokud nepoužíváte podporovaný operátor.

V aplikaci Visual Studio definujte následující transformační dotaz v editoru dotazů ( **soubor Script. asaql** ).

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Místní testování úlohy

K otestování dotazu místně byste měli nahrát ukázková data. Ukázková data můžete získat tak, že si stáhnete registrační data z [úložiště GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) a uložíte je do místního počítače. 

1. Pokud chcete nahrát ukázková data, klikněte pravým tlačítkem na **EdgeInput.jsna** soubor a vyberte **Přidat místní vstup** .  

2. V automaticky otevíraném okně > **přejděte** na ukázková data z místní cesty > vyberte **Uložit** .
   ![Konfigurace místního vstupu v aplikaci Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Do složky vstupy se automaticky přidá soubor s názvem **local_EdgeInput.js** .  
4. Můžete ji buď spustit místně, nebo odeslat do Azure. Chcete-li otestovat dotaz, vyberte možnost **spustit místně** .  
   ![Možnosti spuštění úlohy Stream Analytics v aplikaci Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. V okně příkazového řádku se zobrazí stav úlohy. Když se úloha úspěšně spustí, vytvoří v cestě složky projektu Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42 složku, která bude vypadat jako "2018-02-23-11-31-42". Pro zobrazení výsledků v místní složce přejděte na cestu ke složce:

   Můžete se také přihlásit k Azure Portal a ověřit, zda je úloha vytvořena. 

   ![Složka výsledků úlohy Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Odeslat úlohu do Azure

1. Před odesláním úlohy do Azure se musíte připojit k předplatnému Azure. Otevřete **Průzkumník serveru** > klikněte pravým tlačítkem na **Azure**  >  **Connect a Microsoft Azure předplatné** > Přihlaste se ke svému předplatnému Azure.  

2. Pokud chcete odeslat úlohu do Azure, přejděte do editoru dotazů > vyberte **Odeslat do Azure** .  

3. Otevře se místní okno. Vyberte, chcete-li aktualizovat existující úlohu Stream Analytics Edge, nebo vytvořte novou. Když aktualizujete existující úlohu, nahradí se všechna její konfigurace. v tomto scénáři publikujete novou úlohu. Vyberte **vytvořit novou úlohu Azure Stream Analytics** > zadejte název úlohy, jako je **MyASAEdgeJob** > zvolte požadované **předplatné** , **skupinu prostředků** a **umístění** > vyberte **Odeslat** .

   ![Odeslání úlohy Stream Analytics do Azure ze sady Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Teď se vytvořila vaše úloha Stream Analytics Edge. Informace o tom, jak ho nasadit do zařízení, najdete v [kurzu spuštění úloh v IoT Edge](stream-analytics-edge.md) . 

## <a name="manage-the-job"></a>Spravovat úlohu 

Stav úlohy a diagram úlohy můžete zobrazit z Průzkumník serveru. Z **Stream Analytics** v **Průzkumník serveru** rozbalte předplatné a skupinu prostředků, do které jste nasadili úlohu Stream Analytics Edge. Můžete zobrazit MyASAEdgejob se stavem **Vytvořeno** . Rozbalte uzel úlohy a dvojím kliknutím na něj otevřete zobrazení úlohy.

![Možnosti správy úloh Průzkumníka serveru](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno zobrazení úlohy poskytuje operace, jako je například aktualizace úlohy, odstranění úlohy a otevření úlohy z Azure Portal.

![Diagram úloh a další možnosti v aplikaci Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Kurz k ASA v IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Odeslat zpětnou vazbu týmu pomocí tohoto průzkumu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)