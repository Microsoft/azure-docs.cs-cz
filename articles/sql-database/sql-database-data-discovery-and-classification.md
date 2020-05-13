---
title: Zjišťování a klasifikace dat
description: Klasifikace dat & klasifikace pro Azure SQL Database a Azure synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: 27989687934719be5f1d18b85d3ead92f28b3f60
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123849"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Klasifikace dat & klasifikace pro Azure SQL Database a Azure synapse Analytics

Klasifikace & Discovery dat je integrovaná do Azure SQL Database. Poskytuje pokročilé možnosti pro zjišťování, klasifikaci, označování a vytváření sestav citlivých dat ve vašich databázích.

Vaše nejvíc citlivá data můžou zahrnovat obchodní, finanční, zdravotní péče nebo osobní údaje. Zjišťování a klasifikace těchto dat může hrát kontingenční roli v přístupu k ochraně informací ve vaší organizaci. Může sloužit jako infrastruktura na:

- Pomoc při plnění standardů ochrany osobních údajů a požadavků na dodržování předpisů v souladu s předpisy.
- Různé scénáře zabezpečení, jako je monitorování (auditování) a upozorňování na neobvyklé přístup k citlivým datům.
- Řízení přístupu a posílení zabezpečení databází, které obsahují vysoce citlivá data.

Klasifikace & Discovery dat je součástí rozšířené nabídky [zabezpečení dat](sql-database-advanced-data-security.md) , což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Pomocí centrálního oddílu **zabezpečení SQL Advanced data** v Azure Portal můžete používat a spravovat klasifikaci & Discovery Data.

> [!NOTE]
> Tento článek se týká Azure SQL Database a Azure synapse Analytics. Pro zjednodušení používáme *SQL Database* , abyste odkazovali na SQL Database a Azure synapse. Informace o SQL Server (místních) najdete v tématu věnovaném [zjišťování a klasifikaci dat SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Co je klasifikace & data Discovery?

Klasifikace data Discovery & zavádí sadu pokročilých služeb a nových možností SQL Database. Tvoří nové paradigma pro ochranu informací pro SQL Database, které mají za cíl chránit data a nikoli jenom databázi. Paradigma obsahuje:

- **Zjišťování a doporučení:** Klasifikační modul prohledá vaši databázi a určí sloupce, které obsahují potenciálně citlivá data. Potom vám poskytne snadný způsob, jak zkontrolovat a použít doporučenou klasifikaci prostřednictvím Azure Portal.

- **Popisování:** Popisky klasifikace s rozlišováním citlivosti můžete použít trvale na sloupce pomocí nových atributů metadat, které byly přidány do modulu SQL Database Engine. Tato metadata se pak dají použít k pokročilým scénářům auditování a ochrany na základě citlivosti.

- **Výsledek dotazu – nastavení citlivosti:** Citlivost sady výsledků dotazu je vypočítána v reálném čase pro účely auditování.

- **Viditelnost:** Stav klasifikace databáze můžete zobrazit na podrobném řídicím panelu v Azure Portal. Můžete také stáhnout sestavu ve formátu aplikace Excel a použít ji pro účely dodržování předpisů a auditování a další potřeby.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Zjišťování, klasifikace a označení citlivých sloupců

Tato část popisuje kroky pro:

- Zjišťování, klasifikace a označování sloupců, které obsahují citlivá data ve vaší databázi.
- Zobrazení aktuálního stavu klasifikace databáze a exportování sestav.

Klasifikace obsahuje dva atributy metadat:

- **Labels**: hlavní atributy klasifikace, které slouží k definování úrovně citlivosti dat uložených ve sloupci.  
- **Typy informací**: atributy, které poskytují podrobnější informace o typu dat uložených ve sloupci.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definování a přizpůsobení taxonomie klasifikace

Klasifikace data Discovery & obsahuje integrovanou sadu popisků citlivosti a integrovanou sadu typů informací a logiku zjišťování. Tuto taxonomii si teď můžete přizpůsobit a definovat sadu klasifikačních konstruktorů (včetně hodnocení) na míru pro vaše prostředí.

Na jednom centrálním místě pro celou organizaci Azure definujete a přizpůsobíte svou taxonomii klasifikace. Toto umístění je v [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)jako součást vašich zásad zabezpečení. Tuto úlohu může provést jenom osoba, která má oprávnění správce pro kořenovou skupinu pro správu organizace.

V rámci správy zásad pro SQL Information Protection můžete definovat vlastní popisky, seřadit je a přidružit je k vybrané sadě typů informací. Můžete také přidat vlastní typy informací a nakonfigurovat je pomocí vzorů řetězce. Do logiky zjišťování jsou přidány vzorce pro identifikaci tohoto typu dat ve vašich databázích.

Přečtěte si další informace o přizpůsobení a správě zásad v tématu [Průvodce zásadou pro SQL Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po definování zásad v rámci organizace můžete dál klasifikovat jednotlivé databáze pomocí vlastních zásad.

### <a name="classify-your-sql-database"></a>Klasifikace databáze SQL

1. Přejít na [Azure Portal](https://portal.azure.com).

2. V záhlaví **zabezpečení** v podokně Azure SQL Database otevřete **Rozšířené zabezpečení dat** . Vyberte **Upřesnit zabezpečení dat**a pak vyberte kartu **klasifikace & zjišťování dat** .

   ![Podokno zabezpečení pokročilých dat v Azure Portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na stránce **klasifikace & zjišťování dat** obsahuje karta **Přehled** souhrn aktuálního stavu klasifikace databáze. Souhrn obsahuje podrobný seznam všech klasifikovaných sloupců, které můžete také filtrovat a zobrazovat pouze konkrétní části schématu, typy informací a popisky. Pokud jste zatím neklasifikované žádné sloupce, [přejděte ke kroku 5](#step-5).

   ![Souhrn aktuálního stavu klasifikace](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Chcete-li stáhnout sestavu ve formátu aplikace Excel, v horní nabídce podokna vyberte **exportovat** .

5. <a id="step-5"></a>Pokud chcete začít klasifikovat vaše data, vyberte kartu **klasifikace** na stránce **klasifikace & Discovery Data** .

    Klasifikační modul vyhledá v databázi sloupce obsahující potenciálně citlivá data a poskytne seznam doporučených klasifikací sloupců.

6. Zobrazit a použít doporučení klasifikace:

   - Seznam doporučených klasifikací sloupců zobrazíte tak, že v dolní části podokna vyberete panel doporučení.

   - Chcete-li přijmout doporučení pro určitý sloupec, zaškrtněte políčko v levém sloupci příslušného řádku. Pokud chcete všechna doporučení označit jako přijato, zaškrtněte políčko vlevo v záhlaví tabulky doporučení.

       ![Kontrola a výběr ze seznamu doporučení klasifikace](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Chcete-li použít vybraná doporučení, vyberte možnost **přijmout vybraná doporučení**.

7. Sloupce můžete také klasifikovat ručně, jako alternativu nebo kromě klasifikace založené na doporučeních:

   1. V horní nabídce podokna vyberte **Přidat klasifikaci** .

   1. V kontextovém okně, které se otevře, vyberte schéma, tabulku a sloupec, které chcete klasifikovat, a typ informací a popisek citlivosti.

   1. V dolní části okna kontextu vyberte **Přidat klasifikaci** .

      ![Vyberte sloupec, který se má klasifikovat.](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Pokud chcete dokončit klasifikaci a trvale označit (označit) sloupce databáze s novými metadaty klasifikace, vyberte **Uložit** v horní nabídce okna.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditování přístupu k citlivým datům

Důležitým aspektem paradigmata ochrany informací je schopnost sledovat přístup k citlivým datům. [Auditování Azure SQL Database](sql-database-auditing.md) bylo rozšířeno tak, aby zahrnovalo nové pole v protokolu auditu s názvem `data_sensitivity_information` . Toto pole zapisuje klasifikace citlivosti (popisky) dat vrácených dotazem. Tady je příklad:

![Protokol auditu](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Oprávnění

Tyto předdefinované role mohou číst klasifikaci dat služby Azure SQL Database:

- Vlastník
- Čtenář
- Přispěvatel
- Správce zabezpečení SQL
- Správce přístupu uživatelů

Tyto předdefinované role mohou změnit klasifikaci dat služby Azure SQL Database:

- Vlastník
- Přispěvatel
- Správce zabezpečení SQL

Přečtěte si další informace o oprávněních na základě rolí v [RBAC pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Spravovat klasifikace

Ke správě klasifikací můžete použít T-SQL, REST API nebo PowerShell.

### <a name="use-t-sql"></a>Použití T-SQL

K přidání nebo odebrání klasifikací sloupců a k načtení všech klasifikací pro celou databázi můžete použít T-SQL.

> [!NOTE]
> Když ke správě popisků použijete T-SQL, neexistuje žádné ověření, které v zásadách ochrany informací v organizaci existují popisky, které přidáte do sloupce (sada popisků, které se zobrazují v doporučeních na portálu). To je vše, co můžete ověřit.

Informace o použití T-SQL pro klasifikace naleznete v následujících odkazech:

- Přidání nebo aktualizace klasifikace jednoho nebo více sloupců: [Přidání klasifikace citlivosti](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Odebrání klasifikace z jednoho nebo více sloupců: [klasifikace citlivosti přetažení](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Zobrazení všech klasifikací v databázi: [Sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Použití rutin PowerShell
Pomocí prostředí PowerShell můžete spravovat klasifikace a doporučení pro Azure SQL Database a spravované instance.

#### <a name="powershell-cmdlets-for-sql-database"></a>Rutiny PowerShellu pro SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable – AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Rutiny PowerShellu pro spravované instance

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable – AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Použití rozhraní REST API

Pomocí REST API můžete programově spravovat klasifikace a doporučení. Publikovaný REST API podporuje následující operace:

- [Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): vytvoří nebo aktualizuje popisek citlivosti pro zadaný sloupec.
- [Odstranit](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): odstraní popisek citlivosti pro zadaný sloupec.
- [Zakázat doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): zakáže doporučení citlivosti na zadaném sloupci.
- [Povolit doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): povoluje u zadaného sloupce doporučení citlivosti. (Doporučení jsou ve výchozím nastavení povolená pro všechny sloupce.)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Získá popisek citlivosti určeného sloupce.
- [Vypsat aktuální podle databáze](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Získá aktuální popisky citlivosti zadané databáze.
- [Seznam doporučený databází](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Získá doporučené citlivosti citlivosti zadané databáze.

## <a name="next-steps"></a><a id="next-steps"></a>Další kroky

- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Zvažte konfiguraci [Azure SQL Database auditování](sql-database-auditing.md) pro monitorování a auditování přístupu k utajovaným citlivým datům.
- Prezentaci, která zahrnuje zjišťování a klasifikaci dat, najdete v tématu zjišťování [, klasifikace, označování & ochrana dat SQL | Zpřístupnění dat](https://www.youtube.com/watch?v=itVi9bkJUNc).
