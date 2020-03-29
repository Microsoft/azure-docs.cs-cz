---
title: Úlohy Azure Stream Analytics Edge ve Visual Studiu
description: Tento článek popisuje, jak vytvářet, ladit a vytvářet streamanalýzy na IoT Edge úlohy pomocí nástrojů Stream Analytics pro Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354569"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Vývoj úloh Stream Analytics Edge pomocí nástrojů Visual Studia

V tomto kurzu se dozvíte, jak používat nástroje Stream Analytics pro Visual Studio. Dozvíte se, jak vytvářet, ladit a vytvářet úlohy Stream Analytics Edge. Po vytvoření a testování úlohy můžete přejít na portál Azure a nasadit ji do svých zařízení. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující předpoklady:

* Nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)nebo [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Expresní edice není podporována.  

* Podle [pokynů k instalaci](stream-analytics-tools-for-visual-studio-edge-jobs.md) nainstalujte nástroje Stream Analytics pro Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Vytvoření projektu Stream Analytics Edge 

V sadě Visual Studio vyberte **Soubor** > **nový** > **projekt**. Přejděte do seznamu **Šablony** vlevo > rozbalte aplikaci **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge .** Zadejte název, umístění a název řešení projektu a vyberte **OK**.

![Nový projekt Stream Analytics Edge ve Visual Studiu](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Po vytvoření projektu přejděte do **Průzkumníka řešení** a zobrazte hierarchii složek.

![Zobrazení průzkumníka řešení úlohy Stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Výběr správného předplatného

1. V nabídce **Zobrazení** sady Visual Studio vyberte **Průzkumník serveru**.  

2. Klikněte pravým tlačítkem na **Azure** > vyberte **připojit k předplatnému Microsoft Azure** > a pak se přihlásit pomocí svého účtu Azure.

## <a name="define-inputs"></a>Definice vstupů

1. V **Průzkumníku řešení**rozbalte uzel **Inputs,** u kterých by se měl zobrazit vstup s názvem **EdgeInput.json**. Poklepáním zobrazíte jeho nastavení.  

2. Nastavte typ zdroje na **datový proud**. Potom nastavte source to **Edge Hub**, formát serializace událostí na **Json**a kódování na **UTF8**. Volitelně můžete přejmenovat **vstupní alias**, nechme to tak, jak je tomu v tomto příkladu. V případě, že přejmenujete vstupní alias, použijte název, který jste zadali při definování dotazu. Vyberte **Uložit** a nastavení se uloží.  
   ![Konfigurace vstupu úlohy Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definování výstupů

1. V **Průzkumníku řešení**rozbalte uzel **Výstupy,** měli byste vidět výstup s názvem **EdgeOutput.json**. Poklepáním zobrazíte jeho nastavení.  

2. Ujistěte se, že chcete nastavit funkci Jímka pro výběr **edge hubu**, nastavit formát serializace událostí na **Json**, nastavit kódování na **UTF8**a nastavit pole Format **Array**. Volitelně můžete přejmenovat **output alias**, nechme to tak, jak je v tomto příkladu. V případě, že přejmenujete výstupní alias, použijte název, který jste zadali při definování dotazu. Vyberte **Uložit** a nastavení se uloží. 
   ![Konfigurace výstupu úlohy Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

Úlohy Stream Analytics nasazené v prostředíCh Stream Analytics IoT Edge podporují většinu [odkazů na dotazovacího jazyka Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Pro úlohy Stream Analytics Edge však ještě nejsou podporovány následující operace: 


|**Kategorie**  | **Příkaz**  |
|---------|---------|
|Ostatní operátoři | <ul><li>ODDÍL PODLE</li><li>ČASOVÉ RAZÍTKO OD VÍCE NEŽ</li><li>JavaScript UDF</li><li>Uživatelem definované agregáty (UDA)</li><li>GetMetadataPropertyValue</li><li>Použití více než 14 agregátů v jednom kroku</li></ul>   |

Když na portálu vytvoříte úlohu Stream Analytics Edge, kompilátor vás automaticky upozorní, pokud nepoužíváte podporovaný operátor.

V sadě Visual Studio definujte následující transformační dotaz v editoru dotazů **(soubor script.asaql)**

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testování úlohy místně

Chcete-li dotaz otestovat místně, měli byste nahrát ukázková data. Ukázková data můžete získat stažením registračních dat z [úložiště GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) a jejich uložením do místního počítače. 

1. Chcete-li nahrát ukázková data, klikněte pravým tlačítkem myši na soubor **EdgeInput.json** a zvolte **Přidat místní vstup.**  

2. V rozbalovacím okně > **Procházet** ukázková data z místní cesty > Vybrat **uložit**.
   ![Konfigurace místního vstupu v sadě Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Soubor s názvem **local_EdgeInput.json** je přidán automaticky do složky vstupy.  
4. Můžete buď spustit místně nebo odeslat do Azure. Chcete-li dotaz otestovat, vyberte **možnost Spustit místně**.  
   ![Možnosti spuštění úloh Služby Stream Analytics ve Visual Studiu](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Okno příkazového řádku zobrazuje stav úlohy. Po úspěšném spuštění úlohy vytvoří složku, která vypadá jako "2018-02-23-11-31-42" v cestě ke složce projektu "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Přejděte na cestu ke složce a zobrazte výsledky v místní složce:

   Můžete se také přihlásit k portálu Azure a ověřit, že se úloha vytvoří. 

   ![Složka výsledků úlohy Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Odeslání úlohy do Azure

1. Před odesláním úlohy do Azure se musíte připojit k předplatnému Azure. Otevřete **Průzkumníka serveru** > klikněte pravým tlačítkem na **předplatné Azure** > **Connect k Microsoft Azure** > přihlášení k předplatnému Azure.  

2. Pokud chcete úlohu odeslat do Azure, přejděte do editoru dotazů, > vyberte **Odeslat do Azure**.  

3. Otevře se vyskakovací okno. Zvolte, zda chcete aktualizovat existující úlohu Stream Analytics Edge, nebo vytvořte novou. Při aktualizaci existující úlohy nahradí všechny konfigurace úlohy, v tomto scénáři budete publikovat novou úlohu. Vyberte **Vytvořit novou úlohu Azure Stream Analytics** > zadejte název úlohy podobně jako **MyASAEdgeJob** > zvolte požadované **předplatné**, **skupinu prostředků**a **umístění** > vybrat **odeslat**.

   ![Odeslání úlohy Stream Analytics do Azure z Visual Studia](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Nyní byla vytvořena vaše úloha Stream Analytics Edge. Můžete odkazovat na [spuštění úloh na IoT Edge kurz se dozvíte,](stream-analytics-edge.md) jak ji nasadit do vašich zařízení. 

## <a name="manage-the-job"></a>Správa úlohy 

Stav úlohy a diagram úloh y můžete zobrazit z Průzkumníka serveru. V **Stream Analytics** v **Průzkumníkovi serveru**rozbalte předplatné a skupinu prostředků, do které jste nasadili úlohu Stream Analytics Edge. Můžete zobrazit MyASAEdgejob se stavem **Vytvořeno**. Rozbalte uzel úlohy a poklepáním na něj otevřete zobrazení úlohy.

![Možnosti správy úloh průzkumníka serveru](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno zobrazení úlohy poskytuje operace, jako je aktualizace úlohy, odstranění úlohy a otevření úlohy z portálu Azure.

![Diagram úloh a další možnosti v sadě Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA na IoT Edge kurz](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Odeslat zpětnou vazbu týmu pomocí tohoto průzkumu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
