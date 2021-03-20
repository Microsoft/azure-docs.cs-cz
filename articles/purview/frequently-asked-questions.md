---
title: Nejčastější dotazy
description: Tento článek obsahuje odpovědi na nejčastější dotazy k Azure dosah.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667801"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Nejčastější dotazy k Azure dosah

## <a name="overview"></a>Přehled

Mnoho organizací nemá holistický porozumění jejich datům. Je obtížné pochopit, jaká data existují, kde se nacházejí data a jak najít a přistupovat k relevantním datům. Data neobsahují kontext, jako je například vyřazení a klasifikace, a obsáhlá metadata, což podnikovým uživatelům ztěžuje vyhledat správná data a patřičně je použít. Výsledkem je, že se k informování obchodních rozhodnutí používá jenom malá část shromážděných dat. A konečně určení problémů zabezpečení dat a ochrany citlivých dat je nekonzistentní. Vyžaduje nepřetržitou dobu a úsilí, zejména při zachování flexibility dat.

Azure dosah je řešení pro řízení dat. Pomáhá zákazníkům získat podrobné znalosti všech svých dat a současně zachovat kontrolu nad jejím použitím. S Azure dosah organizace zjišťují a zjišťují data. Získají informace o jejich nemovitosti a centrálně řídí přístup k datům.

## <a name="purpose-of-this-faq"></a>Účel těchto nejčastějších dotazů

V této části Nejčastější dotazy jsou odpovědi na nejčastější dotazy, které zákazníci a členové polí často žádají. Je určený k objasnění otázek týkajících se Azure dosah a souvisejících řešení, jako je například Azure Data Catalog (ADC) Gen 2 (zastaralé) a Azure Information Protection.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Jaké jsou zdrojové typy dostupné pro kontrolu a klasifikaci metadat?

|Azure|Mimo Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (k dispozici po konci 2020)|
|Spravovaná instance Azure SQL|SAP ECC (k dispozici po konci 2020)|
|Průzkumník dat Azure|SAP S/4 HANA (k dispozici po konci 2020)|
|Azure Data Lake Storage Gen1|Podregistr metastore (k dispozici po konci 2020)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure Files|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Jaké datové systémy/procesory se mohou připojit a získat zavedené řádky?

|Datový systém/procesor 
|---------
|Azure Data Factory: aktivita kopírování, aktivita toku dat 
|Vlastní řádek   
|Azure Data Share   
|Power BI    |
|Služba SSIS (SQL Server Integration Services)  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Jak se v souvislosti s konektorem ADC Gen 2, Azure Information Protection a Azure dosah?

Azure dosah původně začal jako konektor ADC Gen 2, ale má po rozšíření v rozsahu. Teď nabízí pokročilé možnosti katalogu ADC Gen 2 kombinované s funkcemi pro klasifikaci dat, popisování a vynucování zásad dodržování předpisů Azure Information Protection. V současné době se podrobněji zarovnává s širší definicí oboru řízení dat.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Co se stane zákazníkům s konektorem ADC Gen 1?

Azure dosah se zaměřuje na všechny inovace produktů v prostoru katalogového řešení pro společnost Microsoft. Konektor ADC Gen 1 bude nadále podporován.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Můžou mít zákazníci víc účtů Azure dosah ve stejném předplatném?

Ano, podporujeme mnoho účtů Azure dosah na jedno předplatné a každého tenanta.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Můžu spustit konektor ADC Gen 1 a Azure dosah paralelně?

Ano. Obojí jsou nezávislé služby.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Návody migrovat existující datové assety ADC 1. generace do Azure dosah?

Rozhraní API Azure dosah můžete použít k extrakci z konektoru ADC Gen 1 a ingestování do Azure dosah. Pro Glosář podporujeme hromadné nástroje založené na CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Návody šifrovat citlivá data pro tabulky SQL pomocí Azure dosah?

Šifrování dat se provádí na úrovni zdroje dat. Azure dosah ukládá pouze metadata. Nezobrazuje náhled dat.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Budou v Azure dosah existovat všechny možnosti konektoru ADC Gen 2?

Ano.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Jaký je rozdíl mezi glosářem a klasifikací?

Glosář používá zásady vytváření názvů následované netechnickými nebo podnikovými uživateli dat, označovaných také jako spotřebitelé dat. Tyto typy lidí jsou obchodní analytici nebo odborníci na data, kteří používají Azure dosah k hledání určitých typů dat na základě využití firmy. Například analytiky dodavatelských řetězců můžou potřebovat vyhledat *typy SKU* a *Podrobnosti o expedici*. Vyhledají v glosáři tyto výrazy, aby našli relevantní data.
Klasifikace je značka použitá pro datový Asset na úrovni tabulky, sloupce nebo souboru, který určuje, jaká data v prostředku existují. Klasifikaci lze použít automaticky nebo ručně na základě typu nalezených dat. Obvykle používáte klasifikační značky k identifikaci, zda Asset obsahuje citlivá data a jaký typ citlivých dat může být.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Prohledává Azure dosah a klasifikuje e-maily, soubory PDF atd. na SharePointu a OneDrivu?

Vyhledávání místních webů a knihoven služby SharePoint je zajištěno prostřednictvím Azure Information Protectionho skeneru. Skener je k dispozici pro použití v rámci předplatného Microsoft 365 zákazníka s následujícími SKU: AIP P1, EMS E3 a M365 E3. Pokud máte některou z těchto SKU, měli byste mít správné nároky na zahájení používání Azure Information Protectionového skeneru.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Jaký je výpočet, který se používá pro kontrolu?
Je k dispozici skenovací infrastruktura spravovaná Microsoftem. Pro většinu prostředků Azure/AWS, které podporujeme, nemusíte nasazovat infrastrukturu pro skenování.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Existuje způsob, jak zřídit Azure dosah prostřednictvím šablony/CLI nebo PowerShellu pro Azure Resource Manager (ARM)?

Ano, šablona ARM je k dispozici

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Už jsem Používám službu Atlas, můžu se snadno přesunout do Azure dosah?

Azure dosah je kompatibilní s rozhraním API pro Atlas. Pokud migrujete z nástroje Atlas, doporučuje se nejprve vyhledat zdroje dat pomocí Azure dosah. Jakmile budou prostředky ve vašem účtu k dispozici, můžete k jejich integraci použít podobná rozhraní API pro funkci Atlas, jako je třeba aktualizace prostředků nebo přidání vlastního řádku. Azure dosah upravuje rozhraní API pro vyhledávání, které se má použít Azure Search takže byste měli být schopni použít rozšířené vyhledávání.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Můžu ve svém tenantovi vytvořit víc katalogů?

Ano, můžete vytvořit několik účtů Azure dosah na jedno předplatné a každého tenanta. Můžete si prohlédnout stránku omezení [Správa a zvýšení kvót pro prostředky pomocí Azure dosah](how-to-manage-quotas.md).

Další doporučení týkající se, kdy byste měli nebo neměli mít více účtů v [osvědčených postupech pro nasazení Azure dosah](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Můžu zaregistrovat víc klientů v rámci jednoho účtu Azure dosah?

Ne. v současné době je nutné v tomto tenantovi vytvořit samostatný účet Azure dosah.

### <a name="does-azure-purview-support-column-level-lineage"></a>Podporuje Azure dosah čáru na úrovni sloupců?

Ano, Azure dosah podporuje čáru na úrovni sloupce.