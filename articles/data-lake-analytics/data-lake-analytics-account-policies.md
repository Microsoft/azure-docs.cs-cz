---
title: Správa zásad účtů Azure Data Lake Analytics
description: Naučte se používat zásady účtů k řízení využití Data Lake Analytics účtu, jako je například maximální Austrálie a maximální počet úloh.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: ba69098f32e131714a15923aef64c3f6ba17e18f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013305"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Správa Azure Data Lake Analytics pomocí zásad účtů

Zásady účtu vám pomůžou řídit, jak se používají prostředky Azure Data Lake Analytics účet. Tyto zásady umožňují řídit náklady na používání Azure Data Lake Analytics. Pomocí těchto zásad můžete například zabránit neočekávaným špičkám nákladů tím, že omezíte počet jednotek Austrálie, které může účet současně používat. # # zásady na úrovni účtů

Tyto zásady platí pro všechny úlohy v účtu Data Lake Analytics.

## <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximální počet jednotek Austrálie v účtu Data Lake Analytics

Zásada řídí celkový počet jednotek analýzy (Austrálie), které může váš účet Data Lake Analytics použít. Ve výchozím nastavení je hodnota nastavena na 250. Například pokud je tato hodnota nastavená na 250 jednotek Austrálie, můžete mít jednu úlohu spuštěnou s přiřazeným 250 jednotkou Austrálie, nebo 10 úloh spuštěných s 25 jednotkami Austrálie. Další odeslané úlohy jsou zařazeny do fronty, dokud nebudou dokončeny spuštěné úlohy. Po dokončení běžících úloh se pro spuštění úloh ve frontě uvolní jednotky Austrálie.

Postup změny počtu jednotek Austrálie pro váš účet Data Lake Analytics:

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **limity a zásady**.
3. V části **maximální Austrálie** přesuňte posuvník pro výběr hodnoty nebo zadejte hodnotu do textového pole.
4. Klikněte na **Uložit**.

   > [!NOTE]
   > Pokud potřebujete více než výchozí (250) Austrálie, klikněte na portálu na tlačítko **help + podpora** a odešlete žádost o podporu. Počet jednotek Austrálie dostupných v účtu Data Lake Analytics můžete zvýšit.

## <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximální počet úloh, které mohou běžet souběžně

Tato zásada omezuje počet úloh, které mohou běžet souběžně. Ve výchozím nastavení je tato hodnota nastavená na 20. Pokud má Data Lake Analytics k dispozici Austrálie, nové úlohy se naplánují tak, aby se spouštěly okamžitě, dokud celkový počet spuštěných úloh nedosáhne hodnoty této zásady. Když dosáhnete maximálního počtu úloh, které můžou běžet současně, následné úlohy se zařadí do fronty v pořadí podle priority, dokud se jedna nebo víc spuštěné úlohy nedokončí (v závislosti na dostupném stupni Austrálie).

Změna počtu úloh, které mohou běžet současně:

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **limity a zásady**.
3. V části **maximální počet spuštěných úloh** přesunutím posuvníku vyberte hodnotu nebo zadejte hodnotu do textového pole.
4. Klikněte na **Uložit**.

   > [!NOTE]
   > Pokud potřebujete spustit více než výchozí (20) počet úloh, na portálu klikněte na tlačítko **help + podpora** a odešlete žádost o podporu. Počet úloh, které lze spustit současně v účtu Data Lake Analytics, lze zvýšit.

## <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak dlouho uchovat metadata a prostředky úlohy

Když vaši uživatelé spouštějí úlohy U-SQL, Služba Data Lake Analytics udržuje všechny související soubory. Tyto soubory obsahují skript U-SQL, soubory DLL, na které odkazuje skript U-SQL, zkompilované prostředky a statistiky. Soubory jsou ve složce/System/výchozího účtu Azure Data Lake Storage. Tato zásada určuje, jak dlouho se tyto prostředky ukládají, než se automaticky odstraní (výchozí hodnota je 30 dní). Tyto soubory můžete použít k ladění a k optimalizaci výkonu úloh, které v budoucnu znovu spustíte.

Postup změny, jak dlouho mají být uchovávány metadata a prostředky úlohy:

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **limity a zásady**.
3. V části **dny uložte dotazy úlohy** přesunutím posuvníku a výběrem hodnoty nebo zadáním hodnoty do textového pole.  
4. Klikněte na **Uložit**.

## <a name="job-level-policies"></a>Zásady na úrovni úlohy

Zásady na úrovni úlohy umožňují řídit maximální Austrálie a maximální prioritu, které mohou jednotliví uživatelé (nebo členové konkrétních skupin zabezpečení) nastavit u úloh, které odesílají. Tato zásada vám umožní řídit náklady vyvolané uživateli. Také vám umožňuje řídit, jaký vliv mají naplánované úlohy na úlohy s vysokou prioritou, které jsou spuštěny ve stejném účtu Data Lake Analytics.

Data Lake Analytics má dvě zásady, které můžete nastavit na úrovni úlohy:

- **Maximální počet jednotek au na úlohu**: uživatelé mohou odesílat pouze úlohy, které mají tento počet jednotek Austrálie. Ve výchozím nastavení je tento limit stejný jako maximální limit AU pro účet.

- **Priorita**: uživatelé mohou odesílat pouze úlohy, jejichž priorita je menší nebo rovna této hodnotě. Vyšší číslo označuje nižší prioritu. Ve výchozím nastavení je toto omezení nastaveno na hodnotu 1, což je nejvyšší možná priorita.

U každého účtu je nastavená výchozí zásada. Výchozí zásady platí pro všechny uživatele účtu. Můžete vytvořit další zásady pro konkrétní uživatele a skupiny.

> [!NOTE]
> Zásady na úrovni účtu a zásady na úrovni úlohy platí současně.

## <a name="add-a-policy-for-a-specific-user-or-group"></a>Přidat zásadu pro určitého uživatele nebo skupinu

1. V Azure Portal přejít na účet Data Lake Analytics.

2. Klikněte na **limity a zásady**.

3. V části **omezení odesílání úloh** klikněte na tlačítko **Přidat zásadu** . Pak vyberte nebo zadejte následující nastavení:

   1. **Název zásad COMPUTE**: zadejte název zásady, který vám připomene účel zásady.

   2. **Vyberte uživatele nebo skupinu**: Vyberte uživatele nebo skupinu, pro které se tyto zásady vztahují.

   3. **Nastavte limit úlohy au**: nastavte limit au, který se vztahuje na vybraného uživatele nebo skupinu.

   4. **Nastavte limit priority**: nastavte limit priority, který se vztahuje k vybranému uživateli nebo skupině.

4. Klikněte na **OK**.

5. Nová zásada je uvedená v tabulce **výchozí** zásady v části **omezení odesílání úloh**.

## <a name="delete-or-edit-an-existing-policy"></a>Odstraní nebo upraví existující zásady.

1. V Azure Portal přejít na účet Data Lake Analytics.

2. Klikněte na **limity a zásady**.

3. V části **omezení odesílání úloh** vyhledejte zásadu, kterou chcete upravit.

4. Pokud chcete zobrazit možnosti **odstranění** a **Úpravy** ve sloupci úplně vpravo v tabulce, klikněte na `...` . # # další prostředky pro zásady úlohy

- [Přehled zásad – Blogový příspěvek](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-overview)
- [Blogový příspěvek zásad na úrovni účtu](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy)
- [Blogový příspěvek zásad na úrovni úlohy](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy)

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Začínáme s Data Lake Analytics pomocí Azure Portal](data-lake-analytics-get-started-portal.md)
- [Správa Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-manage-use-powershell.md)