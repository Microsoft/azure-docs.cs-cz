---
title: 'Kurz: Správa výpočetních prostředků pomocí funkcí Azure'
description: Jak používat funkce Azure ke správě výpočetního fondu SQL v Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0317b3a3e7ab13a78a5d1fe3672d664030436ab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346636"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Správa výpočetních prostředků ve fondu SQL Azure Synapse Analytics pomocí funkcí Azure funkce

Tento kurz používá funkce Azure ke správě výpočetních prostředků pro fond SQL v Azure Synapse Analytics.

Chcete-li používat aplikaci Azure Function App s fondem SQL, musíte vytvořit [účet hlavního účtu služby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) s přístupem přispěvatele v rámci stejného předplatného jako instance fondu SQL. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Nasazení škálování založeného na časovači pomocí šablony Azure Resource Manageru

Chcete-li nasadit šablonu, potřebujete následující informace:

- Název skupiny prostředků, ve které se nachází instance fondu SQL
- Název logického serveru, ve které je instance fondu SQL
- Název instance fondu SQL
- ID tenanta (ID adresáře) vaší služby Azure Active Directory
- ID předplatného 
- ID aplikace instančního objektu
- Tajný klíč instančního objektu

Jakmile budete mít předchozí informace, nasaďte tuto šablonu:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Po nasazení šablony byste měli najít tři nové prostředky: bezplatný plán služby Azure App Service, plán aplikace s využitím funkcí založené na spotřebě a účet úložiště, který zpracovává protokolování a provozní frontu. Pokračujte a přečtěte si další části, ve kterých se dozvíte, jak upravit nasazené funkce, aby vyhovovaly vašim požadavkům.

## <a name="change-the-compute-level"></a>Změna výpočetní úrovně

1. Přejděte do své služby Function App. Pokud jste nasadili šablonu s výchozími hodnotami, měla by tato služba mít název *DWOperations*. Po otevření vaší aplikace Function App byste si měli všimnout, že do vaší služby Function App je nasazených pět funkcí. 

   ![Funkce nasazené s šablonou](./media/manage-compute-with-azure-functions/five-functions.png)

2. Vyberte *DWScaleDownTrigger* nebo *DWScaleUpTrigger* podle toho, jestli chcete změnit čas vertikálního navýšení nebo snížení kapacity. V rozevírací nabídce vyberte Integrovat.

   ![Výběr možnosti Integrace pro funkci](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Momentálně by zobrazená hodnota měla být *%ScaleDownTime%* nebo *%ScaleUpTime%*. Tyto hodnoty označují, že je plán založený na hodnotách definovaných ve vašem [Nastavení aplikace](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). Prozatím můžete tuto hodnotu ignorovat a změnit plán na preferovaný čas na základě dalších kroků.

4. V oblasti plánu přidejte libovolný čas v podobě výrazu CRON, který odráží, jak často chcete vertikálně navyšovat kapacitu služby SQL Data Warehouse. 

   ![Změna plánu funkce](./media/manage-compute-with-azure-functions/change-schedule.png)

   Hodnota `schedule` je [výraz CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression), který zahrnuje těchto šest polí: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Například *"0 30 9 * * 1-5"* by odráželaktivační událost každý všední den v 9:30. Další informace najdete v [příkladech plánů](../../azure-functions/functions-bindings-timer.md#example) služby Azure Functions.


## <a name="change-the-time-of-the-scale-operation"></a>Změna času operace škálování

1. Přejděte do své služby Function App. Pokud jste nasadili šablonu s výchozími hodnotami, měla by tato služba mít název *DWOperations*. Po otevření vaší aplikace Function App byste si měli všimnout, že do vaší služby Function App je nasazených pět funkcí. 

2. Vyberte *DWScaleDownTrigger* nebo *DWScaleUpTrigger* podle toho, jestli chcete změnit hodnotu výpočetního výkonu vertikálního navýšení nebo snížení kapacity. Po výběru funkcí by se ve vašem podokně měl zobrazit soubor *index.js*.

   ![Změna výpočetní úrovně triggeru funkce](././media/manage-compute-with-azure-functions/index-js.png)

3. Změňte hodnotu *ServiceLevelObjective* na požadovanou úroveň a klikněte na Uložit. Tato hodnota je výpočetní úroveň, na kterou se instance datového skladu bude škálovat na základě plánu definovaného v části Integrace.

## <a name="use-pause-or-resume-instead-of-scale"></a>Použití pozastavení nebo obnovení místo škálování 

Aktuálně jsou ve výchozím nastavení zapnuté funkce *DWScaleDownTrigger* a *DWScaleUpTrigger*. Pokud místo toho chcete použít funkce pozastavení a obnovení, můžete povolit *DWPauseTrigger* nebo *DWResumeTrigger*.

1. Přejděte do podokna Funkce.

   ![Podokno Funkce](./media/manage-compute-with-azure-functions/functions-pane.png)



2. Klikněte na posuvný přepínač u odpovídajících triggerů, které chcete povolit.

3. Přejděte na karty *Integrace* příslušných triggerů a změňte jejich plán.

   > [!NOTE]
   > Funkční rozdíl mezi aktivační události škálování a pozastavení/obnovení aktivační události je zpráva, která je odeslána do fronty. Další informace naleznete [v tématu Přidání nové funkce aktivační události](manage-compute-with-azure-functions.md#add-a-new-trigger-function).


## <a name="add-a-new-trigger-function"></a>Přidání nové funkce triggeru

Aktuálně jsou součástí šablony pouze dvě škálovací funkce. S těmito funkcemi můžete během dne škálovat pouze jednou a nahoru. Pro podrobnější ovládací prvek, jako je například škálování dolů několikrát za den nebo s chováním různých změn velikosti o víkendech, je třeba přidat další aktivační událost.

1. Vytvořte novou prázdnou funkci. Vyberte *+* tlačítko poblíž umístění Funkce, chcete-li zobrazit podokno předlohy funkce.

   ![Vytvoření nové funkce](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Jako Jazyk vyberte *Javascript* a pak vyberte *TimerTrigger*.

   ![Vytvoření nové funkce](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Zadejte název funkce a nastavte plán. Tento obrázek ukazuje, jak aktivovat funkci každou sobotu o půlnoci (v noci z pátku).

   ![Vertikální snížení kapacity v sobotu](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Zkopírujte obsah souboru *index.js* z některé jiné funkce triggeru.

   ![Zkopírování obsahu souboru index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Nastavte proměnnou operace na požadované chování následujícím způsobem:

   ```javascript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Složité plánování

Tato část stručně ukazuje, co je nezbytné pro získání složitější plánování pozastavení, obnovení a škálování možností.

### <a name="example-1"></a>Příklad 1:

Každodenní vertikální navýšení kapacity v 8:00 na úroveň DW600 a vertikální snížení kapacity ve 20:00 na úroveň DW200.

| Funkce  | Plán     | Operace                                |
| :-------- | :----------- | :--------------------------------------- |
| Funkce1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Funkce2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Příklad 2: 

Denní vertikálně se škálujte v 8:00 až 1000 DW1000, jednou se škálujte na DW600 v 16 hodin a v 10 hodin se škálujte v 10:00 až DW200.

| Funkce  | Plán     | Operace                                |
| :-------- | :----------- | :--------------------------------------- |
| Funkce1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
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



## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [triggeru časovače](../../azure-functions/functions-create-scheduled-function.md) pro funkce Azure.

Pokladna úložiště [ukázky](https://github.com/Microsoft/sql-data-warehouse-samples)fondu SQL .

