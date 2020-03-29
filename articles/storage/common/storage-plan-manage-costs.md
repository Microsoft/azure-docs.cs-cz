---
title: Plánování a správa nákladů pro Azure Storage
description: Zjistěte, jak plánovat a spravovat náklady pro Azure Storage pomocí analýzy nákladů na webu Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304521"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Plánování a správa nákladů pro Azure Storage

Tento článek popisuje, jak plánujete a spravujete náklady pro Azure Storage. Nejprve použijete cenovou kalkulačku Azure k plánování nákladů na úložiště před přidáním jakýchkoli prostředků. Až začnete používat prostředky Azure Storage, můžete pomocí funkcí správy nákladů nastavit rozpočty a sledovat náklady. Můžete také zkontrolovat předpokládané náklady a sledovat trendy výdajů a určit oblasti, ve kterých budete chtít jednat.

Nezapomeňte, že náklady na Azure Storage jsou jenom část měsíčních nákladů ve vaší azure účtu. I když tento článek vysvětluje, jak plánovat a spravovat náklady pro Azure Storage, se vám účtují za všechny služby Azure a prostředky používané pro vaše předplatné Azure, včetně služeb třetích stran. Až se seznámíte se správou nákladů na Azure Storage, můžete použít podobné metody pro správu nákladů pro všechny služby Azure používané ve vašem předplatném.

## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Odhad nákladů před vytvořením účtu Azure Storage

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady před vytvořením a zahájením přenosu dat do účtu Azure Storage.

1. Na stránce [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/) zvolte dlaždici **Účty úložiště.**

2. Posuňte se dolů po stránce a vyhledejte část **Účty úložiště** v odhadu.

3. Zrozených seznamů vyberte možnosti. 

   Při úpravě hodnoty těchto rozevíracích seznamů se odhad nákladů mění. Tento odhad se zobrazí v horním rohu, stejně jako v dolní části odhadu. 
    
   ![Sledování nákladů pomocí podokna Analýza nákladů](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Při změně hodnoty rozevíracího seznamu **Typ** se mění i další možnosti, které se zobrazí na tomto listu. Pomocí odkazů v části **Další informace** se dozvíte více o tom, co jednotlivé možnosti znamenají a jak tyto možnosti ovlivňují cenu operací souvisejících s úložištěm. 

4. Upravte zbývající možnosti, abyste viděli jejich vliv na váš odhad.

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Pro účely řízení nákladů můžete vytvořit [rozpočty](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) a nastavit upozornění, která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné jako součást celkové strategie monitorování nákladů. Mohou však mít omezené funkce pro správu jednotlivých nákladů na služby Azure, jako jsou náklady na Azure Storage, protože jsou navržené ke sledování nákladů na vyšší úrovni.

## <a name="monitor-costs"></a>Sledování nákladů

Při používání prostředků Azure s Azure Storage vám vznikají náklady. Jednotkové náklady na využití zdrojů se liší podle časových intervalů (sekund, minut, hodin a dnů) nebo podle využití jednotky (bajty, megabajty a tak dále).) Náklady vznikají ihned po spuštění využití Služby Azure Storage. Náklady se zobrazí v podokně [analýzy nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md) na webu Azure Portal.

Když použijete analýzu nákladů, můžete zobrazit náklady na úložiště Azure v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přechod na delší zobrazení v průběhu času vám může pomoci identifikovat trendy výdajů a zjistit, kde mohlo dojít k nadměrným výdajům. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny.

Zobrazení nákladů na úložiště Azure v analýze nákladů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Otevřete okno **Řízení nákladů + Fakturace,** v nabídce vyberte **Řízení nákladů** a pak vyberte **Analýza nákladů**. Potom můžete změnit obor pro konkrétní předplatné z rozbalovací **nabídky Obor.**

   ![Sledování nákladů pomocí podokna Analýza nákladů](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Pokud chcete zobrazit jenom náklady na Azure Storage, vyberte **Přidat filtr** a pak vyberte **Název služby**. Potom zvolte **úložiště** ze seznamu. 

   Tady je příklad, který ukazuje náklady jen na Azure Storage:

   ![Sledování nákladů na úložiště pomocí podokna Analýza nákladů](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

V předchozím příkladu se zobrazí aktuální náklady na službu. Zobrazí se také náklady podle oblastí Azure (umístění) a podle skupiny prostředků.  

## <a name="next-steps"></a>Další kroky

Další informace o správě nákladů pomocí [analýzy nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Další informace o tom, jak ceny ve Službě Azure Storage fungují, najdete v následujících článcích:

- [Ceny Azure Storage Overview](https://azure.microsoft.com/pricing/details/storage/)
- [Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity](../blobs/storage-blob-reserved-capacity.md)
