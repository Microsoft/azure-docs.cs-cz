---
title: Správa Azure Data Lake Analytics pomocí portálu Azure
description: Tento článek popisuje, jak pomocí portálu Azure spravovat účty Data Lake Analytics, zdroje dat, uživatele & úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265698"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Správa Azure Data Lake Analytics pomocí webu Azure Portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí portálu Azure.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Správa účtů Data Lake Analytics

### <a name="create-an-account"></a>Vytvoření účtu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klikněte na **Vytvořit** > **zdroj Inteligence + analytics** > Data Lake**Analytics**.
3. Vyberte hodnoty pro následující položky: 
   1. **Název**: Název účtu Data Lake Analytics.
   2. **Předplatné**: Předplatné Azure používané pro účet.
   3. **Skupina prostředků:** Skupina prostředků Azure, ve které chcete vytvořit účet. 
   4. **Umístění:** Datové centrum Azure pro účet Data Lake Analytics. 
   5. **Úložiště datových jezer**: Výchozí úložiště, které se má použít pro účet Data Lake Analytics. Účet Azure Data Lake Store a účet Data Lake Analytics musí být ve stejném umístění.
4. Klikněte na **Vytvořit**. 

### <a name="delete-a-data-lake-analytics-account"></a>Odstranění účtu Data Lake Analytics

Před odstraněním účtu Data Lake Analytics odstraňte jeho výchozí účet Data Lake Store.

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klepněte na **tlačítko Odstranit**.
3. Zadejte název účtu.
4. Klepněte na **tlačítko Odstranit**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Správa zdrojů dat

Služba Data Lake Analytics podporuje následující zdroje dat:

* Data Lake Store
* Azure Storage

Pomocí Průzkumníka dat můžete procházet zdroje dat a provádět základní operace správy souborů. 

### <a name="add-a-data-source"></a>Přidání zdroje dat

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klepněte na **položku Zdroje dat**.
3. Klepněte na **tlačítko Přidat zdroj dat**.
    
   * Chcete-li přidat účet Úložiště datového jezera, potřebujete název účtu a přístup k účtu, abyste se na něj mohli dotazovat.
   * Chcete-li přidat úložiště objektů Blob Azure, potřebujete účet úložiště a klíč účtu. Chcete-li je najít, přejděte na účet úložiště na portálu.

## <a name="set-up-firewall-rules"></a>Nastavení pravidel brány firewall

Pomocí služby Data Lake Analytics můžete dále uzamknout přístup ke svému účtu Data Lake Analytics na úrovni sítě. Můžete povolit bránu firewall, zadat adresu IP nebo definovat rozsah IP adres pro důvěryhodné klienty. Po povolení těchto opatření se k úložišti mohou připojit pouze klienti, kteří mají adresy IP v rámci definovaného rozsahu.

Pokud se k účtu Data Lake Analytics připojují jiné služby Azure, jako je Azure Data Factory nebo Virtuální počítače, ujistěte se, že je **zapnutá** **funkce Povolit služby Azure** . 

### <a name="set-up-a-firewall-rule"></a>Nastavení pravidla brány firewall

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. V nabídce vlevo klepněte na položku **Brána firewall**.

## <a name="add-a-new-user"></a>Přidání nového uživatele

Pomocí Průvodce **přidáním uživatele** můžete snadno zřídit nové uživatele datového jezera.

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Vlevo v části **Začínáme**klikněte na **Průvodce přidáním uživatele**.
3. Vyberte uživatele a klepněte na tlačítko **Vybrat**.
4. Vyberte roli a klepněte na tlačítko **Vybrat**. Pokud chcete nastavit nového vývojáře, který bude používat Azure Data Lake, vyberte roli **Vývojář data lake analytics.**
5. Vyberte seznamy řízení přístupu (ACL) pro u-SQL databáze. Až budete s volbami spokojeni, klikněte na **Vybrat**.
6. Vyberte seznamy ACL pro soubory. Pro výchozí úložiště neměňte alokace pro kořenovou složku "/" a pro složku /system. Klepněte na **tlačítko Vybrat**.
7. Zkontrolujte všechny vybrané změny a klepněte na tlačítko **Spustit**.
8. Po dokončení průvodce klepněte na tlačítko **Hotovo**.

## <a name="manage-role-based-access-control"></a>Správa řízení přístupu na základě rolí

Stejně jako ostatní služby Azure můžete pomocí řízení přístupu na základě rolí (RBAC) řídit, jak uživatelé interagují se službou.

Standardní role RBAC mají následující možnosti:
* **Vlastník**: Může odesílat úlohy, monitorovat úlohy, rušit úlohy od libovolného uživatele a konfigurovat účet.
* **Přispěvatel**: Může odesílat úlohy, monitorovat úlohy, rušit úlohy od libovolného uživatele a konfigurovat účet.
* **Reader**: Může sledovat úlohy.

Pomocí role vývojáře Data Lake Analytics můžete vývojářům U-SQL umožnit používat službu Data Lake Analytics. Roli vývojáře Data Lake Analytics můžete použít k:
* Odeslat úlohy.
* Sledujte stav úlohy a průběh úloh odeslaných libovolným uživatelem.
* Podívejte se na u-SQL skripty z úloh odeslaných libovolným uživatelem.
* Zrušte pouze své vlastní práce.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Přidání uživatelů nebo skupin zabezpečení do účtu Data Lake Analytics

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klikněte na **Ovládací prvek přístupu (IAM)** > **Přidat přiřazení role**.
3. Vyberte roli.
4. Přidejte uživatele.
5. Klikněte na tlačítko **OK**.

>[!NOTE]
>Pokud uživatel nebo skupina zabezpečení potřebuje odeslat úlohy, potřebují také oprávnění k účtu úložiště. Další informace naleznete v [tématu Zabezpečení dat uložených v úložišti data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Správa úloh

### <a name="submit-a-job"></a>Odeslání úlohy

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.

2. Klepněte na tlačítko **Nová úloha**. Pro každou úlohu nakonfigurujte:

    1. **Název úlohy**: Název úlohy.
    2. **Priorita**: Nižší čísla mají vyšší prioritu. Pokud dvě úlohy jsou zařazeny do fronty, jeden s nižší prioritou hodnota spustí jako první.
    3. **Paralelismus**: Maximální počet výpočetních procesů, které mají být vyhrazeny pro tuto úlohu.

3. Klikněte na **Odeslat úlohu**.

### <a name="monitor-jobs"></a>Monitorování úloh

1. Na webu Azure portal přejděte na svůj účet Data Lake Analytics.
2. Klepněte na **tlačítko Zobrazit všechny úlohy**. Zobrazí se seznam všech aktivních a nedávno dokončených úloh v účtu.
3. Případně můžete klepnout na **filtr,** abyste našli úlohy podle **časového rozsahu**, **názvu úlohy**a hodnot **autora.** 

### <a name="monitoring-pipeline-jobs"></a>Monitorování úloh kanálu
Úlohy, které jsou součástí kanálu pracovat společně, obvykle postupně, k dosažení konkrétního scénáře. Můžete mít například kanál, který čistí, extrahuje, transformuje agreguje využití pro přehledy zákazníků. Úlohy kanálu jsou identifikovány pomocí vlastnosti "Pipeline" při odeslání úlohy. Úlohy naplánované pomocí ADF V2 bude mít tuto vlastnost vyplněna. 

Chcete-li zobrazit seznam úloh U-SQL, které jsou součástí kanálů: 

1. Na webu Azure portal přejděte na své účty Data Lake Analytics.
2. Klepněte na **položku Přehledy úloh**. Karta Všechny úlohy bude výchozí a zobrazí se seznam spuštěných, zařazených a ukončených úloh ve frontě.
3. Klikněte na kartu **Úlohy kanálu.** Zobrazí se seznam úloh kanálu spolu s agregovanými statistikami pro každý kanál.

### <a name="monitoring-recurring-jobs"></a>Sledování opakovaných úloh
Opakovaná úloha je úloha, která má stejnou obchodní logiku, ale při každém spuštění používá různá vstupní data. V ideálním případě by opakované úlohy měly vždy úspěšné a měly by mít relativně stabilní dobu provádění. sledování těchto chování pomůže zajistit, že úloha je v pořádku. Opakované úlohy jsou identifikovány pomocí vlastnosti Opakování. Úlohy naplánované pomocí ADF V2 bude mít tuto vlastnost vyplněna.

Chcete-li zobrazit seznam opakovaných úloh U-SQL: 

1. Na webu Azure portal přejděte na své účty Data Lake Analytics.
2. Klepněte na **položku Přehledy úloh**. Karta Všechny úlohy bude výchozí a zobrazí se seznam spuštěných, zařazených a ukončených úloh ve frontě.
3. Klikněte na kartu **Opakované úlohy.** Zobrazí se seznam opakovaných úloh spolu s agregovanými statistikami pro každou opakovanou úlohu.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)
* [Správa Azure Data Lake Analytics s využitím zásad](data-lake-analytics-account-policies.md)
