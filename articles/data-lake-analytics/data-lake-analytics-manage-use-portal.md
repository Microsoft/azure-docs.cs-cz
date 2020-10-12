---
title: Správa Azure Data Lake Analytics pomocí Azure Portal
description: Tento článek popisuje, jak použít Azure Portal ke správě účtů Data Lake Analytics, zdrojů dat, uživatelů a & úloh.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: cbce903bc7463cd917d48e341614afbe18536262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87531205"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Správa Azure Data Lake Analytics pomocí webu Azure Portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Azure Portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Správa účtů Data Lake Analytics

### <a name="create-an-account"></a>Vytvoření účtu

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Klikněte na **vytvořit prostředek**  >  **Intelligence + Analytics**  >  **Data Lake Analytics**.
3. Vyberte hodnoty pro následující položky: 
   1. **Název**: název účtu Data Lake Analytics.
   2. **Předplatné**: předplatné Azure použité pro tento účet.
   3. **Skupina prostředků**: Skupina prostředků Azure, ve které se má účet vytvořit. 
   4. **Umístění**: datové centrum Azure pro účet Data Lake Analytics. 
   5. **Data Lake Store**: výchozí úložiště, které se má použít pro účet Data Lake Analytics. Účet Azure Data Lake Store a účet Data Lake Analytics musí být ve stejném umístění.
4. Klikněte na **Vytvořit**. 

### <a name="delete-a-data-lake-analytics-account"></a>Odstranit účet Data Lake Analytics

Než odstraníte účet Data Lake Analytics, odstraňte jeho výchozí účet Data Lake Store.

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **Odstranit**.
3. Zadejte název účtu.
4. Klikněte na **Odstranit**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics podporuje následující zdroje dat:

* Data Lake Store
* Azure Storage

Průzkumník dat můžete použít k procházení zdrojů dat a provádění základních operací správy souborů. 

### <a name="add-a-data-source"></a>Přidání zdroje dat

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **zdroje dat**.
3. Klikněte na **Přidat zdroj dat**.
    
   * Pokud chcete přidat účet Data Lake Store, budete potřebovat název účtu a přístup k tomuto účtu, abyste se mohli dotazovat.
   * Pokud chcete přidat úložiště objektů BLOB v Azure, potřebujete účet úložiště a klíč účtu. Pokud je chcete najít, na portálu se podívejte na účet úložiště.

## <a name="set-up-firewall-rules"></a>Nastavení pravidel brány firewall

Pomocí Data Lake Analytics můžete dál uzamknout přístup k vašemu účtu Data Lake Analytics na úrovni sítě. Můžete povolit bránu firewall, zadat IP adresu nebo definovat rozsah IP adres pro důvěryhodné klienty. Po povolení těchto měr se ke Storu můžou připojit jenom klienti, kteří mají IP adresy v definovaném rozsahu.

Pokud se k účtu Data Lake Analytics připojí jiné služby Azure, například Azure Data Factory nebo virtuální počítače, ujistěte se, že **je zapnutá**možnost **Povolit služby Azure** . 

### <a name="set-up-a-firewall-rule"></a>Nastavení pravidla brány firewall

1. V Azure Portal přejít na účet Data Lake Analytics.
2. V nabídce na levé straně klikněte na **firewall**.

## <a name="add-a-new-user"></a>Přidání nového uživatele

**Průvodce přidáním uživatele** můžete použít ke snadnému zřízení nových uživatelů Data Lake.

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Na levé straně v části **Začínáme**klikněte na možnost **Průvodce přidáním uživatele**.
3. Vyberte uživatele a pak klikněte na **Vybrat**.
4. Vyberte roli a pak klikněte na **Vybrat**. Pokud chcete nastavit nového vývojáře pro použití Azure Data Lake, vyberte roli **vývojář Data Lake Analytics** .
5. Vyberte seznamy řízení přístupu (ACL) pro databáze U-SQL. Až budete s vašimi možnostmi spokojeni, klikněte na **Vybrat**.
6. Vyberte seznamy ACL pro soubory. U výchozího úložiště neměňte seznamy ACL pro kořenovou složku "/" a složku/. Klikněte na **Vybrat**.
7. Zkontrolujte všechny vybrané změny a pak klikněte na **Spustit**.
8. Po dokončení průvodce klikněte na **Hotovo**.

## <a name="manage-role-based-access-control"></a>Spravovat Role-Based Access Control

Stejně jako jiné služby Azure můžete použít Role-Based Access Control (RBAC) k řízení interakce uživatelů se službou.

Standardní role Azure mají tyto možnosti:
* **Vlastník**: může odesílat úlohy, monitorovat úlohy, rušit úlohy od libovolného uživatele a nakonfigurovat účet.
* **Přispěvatel**: může odesílat úlohy, monitorovat úlohy, rušit úlohy od libovolného uživatele a konfigurovat účet.
* **Čtecí modul**: může monitorovat úlohy.

Pomocí vývojářské role Data Lake Analytics můžete povolit vývojářům U-SQL používat službu Data Lake Analytics. Roli vývojáře Data Lake Analytics můžete použít k těmto akcím:
* Odešlete úlohy.
* Monitoruje stav úlohy a průběh úloh odeslaných libovolným uživatelem.
* Podívejte se na skripty U-SQL z úloh odeslaných libovolným uživatelem.
* Zruší pouze vaše vlastní úlohy.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Přidání uživatelů nebo skupin zabezpečení k účtu Data Lake Analytics

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **řízení přístupu (IAM)**  >  **Přidat přiřazení role**.
3. Vyberte roli.
4. Přidejte uživatele.
5. Klikněte na **OK**.

>[!NOTE]
>Pokud uživatel nebo skupina zabezpečení potřebuje odesílat úlohy, potřebují taky oprávnění k účtu Store. Další informace najdete v tématu [zabezpečení dat uložených v Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Správa úloh

### <a name="submit-a-job"></a>Odeslat úlohu

1. V Azure Portal přejít na účet Data Lake Analytics.

2. Klikněte na **Nová úloha**. Pro každou úlohu nakonfigurujte:

    1. **Název úlohy**: název úlohy.
    2. **Priorita**: nižší čísla mají vyšší prioritu. Pokud jsou dvě úlohy zařazené do fronty, spustí se jako první hodnota s nižší prioritou.
    3. **Paralelismus**: maximální počet výpočetních procesů, které se mají pro tuto úlohu vyhradit.

3. Klikněte na **Odeslat úlohu**.

### <a name="monitor-jobs"></a>Monitorování úloh

1. V Azure Portal přejít na účet Data Lake Analytics.
2. Klikněte na **Zobrazit všechny úlohy**. Zobrazí se seznam všech aktivních a nedávno dokončených úloh v účtu.
3. V případě potřeby můžete kliknout na tlačítko **Filtr** , což vám umožní najít úlohy podle **časového rozsahu**, **názvu úlohy**a hodnot **autora** . 

### <a name="monitoring-pipeline-jobs"></a>Monitorování úloh kanálu
Úlohy, které jsou součástí kanálu, jsou většinou sekvenční, k dosažení konkrétního scénáře. Můžete mít například kanál, který vyčistí, extrahuje, transformuje a agreguje využití pro zákaznické poznatky. Úlohy kanálu se při odeslání úlohy identifikují pomocí vlastnosti kanálu. U úloh naplánovaných pomocí ADF v2 se tato vlastnost naplní automaticky. 

Zobrazení seznamu úloh U-SQL, které jsou součástí kanálů: 

1. V Azure Portal přejdete na účty Data Lake Analytics.
2. Klikněte na **přehledy úloh**. Karta všechny úlohy se nastaví na výchozí hodnotu, která zobrazí seznam spuštěných, zařazených do fronty a ukončených úloh.
3. Klikněte na kartu **úlohy kanálu** . Zobrazí se seznam úloh kanálu společně s agregovanými statistikami pro každý kanál.

### <a name="monitoring-recurring-jobs"></a>Monitorování opakujících se úloh
Opakovaná úloha je ta, která má stejnou obchodní logiku, ale při každém spuštění používá jiná vstupní data. V ideálním případě by opakované úlohy měly být vždy úspěšné a měly poměrně stabilní dobu provádění; monitorování tohoto chování pomůže zajistit, aby byla úloha v pořádku. Opakující se úlohy se identifikují pomocí vlastnosti opakování. U úloh naplánovaných pomocí ADF v2 se tato vlastnost naplní automaticky.

Chcete-li zobrazit seznam úloh U-SQL, které jsou opakované: 

1. V Azure Portal přejdete na účty Data Lake Analytics.
2. Klikněte na **přehledy úloh**. Karta všechny úlohy se nastaví na výchozí hodnotu, která zobrazí seznam spuštěných, zařazených do fronty a ukončených úloh.
3. Klikněte na kartu **opakované úlohy** . Zobrazí se seznam opakujících se úloh spolu s agregovanou statistikou pro každou opakovanou úlohu.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Správa Azure Data Lake Analytics s využitím zásad](data-lake-analytics-account-policies.md)
