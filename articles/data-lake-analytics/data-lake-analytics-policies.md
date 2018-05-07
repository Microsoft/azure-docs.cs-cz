---
title: Správa zásad Azure Data Lake Analytics | Microsoft Docs
description: Naučte se používat zásady řízení použití účtu Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/30/2018
ms.author: saveenr
ms.openlocfilehash: db2712f0b57f2a5d60eae4aec21a4070c7a907a3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Správa Azure Data Lake Analytics pomocí zásad

Pomocí zásady účtů, můžete určit, jak prostředků účtu Azure Data Lake Analytics se používá. Tyto zásady umožňují řídit náklady na používání Azure Data Lake Analytics. Například pomocí těchto zásad můžete zabránit neočekávané náklady špičky omezením kolik Austrálie účet můžete použít současně.

## <a name="account-level-policies"></a>Zásady na úrovni účtu

Tyto zásady platí pro všechny úlohy v účtu Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximální počet Austrálie v účtu Data Lake Analytics
Zásady řídí celkový počet jednotek Analytics (Austrálie) můžete použít váš účet Data Lake Analytics. Ve výchozím nastavení je hodnota nastavena na 250. Například, pokud je tato hodnota nastavena na 250 Austrálie, může mít jednu úlohu s 250 Austrálie přiřazené nebo běží s 25 10 úlohy Austrálie každý. Další úlohy, které jsou odeslány jsou zařazeny do fronty, dokud se všechny spuštěné úlohy. Po dokončení probíhající úlohy jsou Austrálie jsou uvolněny pro spuštění ve frontě úloh.

Chcete-li změnit počet Austrálie pro váš účet Data Lake Analytics:

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **maximální Austrálie**, přesuňte jezdec vyberte hodnotu nebo zadejte hodnotu v textovém poli. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete více než výchozí (250) Austrálie, na portálu, klikněte na tlačítko **podpora + nápovědy** odeslat žádost o podporu. Je možné zvýšit počet Austrálie k dispozici v účtu Data Lake Analytics.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximální počet úloh, které můžou běžet současně
Zásady určuje, kolik úlohy můžete spustit ve stejnou dobu. Ve výchozím nastavení je tato hodnota nastavena na hodnotu 20. Pokud vaše Data Lake Analytics má Austrálie k dispozici, nové úlohy jsou naplánovány ke spuštění až celkový počet spuštěných úloh nedosáhne hodnoty těchto zásad. Když se dostanete maximální počet úloh, které můžou běžet současně, následné úlohy jsou zařazeny do fronty v pořadí podle priority, dokud jeden nebo více spuštěné úlohy dokončení (v závislosti na dostupnosti AU).

Chcete-li změnit počet úloh, které můžou běžet současně:

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **maximální číslo z spuštění úlohy**, přesuňte jezdec vyberte hodnotu nebo zadejte hodnotu v textovém poli. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete více než výchozí (20) počet úloh, spusťte na portálu, klikněte na tlačítko **podpora + nápovědy** odeslat žádost o podporu. Je možné zvýšit počet úloh, které můžou běžet současně v účtu Data Lake Analytics.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak dlouho chcete zachovat metadata úlohy a prostředky 
Když uživatelé spustí úloh U-SQL, službě Data Lake Analytics uchovává všechny související soubory. Související soubory zahrnují skript U-SQL, soubory knihoven DLL, kterou se odkazuje v skript U-SQL, kompilované prostředků a statistiky. Soubory jsou ve složce /system/ výchozí účet Azure Data Lake Storage. Tato zásada určuje, jak dlouho tyto prostředky jsou uložené před automaticky odstraní (výchozí hodnota je 30 dní). Tyto soubory můžete použít pro ladění a optimalizace výkonu úloh, které budete v budoucnu spusťte znovu.

Chcete-li změnit jak dlouho Pokud chcete zachovat metadata úlohy a prostředky:

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **dnů zachovat úloha se dotazuje**, přesuňte jezdec vyberte hodnotu nebo zadejte hodnotu v textovém poli.  
4. Klikněte na **Uložit**.

## <a name="job-level-policies"></a>Zásady na úrovni úlohy

Pomocí zásad na úrovni úlohy můžete určit maximální Austrálie a maximální priority, kterou jednotlivé uživatele (nebo členy určité skupiny zabezpečení) můžete nastavit na úlohy, které se odesílají. Tato zásada umožňuje řídit náklady uživatelé. To také umožňuje řízení o tom, že naplánované úlohy může mít na produkční s vysokou prioritou úloh, které jsou spuštěné ve stejném účtu Data Lake Analytics.

Data Lake Analytics má dvě zásady, které můžete nastavit na úrovni úlohy:

* **AU limit na jednu úlohu**: uživatelé lze odeslat pouze úlohy, které je nutné tento počet Austrálie. Ve výchozím nastavení tento limit je stejná jako maximální limit AU pro účet.
* **Priorita**: uživatelé lze odeslat pouze úlohy, které mají nižší než nebo rovna hodnotě tuto hodnotu. Vyšší číslo označuje s nižší prioritou. Tento limit je ve výchozím nastavení, nastavena na hodnotu 1, což je nejvyšší možná priorita.

Existuje výchozí zásada, nastavte pro každý účet. Výchozí zásady platí pro všechny uživatele účtu. Další zásady můžete nastavit pro konkrétní uživatele a skupiny. 

> [!NOTE]
> Zásady na úrovni účtu a zásady na úrovni úlohy použít současně.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Přidání zásad pro konkrétního uživatele nebo skupiny

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **úlohy odeslání omezení**, klikněte **přidat zásadu** tlačítko. Potom vyberte nebo zadejte následující nastavení:
    1. **Název zásady výpočetní**: Zadejte název zásady, a tak poznáte, účelu zásad.
    2. **Vyberte uživatele nebo skupiny**: Vyberte uživatele nebo skupiny, tato zásada se vztahuje na.
    3. **Nastavit Limit AU úlohy**: omezit AU, která se vztahuje na vybrané uživatele nebo skupiny.
    4. **Nastavit Priority Limit**: Omezit prioritu, která se vztahuje na vybrané uživatele nebo skupiny.

4. Klikněte na tlačítko **OK**.

5. Nové zásady, je uvedena ve **výchozí** zásad v části tabulky **úlohy odeslání omezení**. 

### <a name="delete-or-edit-an-existing-policy"></a>Odstraňte nebo upravte existující zásady

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **úlohy odeslání omezení**, vyhledejte zásadu, kterou chcete upravit.
4.  Chcete-li zobrazit **odstranit** a **upravit** možnosti v polí v pravém sloupci tabulky, klikněte na tlačítko `...`.

## <a name="additional-resources-for-job-policies"></a>Další prostředky pro úlohy zásady
* [Příspěvek blogu přehled zásad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Zásady na úrovni účtu příspěvku na blogu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Zásady na úrovni úlohy příspěvku na blogu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Další postup

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

