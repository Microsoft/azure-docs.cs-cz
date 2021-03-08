---
title: Azure DevTest Labs využití napříč několika cvičeními a předplatnými
description: Naučte se, jak ohlásit Azure DevTest Labs využití napříč několika cvičeními a předplatnými.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1e4d1f0abb5596c7fd9d22740bf052827c2ca666
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452641"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Nahlášení využití Azure DevTest Labs napříč několika cvičeními a předplatnými

Většina velkých organizací chce pomocí těchto prostředků sledovat trendy a odlehlé využití prostředků, aby bylo možné s těmito prostředky lépe zefektivnit. Na základě využití prostředků můžou vlastníci testovacího prostředí nebo manažeři přizpůsobit cvičení, aby [vylepšili využití prostředků a náklady](../cost-management-billing/cost-management-billing-overview.md). V Azure DevTest Labs můžete stáhnout využití prostředků na testovací prostředí, které vám umožní hlubší historický vzhled vzorců používání. Tyto vzorce používání můžou usnadnit změnu v vylepšení efektivity. Většina podniků má individuální využití testovacího prostředí a celkové využití v rámci [více cvičení a předplatných](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

Tento článek popisuje, jak zpracovávat informace o využití prostředků napříč několika cvičeními a předplatnými.

![Hlášení využití](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Individuální použití testovacího prostředí

Tato část popisuje, jak exportovat využití prostředků v jednom testovacím prostředí.

Než budete moct exportovat využití prostředků DevTest Labs, musíte nastavit účet Azure Storage, aby bylo možné ukládat různé soubory, které obsahují data o využití. Existují dva běžné způsoby, jak spustit Export dat:

* [REST API DevTest Labs](/rest/api/dtl/labs/exportresourceusage) 
* PowerShell AZ. Resource Module [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) s akcí `exportResourceUsage` , ID prostředku testovacího prostředí a potřebné parametry. 

    Článek o [exportu nebo odstranění osobních údajů](personal-data-delete-export.md) obsahuje ukázkový skript prostředí PowerShell s podrobnými informacemi o exportovaných datech. 

    > [!NOTE]
    > Parametr data neobsahuje časové razítko, takže data zahrnují vše od půlnoci na základě časového pásma, ve kterém je testovací prostředí umístěno.

Po dokončení exportu bude v úložišti objektů BLOB více souborů CSV s různými informacemi o prostředcích.
  
V současné době jsou k dispozici dva soubory CSV:

* *virtualmachines.csv* – obsahuje informace o virtuálních počítačích v testovacím prostředí.
* *disks.csv* – obsahuje informace o různých discích v testovacím prostředí. 

Tyto soubory jsou uložené v kontejneru objektů BLOB *labresourceusage* v rámci názvu testovacího prostředí, jedinečného ID testovacího prostředí, data provedení a úplného nebo počátečního data, které bylo založené na žádosti o export. Příklad struktury objektu BLOB by byl:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Export využití pro všechny laboratoře

Pokud chcete exportovat informace o využití pro víc cvičení, zvažte použití 

* [Azure Functions](../azure-functions/index.yml), k dispozici v mnoha jazycích, včetně PowerShellu nebo 
* [Azure Automation sadu Runbook](../automation/index.yml), pomocí PowerShellu, Pythonu nebo vlastního grafického návrháře napište kód exportu.

Pomocí těchto technologií můžete spustit jednotlivé exporty testovacího prostředí ve všech cvičeních v konkrétní datum a čas. 

Funkce Azure by měla nabízet data pro dlouhodobé uložení. Při exportu dat pro více cvičení může export nějakou dobu trvat. Abychom vám pomohli s výkonem a omezit možnost duplikace informací, doporučujeme spustit každé testovací prostředí paralelně. Pro dosažení paralelismu spouštějte Azure Functions asynchronně. Využijte také Trigger časovače, který Azure Functions nabídku.

## <a name="using-a-long-term-storage"></a>Použití dlouhodobě vybývajícího úložiště

Dlouhodobá úložiště konsoliduje informace o exportu z různých laboratoří do jednoho zdroje dat. Další výhodou použití dlouhodobého úložiště je schopnost odebrat soubory z účtu úložiště, aby se snížilo duplicity a náklady. 

Dlouhodobé úložiště lze použít k provedení jakékoli manipulace s textem, například: 

* přidávání popisných názvů
* vytváření komplexních seskupení
* agregace dat.

Mezi běžná řešení úložiště patří: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)a [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Výběr řešení dlouhodobého úložiště, které zvolíte, závisí na předvolbách. Můžete zvážit výběr nástroje podle toho, co nabízí z pohledu dostupnosti interakce při vizualizaci dat.

## <a name="visualizing-data-and-gathering-insights"></a>Vizualizace dat a shromažďování přehledů

Pomocí nástroje pro vizualizaci dat podle vlastního výběru se připojte k dlouhodobému úložišti, abyste mohli zobrazit data o využití a shromažďovat přehledy pro ověření efektivity využití. [Power BI](/power-bi/power-bi-overview) lze například použít k uspořádání a zobrazení dat o využití. 

Pomocí [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) můžete vytvářet, propojovat a spravovat prostředky v rámci jednoho rozhraní umístění. Pokud je potřeba větší řízení, může se jednotlivé prostředky vytvořit v rámci jedné skupiny prostředků a spravovat nezávisle na službě Data Factory.  

## <a name="next-steps"></a>Další kroky

Až se systém nastaví a data se přesunou do dlouhodobého úložiště, další krok se doplní otázkami, které data potřebují k zodpovězení. Například: 

-   Jaké je využití velikosti virtuálního počítače?

    Mají uživatelé výběr vysoce výkonných (dražších) velikostí virtuálních počítačů?
-   Které image Marketplace se používají?

    Jsou vlastní image nejběžnějším základem virtuálních počítačů, které by měly být sestavené jako [Galerie sdílených imagí](../virtual-machines/shared-image-galleries.md) nebo [objekt pro vytváření imagí](image-factory-create.md).
-   Které vlastní image se používají nebo které se nepoužívají?