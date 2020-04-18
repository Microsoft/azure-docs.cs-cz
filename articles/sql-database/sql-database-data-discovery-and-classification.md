---
title: Zjišťování a klasifikace dat
description: Zjišťování dat & klasifikace pro Azure SQL Database a Azure Synapse Analytics
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
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616752"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Zjišťování dat & klasifikace pro Azure SQL Database a Azure Synapse Analytics

Zjišťování dat & klasifikace je integrovaná do Azure SQL Database. Poskytuje pokročilé funkce pro zjišťování, klasifikaci, označování a vykazování citlivých dat v databázích.

Vaše nejcitlivější údaje mohou zahrnovat obchodní, finanční, zdravotní nebo osobní údaje. Zjišťování a klasifikace těchto dat může hrát klíčovou roli v přístupu organizace k ochraně informací. Může sloužit jako infrastruktura na:

- Pomáháme plnit standardy ochrany osobních údajů a požadavky na dodržování předpisů.
- Různé scénáře zabezpečení, jako je například monitorování (auditování) a upozornění na neobvyklý přístup k citlivým datům.
- Řízení přístupu k databázím, které obsahují vysoce citlivá data, a posílení zabezpečení databáze.

Zjišťování dat & klasifikace je součástí nabídky [Advanced Data Security,](sql-database-advanced-data-security.md) což je jednotný balíček pro pokročilé možnosti zabezpečení SQL. K zjišťování dat můžete přistupovat a spravovat & klasifikace prostřednictvím centrální části **SQL Advanced Data Security** na webu Azure Portal.

> [!NOTE]
> Tento článek se týká Azure SQL Database a Azure Synapse Analytics. Pro jednoduchost zde používáme *SQL Database* odkazovat na SQL Database a Azure Synapse. Informace o serveru SQL Server (místně) naleznete v tématu [zjišťování a klasifikace dat SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Co je zjišťování dat & klasifikace?

Zjišťování dat & klasifikace zavádí sadu pokročilých služeb a nové funkce databáze SQL. Vytváří nové paradigma ochrany informací pro SQL Database, zaměřené na ochranu dat, a nikoli pouze databáze. Paradigma zahrnuje:

- **Zjišťování a doporučení:** Modul klasifikace prohledá databázi a identifikuje sloupce, které obsahují potenciálně citlivá data. Pak vám poskytuje snadný způsob, jak zkontrolovat a použít doporučenou klasifikaci prostřednictvím portálu Azure.

- **Označení:** Popisky klasifikace citlivosti můžete trvale použít na sloupce pomocí nových atributů metadat, které byly přidány do databázového stroje SQL. Tato metadata pak lze použít pro pokročilé, citlivost založené auditování a ochrany scénáře.

- **Citlivost výsledkové sady dotazu:** Citlivost sady výsledků dotazu se vypočítá v reálném čase pro účely auditování.

- **Viditelnost:** Stav klasifikace databáze můžete zobrazit na podrobném řídicím panelu na webu Azure Portal. Můžete si také stáhnout sestavu ve formátu Excelu, která se použije pro účely dodržování předpisů a auditování a další potřeby.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Zjišťování, klasifikace a označení citlivých sloupců

Tato část popisuje kroky pro:

- Zjišťování, klasifikace a popisování sloupců, které obsahují citlivá data v databázi.
- Zobrazení aktuálního stavu klasifikace databáze a exportu sestav.

Klasifikace obsahuje dva atributy metadat:

- **Popisky**: Hlavní atributy klasifikace, které se používají k definování úrovně citlivosti dat uložených ve sloupci.  
- **Typy informací**: Atributy, které poskytují podrobnější informace o typu dat uložených ve sloupci.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definování a přizpůsobení taxonomie klasifikace

Zjišťování dat & klasifikace je dodáván s integrovanou sadou popisků citlivosti a vestavěnou sadou typů informací a logiky zjišťování. Tuto taxonomii si teď můžete přizpůsobit a definovat sadu klasifikačních konstruktorů (včetně hodnocení) na míru pro vaše prostředí.

Taxonomii klasifikace definujete a přizpůsobujete na jednom centrálním místě pro celou organizaci Azure. Toto umístění je v [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)jako součást zásad zabezpečení. Tento úkol může provést pouze uživatel s právy správce ve skupině pro správu kořenové správy organizace.

Jako součást správy zásad pro ochranu informací SQL můžete definovat vlastní popisky, seřadit je a přidružit je k vybrané sadě typů informací. Můžete také přidat vlastní typy informací a nakonfigurovat je pomocí vzorů řetězců. Vzory jsou přidány do logiky zjišťování pro identifikaci tohoto typu dat v databázích.

Další informace o přizpůsobení a správě zásad naleznete v [příručce k zásadám ochrany informací SQL](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po definování zásad pro celou organizaci můžete pokračovat ve klasifikaci jednotlivých databází pomocí vlastních zásad.

### <a name="classify-your-sql-database"></a>Klasifikovat databázi SQL

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Přejděte na **Rozšířené zabezpečení dat** v záhlaví **Zabezpečení** v podokně Databáze Azure SQL. Vyberte **Rozšířené zabezpečení dat**a pak vyberte kartu Zjišťování dat & **klasifikace.**

   ![Podokno Rozšířené zabezpečení dat na webu Azure Portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na stránce **zjišťování dat & klasifikace** obsahuje karta **Přehled** souhrn aktuálního stavu klasifikace databáze. Souhrn obsahuje podrobný seznam všech klasifikovaných sloupců, které můžete také filtrovat tak, aby zobrazoval pouze určité části schématu, typy informací a popisky. Pokud jste ještě neklasifikovali žádné sloupce, [přejděte ke kroku 5](#step-5).

   ![Souhrn aktuálního stavu klasifikace](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Chcete-li stáhnout sestavu ve formátu Excelu, vyberte **Exportovat** v horní nabídce podokna.

5. <a id="step-5"></a>Chcete-li začít klasifikovat data, vyberte kartu **Klasifikace** na stránce **zjišťování dat & klasifikace.**

    Modul klasifikace prohledá v databázi sloupce obsahující potenciálně citlivá data a poskytuje seznam doporučených klasifikací sloupců.

6. Zobrazit a použít doporučení klasifikace:

   - Chcete-li zobrazit seznam doporučených klasifikací sloupců, vyberte panel doporučení v dolní části podokna.

   - Chcete-li přijmout doporučení pro určitý sloupec, zaškrtněte políčko v levém sloupci příslušného řádku. Chcete-li označit všechna doporučení jako přijatá, zaškrtněte políčko v záhlaví tabulky doporučení zcela vlevo.

       ![Kontrola a výběr ze seznamu doporučení pro klasifikaci](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Chcete-li použít vybraná doporučení, vyberte **Přijmout vybraná doporučení**.

7. Sloupce můžete také klasifikovat ručně, jako alternativu nebo vedle klasifikace založené na doporučení:

   1. V horní nabídce podokna vyberte **Přidat klasifikaci.**

   1. V okně kontextu, které se otevře, vyberte schéma, tabulku a sloupec, které chcete klasifikovat, a popisek typu a citlivosti informací.

   1. V dolní části kontextového okna vyberte **Přidat klasifikaci.**

      ![Výběr sloupce, který chcete klasifikovat](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Chcete-li dokončit klasifikaci a trvale označovat (označovat) sloupce databáze novými metadaty klasifikace, vyberte **Uložit** v horní nabídce okna.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Audit přístupu k citlivým datům

Důležitým aspektem paradigmatu ochrany informací je schopnost sledovat přístup k citlivým údajům. [Auditování azure sql database bylo](sql-database-auditing.md) vylepšeno tak, aby `data_sensitivity_information`zahrnovalo nové pole do protokolu auditu nazývaného . Toto pole zaznamenává klasifikace citlivosti (popisky) dat, která byla vrácena dotazem. Tady je příklad:

![Protokol auditu](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Oprávnění

Tyto předdefinované role můžou číst klasifikaci dat databáze Azure SQL:

- Vlastník
- Čtenář
- Přispěvatel
- Správce zabezpečení SQL
- Správce přístupu uživatelů

Tyto předdefinované role můžou upravit klasifikaci dat databáze Azure SQL:

- Vlastník
- Přispěvatel
- Správce zabezpečení SQL

Další informace o oprávněních založených na rolích v [RBAC pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Správa klasifikací

Můžete použít T-SQL, REST API nebo PowerShell ke správě klasifikací.

### <a name="use-t-sql"></a>Použití T-SQL

Pomocí T-SQL můžete přidat nebo odebrat klasifikace sloupců a načíst všechny klasifikace pro celou databázi.

> [!NOTE]
> Při použití T-SQL ke správě popisků neexistuje žádné ověření, že popisky, které přidáte do sloupce existují v organizaci zásady ochrany informací (sada popisků, které se zobrazí v doporučení portálu). Takže je na vás, abyste to potvrdili.

Informace o použití T-SQL pro klasifikace, naleznete v následujících odkazech:

- Přidání nebo aktualizace klasifikace jednoho nebo více sloupců: [PŘIDAT KLASIFIKACI CITLIVOSTI](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Odebrání klasifikace z jednoho nebo více sloupců: [KLASIFIKACE CITLIVOSTI PŘETAŽENÍ](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Chcete-li zobrazit všechny klasifikace v databázi: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Použití rozhraní Rest API

Rozhraní REST API můžete použít k programové správě klasifikací a doporučení. Publikované rozhraní REST API podporuje následující operace:

- [Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): Vytvoří nebo aktualizuje popisek citlivosti zadaného sloupce.
- [Odstranit](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Odstraní popisek citlivosti zadaného sloupce.
- [Zakázat doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Zakáže doporučení citlivosti v zadaném sloupci.
- [Povolit doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): Povolí doporučení citlivosti v zadaném sloupci. (Doporučení jsou ve výchozím nastavení povolena ve všech sloupcích.)
- [Získat](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Získá popisek citlivosti zadaného sloupce.
- [Seznam aktuální podle databáze](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Získá aktuální citlivost popisky zadané databáze.
- [Seznam doporučený podle databáze](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Získá popisky doporučené citlivosti zadané databáze.

### <a name="use-powershell-cmdlets"></a>Použití rutin PowerShell
Pomocí PowerShellu můžete spravovat klasifikace a doporučení pro Azure SQL Database a spravované instance.

#### <a name="powershell-cmdlets-for-sql-database"></a>Rutiny prostředí PowerShell pro databázi SQL

- [Get-AzSqlDatabázeSensitivityKlasifikace](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabázeSensitivityKlasifikace](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Odebrat-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Doporučení k citlivosti databáze Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaDoporučení citlivosti sekvitu](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Zakázat azsqldatabasesensitivitydoporučenídoporučení](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Rutiny prostředí PowerShell pro spravované instance

- [Get-AzSqlInstanceSensitivityKlasifikace citlivosti](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Klasifikace citlivosti citlivosti databáze Set-AzSqlInstanceInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Odebrat-AzSqlInstanceDatabázeSensitivityKlasifikace](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Doporučení k citlivosti databáze Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Povolit azsqlinstancedatabázesensitivityrecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Zakázat azsqlinstancedatabázesensitivitydoporučenídoporučení](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Další kroky

- Další informace o [rozšířeném zabezpečení dat](sql-database-advanced-data-security.md).
- Zvažte konfiguraci [Azure SQL Database Auditing](sql-database-auditing.md) pro monitorování a auditování přístupu k klasifikovaným citlivým datům.
- Prezentace, která zahrnuje zjišťování a klasifikaci dat, naleznete v [tématu Zjišťování, klasifikace, označování & ochrana dat SQL | Data Vystavena](https://www.youtube.com/watch?v=itVi9bkJUNc).
