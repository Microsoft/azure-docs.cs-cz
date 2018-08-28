---
title: Správa Azure Data Lake Analytics pomocí webu Azure portal
description: Tento článek popisuje, jak pomocí webu Azure portal ke správě účtů Data Lake Analytics, zdroje dat, uživatele a úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1d49403ec04b2ec35291869385c316cb5ab3b0da
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044904"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Správa Azure Data Lake Analytics pomocí webu Azure portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty, zdroje dat, uživatele a úlohy Azure Data Lake Analytics pomocí webu Azure portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Správa účtů Data Lake Analytics

### <a name="create-an-account"></a>Vytvoření účtu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek** > **inteligence a analýza** > **Data Lake Analytics**.
3. Vyberte hodnoty pro následující položky: 
   1. **Název**: název účtu Data Lake Analytics.
   2. **Předplatné**: předplatné Azure použité pro účet.
   3. **Skupina prostředků**: Skupina prostředků Azure, ve kterém chcete vytvořit účet. 
   4. **Umístění**: datové centrum Azure pro účet Data Lake Analytics. 
   5. **Data Lake Store**: výchozí úložiště pro účet Data Lake Analytics. Účet Azure Data Lake Store a účet Data Lake Analytics musí být ve stejném umístění.
4. Klikněte na možnost **Vytvořit**. 

### <a name="delete-a-data-lake-analytics-account"></a>Odstranění účtu Data Lake Analytics

Před odstraněním účtu Data Lake Analytics, odstraňte jeho výchozí účet Data Lake Store.

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na **Odstranit**.
3. Zadejte název účtu.
4. Klikněte na **Odstranit**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics podporuje následující zdroje dat:

* Data Lake Store
* Azure Storage

Průzkumník dat můžete použít k procházení zdroje dat a provádění operací správy základního souboru. 

### <a name="add-a-data-source"></a>Přidání zdroje dat

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na tlačítko **zdroje dat**.
3. Klikněte na tlačítko **přidat zdroj dat**.
    
   * Přidat účet Data Lake Store, budete potřebovat název účtu a přístup k účtu a mít možnost provádět dotazy ho.
   * Přidání úložiště objektů Blob v Azure, budete potřebovat účet úložiště a klíč účtu. Je najít, přejděte na účet úložiště na portálu.

## <a name="set-up-firewall-rules"></a>Nastavení pravidel brány firewall

Data Lake Analytics můžete použít k další uzamčení přístupu k vašemu účtu Data Lake Analytics na úrovni sítě. Můžete povolit bránu firewall, zadejte IP adresu nebo definovat rozsah IP adres pro důvěryhodného klienty. Po povolení těchto opatření můžete pouze klienti, kteří mají IP adresy v definovaném rozsahu připojit k úložišti.

Pokud dalšími službami Azure, jako je Azure Data Factory nebo virtuální počítače, připojit k účtu Data Lake Analytics, ujistěte se, že **povolit službám Azure** zapnuté **na**. 

### <a name="set-up-a-firewall-rule"></a>Nastavení pravidla brány firewall

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. V nabídce na levé straně klikněte na tlačítko **brány Firewall**.

## <a name="add-a-new-user"></a>Přidání nového uživatele

Můžete použít **Průvodce přidáním uživatele** snadno zřizovat noví uživatelé Data Lake.

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Na levé straně v části **Začínáme**, klikněte na tlačítko **Průvodce přidáním uživatele**.
3. Vyberte uživatele a pak klikněte na tlačítko **vyberte**.
4. Vyberte roli a potom klikněte na tlačítko **vyberte**. Chcete-li nastavit nový vývojář použití Azure Data Lake, vyberte **Data Lake Analytics Developer** role.
5. Vyberte seznamy řízení přístupu (ACL) pro databáze U-SQL. Jakmile budete spokojeni s vaší volby, klikněte na tlačítko **vyberte**.
6. Vyberte seznamy ACL pro soubory. Pro výchozí úložiště neměnit seznamy ACL pro kořenovou složku "/" a složky/System. Klikněte na **Vybrat**.
7. Zkontrolujte všechny vybrané změny a klikněte na **spustit**.
8. Po dokončení průvodce klikněte na tlačítko **provádí**.

## <a name="manage-role-based-access-control"></a>Správa řízení přístupu na základě rolí

Stejně jako ostatní služby Azure můžete použít řízení přístupu na základě Role (RBAC) k řízení, jak uživatelé komunikovat se službou.

Standardní role RBAC mít k dispozici následující možnosti:
* **Vlastník**: můžete odesílat úlohy, monitorování úloh, zrušit úlohy od libovolného uživatele a konfiguraci účtu.
* **Přispěvatel**: můžete odesílat úlohy, monitorování úloh, zrušit úlohy od libovolného uživatele a konfiguraci účtu.
* **Čtečka**: můžete monitorovat úlohy.

Pomocí Data Lake Analytics Developer role a umožňuje vývojářům U-SQL pro použití služby Data Lake Analytics. Můžete použít roli vývojář Data Lake Analytics:
* Odesílání úloh.
* Monitorování stavu úlohy a průběh úlohy odeslané žádný uživatel.
* Podívejte se skripty U-SQL z úlohy odeslané žádný uživatel.
* Zrušte pouze své vlastní úlohy.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Přidání uživatele nebo skupiny zabezpečení do účtu Data Lake Analytics

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na tlačítko **řízení přístupu (IAM)** > **přidat**.
3. Vyberte roli.
4. Přidání uživatele.
5. Klikněte na **OK**.

>[!NOTE]
>Pokud uživatel nebo skupina zabezpečení je potřeba odesílat úlohy, potřebují také oprávnění u účtu úložiště. Další informace najdete v tématu [zabezpečení dat uložených v Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Správa úloh

### <a name="submit-a-job"></a>Odeslání úlohy

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.

2. Klikněte na tlačítko **nová úloha**. Pro každou úlohu konfigurace:

    1. **Název úlohy**: název úlohy.
    2. **Priorita**: nižší čísla mají vyšší prioritu. Pokud dvě úlohy se zařadí do fronty, spustí první kategorií s nižší hodnotou priority.
    3. **Paralelismus**: maximální počet výpočetních procesů vyhradit pro tuto úlohu.

3. Klikněte na **Odeslat úlohu**.

### <a name="monitor-jobs"></a>Monitorování úloh

1. Na webu Azure Portal přejděte k vašemu účtu Data Lake Analytics.
2. Klikněte na tlačítko **zobrazit všechny úlohy**. Seznam všech aktivních a nedávno dokončené úlohy v rámci účtu se zobrazí.
3. Případně můžete kliknout **filtr** vám pomůžou najít úlohy podle **časový rozsah**, **název úlohy**, a **Autor** hodnoty. 

### <a name="monitoring-pipeline-jobs"></a>Monitorování úloh kanálu
Úlohy, které jsou součástí kanálu fungují společně, obvykle postupně provádět konkrétní scénář. Například můžete mít kanál, který vyčistí, extrahuje, transformuje, agreguje využití přehledů zákazníků. Úlohy kanálu jsou označeny pomocí vlastnosti "Kanál", pokud úloha byla odeslána. Tato vlastnost naplní se mají automaticky úlohy naplánovat pomocí ADF V2. 

Chcete-li zobrazit seznam úloh U-SQL, které jsou součástí kanály: 

1. Na webu Azure Portal přejděte na vaše účty Data Lake Analytics.
2. Klikněte na tlačítko **úlohy Insights**. Na kartě "Všechny úlohy" bude nastavena jako výchozí, zobrazujícím seznam spuštění ve frontě a ukončení úlohy.
3. Klikněte na tlačítko **úloh kanálu** kartu. Zobrazí se seznam úloh kanálu spolu s agregované statistiky pro každý kanál.

### <a name="monitoring-recurring-jobs"></a>Opakující se úlohy monitorování
Opakovaná úloha je ten, který má stejné obchodní logiku, ale používá jiné vstupní data pokaždé, když ji spustí. V ideálním případě opakujících se úloh by měl vždy úspěšně a mají relativně stabilními doba provádění; monitorování těchto projevů vám pomůže zajistit, že úloha je v pořádku. Opakující se úlohy jsou označeny pomocí vlastnosti "Opakování". Tato vlastnost naplní se mají automaticky úlohy naplánovat pomocí ADF V2.

Chcete-li zobrazit seznam úloh U-SQL, které jsou opakování: 

1. Na webu Azure Portal přejděte na vaše účty Data Lake Analytics.
2. Klikněte na tlačítko **úlohy Insights**. Na kartě "Všechny úlohy" bude nastavena jako výchozí, zobrazujícím seznam spuštění ve frontě a ukončení úlohy.
3. Klikněte na tlačítko **opakované úlohy** kartu. Seznam opakující se úlohy se zobrazí spolu s agregované statistiky pro každou úlohu opakované.

## <a name="next-steps"></a>Další postup

* [Přehled služby Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure Powershellu](data-lake-analytics-manage-use-powershell.md)
* [Správa Azure Data Lake Analytics pomocí zásad](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
