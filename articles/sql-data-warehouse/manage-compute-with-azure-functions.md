---
title: 'Kurz: Spravovat výpočetní s Azure Functions v Azure SQL Data Warehouse | Microsoft Docs'
description: Použití funkcí Azure ke správě výpočetního výkonu datového skladu.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 48428ef329de4719a25afd20c21ac102bba540a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188442"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Funkce Azure použijte ke správě výpočetní prostředky v Azure SQL Data Warehouse

Tento kurz používá funkce Azure ke správě výpočetních prostředků pro datový sklad v Azure SQL Data Warehouse.

Abyste mohli používat aplikaci Azure Function App se službou SQL Data Warehouse, musíte ve stejném předplatném jako instanci datového skladu vytvořit [účet instančního objektu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) s přístupem Přispěvatel. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Nasazení na základě časovače škálování pomocí šablony Azure Resource Manager

Pokud chcete nasadit šablonu, potřebujete následující informace:

- Název skupiny prostředků, ve které je vaše instance SQL Data Warehouse
- Název logického serveru, na kterém je vaše instance SQL Data Warehouse
- Název vaší instance SQL Data Warehouse
- ID tenanta (ID adresáře) vaší služby Azure Active Directory
- ID předplatného 
- ID aplikace instančního objektu
- Tajný klíč instančního objektu

Jakmile máte předchozí informace o nasazení této šablony:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Jakmile nasadíte šablony, byste měli najít tři nové prostředky: bezplatným plánem Azure App Service, plán na základě spotřeby aplikaci funkce a účet úložiště, která zpracovává protokolování a operace fronty. Pokračujte a přečtěte si další části, ve kterých se dozvíte, jak upravit nasazené funkce, aby vyhovovaly vašim požadavkům.

## <a name="change-the-compute-level"></a>Změnit úroveň výpočetní

1. Přejděte do své služby Function App. Pokud jste nasadili šablonu s výchozími hodnotami, měla by tato služba mít název *DWOperations*. Po otevření vaší aplikace Function App byste si měli všimnout, že do vaší služby Function App je nasazených pět funkcí. 

   ![Funkce nasazené s šablonou](media/manage-compute-with-azure-functions/five-functions.png)

2. Vyberte *DWScaleDownTrigger* nebo *DWScaleUpTrigger* podle toho, jestli chcete změnit čas vertikálního navýšení nebo snížení kapacity. V rozevírací nabídce vyberte integrací.

   ![Výběr možnosti Integrace pro funkci](media/manage-compute-with-azure-functions/select-integrate.png)

3. Momentálně by zobrazená hodnota měla být *%ScaleDownTime%* nebo *%ScaleUpTime%*. Tyto hodnoty označují, že je plán založený na hodnotách definovaných ve vašem [Nastavení aplikace][Application Settings]. Teď můžete ignorovat tuto hodnotu a změnit plán upřednostňovaný čas podle dalších krocích.

4. V oblasti plánu přidejte libovolný čas v podobě výrazu CRON, který odráží, jak často chcete vertikálně navyšovat kapacitu služby SQL Data Warehouse. 

  ![Změna plánu funkce](media/manage-compute-with-azure-functions/change-schedule.png)

  Hodnota `schedule` je [výraz CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression), který zahrnuje těchto šest polí: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Například *"0 30 9 ** 1-5"* by odráželo aktivační událost každý den v týdnu v 9:30:00. Další informace najdete v [příkladech plánů][schedule examples] služby Azure Functions.


## <a name="change-the-time-of-the-scale-operation"></a>Změňte čas operace škálování

1. Přejděte do své služby Function App. Pokud jste nasadili šablonu s výchozími hodnotami, měla by tato služba mít název *DWOperations*. Po otevření vaší aplikace Function App byste si měli všimnout, že do vaší služby Function App je nasazených pět funkcí. 

2. Vyberte *DWScaleDownTrigger* nebo *DWScaleUpTrigger* podle toho, jestli chcete změnit hodnotu výpočetního výkonu vertikálního navýšení nebo snížení kapacity. Po výběru funkcí by se ve vašem podokně měl zobrazit soubor *index.js*.

   ![Změna výpočetní úrovně triggeru funkce](media/manage-compute-with-azure-functions/index-js.png)

3. Změňte hodnotu *ServiceLevelObjective* na požadovanou úroveň a klikněte na Uložit. Tato hodnota je výpočetní úroveň, která bude škálovat podle plánu definovaný v oddílu integrací vaší instance datového skladu.

## <a name="use-pause-or-resume-instead-of-scale"></a>Použití pozastavení nebo obnovení místo škálování 

Aktuálně jsou ve výchozím nastavení zapnuté funkce *DWScaleDownTrigger* a *DWScaleUpTrigger*. Pokud místo toho chcete použít funkce pozastavení a obnovení, můžete povolit *DWPauseTrigger* nebo *DWResumeTrigger*.

1. Přejděte do podokna Funkce.

   ![Podokno Funkce](media/manage-compute-with-azure-functions/functions-pane.png)



2. Klikněte na posuvný přepínač u odpovídajících triggerů, které chcete povolit.

3. Přejděte na karty *Integrace* příslušných triggerů a změňte jejich plán.

   > [!NOTE]
   > Funkční rozdíl mezi škálování aktivační události a aktivačních událostí pozastavení nebo obnovení je zprávu, která je odeslána do fronty. Další informace najdete v tématu [přidat novou funkci aktivační událost][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Přidání nové funkce triggeru

Aktuálně jsou součástí šablony pouze dvě škálovací funkce. Pomocí těchto funkcí v průběhu dne, je možné pouze škálovat dolů jednou a až jednou. K podrobnějšímu řízení, jako je například škálování několikrát za den nebo s jiným chováním škálování na víkendy je nutné přidat další aktivační události.

1. Vytvořte novou prázdnou funkci. Vyberte *+* tlačítko téměř vaši polohu funkce Zobrazit v podokně funkce šablony.

   ![Vytvoření nové funkce](media/manage-compute-with-azure-functions/create-new-function.png)

2. Jako Jazyk vyberte *Javascript* a pak vyberte *TimerTrigger*.

   ![Vytvoření nové funkce](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Zadejte název funkce a nastavte plán. Tento obrázek ukazuje, jak aktivovat funkci každou sobotu o půlnoci (v noci z pátku).

   ![Vertikální snížení kapacity v sobotu](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Zkopírujte obsah souboru *index.js* z některé jiné funkce triggeru.

   ![Zkopírování obsahu souboru index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Nastavte vaše proměnná operace požadovaného chování takto:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Složité plánování

V této části ukážeme stručně co je nutné získat složitější plánování pozastavení, obnovení a škálování.

### <a name="example-1"></a>Příklad 1:

Každodenní vertikální navýšení kapacity v 8:00 na úroveň DW600 a vertikální snížení kapacity ve 20:00 na úroveň DW200.

| Funkce  | Plán     | Operace                                |
| :-------- | :----------- | :--------------------------------------- |
| Funkce1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Funkce2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Příklad 2: 

Denní škálování nahoru v 8: 00 do DW1000, snižovat jednou k DW600 ve 4 a snižovat na 22: 00 do DW200.

| Funkce  | Plán     | Operace                                |
| :-------- | :----------- | :--------------------------------------- |
| Funkce1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Funkce2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Funkce3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Příklad 3: 

Vertikální navýšení kapacity v 8:00 na úroveň DW1000 a jedno vertikální snížení kapacity na úroveň DW600 v 16:00 ve všední dny. Pozastavení v pátek ve 23:00 a obnovení v pondělí v 7:00 ráno.

| Funkce  | Plán       | Operace                                |
| :-------- | :------------- | :--------------------------------------- |
| Funkce1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Funkce2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Funkce3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Funkce4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [triggeru časovače](../azure-functions/functions-create-scheduled-function.md) pro funkce Azure.

Podívejte se na [úložiště ukázek](https://github.com/Microsoft/sql-data-warehouse-samples) pro službu SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function