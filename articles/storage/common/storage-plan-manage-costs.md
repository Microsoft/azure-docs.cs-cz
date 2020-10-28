---
title: Plánování a Správa nákladů pro Azure Blob Storage
description: Naučte se plánovat a spravovat náklady na úložiště objektů BLOB v Azure pomocí analýzy nákladů v Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: c809a1ae525fb7fab6dceebb3241e229e8a0284e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781834"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Plánování a Správa nákladů pro Azure Blob Storage

Tento článek vám pomůže plánovat a spravovat náklady na službu Azure Blob Storage. Nejprve odhadujte náklady pomocí cenové kalkulačky Azure. Po vytvoření účtu úložiště Optimalizujte účet, abyste platíte jenom za to, co potřebujete. Pomocí funkcí pro správu nákladů můžete nastavit rozpočty a sledovat náklady. Můžete si také projít předpokládané náklady a monitorovat trendy útraty a identifikovat oblasti, kde byste mohli chtít působit.

Pamatujte, že náklady na úložiště objektů BLOB jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak odhadnout a spravovat náklady na úložiště objektů blob, účtují se vám všechny služby a prostředky Azure, které se používají pro vaše předplatné Azure, včetně služeb třetích stran. Až budete obeznámeni se správou nákladů na úložiště objektů blob, můžete použít podobné metody pro správu nákladů na všechny služby Azure používané ve vašem předplatném.

## <a name="estimate-costs"></a>Odhadněte náklady

Pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady před tím, než vytvoříte a začnete přenášet data na účet Azure Storage.

1. Na stránce [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte dlaždici **účty úložiště** .

2. Posuňte se dolů na stránce a vyhledejte část s **účty úložiště** vašeho odhadu.

3. V rozevíracích seznamech vyberte možnosti. 

   Při úpravě hodnoty těchto rozevíracích seznamů se odhad nákladů změní. Tento odhad se zobrazí v horním rohu a také na konci odhadu.

   ![Snímek obrazovky zobrazující odhad](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Při změně hodnoty rozevíracího seznamu **typ** se změní také další možnosti, které se zobrazí v tomto listu. Pomocí odkazů v části **Další informace** se dozvíte víc o tom, co jednotlivé možnosti znamená a jak tyto možnosti ovlivňují cenu operací souvisejících s úložištěm. 

4. Úpravou zbývajících možností zobrazíte jejich vliv na odhad.

## <a name="optimize-costs"></a>Optimalizace nákladů

Pomocí těchto možností můžete snížit náklady. 

- Rezervovat kapacitu úložiště

- Uspořádání dat do úrovní přístupu

- Automaticky přesouvat data mezi úrovněmi přístupu

V této části jsou popsány jednotlivé možnosti podrobněji. 

#### <a name="reserve-storage-capacity"></a>Rezervovat kapacitu úložiště

Můžete ušetřit peníze za náklady na úložiště dat objektů BLOB s využitím Azure Storage rezervované kapacity. Azure Storage Rezervovaná kapacita nabízí slevu na kapacitu pro objekty blob bloku a pro Azure Data Lake Storage Gen2 data v účtech úložiště Standard, když potvrdíte rezervaci za jeden rok nebo tři roky. Rezervace poskytuje pevnou velikost kapacity úložiště pro podmínky rezervace. Azure Storage Rezervovaná kapacita může významně snížit náklady na kapacitu pro objekty blob bloku a Azure Data Lake Storage Gen2 data. 

Další informace najdete v tématu [optimalizace nákladů na úložiště objektů BLOB s využitím rezervované kapacity](../blobs/storage-blob-reserved-capacity.md).

#### <a name="organize-data-into-access-tiers"></a>Uspořádání dat do úrovní přístupu

Můžete snížit náklady tím, že umístíte data objektů blob do nejnižších cenově nejefektivnějších úrovní přístupu. Vyberte si ze tří vrstev, které jsou navržené k optimalizaci nákladů na používání dat. Například vrstva *Hot* má vyšší náklady na úložiště, ale nižší náklady na přístup. Proto pokud plánujete přístup k datům často, může být horká úroveň nejúčinnější volbou. Pokud plánujete přístup k datům méně často, úroveň *studená* nebo *archivní* může být nejužitečnější, protože vyvolává náklady na přístup k datům a současně snižuje náklady na ukládání dat.    

Další informace najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Automaticky přesouvat data mezi úrovněmi přístupu

Zásady správy životního cyklu použijte k pravidelnému přesunu dat mezi vrstvami, abyste ušetřili nejvíc peněz. Tyto zásady mohou přesouvat data do pomocí pravidel, která zadáte. Můžete například vytvořit pravidlo, které přesune objekty blob do archivní úrovně, pokud se tento objekt BLOB v 90 dnech nezměnil. Vytvořením zásad, které upraví úroveň přístupu vašich dat, můžete navrhnout nejlevnější možnosti úložiště podle vašich potřeb.

Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) .

## <a name="create-budgets"></a>Tvorba rozpočtů

Pro účely řízení nákladů můžete vytvořit [rozpočty](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) a nastavit upozornění, která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. Můžou ale mít omezené funkce pro správu jednotlivých nákladů na služby Azure, jako jsou náklady na Azure Storage, protože jsou navržené tak, aby sledovaly náklady na vyšší úrovni.

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků Azure s Azure Storage se vám účtují náklady. Náklady na jednotku využití prostředků se liší podle časových intervalů (sekundy, minuty, hodiny a dny) nebo podle využití jednotek (bajty, megabajtů atd.). Náklady se účtují ihned po využití Azure Storage spustí. Náklady můžete zobrazit v podokně [Analýza nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md) v Azure Portal.

Při použití analýzy nákladů můžete zobrazit Azure Storage náklady v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Můžete také zobrazit náklady na rozpočty a předpokládané náklady. Přepínáním na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty a zjistit, kde došlo k nadměrnému útratě. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde byly překročeny.

>[!NOTE]
> Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure. Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../../cost-management-billing/costs/assign-access-acm-data.md).

Postup zobrazení Azure Storagech nákladů při analýze nákladů:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

2. Otevřete okno **cost management + fakturace** , v nabídce vyberte možnost **Správa nákladů** a pak vyberte **Analýza nákladů** . V rozevíracím seznamu **Rozsah** pak můžete změnit obor pro konkrétní předplatné.

   ![Snímek obrazovky znázorňující rozsah](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Chcete-li zobrazit pouze náklady na Azure Storage, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby** . Pak ze seznamu vyberte **úložiště** . 

   Tady je příklad, který ukazuje náklady jenom pro Azure Storage:

   ![Snímek obrazovky zobrazující filtr podle úložiště](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

V předchozím příkladu vidíte aktuální náklady za službu. Také se zobrazí náklady podle oblastí Azure (umístění) a skupiny prostředků. Můžete přidat i další filtry (například: filtr pro zobrazení nákladů na konkrétní účty úložiště).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Další informace o tom, jak ceny pracují s Azure Storage, najdete v následujících článcích:

- [Azure Storage Přehled cen](https://azure.microsoft.com/pricing/details/storage/)
- [Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity](../blobs/storage-blob-reserved-capacity.md)