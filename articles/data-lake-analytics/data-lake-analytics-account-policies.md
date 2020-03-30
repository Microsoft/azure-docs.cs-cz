---
title: Správa zásad účtu Azure Data Lake Analytics
description: Přečtěte si, jak pomocí zásad účtu řídit využití účtu Data Lake Analytics, jako jsou maximální au a maximální počet úloh.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72966434"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Správa Azure Data Lake Analytics pomocí zásad účtů

Zásady účtu vám pomůžou řídit, jak se budou používat prostředky účtu Azure Data Lake Analytics. Tyto zásady umožňují řídit náklady na používání Azure Data Lake Analytics. Například s těmito zásadami můžete zabránit neočekávaným nárůstem nákladů omezením počtu au účet může současně použít.

## <a name="account-level-policies"></a>Zásady na úrovni účtu

Tyto zásady platí pro všechny úlohy v účtu Data Lake Analytics.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximální počet au v účtu Data Lake Analytics
Zásada řídí celkový počet jednotek Analytics (AU), které může váš účet Data Lake Analytics používat. Ve výchozím nastavení je hodnota nastavena na 250. Například pokud je tato hodnota nastavena na 250 au, můžete mít jednu úlohu spuštěnou s 250 au přiřazenými nebo 10 úloh spuštěných s 25 au. Další úlohy, které jsou odeslány, jsou zařazeny do fronty, dokud nebudou spuštěny spuštěné úlohy. Po dokončení spuštění úlohy au jsou uvolněny pro úlohy ve frontě ke spuštění.

Změna počtu au pro váš účet Data Lake Analytics:

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klikněte na **Omezení a zásady**.
3. V části **Maximální počet vstupů**přesuňte jezdec a vyberte hodnotu nebo ji zadejte do textového pole. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete více než výchozí (250) AU, na portálu klikněte na **nápovědu+ podporu** a odešlete žádost o podporu. Počet au dostupných ve vašem účtu Data Lake Analytics lze zvýšit.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximální počet úloh, které lze spustit současně
Tato zásada omezuje, kolik úloh lze spustit současně. Ve výchozím nastavení je tato hodnota nastavena na 20. Pokud vaše Analýza datového jezera má k dispozici au, nové úlohy jsou naplánovány spustit okamžitě, dokud celkový počet spuštěných úloh dosáhne hodnoty této zásady. Když dosáhnete maximálního počtu úloh, které lze spustit současně, následné úlohy jsou zařazeny do fronty v pořadí priorit, dokud nebude dokončena jedna nebo více spuštěných úloh (v závislosti na dostupných au).

Chcete-li změnit počet úloh, které lze spustit současně:

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klikněte na **Omezení a zásady**.
3. V části **Maximální počet spuštěných úloh**přesuňte jezdec, abyste vyberte hodnotu, nebo ji zadejte do textového pole. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete spustit více než výchozí (20) počet úloh, na portálu klikněte na **nápovědu+ podporu** a odešlete žádost o podporu. Počet úloh, které lze spustit současně ve vašem účtu Data Lake Analytics, lze zvýšit.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak dlouho chcete zachovat metadata a prostředky úlohy 
Když uživatelé spouštějí úlohy U-SQL, služba Data Lake Analytics zachová všechny související soubory. Tyto soubory zahrnují u-SQL skript, DLL soubory odkazuje ve skriptu U-SQL, kompilované prostředky a statistiky. Soubory jsou ve složce /system/ výchozího účtu Azure Data Lake Storage. Tato zásada určuje, jak dlouho jsou tyto prostředky uloženy před jejich automatickým odstraněním (výchozí hodnota je 30 dní). Tyto soubory můžete použít pro ladění a pro optimalizaci výkonu úloh, které budete znovu spustit v budoucnu.

Chcete-li změnit dobu uchovávání metadat a prostředků úlohy:

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klikněte na **Omezení a zásady**.
3. V části **Dny pro zachování dotazů na úlohy**přesuňte jezdec a vyberte hodnotu nebo zadejte hodnotu do textového pole.  
4. Klikněte na **Uložit**.

## <a name="job-level-policies"></a>Politiky na úrovni pracovních míst

Zásady na úrovni úloh umožňují řídit maximální au a maximální prioritu, kterou mohou jednotliví uživatelé (nebo členové určitých skupin zabezpečení) nastavit u úloh, které odešlou. Tato zásada umožňuje řídit náklady vzniklé uživatelům. Umožňuje také řídit vliv, který mohou mít naplánované úlohy na produkční úlohy s vysokou prioritou, které jsou spuštěny ve stejném účtu Data Lake Analytics.

Služba Data Lake Analytics má dvě zásady, které můžete nastavit na úrovni úlohy:

* **AU limit na úlohu**: Uživatelé mohou odesílat pouze úlohy, které mají až tento počet AU. Ve výchozím nastavení je tento limit stejný jako maximální limit AU pro účet.
* **Priorita:** Uživatelé mohou odesílat pouze úlohy, které mají prioritu nižší nebo rovnou této hodnotě. Vyšší číslo označuje nižší prioritu. Ve výchozím nastavení je tento limit nastaven na hodnotu 1, což je nejvyšší možná priorita.

Pro každý účet je nastavena výchozí zásada. Výchozí zásada platí pro všechny uživatele účtu. Můžete vytvořit další zásady pro konkrétní uživatele a skupiny. 

> [!NOTE]
> Zásady na úrovni účtu a zásady na úrovni úloh platí současně.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Přidání zásady pro konkrétního uživatele nebo skupinu

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klikněte na **Omezení a zásady**.
3. V části **Limity pro odesílání úloh**klikněte na tlačítko Přidat **zásady.** Potom vyberte nebo zadejte následující nastavení:
    1. **Název zásad y výpočetních prostředků**: Zadejte název zásady, který vám připomene účel zásady.
    2. **Vybrat možnost Uživatel nebo Skupina**: Vyberte uživatele nebo skupinu, na kterou se tato zásada vztahuje.
    3. **Nastavení limitu au úlohy**: Nastavte limit AU, který se vztahuje na vybraného uživatele nebo skupinu.
    4. **Nastavení limitu priority**: Nastavte limit priority, který se vztahuje na vybraného uživatele nebo skupinu.

4. Klikněte na **OK**.

5. Nová zásada je uvedena v tabulce **Výchozí** zásady v části **Limity odeslání úlohy**. 

### <a name="delete-or-edit-an-existing-policy"></a>Odstranění nebo úprava existující zásady

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klikněte na **Omezení a zásady**.
3. V části **Limity pro odesílání úloh**najděte zásady, které chcete upravit.
4.  Pokud chcete zobrazit možnosti **Odstranit** a **upravit,** klikněte `...`v pravém sloupci tabulky na .

## <a name="additional-resources-for-job-policies"></a>Další zdroje pro zásady zaměstnanosti
* [Příspěvek blogu přehled zásad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Příspěvek blogu zásad na úrovni účtu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Příspěvek blogu zásad na úrovni úloh](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Analýzou datových jezer pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)