---
title: Data v databázi Azure SQL zjišťování & klasifikace | Microsoft Docs
description: Data v databázi Azure SQL zjišťování & klasifikace
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 375142b0e55c741e6ab914e969751833f989d2fb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Data v databázi Azure SQL zjišťování a klasifikace
Data zjišťování a klasifikace (momentálně ve verzi preview) a poskytuje pokročilé možnosti integrovaný do databáze SQL Azure pro **zjišťování**, **klasifikace**, **označování**  &  **ochranu** citlivá data v databázích máte.
Zjišťování a klasifikace nejcitlivější data (business, finanční, zdravotní péče, osobní údaje, atd.) můžete přehrát hrají roli v vzrůstem ochrany vaší organizace. Může sloužit jako infrastruktury pro:
* Vám pomáhá s splnění standardů data o ochraně osobních údajů a dodržování legislativních požadavků, jako je například GDPR.
* Různé scénáře zabezpečení, jako jsou monitorování (auditování) a výstrah na neobvyklé přístup k citlivým datům.
* Řízení přístupu k a posílení zabezpečení databáze, které obsahují vysoce důvěrné osobní údaje.

> [!NOTE]
> Tento dokument se vztahuje pouze k Azure SQL Database. SQL Server (místní), najdete v části [SQL Data zjišťování a klasifikace](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Přehled
Data zjišťování a klasifikace zavádí sadu pokročilé služby a nové funkce SQL, které tvoří nové zlepší SQL Information Protection zaměřené na ochranu dat, nikoli pouze databázi:
* **Zjišťování & doporučení** – modul klasifikace skenuje vaši databázi a identifikuje sloupců obsahujících potenciálně citlivá data. Pak poskytuje snadný způsob, jak zkontrolovat a použít příslušné klasifikaci doporučení prostřednictvím portálu Azure.
* **Označování** – velkých a malých písmen klasifikační štítky lze trvale označit na sloupce pomocí nové atributy metadata klasifikace zasaženo stroj SQL. Tato metadata, můžete pak použít pro rozšířené na základě velkých a malých písmen auditování a ochrany scénáře.
* **Výsledek dotazu nastavit citlivost** – citlivosti výslednou sadu dotazu se počítá v reálném čase pro účely auditování.
* **Viditelnost** -klasifikace stav databáze lze zobrazit v podrobné řídicí panel portálu. Kromě toho si můžete stáhnout sestav (ve formátu aplikace Excel) má být použit pro dodržování předpisů a auditování, stejně jako jiné požadavky.

## <a id="subheading-2"></a>Zjišťování, klasifikaci a označování citlivé sloupců
Následující část popisuje kroky pro zjišťování, klasifikaci a označování sloupců obsahujících citlivá data v databázi, a také zobrazení aktuálního stavu klasifikace vaši databázi a export sestav.

Klasifikace obsahuje dva atributy metadat:
* Popisky – atributy hlavní klasifikace se používá k definování úrovní citlivosti dat uložených ve sloupci.  
* Informace o typech – zadejte další členitosti do typu dat uložených ve sloupci.

<br>
**Ke klasifikaci vaší databázi SQL:**

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Přejděte na **Data zjišťování a klasifikace (preview)** nastavení ve vaší databázi SQL.

    ![Navigační podokno][1]

3. **Přehled** karta obsahuje souhrn aktuálního stavu klasifikace databáze, včetně podrobný seznam všechny klasifikovaného sloupce, které můžete také filtrovat zobrazení pouze konkrétní schématu částí typy informací a popisky. Pokud ještě nejsou klasifikovaná žádné sloupce [přejděte ke kroku 5](#step-5).

    ![Navigační podokno][2]

4. Chcete-li stáhnout sestavu ve formátu aplikace Excel, klikněte na **exportovat** možnost v nabídce v horní části okna.

    ![Navigační podokno][3]

5.  <a id="step-5"></a>Chcete-li začít, klasifikace dat, klikněte na **klasifikace karta** v horní části okna.

    ![Navigační podokno][4]

6. Modul klasifikace kontroly databáze pro sloupců obsahujících potenciálně citlivých data a poskytuje seznam **doporučená klasifikace sloupec**. Zobrazení a použití klasifikaci doporučení:

    * Chcete-li zobrazit seznam sloupci klasifikace, klikněte na panelu doporučení v dolní části okna:

        ![Navigační podokno][5]

    * Projděte si seznam doporučení – přijímala doporučení pro konkrétní sloupec, zaškrtněte políčko v levém sloupci relevantní řádku. Můžete také označit *všechna doporučení* jako přijatý zaškrtnutím políčka v záhlaví tabulky doporučení.

        ![Navigační podokno][6]

    * Chcete-li použít vybraný doporučení, klikněte na modrý **přijmout vybrané doporučení** tlačítko.

        ![Navigační podokno][7]

7. Můžete také **klasifikovat ručně** sloupců, jako alternativu, nebo navíc na základě doporučení klasifikaci:

    * Klikněte na **přidat klasifikace** v horní nabídce okna.

        ![Navigační podokno][8]

    * V okně kontextu, které se otevře, vyberte schéma > tabulky > sloupec, který chcete klasifikovat a informace o typu a citlivosti popisek. Potom klikněte na modrý **přidat klasifikace** tlačítko v dolní části okna kontextu.

        ![Navigační podokno][9]

8. Chcete-li dokončit vaše klasifikace a trvalé označovat sloupců (značka) databáze s novými metadaty klasifikace, klikněte na **Uložit** v horní nabídce okna.

    ![Navigační podokno][10]

## <a id="subheading-3"></a>Auditování přístupu k citlivým datům

Důležitým aspektem zlepší ochrany informací je schopnost sledovat přístup k citlivým datům.

[Auditování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) je vylepšená tak, aby zahrnout nové pole do protokolu auditování názvem *data_sensitivity_information*, které protokoly klasifikace citlivosti (popisky) vlastní data, která vrátila dotaz.

![Navigační podokno][11]

## <a id="subheading-4"></a>Další kroky
Zvažte nakonfigurování [auditování databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) pro sledování a auditování přístupu k vaší klasifikovaný citlivá data.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
