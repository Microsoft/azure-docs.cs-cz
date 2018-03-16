---
title: "Správa Azure Data Lake Analytics pomocí portálu Azure | Microsoft Docs"
description: "Naučte se spravovat počtech, zdroje dat, uživatele a úlohy Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 93815904e7e21e1ba8283d7a522297c7e3466702
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Správa Azure Data Lake Analytics pomocí portálu Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Naučte se spravovat účty, účet zdroje dat, uživatele a úlohy Azure Data Lake Analytics pomocí portálu Azure. Témata týkající se řízení o pomocí jiných nástrojů zobrazíte kliknutím na karty v horní části stránky.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Správa účtů Data Lake Analytics

### <a name="create-an-account"></a>Vytvoření účtu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** > **Intelligence + analýzy** > **Data Lake Analytics**.
3. Vyberte hodnoty pro následující položky: 
   1. **Název**: název účtu Data Lake Analytics.
   2. **Předplatné**: předplatné Azure použité pro účet.
   3. **Skupina prostředků**: Skupina prostředků Azure, ve které chcete vytvořit účet. 
   4. **Umístění**: datové centrum Azure pro účet Data Lake Analytics. 
   5. **Data Lake Store**: výchozí úložiště, který má být použit pro účet Data Lake Analytics. Účet Azure Data Lake Store a účet Data Lake Analytics musí být ve stejném umístění.
4. Klikněte na možnost **Vytvořit**. 

### <a name="delete-a-data-lake-analytics-account"></a>Odstranění účtu Data Lake Analytics

Před odstraněním účtu Data Lake Analytics, odstraňte jeho výchozí účet Data Lake Store.

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Odstranit**.
3. Zadejte název účtu.
4. Klikněte na **Odstranit**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics podporuje následující zdroje dat:

* Data Lake Store
* Azure Storage

Průzkumník dat můžete procházet zdroje dat a provádět operace správy základního souboru. 

### <a name="add-a-data-source"></a>Přidání zdroje dat

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na tlačítko **zdroje dat**.
3. Klikněte na tlačítko **přidat zdroj dat**.
    
   * Přidání účtu Data Lake Store, potřebujete název účtu a přístup k účtu mohli dotaz ho.
   * Přidat úložiště objektů Blob v Azure, budete potřebovat účet úložiště a klíč účtu. Kde je najít, přejděte k účtu úložiště na portálu.

## <a name="set-up-firewall-rules"></a>Nastavení pravidel brány firewall

Data Lake Analytics můžete další uzamčení přístup ke svému účtu Data Lake Analytics na úrovni sítě. Můžete povolit bránu firewall, zadejte IP adresu nebo zadejte rozsah IP adres pro klienty důvěryhodné. Povolíte-li tato opatření, pouze klienti, kteří mají IP adresy v definovaném rozsahu může připojit k úložišti.

Pokud jinými službami Azure, jako je Azure Data Factory nebo virtuální počítače, připojení k účtu Data Lake Analytics, ujistěte se, že **povolit služby Azure** je zapnuta **na**. 

### <a name="set-up-a-firewall-rule"></a>Nastavení pravidla brány firewall

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. V nabídce na levé straně klikněte na tlačítko **brány Firewall**.

## <a name="add-a-new-user"></a>Přidání nového uživatele

Můžete použít **Průvodce přidáním uživatele** snadno zřídit nové uživatele Data Lake.

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Na levé straně v části **Začínáme**, klikněte na tlačítko **Průvodce přidáním uživatele**.
3. Vyberte uživatele a pak klikněte na **vyberte**.
4. Vyberte roli a pak klikněte na tlačítko **vyberte**. Chcete-li nastavit nový vývojář používat Azure Data Lake, vyberte **Data Lake Analytics vývojáře** role.
5. Vyberte seznamy řízení přístupu (ACL) pro databáze U-SQL. Až budete spokojeni s vaší volby, klikněte na tlačítko **vyberte**.
6. Vyberte seznamy ACL pro soubory. Pro výchozí úložiště neměnit seznamy ACL pro kořenovou složku "/" a ve složce/System. Klikněte na **Vybrat**.
7. Zkontrolujte všechny vybrané změny a pak klikněte na tlačítko **spustit**.
8. Po dokončení průvodce klikněte na tlačítko **provádí**.

## <a name="manage-role-based-access-control"></a>Správa řízení přístupu na základě rolí

Jako jinými službami Azure můžete řídit, jak uživatelé komunikovat se službou řízení přístupu na základě Role (RBAC).

Standardní role RBAC mají následující možnosti:
* **Vlastník**: můžete odeslat úlohy sledování úloh, zrušte úlohy z libovolného uživatele a nakonfigurovat účet.
* **Přispěvatel**: můžete odeslat úlohy sledování úloh, zrušte úlohy z libovolného uživatele a nakonfigurovat účet.
* **Čtečka**: můžete sledovat úlohy.

Pomocí role Data Lake Analytics Developer a umožňuje vývojářům používat službu Data Lake Analytics U-SQL. Můžete použít roli Data Lake Analytics vývojáře tak, aby:
* Odeslání úlohy.
* Monitorování stavu úlohy a průběh úlohy, odeslané žádný uživatel.
* V tématu skriptů U-SQL z úlohy, odeslané žádný uživatel.
* Zrušte jenom vlastní úlohy.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Přidat uživatele nebo skupiny zabezpečení do účtu Data Lake Analytics

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na tlačítko **přístup k ovládacímu prvku (IAM)** > **přidat**.
3. Vyberte roli.
4. Přidání uživatele.
5. Klikněte na **OK**.

>[!NOTE]
>Pokud uživatele nebo skupinu zabezpečení potřebuje k odesílání úloh, potřebují také mít oprávnění pro účet úložiště. Další informace najdete v tématu [zabezpečení dat uložených v Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Správa úloh

### <a name="submit-a-job"></a>Odeslání úlohy

1. V portálu Azure přejděte do účtu Data Lake Analytics.

2. Klikněte na tlačítko **nová úloha**. Pro každou úlohu konfigurace:

    1. **Název úlohy**: název úlohy.
    2. **Priorita**: nižší čísla mají vyšší prioritu. Pokud dvě úlohy jsou zařazeny do fronty, spustí se první kategorií s nižší hodnotou priority.
    3. **Paralelismus**: maximální počet výpočetních procesů můžete vyhradit pro tuto úlohu.

3. Klikněte na **Odeslat úlohu**.

### <a name="monitor-jobs"></a>Monitorování úloh

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na tlačítko **vidět všechny úlohy**. Se zobrazí seznam všech aktivní a nedávno dokončení úloh v účtu.
3. Volitelně klikněte na **filtru** a umožňují najít úloh podle **časový rozsah**, **název úlohy**, a **Autor** hodnoty. 

### <a name="monitoring-pipeline-jobs"></a>Sledování úloh kanálu
Úlohy, které jsou součástí kanálu fungovat společně, obvykle postupně k provedení určitého scénáře. Například můžete mít kanál, který odstraní, extrahuje, transformuje, agreguje využití přehledů zákazníka. Úlohy kanálu se identifikují pomocí vlastnosti "Kanál", pokud úloha byla odeslána. Pomocí ADF V2 naplánované úlohy mít tato vlastnost vyplní automaticky. 

Chcete-li zobrazit seznam úloh U-SQL, které jsou součástí kanály: 

1. V portálu Azure přejděte do své účty Data Lake Analytics.
2. Klikněte na tlačítko **úlohy Insights**. Na kartě "Všechny úlohy" bude použita jako výchozí, zobrazující seznam spuštěná, zařazených do fronty a skončila úlohy.
3. Klikněte **kanálu úlohy** kartě. Zobrazí se seznam úloh kanálu spolu s souhrnných statistik pro každý kanál.

### <a name="monitoring-recurring-jobs"></a>Monitorování opakované úlohy
Opakování úlohy je ten, který má stejné obchodní logiku, ale používá jiné vstupní data pokaždé, když ji spustí. V ideálním případě opakované úlohy doporučujeme vždy úspěšné a mají relativně stabilní čas provádění; monitorování těchto chování pomůže zajistit, že úloha je v pořádku. Opakované úlohy se identifikují pomocí vlastnosti "Recurrence". Pomocí ADF V2 naplánované úlohy mít tato vlastnost vyplní automaticky.

Chcete-li zobrazit seznam úloh U-SQL, které jsou opakování: 

1. V portálu Azure přejděte do své účty Data Lake Analytics.
2. Klikněte na tlačítko **úlohy Insights**. Na kartě "Všechny úlohy" bude použita jako výchozí, zobrazující seznam spuštěná, zařazených do fronty a skončila úlohy.
3. Klikněte **opakovaných úloh** kartě. Společně s souhrnných statistik pro každou úlohu opakující se zobrazí seznam opakované úlohy.

## <a name="manage-policies"></a>Správa zásad

### <a name="account-level-policies"></a>Zásady na úrovni účtu

Tyto zásady platí pro všechny úlohy v účtu Data Lake Analytics.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximální počet Austrálie v účtu Data Lake Analytics
Zásady řídí celkový počet jednotek Analytics (Austrálie) můžete použít váš účet Data Lake Analytics. Ve výchozím nastavení je hodnota nastavena na 250. Například, pokud je tato hodnota nastavena na 250 Austrálie, může mít jednu úlohu s 250 Austrálie přiřazené nebo běží s 25 10 úlohy Austrálie každý. Další úlohy, které jsou odeslány jsou zařazeny do fronty, dokud se všechny spuštěné úlohy. Po dokončení probíhající úlohy jsou Austrálie jsou uvolněny pro spuštění ve frontě úloh.

Chcete-li změnit počet Austrálie pro váš účet Data Lake Analytics:

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **maximální Austrálie**, přesuňte jezdec vyberte hodnotu nebo zadejte hodnotu v textovém poli. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete více než výchozí (250) Austrálie, na portálu, klikněte na tlačítko **podpora + nápovědy** odeslat žádost o podporu. Je možné zvýšit počet Austrálie k dispozici v účtu Data Lake Analytics.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximální počet úloh, které můžou běžet současně
Zásady určuje, kolik úlohy můžete spustit ve stejnou dobu. Ve výchozím nastavení je tato hodnota nastavena na hodnotu 20. Pokud vaše Data Lake Analytics má Austrálie k dispozici, nové úlohy jsou naplánovány ke spuštění až celkový počet spuštěných úloh nedosáhne hodnoty těchto zásad. Když se dostanete maximální počet úloh, které můžou běžet současně, následné úlohy jsou zařazeny do fronty v pořadí podle priority, dokud jeden nebo více spuštěné úlohy dokončení (v závislosti na dostupnosti AU).

Chcete-li změnit počet úloh, které můžou běžet současně:

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **maximální číslo z spuštění úlohy**, přesuňte jezdec vyberte hodnotu nebo zadejte hodnotu v textovém poli. 
4. Klikněte na **Uložit**.

> [!NOTE]
> Pokud potřebujete více než výchozí (20) počet úloh, spusťte na portálu, klikněte na tlačítko **podpora + nápovědy** odeslat žádost o podporu. Je možné zvýšit počet úloh, které můžou běžet současně v účtu Data Lake Analytics.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Jak dlouho chcete zachovat metadata úlohy a prostředky 
Když uživatelé spustí úloh U-SQL, službě Data Lake Analytics uchovává všechny související soubory. Související soubory zahrnují skript U-SQL, soubory knihoven DLL, kterou se odkazuje v skript U-SQL, kompilované prostředků a statistiky. Soubory jsou ve složce /system/ výchozí účet Azure Data Lake Storage. Tato zásada určuje, jak dlouho tyto prostředky jsou uložené před automaticky odstraní (výchozí hodnota je 30 dní). Tyto soubory můžete použít pro ladění a optimalizace výkonu úloh, které budete v budoucnu spusťte znovu.

Chcete-li změnit jak dlouho Pokud chcete zachovat metadata úlohy a prostředky:

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **dnů zachovat úloha se dotazuje**, přesuňte jezdec vyberte hodnotu nebo zadejte hodnotu v textovém poli.  
4. Klikněte na **Uložit**.

### <a name="job-level-policies"></a>Zásady na úrovni úlohy
Pomocí zásad na úrovni úlohy můžete určit maximální Austrálie a maximální priority, kterou jednotlivé uživatele (nebo členy určité skupiny zabezpečení) můžete nastavit na úlohy, které se odesílají. Díky tomu můžete řídit náklady uživatelé. To také umožňuje řízení o tom, že naplánované úlohy může mít na produkční s vysokou prioritou úloh, které jsou spuštěné ve stejném účtu Data Lake Analytics.

Data Lake Analytics má dvě zásady, které můžete nastavit na úrovni úlohy:

* **AU limit na jednu úlohu**: uživatelé lze odeslat pouze úlohy, které je nutné tento počet Austrálie. Ve výchozím nastavení tento limit je stejná jako maximální limit AU pro účet.
* **Priorita**: uživatelé lze odeslat pouze úlohy, které mají nižší než nebo rovna hodnotě tuto hodnotu. Všimněte si, že vyšší číslo znamená s nižší prioritou. Ve výchozím nastavení to je nastavena na hodnotu 1, což je nejvyšší možná priorita.

Existuje výchozí zásada, nastavte pro každý účet. Výchozí zásady platí pro všechny uživatele účtu. Další zásady můžete nastavit pro konkrétní uživatele a skupiny. 

> [!NOTE]
> Zásady na úrovni účtu a zásady na úrovni úlohy použít současně.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Přidání zásad pro konkrétního uživatele nebo skupiny

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **úlohy odeslání omezení**, klikněte **přidat zásadu** tlačítko. Potom vyberte nebo zadejte následující nastavení:
    1. **Název zásady výpočetní**: Zadejte název zásady, a tak poznáte, účelu zásad.
    2. **Vyberte uživatele nebo skupiny**: Vyberte uživatele nebo skupiny, tato zásada se vztahuje na.
    3. **Nastavit Limit AU úlohy**: omezit AU, která se vztahuje na vybrané uživatele nebo skupiny.
    4. **Nastavit Priority Limit**: Omezit prioritu, která se vztahuje na vybrané uživatele nebo skupiny.

4. Klikněte na tlačítko **OK**.

5. Nové zásady, je uvedena ve **výchozí** zásad v části tabulky **úlohy odeslání omezení**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Odstraňte nebo upravte existující zásady

1. V portálu Azure přejděte do účtu Data Lake Analytics.
2. Klikněte na **Vlastnosti**.
3. V části **úlohy odeslání omezení**, vyhledejte zásadu, kterou chcete upravit.
4.  Chcete-li zobrazit **odstranit** a **upravit** možnosti v polí v pravém sloupci tabulky, klikněte na tlačítko **...** .

### <a name="additional-resources-for-job-policies"></a>Další prostředky pro úlohy zásady
* [Příspěvek blogu přehled zásad](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Zásady na úrovni účtu příspěvku na blogu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Zásady na úrovni úlohy příspěvku na blogu](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Další postup

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

