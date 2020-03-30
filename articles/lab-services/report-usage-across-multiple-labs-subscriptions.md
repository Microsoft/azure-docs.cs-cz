---
title: Využití azure devtest labs ve více testovacích prostředích a předplatných
description: Zjistěte, jak nahlásit využití azure devtest labs napříč několika testovacími prostředími a předplatnými.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169184"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Nastavte využití laboratoří Azure DevTest Labs ve více testovacích prostředích a předplatných

Většina velkých organizací chce sledovat využití prostředků, aby byly efektivnější s těmito prostředky vizualizací trendů a odlehlých hodnot v využití. Na základě využití prostředků mohou vlastníci nebo správci testovacího prostředí přizpůsobit testovací prostředí za [účelem zlepšení využití prostředků a nákladů](https://docs.microsoft.com/azure/billing/billing-getting-started). V Azure DevTest Labs, můžete stáhnout využití prostředků na testovací prostředí umožňuje hlubší historický pohled na vzorce využití. Tyto vzorce použití mohou pomoci určit změny ke zlepšení efektivity. Většina podniků chce použití jednotlivých testovacích prostředí a celkové využití ve [více testovacích prostředích a předplatných](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

Tento článek popisuje, jak zpracovat informace o využití prostředků napříč více testovacích prostředí a odběry.

![Hlášení využití](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Individuální použití v laboratoři

Tato část popisuje, jak exportovat využití prostředků pro jedno testovací prostředí.

Před exportem využití prostředků DevTest Labs, musíte nastavit účet Azure Storage povolit různé soubory, které obsahují data o využití, které mají být uloženy. Existují dva běžné způsoby provedení exportu dat:

* [Rozhraní REST DevTest Labs](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* Modul PowerShell Az.Resource [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) s `exportResourceUsage`akcí , ID prostředku testovacího prostředí a potřebné parametry. 

    Článek [o exportu nebo odstranění osobních dat](personal-data-delete-export.md) obsahuje ukázkový skript prostředí PowerShell s podrobnými informacemi o exportovaných datech. 

    > [!NOTE]
    > Parametr date neobsahuje časové razítko, takže data zahrnují vše od půlnoci na základě časového pásma, ve kterém se nachází testovací prostředí.

Po dokončení exportu bude v úložišti objektů blob více souborů CSV s různými informacemi o prostředkůch.
  
V současné době existují dva soubory CSV:

* *virtualmachines.csv* - obsahuje informace o virtuálních počítačích v laboratoři
* *disks.csv* - obsahuje informace o různých discích v testovacím prostředí 

Tyto soubory jsou uloženy v kontejneru objektů blob *labresourceusage* pod názvem testovacího prostředí, jedinečné ID testovacího prostředí, datum spuštění a úplné nebo počáteční datum, které bylo založeno do požadavku na export. Příklad struktury objektu blob by bylo:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Export využití pro všechna testovací prostředí

Chcete-li exportovat informace o použití pro více testovacích prostředí, zvažte použití 

* [Funkce Azure](https://docs.microsoft.com/azure/azure-functions/), dostupné v mnoha jazycích, včetně PowerShellu, nebo 
* [Azure Automation runbook](https://docs.microsoft.com/azure/automation/), použijte PowerShell, Python nebo vlastní grafický návrhář k napsání kódu exportu.

Pomocí těchto technologií můžete provést jednotlivé testovací exporty ve všech testovacích prostředích v určitém datu a čase. 

Vaše funkce Azure by měla tlačit data do dlouhodobějšího úložiště. Při exportu dat pro více testovacích prostředí může export nějakou dobu trvat. Chcete-li pomoci s výkonem a snížit možnost duplikace informací, doporučujeme spustit každou laboratoř paralelně. Chcete-li dosáhnout paralelismu, spusťte funkce Azure asynchronně. Využijte také aktivační událost časovače, kterou funkce Azure nabízejí.

## <a name="using-a-long-term-storage"></a>Použití dlouhodobého skladování

Dlouhodobé úložiště konsoliduje informace o exportu z různých testovacích prostředí do jednoho zdroje dat. Další výhodou použití dlouhodobého úložiště je možnost odebrat soubory z účtu úložiště, aby se snížila duplicita a náklady. 

Dlouhodobé úložiště lze použít k jakékoli manipulaci s textem, například: 

* přidání popisných názvů
* vytváření složitých seskupení
* agregace dat.

Některá běžná řešení úložiště jsou: [SQL Server](https://azure.microsoft.com/services/sql-database/), Azure [Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)a [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Výběr dlouhodobého úložného řešení, které zvolíte, závisí na preferencích. Můžete zvážit výběr nástroje v závislosti na tom, co nabízí z hlediska dostupnosti interakce při vizualizaci dat.

## <a name="visualizing-data-and-gathering-insights"></a>Vizualizace dat a shromažďování přehledů

Pomocí nástroje pro vizualizaci dat, který si vyberete, se můžete připojit k dlouhodobému úložišti, abyste zobrazili data o využití a shromáždili přehledy pro ověření efektivity využití. [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) lze například použít k uspořádání a zobrazení dat o využití. 

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) můžete použít k vytváření, propojování a správě prostředků v rámci rozhraní s jedním umístěním. Pokud je potřeba větší kontrolu, jednotlivé prostředky lze vytvořit v rámci jedné skupiny prostředků a spravovat nezávisle na službě Data Factory.  

## <a name="next-steps"></a>Další kroky

Jakmile je systém nastaven a data se přesunou do dlouhodobého úložiště, dalším krokem je přijít s otázkami, které data musí odpovědět. Například: 

-   Co je využití velikosti virtuálního počítače?

    Vybírají uživatelé vysoce výkonné (dražší) velikosti virtuálních počítačů?
-   Které obrázky marketplace se používají?

    Jsou vlastní image nejběžnější základnou virtuálních počítačů, pokud by se vytvořilo společné úložiště obrázků, jako [je Galerie sdílených obrázků](../virtual-machines/windows/shared-image-galleries.md) nebo [Továrna obrázků](image-factory-create.md).
-   Které vlastní obrázky jsou používány nebo nepoužívány?
