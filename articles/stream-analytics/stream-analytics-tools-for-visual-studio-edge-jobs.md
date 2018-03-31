---
title: Nastavit průběžnou integraci a nasazení procesu pomocí nástrojů Stream Analytics Visual Studio | Microsoft Docs
description: Kurz k vývoji Stream Analytics nástrojů pro Visual Studio pro vytváření, ladění a vytvořte vaše úlohy Stream Analytics okraj.
keywords: Visual studio, NuGet, DevOps, Edge úlohy Stream analytics
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/13/2018
ms.author: sujie
ms.openlocfilehash: c6e1d0693035ef343e20cee4b09f0669e089afee
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="develop-stream-analytics-edge-jobs-by-using-visual-studio-tools"></a>Pomocí nástrojů Visual Studio pro vývoj úlohy Stream Analytics Edge

V tomto kurzu zjistěte, jak pomocí Stream Analytics nástrojů pro Visual Studio pro vytváření, ladění a vytvořte vaše úlohy Stream Analytics okraj. Když vytvoříte a otestujete úlohu, můžete přejít na portál Azure k nasazení do zařízení. 

## <a name="prerequisites"></a>Požadavky

Požadovaný k dokončení tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio 2017](https://www.visualstudio.com/downloads/), [sady Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), nebo [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován.  

* Postupujte podle [pokyny k instalaci](stream-analytics-tools-for-visual-studio-edge-jobs.md) k instalaci služby Stream Analytics tools pro sadu Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Vytvoření Stream Analytics Edge projektu 

Ze sady Visual Studio, vyberte **soubor** > **nový** > **projektu**. Přejděte na **šablony** seznamu na levé straně > rozbalte **Azure Stream Analytics** > **Stream Analytics Edge**  >   **Azure Stream Analytics okraj aplikace**. Zadejte název projektu a vyberte název, umístění a řešení **OK**.

![Nový projekt Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Po vytvoření projektu získá přejděte do **Průzkumníku řešení** zobrazíte hierarchii složek.

![Průzkumník řešení](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Vyberte správné předplatné.

1. Z Visual Studia **zobrazení** nabídce vyberte možnost **Průzkumníka serveru**.  

2. Klikněte pravým tlačítkem na **Azure** > vyberte **připojit k předplatnému Microsoft Azure** > a potom se přihlaste pomocí účtu Azure.

## <a name="define-inputs"></a>Zadejte vstupy

1. Z **Průzkumníku řešení**, rozbalte **vstupy** uzlu byste měli vidět vstup s názvem **EdgeInput.json**. Dvojitým kliknutím zobrazíte jeho nastavení.  

2. Ujistěte se, že typ zdroje je nastavena na **datový proud** > zdroj je nastavena na **Edge rozbočovače** > formát serializace událostí nastavit na **Json** > a kódování je nastavena na **UTF8**. Volitelně můžete přejmenovat **vstupní Alias**, můžeme ponechte jako je v tomto příkladu. V případě, že vstupní alias přejmenujete, použijte název, který jste zadali při definování dotazu. Vyberte **Uložit** a nastavení se uloží.  
   ![Vstupní konfigurace](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definování výstupy

1. Z **Průzkumníku řešení**, rozbalte **výstupy** uzlu byste měli vidět výstup s názvem **EdgeOutput.json**. Dvojitým kliknutím zobrazíte jeho nastavení.  

2. Ujistěte se, že podřízený je nastavená na vyberte **Edge rozbočovače** > formát serializace událostí nastavit na **Json** > a kódování je nastavena na **UTF8** > formátu je nastavená na  **Pole**. Volitelně můžete přejmenovat **Alias pro výstup**, můžeme ponechte jako je v tomto příkladu. V případě, že přejmenujete alias pro výstup, použijte název, který jste zadali při definování dotazu. Vyberte **Uložit** a nastavení se uloží. 
   ![Výstup konfigurace](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Zadejte dotaz transformace

Úlohy Stream Analytics nasadit v prostředí hraniční podporují většinu [Stream Analytics Query Language odkaz](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), ale pro úlohy Edge ještě nepodporuje následující operace: 


|**Kategorie**  | **Příkaz**  |
|---------|---------|
|Geoprostorové operátory |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Jiné operátory | <ul><li>ODDÍL</li><li>ČASOVÉ RAZÍTKO PODLE PŘES</li><li>DISTINCT</li><li>Parametr výraz v operátoru COUNT</li><li>Mikrosekund v funkce data a času</li><li>JavaScript UDA (Tato funkce je stále ve verzi preview pro úlohy nasazené v cloudu)</li></ul>   |

Když vytvoříte úlohu Edge na portálu, kompilátor automaticky upozornění, pokud nepoužíváte podporované operátor.

Z Visual Studia, zadejte následující dotaz transformaci v editoru dotazů (**script.asaql souboru**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testovací úloha místně

Otestujte dotaz místně, byste měli nahrávat ukázková data. Ukázková data můžete získat tak, že stáhnete registrační data z [úložiště GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) a uložte ho do místního počítače. 

1. Nahrát ukázková data > klikněte pravým tlačítkem na **EdgeInput.json** soubor > vyberte **přidat místní vstup**  

2. V místním okně > **Procházet** ukázková data z vaší místní cesty > vyberte **Uložit**.
   ![Místní vstupní konfigurace](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Soubor s názvem **local_EdgeInput.json** je automaticky přidán do složky vstupy.  
4. můžete ji spustit místně nebo odeslání do Azure. Otestujte dotaz > vyberte **spustit místně**.  
   ![Možnosti spuštění](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. Okno příkazového řádku se zobrazuje stav úlohy. Pokud má být úloha spuštěna úspěšně, vytvoří složku, která vypadá jako "2018-02-23-11-31-42" váš projekt ve složce v cestě "Sady Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Přejděte do cesty ke složce, pokud chcete zobrazit výsledky v místní složce:

   Můžete také přihlásit k portálu Azure a ověřte, že se vytvoří úloha. 

   ![Složka výsledků](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Odeslání úlohy do Azure

1. Před odesláním úlohu do Azure, je nutné připojit k předplatnému Azure. Otevřete **Průzkumníka serveru** > klikněte pravým tlačítkem na **Azure** > **připojit se k odběru služby Microsoft Azure** > Přihlaste se k předplatnému Azure.  

2. Chcete-li odeslat úlohu do Azure, přejděte do editoru dotazů > vyberte **odeslání do Azure**.  

3. Otevře se automaticky otevírané okno, kde můžete aktualizovat existující hraniční úlohu nebo vytvořte novou. Když aktualizujete stávající úloze, nahradí všechny úlohy konfigurace, v tomto scénáři, bude publikovat novou úlohu. Vyberte **vytvoření nové úlohy Azure Stream Analytics** > zadejte název pro úlohu něco jako **MyASAEdgeJob** > vyberte požadované **předplatné**, **Skupiny prostředků**, a **umístění** > vyberte **odeslání**.

   ![Odeslat do Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Teď vaše úlohy Stream Analytics Edge byl vytvořen, můžete se podívat do [spouštění úloh na IoT Edge kurzu](stream-analytics-edge.md) se dozvíte, jak ji nasadit do zařízení. 

## <a name="manage-the-job"></a>Spravovat úlohy 

Můžete zobrazit stav úlohy a úlohy diagram z Průzkumníka serveru. Z **Průzkumníka serveru** > **Stream Analytics** > rozbalte předplatné a skupině prostředků, kde jste nasadili úlohu Edge > můžete zobrazit MyASAEdgejob stavem  **Vytvořit**. Rozbalte uzel vaší úlohy a dvakrát klikněte na něj a otevřete zobrazení úloh.

![Možnosti Průzkumníka serveru](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno zobrazení úlohy vám poskytne operací, jako je aktualizace úlohu Odstranění úlohy, otevřením úlohu z portálu Azure atd.

![Diagram úlohy a další možnosti](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Další postup

* [Další informace o Azure Iot Edge](../iot-edge/how-iot-edge-works.md)
* [ASA na IoT Edge kurzu](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Pošlete svůj názor týmu pomocí tohoto průzkumu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
