---
title: Kurz Adventure Works pro Azure Analysis Services | Dokumentace Microsoftu
description: Představuje kurz Adventure Works pro Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3c95e755c66cfd1bc0a4e4e55bf1d05f08214925
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services – Kurz Adventure Works

V tomto kurzu najdete lekce zabývající se vytvořením a nasazením tabelárního modelu na úrovni kompatibility 1400 s použitím sady Visual Studio a [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

Pokud se službou Analysis Services a tabelárním modelováním teprve začínáte, absolvováním tohoto kurzu se nejrychleji naučíte vytvořit a nasadit základní tabelární model s využitím sady Visual Studio. Jakmile splníte všechny požadavky, dokončení kurzu by vám mělo trvat přibližně 2 až 3 hodiny.  
  
## <a name="what-you-learn"></a>Co se naučíte   
  
-   Vytvořit v sadě Visual Studio se SSDT nový projekt s tabelárním modelem na **úrovni kompatibility 1400**.
  
-   Importovat data z relační databáze do databáze pracovního prostoru projektu s tabelárním modelem.  
  
-   Vytvořit a spravovat relace mezi tabulkami v modelu.  
  
-   Vytvořit počítané sloupce, míry a klíčové ukazatele výkonu, které uživatelům pomůžou analyzovat důležité obchodní metriky.  
  
-   Vytvořit a spravovat perspektivy a hierarchie, které uživatelům usnadní procházení dat modelu díky poskytování pohledů specifických pro podnik a aplikaci.  
  
-   Vytvořit oddíly, které rozdělí tabulková data do menších logické jednotky s možností zpracování nezávisle na ostatních oddílech.  
  
-   Zabezpečit objekty a data modelu vytvořením rolí s uživateli jako členy.  
  
-   Nasadit tabelární model na server **Azure Analysis Services** nebo server **SQL Server 2017 Analysis Services** pomocí SSDT.  
  
## <a name="prerequisites"></a>Požadavky  
Pro absolvování tohoto kurzu potřebujete:  
  
-   Server Azure Analysis Services. Zaregistrujte si bezplatnou [zkušební verzi Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) a [vytvořte server](../analysis-services-create-server.md). 

-   [Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) s **ukázkovou databází AdventureWorksDW**, nebo SQL Server Data Warehouse s [ukázkovou databází Adventure Works](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks).

    **Důležité:** Pokud instalujete ukázkovou databázi na místní SQL Server Data Warehouse a model nasazujete na server Azure Analysis Services, vyžaduje se [místní brána dat](../analysis-services-gateway.md).

-   Nejnovější verzi [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) pro Visual Studio.

-   Nejnovější verzi aplikace [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   Klientskou aplikaci, jako je [Power BI Desktop](https://powerbi.microsoft.com/desktop/) nebo Excel. 

## <a name="scenario"></a>Scénář  
Tento kurz je založený na fiktivní společnosti Adventure Works Cycles. Adventure Works je velká mezinárodní výrobní společnost, která vyrábí jízdní kola, náhradní díly a příslušenství a distribuuje je na komerční trhy v Severní Americe, Evropě a Asii. Společnost zaměstnává 500 pracovníků. Kromě toho Adventure Works zaměstnává několik regionálních prodejních týmů na všech základních trzích. Vaším projektem je vytvoření tabelárního modelu pro uživatele prodeje a marketingu, pomocí kterého budou moci analyzovat data o prodejích na internetu v databázi AdventureWorksDW.  
  
Pro absolvování tohoto kurzu musíte dokončit různé lekce. Každá lekce zahrnuje úkoly. Dokončení všech úkolů v daném pořadí je nezbytné pro dokončení lekce. Některé lekce obsahují několik úkolů, jejichž výsledek je podobný, ale způsob jejich dokončení se mírně liší. Tato metoda ukazuje, že k dokončení úkolu často vede víc cest, a vybízí vás k využití dovedností, které jste se naučili v předchozích lekcích a úkolech.  
  
Účelem lekcí je provést vás vytvořením a spuštěním základního tabelárního modelu pomocí řady funkcí obsažených v SSDT. Vzhledem k tomu, že každá lekce navazuje na předchozí lekci, měli byste lekce dokončovat v daném pořadí.
  
Tento kurz nezahrnuje lekce týkající se správy serveru na webu Azure Portal, správy serveru nebo databáze pomocí aplikace SSMS ani procházení dat modelu pomocí klientské aplikace. 


## <a name="lessons"></a>Lekce  
Tento kurz se skládá z následujících lekcí:  
  
|Lekce|Odhadovaný čas dokončení|  
|----------|------------------------------|  
|[1 – Vytvoření nového projektu s tabelárním modelem](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minut|  
|[2 – Získání dat](../tutorials/aas-lesson-2-get-data.md)|10 minut|  
|[3 – Označení jako datové tabulky](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minuty|  
|[4 – Vytvoření relací](../tutorials/aas-lesson-4-create-relationships.md)|10 minut|  
|[5 – Vytvoření počítaných sloupců](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minut|
|[6 – Vytvoření měr](../tutorials/aas-lesson-6-create-measures.md)|30 minut|  
|[7 – Vytvoření klíčových ukazatelů výkonu (KPI)](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minut|  
|[8 – Vytvoření perspektiv](../tutorials/aas-lesson-8-create-perspectives.md)|5 minut|  
|[9 – Vytvoření hierarchií](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minut|  
|[10 – Vytvoření oddílů](../tutorials/aas-lesson-10-create-partitions.md)|15 minut|  
|[11 – Vytvoření rolí](../tutorials/aas-lesson-11-create-roles.md)|15 minut|  
|[12 – Analýza v aplikaci Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minut| 
|[13 – Nasazení](../tutorials/aas-lesson-13-deploy.md)|5 minut|  
  
## <a name="supplemental-lessons"></a>Doplňkové lekce  
Tyto lekce nejsou vyžadovány pro absolvování kurzu, ale můžete díky nim lépe porozumět pokročilým funkcím pro vytváření tabelárních modelů.  
  
|Lekce|Odhadovaný čas dokončení|  
|----------|------------------------------|  
|[Řádky podrobností](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minut|
|[Dynamické zabezpečení](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minut|
|[Nepravidelné hierarchie](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minut| 

  
## <a name="next-steps"></a>Další postup  
Pokud chcete začít, přejděte k [lekci 1: Vytvoření nového projektu s tabelárním modelem](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

