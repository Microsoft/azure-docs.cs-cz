---
title: Správa zásad Azure Data Lake Analytics
description: Další informace o použití zásad pro řízení použití účtu Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: f84cb59e7d4fd7d8301d22348ca066a7f9d9e94e
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048814"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Správa Azure Data Lake Analytics pomocí zásad

Pomocí zásady účtů, můžete určit, jak prostředky účtu Azure Data Lake Analytics se používá. Tyto zásady umožňují řídit náklady na používání Azure Data Lake Analytics. Pomocí těchto zásad je třeba zabránit neočekávané poplatky špičky tím, že omezíte počet jednotek au účtu je současně využít.

## <a name="account-level-policies"></a>Zásady na úrovni účtu

Tyto zásady platí pro všechny úlohy v účtu Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximální počet jednotek analýzy v účtu Data Lake Analytics
Zásady řídí celkový počet jednotek analýzy (AU) můžete použít svůj účet Data Lake Analytics. Ve výchozím nastavení je hodnota nastavena na 250. Například, pokud je tato hodnota nastavena na 250 jednotek analýzy, může mít jednu úlohu s 250 jednotek analýzy přiřazených k nebo 10 úlohy spuštěné s 25 au každý. Další úlohy, které jsou odeslány se zařadí do fronty, dokud se nedokončí spuštěné úlohy. Po dokončení úlohy spuštěné au jsou uvolněny pro spuštění ve frontě úloh.

Chcete-li změnit počet jednotek AU pro svůj účet Data Lake Analytics:

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **maximální počet jednotek au**, přesuňte jezdec vybrat hodnotu nebo zadejte hodnotu v textovém poli. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete více než výchozí (250) jednotek analýzy, na portálu klikněte na **podporu a nápovědu** odeslat žádost o podporu. Je možné zvýšit počet jednotek analýzy, které jsou k dispozici v účtu Data Lake Analytics.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximální počet úloh, které můžou běžet současně
Zásady řídí, kolik úlohy můžete spouštět ve stejnou dobu. Ve výchozím nastavení tato hodnota nastavena na hodnotu 20. Pokud vaše Data Lake Analytics má k dispozici jednotek AU, nové úlohy jsou naplánovány ke spuštění okamžitě, dokud celkový počet spuštěných úloh dosáhne hodnoty těchto zásad. Při dosažení maximální počet úloh, které můžou běžet současně, následné úlohy se zařadí do fronty v pořadí podle priority, až do dokončení jedné nebo více spuštěných úloh (v závislosti na dostupnosti AU).

Chcete-li změnit počet úloh, které můžou běžet současně:

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **maximální číslo z spuštění úlohy**, přesuňte jezdec vybrat hodnotu nebo zadejte hodnotu v textovém poli. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete spustit více než výchozí (20) počet úloh, na portálu, klikněte na tlačítko **podporu a nápovědu** odeslat žádost o podporu. Je možné zvýšit počet úloh, které můžou běžet současně ve svém účtu Data Lake Analytics.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak dlouho chcete zachovat metadata úlohy a prostředky 
Pokud vaši uživatelé spuštění úloh U-SQL služby Data Lake Analytics uchovává všechny související soubory. Související soubory zahrnují skript U-SQL, souborů DLL odkazuje ve skriptu U-SQL, zkompilované prostředky a statistiky. Soubory jsou ve složce /system/ výchozího účtu úložiště Azure Data Lake. Tato zásada určuje, jak dlouho se uchovávají těchto prostředků, než se odstraní automaticky (výchozí hodnota je 30 dnů). Tyto soubory můžete použít pro ladění a optimalizace výkonu úloh, které budete v budoucnu znovu spustit.

Chcete-li změnit jak dlouho udržovat metadata úlohy a prostředky:

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **dnů uchování dotazů úloh**, přesuňte jezdec vybrat hodnotu nebo zadejte hodnotu v textovém poli.  
4. Klikněte na **Uložit**.

## <a name="job-level-policies"></a>Zásady na úrovni projektu

Pomocí zásad na úrovni projektu můžete určit maximální počet jednotek AU a nejvyšší prioritu, jednotlivé uživatele (nebo členy z konkrétních skupin zabezpečení) můžete nastavit na úlohách, které odesílají. Tato zásada umožňuje mít pod kontrolou náklady vzniklé uživatelů. Umožňuje vám také ovládací prvek může mít vliv naplánované úlohy v produkčním prostředí s vysokou prioritou úloh, které jsou spuštěny v rámci stejného účtu Data Lake Analytics.

Data Lake Analytics má dvě zásady, které můžete nastavit na úrovni úlohy:

* **Limit AU na úlohu**: uživatelé mohli odesílat pouze úlohy, které je nutné tento počet jednotek au. Ve výchozím nastavení toto omezení je stejná jako maximální limit AU pro tento účet.
* **Priorita**: uživatelé mohou odesílat pouze úlohy, s prioritou nižší než nebo rovna této hodnotě. Větší číslo označuje s nižší prioritou. Toto omezení je ve výchozím nastavení, nastavena na hodnotu 1, což je nejvyšší možná priorita.

Neexistuje výchozí zásadu nastavit u všech účtů. Výchozí zásady platí pro všechny uživatele účtu. Další zásady můžete nastavit pro konkrétní uživatele a skupiny. 

> [!NOTE]
> Zásady na úrovni účtu a zásad na úrovni projektu použít současně.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Přidání zásad pro konkrétního uživatele nebo skupiny

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **omezení odesílání úloh**, klikněte na tlačítko **přidat zásadu** tlačítko. Potom vyberte nebo zadejte následující nastavení:
    1. **Název zásady pro výpočet**: Zadejte název zásady, vás upozorní účel zásady.
    2. **Vyberte uživatele nebo skupinu**: Vyberte uživatele nebo skupiny, tyto zásady platí pro.
    3. **Nastavit Limit AU úlohy**: nastavte limit AU, který se vztahuje na vybrané uživatele nebo skupinu.
    4. **Nastavit Priority Limit**: omezit priority, která se použije pro vybrané uživatele nebo skupiny.

4. Klikněte na tlačítko **OK**.

5. Nová zásada se zobrazí v **výchozí** zásad tabulky v části **omezení odesílání úloh**. 

### <a name="delete-or-edit-an-existing-policy"></a>Odstranit nebo upravit existující zásadu

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **omezení odesílání úloh**, vyhledejte zásadu, kterou chcete upravit.
4.  Pokud chcete zobrazit **odstranit** a **upravit** možnosti, ve sloupci úplně vpravo v tabulce, klikněte na tlačítko `...`.

## <a name="additional-resources-for-job-policies"></a>Další zdroje informací pro zásady úlohy
* [Příspěvek na blogu přehled zásad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Zásady na úrovni účtu blogový příspěvek](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Příspěvek na blogu zásad na úrovni projektu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Další postup

* [Přehled služby Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí webu Azure portal](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure Powershellu](data-lake-analytics-manage-use-powershell.md)

