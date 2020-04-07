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
ms.openlocfilehash: b3a08eb351d29fd71889807c9a21d03b564117a7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673754"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Zjišťování dat & klasifikace pro Azure SQL Database a Azure Synapse Analytics

Zjišťování dat & klasifikace poskytuje pokročilé funkce integrované do Azure SQL Database pro **zjišťování**, **klasifikaci**a **označování** & **citlivých** dat ve vašich databázích.

Zjišťování a klasifikace nejcitlivějších dat (obchodní, finanční, zdravotní, osobně identifikovatelná data a tak dále) může hrát klíčovou roli ve vaší organizační ochraně informací. Může sloužit jako infrastruktura na:

- podporu dodržování předpisů v oblasti ochrany osobních údajů a dalších regulací;
- Různé scénáře zabezpečení, jako je například monitorování (auditování) a upozornění na neobvyklý přístup k citlivým datům.
- řízení přístupu k databázím obsahujícím vysoce citlivá data a posílení jejich zabezpečení.

Zjišťování dat & klasifikace je součástí nabídky [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé možnosti zabezpečení SQL. zjišťování dat & klasifikace je přístupná a spravovaná prostřednictvím centrálního portálu SQL ADS.

> [!NOTE]
> Tento dokument se týká Azure SQL Database a Azure Synapse. Pro jednoduchost SQL Database se používá při odkazování na SQL Database a Azure Synapse. Sql Server (místně) viz [Zjišťování a klasifikace dat SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Co je zjišťování dat & klasifikace

Data Discovery & klasifikace zavádí sadu pokročilých služeb a nové funkce SQL, které tvoří nové sql information protection paradigma zaměřené na ochranu dat, a to nejen databáze:

- **Doporučení & zjišťování**

  Modul klasifikace prohledá databázi a identifikuje sloupce obsahující potenciálně citlivá data. Poté vám je poskytne v jednoduché podobě ke kontrole, abyste mohli uplatnit příslušná doporučení ke klasifikaci na webu Azure Portal.

- **Označování**

  Popisky klasifikace citlivosti mohou být trvale označeny ve sloupcích pomocí nových atributů metadat klasifikace zavedených do modulu SQL Engine. Tato metadata se pak dají využít v pokročilých scénářích auditu a ochrany podle citlivosti.

- **Citlivost sady výsledků dotazu**

  Citlivost sady výsledků dotazu se vypočítá v reálném čase pro účely auditování.

- **Viditelnost**

  Stav klasifikace databáze lze zobrazit v podrobném řídicím panelu na portálu. Navíc si můžete stáhnout sestavu (ve formátu Excelu), kterou můžete využít mimo jiné pro potřeby zajišťování souladu s předpisy nebo auditování.

## <a name="discover-classify--label-sensitive-columns"></a><a id="discover-classify-columns"></a>Zjišťování, klasifikace sloupců citlivých na popisky &

Následující část popisuje kroky pro zjišťování, klasifikaci a popisování sloupců obsahujících citlivá data v databázi a také zobrazení aktuálního stavu klasifikace databáze a exportu sestav.

Klasifikace obsahuje dva atributy metadat:

- **Popisky** – hlavní atributy klasifikace, které slouží k definování úrovně citlivosti dat uložených ve sloupci.  
- **Typy informací** – poskytují další rozlišovací schopnost do typu dat uložených ve sloupci.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definování a přizpůsobení taxonomie klasifikace

Zjišťování dat & klasifikace je dodáván s integrovanou sadou popisků citlivosti a vestavěnou sadou typů informací a logiky zjišťování. Nyní máte možnost přizpůsobit tuto taxonomii a definovat sadu a pořadí klasifikačních konstrukcí speciálně pro vaše prostředí.

Definice a přizpůsobení taxonomie klasifikace se provádí na jednom centrálním místě pro celého klienta Azure. Toto umístění je v [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), jako součást zásad zabezpečení. Tento úkol může provést pouze uživatel s právy správce ve skupině pro správu kořenového klienta.

Jako součást správy zásad ochrany informací SQL můžete definovat vlastní popisky, seřadit je a přidružit je k vybrané sadě typů informací. Můžete také přidat vlastní typy informací a nakonfigurovat je pomocí vzorů řetězců, které se přidají do logiky zjišťování pro rozpoznání tohoto typu dat ve vašich databázích.
Další informace o přizpůsobení a správě zásad naleznete v [příručce k zásadám ochrany informací SQL](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po definování zásad y pro celý klient můžete pokračovat v klasifikaci jednotlivých databází pomocí vlastních zásad.

## <a name="classify-your-sql-database"></a>Klasifikovat databázi SQL

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Přejděte na **Rozšířené zabezpečení dat** pod nadpisem Zabezpečení v podokně Databáze Azure SQL. Klepnutím povolíte rozšířené zabezpečení dat a potom kliknete na kartu **& klasifikace zjišťování dat.**

   ![Skenování databáze](./media/sql-data-discovery-and-classification/data_classification.png)

3. Karta **Přehled** obsahuje souhrn aktuálního stavu klasifikace databáze, včetně podrobného seznamu všech klasifikovaných sloupců, které můžete také filtrovat a zobrazit pouze určité části schématu, typy informací a popisky. Pokud jste ještě neklasifikovali žádné sloupce, [přejděte ke kroku 5](#step-5).

   ![Souhrn aktuálního stavu klasifikace](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Chcete-li stáhnout sestavu ve formátu aplikace Excel, klikněte na možnost **Exportovat** v horní nabídce okna.

5. <a id="step-5"></a>Chcete-li začít klasifikovat data, klikněte na **kartu Klasifikace** v horní části okna.

6. Modul klasifikace prohledá v databázi sloupce obsahující potenciálně citlivá data a obsahuje seznam **doporučených klasifikací sloupců**. Zobrazení a použití doporučení pro klasifikaci:

   - Chcete-li zobrazit seznam doporučených klasifikací sloupců, klikněte na panel doporučení v dolní části okna.

   - Projděte si seznam doporučení – chcete-li přijmout doporučení pro konkrétní sloupec, zaškrtněte políčko v levém sloupci příslušného řádku. *Všechna doporučení* můžete také označit jako přijatá zaškrtnutím zaškrtávacího políčka v záhlaví tabulky doporučení.

       ![Zkontrolovat seznam doporučení](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Chcete-li použít vybraná doporučení, klikněte na modré tlačítko **Přijmout vybraná doporučení.**

7. Sloupce můžete také **ručně klasifikovat** jako alternativu nebo navíc jako klasifikaci založenou na doporučení:

   - Klikněte na **Přidat klasifikaci** v horní nabídce okna.

   - V okně kontextu, které se otevře, vyberte schéma > tabulka > sloupec, který chcete klasifikovat, a popisek typu informací a citlivosti. Poté klikněte na modré tlačítko **Přidat klasifikaci** v dolní části kontextového okna.

      ![Výběr sloupce, který chcete klasifikovat](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Chcete-li dokončit klasifikaci a trvale označit (označit) sloupce databáze novými metadaty klasifikace, klikněte na **Uložit** v horní nabídce okna.

## <a name="auditing-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditování přístupu k citlivým datům

Důležitým aspektem paradigmatu ochrany informací je schopnost sledovat přístup k citlivým údajům. [Auditování azure sql database bylo](sql-database-auditing.md) vylepšeno tak, aby zahrnovalo nové pole do protokolu auditu s názvem *data_sensitivity_information*, které zaznamenává klasifikace citlivosti (popisky) skutečných dat, která byla vrácena dotazem.

![Protokol auditu](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Oprávnění

Následující předdefinované role můžou číst klasifikaci dat `Owner`databáze `Reader` `Contributor`Azure `SQL Security Manager` `User Access Administrator`SQL: , , , a .

Následující předdefinované role můžete upravit klasifikaci dat databáze `Owner` `Contributor`Azure `SQL Security Manager`SQL: , , .

Další informace o [RBAC pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Správa klasifikací

### <a name="using-t-sql"></a>Použití T-SQL
Můžete použít T-SQL přidat nebo odebrat klasifikace sloupců, stejně jako načíst všechny klasifikace pro celou databázi.

> [!NOTE]
> Při použití T-SQL ke správě popisků neexistuje žádné ověření, že popisky přidané do sloupce existují v zásadách ochrany informací organizace (sada popisků, které se zobrazují v doporučeních portálu). Je tedy na vás, abyste to potvrdili.

- Přidání nebo aktualizace klasifikace jednoho nebo více sloupců: [PŘIDAT KLASIFIKACI CITLIVOSTI](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Odebrání klasifikace z jednoho nebo více sloupců: [KLASIFIKACE CITLIVOSTI PŘETAŽENÍ](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Zobrazit všechny klasifikace v databázi: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="using-rest-api"></a>Použití rozhraní Rest API
Rozhraní REST API můžete použít k programové správě klasifikací a doporučení. Publikované rozhraní REST API podporuje následující operace:

- [Vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Vytvoří nebo aktualizuje popisek citlivosti daného sloupce
- [Odstranit](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Odstraní popisek citlivosti daného sloupce.
- [Zakázat doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) – zakáže doporučení citlivosti v daném sloupci.
- [Povolit doporučení](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) – povolí doporučení citlivosti v daném sloupci (doporučení jsou ve výchozím nastavení povolena ve všech sloupcích)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) - Získá popisek citlivosti daného sloupce
- [Seznam aktuální podle databáze](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - získá aktuální citlivost popisky dané databáze
- [Seznam doporučený podle databáze](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - získá popisky doporučené citlivosti dané databáze

### <a name="using-powershell-cmdlet"></a>Použití rutiny prostředí PowerShell
Pomocí PowerShellu můžete spravovat klasifikace a doporučení pro Azure SQL Database a spravovanou instanci.

#### <a name="powershell-cmdlet-for-azure-sql-database"></a>Rutina prostředí PowerShell pro azure sql databázi
- [Get-AzSqlDatabázeSensitivityKlasifikace](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabázeSensitivityKlasifikace](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Odebrat-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Doporučení k citlivosti databáze Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaDoporučení citlivosti sekvitu](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Zakázat azsqldatabasesensitivitydoporučenídoporučení](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instance"></a>Rutiny prostředí PowerShell pro spravovanou instanci
- [Get-AzSqlInstanceSensitivityKlasifikace citlivosti](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Klasifikace citlivosti citlivosti databáze Set-AzSqlInstanceInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Odebrat-AzSqlInstanceDatabázeSensitivityKlasifikace](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Doporučení k citlivosti databáze Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Povolit azsqlinstancedatabázesensitivityrecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Zakázat azsqlinstancedatabázesensitivitydoporučenídoporučení](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Další kroky

- Další informace o [rozšířeném zabezpečení dat](sql-database-advanced-data-security.md).
- Zvažte konfiguraci [Azure SQL Database Auditing](sql-database-auditing.md) pro monitorování a auditování přístupu k klasifikovaným citlivým datům.
- Prezentace, která obsahuje & klasifikace zjišťování dat, najdete v tématu [Zjišťování, klasifikace, označování & ochrana dat SQL | Data Vystavena](https://www.youtube.com/watch?v=itVi9bkJUNc).
