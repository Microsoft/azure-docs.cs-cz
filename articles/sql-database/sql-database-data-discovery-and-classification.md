---
title: Zjišťování a klasifikace dat
description: Klasifikace & Azure SQL Database a zjišťování dat
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: dda65c94671044f3c5a569a3f9753951de9eee3a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717683"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Klasifikace & Azure SQL Database a SQL Data Warehouse Discovery Data

Klasifikace & Discovery dat poskytuje pokročilé funkce, které jsou integrované do **Azure SQL Database pro zjišťování**, **klasifikaci**, **označování** , & **oznamování** citlivých dat ve vašich databázích.

Vyhledávání a klasifikace vašich citlivých dat (podniková, finanční, zdravotní péče, identifikovatelné osobní údaje) a podobně.) mohou hrát pivotovou roli ve vaší organizaci stature Information Protection. Může sloužit jako infrastruktura pro:

- Pomoc se splněním standardů ochrany osobních údajů a požadavků na dodržování legislativních předpisů.
- Různé scénáře zabezpečení, jako je monitorování (auditování) a upozorňování na neobvyklé přístup k citlivým datům.
- Řízení přístupu a posílení zabezpečení databází obsahujících vysoce citlivá data.

Klasifikace data Discovery & je součástí nabídky služby ADS ( [Advanced Data Security](sql-database-advanced-data-security.md) ), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. k klasifikaci & pro zjišťování dat lze využít a spravovat prostřednictvím centrálního portálu SQL ADS.

> [!NOTE]
> Tento dokument se týká Azure SQL Database a Azure SQL Data Warehouse. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse. Pro SQL Server (místně) se podívejte na téma [zjišťování a klasifikace dat SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Co je klasifikace & Discovery Data

Klasifikace data Discovery & zavádí sadu pokročilých služeb a nových funkcí SQL a vytváří nové funkce SQL Information Protection, které mají za cíl chránit data, nikoli jenom databázi:

- **& Doporučení pro zjišťování**

  Klasifikační modul prohledá vaši databázi a určí sloupce obsahující potenciálně citlivá data. Poskytuje vám tak snadný způsob, jak zkontrolovat a použít příslušná doporučení klasifikace prostřednictvím Azure Portal.

- **Označování**

  Popisky klasifikace citlivosti mohou být trvale označeny na sloupcích pomocí nových atributů klasifikace metadat představených v modulu SQL Engine. Tato metadata se pak dají využít k pokročilým scénářům auditování a ochrany na základě citlivosti.

- **Citlivost sady výsledků dotazu**

  Citlivost sady výsledků dotazu je vypočítána v reálném čase pro účely auditování.

- **Viditelnost**

  Stav klasifikace databáze se dá zobrazit v podrobném řídicím panelu na portálu. Kromě toho můžete stáhnout sestavu (ve formátu aplikace Excel), která se použije pro účely dodržování předpisů & auditování a také pro jiné potřeby.

## <a id="subheading-2"></a>Zjišťování, klasifikace & citlivých sloupců popisku

Následující část popisuje kroky pro zjišťování, klasifikaci a označování sloupců obsahujících citlivá data v databázi a zobrazení aktuálního stavu klasifikace databáze a exportu sestav.

Klasifikace obsahuje dva atributy metadat:

- Labels – hlavní atributy klasifikace, které slouží k definování úrovně citlivosti dat uložených ve sloupci.  
- Typy informací – poskytují další členitost do typu dat uložených ve sloupci.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definování a přizpůsobení taxonomie klasifikace

Klasifikace & Discovery Data Discovery obsahuje integrovanou sadu popisků citlivosti a integrovanou sadu typů informací a logiku zjišťování. Nyní máte možnost přizpůsobit si tuto taxonomii a definovat sadu a klasifikaci klasifikačních konstrukcí konkrétně pro vaše prostředí.

Definice a přizpůsobení taxonomie klasifikace se provádí na jednom centrálním místě pro celý tenant Azure. Toto umístění je v [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)jako součást vašich zásad zabezpečení. Tuto úlohu mohou provádět pouze uživatelé, kteří mají oprávnění správce pro kořenovou skupinu pro správu klienta.

V rámci správy zásad Information Protection můžete definovat vlastní popisky, seřadit je a přidružit je k vybrané sadě typů informací. Můžete také přidat vlastní typy informací a nakonfigurovat je pomocí vzorů řetězce, které jsou přidány do logiky zjišťování pro identifikaci tohoto typu dat ve vašich databázích.
Přečtěte si další informace o přizpůsobení a správě zásad v tématu [Průvodce zásadami Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po definování zásad pro všechny klienty můžete pokračovat v klasifikaci jednotlivých databází pomocí vlastních zásad.

## <a name="classify-your-sql-database"></a>Klasifikace SQL Database

1. Přejděte na [Azure Portal](https://portal.azure.com).

2. V záhlaví zabezpečení v podokně Azure SQL Database přejděte na **Rozšířené zabezpečení dat** . Kliknutím povolíte rozšířené zabezpečení dat a potom kliknete na kartu **klasifikace & zjišťování dat** .

   ![Kontrola databáze](./media/sql-data-discovery-and-classification/data_classification.png)

3. Karta **Přehled** obsahuje souhrn aktuálního stavu klasifikace databáze, včetně podrobného seznamu všech klasifikovaných sloupců, které můžete také filtrovat a zobrazit pouze konkrétní části schématu, typy informací a popisky. Pokud jste ještě neklasifikované žádné sloupce, [přejděte ke kroku 5](#step-5).

   ![Souhrn aktuálního stavu klasifikace](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Chcete-li stáhnout sestavu ve formátu aplikace Excel, klikněte v horní nabídce okna na možnost **exportovat** .

   ![Export do aplikace Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Chcete-li začít klasifikovat vaše data, klikněte na **kartu klasifikace** v horní části okna.

    ![Klasifikace dat](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Klasifikační modul vyhledá v databázi sloupce obsahující potenciálně citlivá data a poskytne seznam **doporučených klasifikací sloupců**. Postup zobrazení a použití doporučení klasifikace:

   - Pokud chcete zobrazit seznam doporučených klasifikací sloupců, klikněte na panelu doporučení v dolní části okna:

      ![Klasifikace dat](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Projděte si seznam doporučení – Pokud chcete přijmout doporučení pro konkrétní sloupec, zaškrtněte políčko v levém sloupci příslušného řádku. *Všechna doporučení* můžete také označit jako přijato zaškrtnutím políčka v záhlaví tabulky doporučení.

       ![Seznam doporučení pro kontrolu](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Chcete-li použít vybraná doporučení, klikněte na tlačítko modré **přijmout vybraná doporučení** .

      ![Použít doporučení](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Sloupce můžete také **ručně klasifikovat** jako alternativu nebo také do klasifikace založené na doporučeních:

   - V horní nabídce okna klikněte na **Přidat klasifikaci** .

      ![Ruční přidání klasifikace](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - V okně, které se otevře, vyberte schéma > tabulce > sloupec, který chcete klasifikovat, a typ informací a popisek citlivosti. Pak klikněte na tlačítko modrý **Přidat klasifikaci** ve spodní části okna kontextu.

      ![Vybrat sloupec ke klasifikaci](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Pokud chcete dokončit klasifikaci a trvale označit (označit) sloupce databáze s novými metadaty klasifikace, klikněte v horní nabídce okna na **Uložit** .

   ![Uložení](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Auditování přístupu k citlivým datům

Důležitým aspektem paradigmata Information Protection je schopnost sledovat přístup k citlivým datům. [Auditování Azure SQL Database](sql-database-auditing.md) bylo rozšířeno tak, aby zahrnovalo nové pole v protokolu auditu s názvem *data_sensitivity_information*, který zapisuje klasifikace citlivosti (popisky) skutečných dat vrácených dotazem.

![Protokol auditu](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Oprávnění

Následující předdefinované role mohou číst klasifikaci dat služby Azure SQL Database: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` a `User Access Administrator`.

Následující předdefinované role mohou změnit klasifikaci dat služby Azure SQL Database: `Owner`, `Contributor``SQL Security Manager`.

Další informace o [RBAC pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Spravovat klasifikace

# <a name="t-sqltabazure-t-sql"></a>[T-SQL](#tab/azure-t-sql)
Pomocí T-SQL můžete přidat nebo odebrat klasifikace sloupců a načíst všechny klasifikace pro celou databázi.

> [!NOTE]
> Pokud ke správě popisků používáte T-SQL, neexistuje žádné ověření, že v zásadách organizace Information Protection existují popisky přidané do sloupce (sada popisků, které se zobrazují v doporučeních na portálu). Proto je možné to ověřit.

- Přidat nebo aktualizovat klasifikaci jednoho nebo více sloupců: [Přidání klasifikace citlivosti](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Odeberte klasifikaci z jednoho nebo více sloupců: [klasifikace citlivosti přetažení](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Zobrazit všechny klasifikace v databázi: [Sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apistabazure-rest-api"></a>[Rozhraní REST API](#tab/azure-rest-api)
Pomocí rozhraní REST API můžete programově spravovat klasifikace a doporučení. Publikovaná rozhraní REST API podporují následující operace:

- [Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – vytvoří nebo aktualizuje popisek citlivosti daného sloupce.
- [Odstranit](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – odstraní popisek citlivosti daného sloupce.
- [Zakázat doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) – zakáže doporučení citlivosti na daném sloupci.
- [Povolit doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) – povolí u daného sloupce doporučení citlivosti (ve výchozím nastavení jsou u všech sloupců povolené doporučení).
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) – získá popisek citlivosti daného sloupce.
- [List Current by Database](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) – získá aktuální popisky citlivosti dané databáze.
- [Seznam doporučený databází](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) – získá Doporučené popisky citlivosti dané databáze.

# <a name="powershell-cmdlettabazure-powelshell"></a>[Rutina PowerShellu](#tab/azure-powelshell)
Pomocí prostředí PowerShell můžete spravovat klasifikace a doporučení pro Azure SQL Database a spravovanou instanci.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>Rutina PowerShellu pro Azure SQL Database
- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable – AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Rutiny PowerShellu pro spravovanou instanci
- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable – AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a id="subheading-6"></a>Další kroky

- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Zvažte konfiguraci [Azure SQL Database auditování](sql-database-auditing.md) pro monitorování a auditování přístupu k utajovaným citlivým datům.
- Pro prezentaci na YouTube, která zahrnuje klasifikaci & Discovery dat, najdete informace v tématu zjišťování [, klasifikace, označování & ochrana dat SQL | Zpřístupnění dat](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
