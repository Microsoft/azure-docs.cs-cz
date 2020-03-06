---
title: Plánování a Správa nákladů na Azure Storage
description: Naučte se plánovat a spravovat náklady na Azure Storage pomocí analýzy nákladů v Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304521"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Plánování a Správa nákladů na Azure Storage

Tento článek popisuje, jak plánujete a spravovat náklady na Azure Storage. Nejprve pomocí cenové kalkulačky Azure naplánujte náklady na úložiště před přidáním prostředků. Po zahájení používání prostředků Azure Storage použijte funkce pro správu nákladů k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpovědi s předpokládanými náklady a monitorovat trendy útraty a identifikovat oblasti, kde se možná budete chtít chovat.

Pamatujte na to, že náklady na Azure Storage jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na Azure Storage, účtují se vám všechny služby a prostředky Azure, které se používají pro vaše předplatné Azure, včetně služeb třetích stran. Až budete obeznámeni se správou nákladů na Azure Storage, můžete použít podobné metody pro správu nákladů na všechny služby Azure používané ve vašem předplatném.

## <a name="prerequisites"></a>Předpoklady

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku, který [vysvětluje data služby Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Odhad nákladů před vytvořením účtu Azure Storage

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady před tím, než vytvoříte a začnete přenášet data na účet Azure Storage.

1. Na stránce [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte dlaždici **účty úložiště** .

2. Posuňte se dolů na stránce a vyhledejte část s **účty úložiště** vašeho odhadu.

3. V rozevíracích seznamech vyberte možnosti. 

   Při úpravě hodnoty těchto rozevíracích seznamů se odhad nákladů změní. Tento odhad se zobrazí v horním rohu a také na konci odhadu. 
    
   ![Sledovat náklady pomocí podokna analýza nákladů](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Při změně hodnoty rozevíracího seznamu **typ** se změní také další možnosti, které se zobrazí v tomto listu. Pomocí odkazů v části **Další informace** se dozvíte víc o tom, co jednotlivé možnosti znamená a jak tyto možnosti ovlivňují cenu operací souvisejících s úložištěm. 

4. Úpravou zbývajících možností zobrazíte jejich vliv na odhad.

## <a name="use-budgets-and-cost-alerts"></a>Použití rozpočtů a upozornění na náklady

Můžete vytvářet [rozpočty](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) pro správu nákladů a vytvářet výstrahy, které automaticky upozorňují zúčastněné strany na anomálie a rizika plynoucí z výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. Můžou ale mít omezené funkce pro správu jednotlivých nákladů na služby Azure, jako jsou náklady na Azure Storage, protože jsou navržené tak, aby sledovaly náklady na vyšší úrovni.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků Azure s Azure Storage se vám účtují náklady. Náklady na jednotku využití prostředků se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek (bajty, megabajtů atd.). Náklady se účtují ihned po využití Azure Storage spustí. Náklady můžete zobrazit v podokně [Analýza nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md) v Azure Portal.

Při použití analýzy nákladů můžete zobrazit Azure Storage náklady v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přepínáním na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty a zjistit, kde došlo k nadměrnému útratě. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny.

Postup zobrazení Azure Storagech nákladů při analýze nákladů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

2. Otevřete okno **cost management + fakturace** , v nabídce vyberte možnost **Správa nákladů** a pak vyberte **Analýza nákladů**. V rozevíracím seznamu **Rozsah** pak můžete změnit obor pro konkrétní předplatné.

   ![Sledovat náklady pomocí podokna analýza nákladů](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Chcete-li zobrazit pouze náklady na Azure Storage, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak ze seznamu vyberte **úložiště** . 

   Tady je příklad, který ukazuje náklady jenom pro Azure Storage:

   ![Monitorování nákladů na úložiště pomocí podokna analýza nákladů](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

V předchozím příkladu vidíte aktuální náklady za službu. Také se zobrazí náklady podle oblastí Azure (umístění) a skupiny prostředků.  

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Další informace o tom, jak ceny pracují s Azure Storage, najdete v následujících článcích:

- [Azure Storage Přehled cen](https://azure.microsoft.com/pricing/details/storage/)
- [Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity](../blobs/storage-blob-reserved-capacity.md)
