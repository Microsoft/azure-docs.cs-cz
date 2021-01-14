---
title: Automatické použití popisků citlivosti pro vaše data
description: Naučte se vytvářet popisky citlivosti a automaticky je použít pro vaše data během kontroly.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 003a71f962652b1a1436f5d9875835534090a77a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196584"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Automatické označování dat ve službě Azure dosah

Tento článek popisuje, jak vytvořit popisky citlivosti Microsoft Information Protection (MIP) a automaticky je použít pro vaše prostředky Azure v Azure dosah.

## <a name="what-are-sensitivity-labels"></a>Co jsou popisky citlivosti? 

Aby uživatelé ve vaší organizaci mohli práci dělat, spolupracují s ostatními i mimo organizaci. Data v cloudu vždycky nezůstávají a často se v celém zařízení, aplikacích a službách často roamingou. 

Když vaše data roamingete, chcete, aby tak učinil zabezpečený a chráněný způsob, který splňuje zásady pro podnikání a dodržování předpisů vaší organizace.

Použití popisků citlivosti vám umožní určit, jak citlivá data jsou ve vaší organizaci. Konkrétní název projektu může být například vysoce důvěrný v rámci vaší organizace, zatímco stejný termín není důvěrný pro jiné organizace. 

### <a name="sensitivity-labels-in-azure-purview"></a>Popisky citlivostí ve službě Azure dosah

V dosah se klasifikace podobají značkám předmětu a používají se k označení a identifikaci dat určitého typu, který se nachází v rámci vašich datových nemovitostí během kontroly.

Dosah používá stejné klasifikace, označované také jako typy citlivých informací, jak Microsoft 365.  Popisky citlivosti MIP se vytvářejí v centru zabezpečení Microsoft 365 Security a dodržování předpisů (SCC). To vám umožní roztáhnout stávající popisky citlivosti napříč prostředky Azure dosah.

> [!NOTE]
> Klasifikace se shodují přímo, jako je číslo sociálního pojištění, které má klasifikaci **čísla sociálního pojištění**. 
>
> Na rozdíl od jsou popisky citlivosti aplikovány, když se jedna nebo více klasifikací a podmínek společně nacházejí. V tomto kontextu [podmínky](/microsoft-365/compliance/apply-sensitivity-label-automatically) odkazují na všechny parametry, které můžete definovat pro nestrukturovaná data, jako je například **blízkost jiné klasifikace**, a **spolehlivost%**. 

Popisky citlivosti ve službě Azure dosah se dají použít k automatickému použití popisků pro soubory a sloupce databáze.

Další informace najdete tady:

- [Informace o popisech citlivosti](/microsoft-365/compliance/sensitivity-labels) v dokumentaci k Microsoft 365
- [Co jsou pravidla pro popisky?](#what-are-autolabeling-rules)
- [Podporované datové typy pro popisky citlivosti ve službě Azure dosah](#supported-data-types-for-sensitivity-labels-in-azure-purview)

#### <a name="what-are-autolabeling-rules"></a>Co jsou pravidla pro popisky?

Vaše data se neustále zvětšují a mění. Sledování dat, která jsou v tuto chvíli neoznačená, a postup ručního použití popisků je nenáročný, ale také je nepotřebná starostí. 

Pravidla autolabeling jsou podmínky, které zadáte, a určete, kdy má být použit konkrétní popisek. Když jsou tyto podmínky splněné, popisek se automaticky přiřadí k datům a uchovává konzistentní popisky citlivosti pro vaše data ve velkém měřítku.

Při vytváření popisků nezapomeňte definovat pravidla automatických popisků pro [soubory](#define-autolabeling-rules-for-files) i [databázové sloupce](#define-autolabeling-rules-for-database-columns) , abyste mohli popisky použít automaticky při každé kontrole dat. 

Po kontrole dat v dosah můžete popisky zobrazit automaticky v katalogu dosah a v sestavách Insight.

#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Podporované datové typy pro popisky citlivosti ve službě Azure dosah

Popisky citlivosti se v Azure dosah podporují pro následující typy dat:

|Datový typ  |zdroje  |
|---------|---------|
|Automatické označování souborů     |     – Azure Blob Storage  </br>-Azure Data Lake Storage obecné 1 a 2. generace  |
|Automatické označování pro sloupce databáze     |  – SQL Server </br>– Azure SQL Database </br>-Azure SQL Database spravované instance   <br> – Azure synapse  <br>-Azure Cosmos DB   |
| | |

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Vytváření popisků citlivosti v Microsoft 365

Pokud ještě nemáte popisky citlivosti, budete je muset vytvořit a zpřístupnit pro Azure dosah. Existující popisky citlivosti je taky možné upravit tak, aby byly dostupné pro Azure dosah.

Další informace najdete tady:

- [Licenční požadavky](#licensing-requirements)
- [Rozšíření popisků citlivosti na Azure dosah](#extending-sensitivity-labels-to-azure-purview)
- [Vytváření nových popisků citlivosti nebo změna stávajících popisků](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Licenční požadavky

Popisky citlivosti MIP se vytvářejí a spravují v centru zabezpečení Microsoft 365 a dodržování předpisů. Pokud chcete vytvořit popisky citlivosti pro použití v Azure dosah, musíte mít licenci na aktivní Microsoft 365 E5.

Pokud ještě nemáte požadovanou licenci, můžete si zaregistrovat zkušební verzi [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Rozšíření popisků citlivosti na Azure dosah

Ve výchozím nastavení jsou popisky citlivosti MIP dostupné jenom pro prostředky v Microsoft 365, kde je můžete použít pro soubory a e-maily.

Pokud chcete použít popisky citlivosti MIP na prostředky Azure v Azure dosah, musíte explicitně vyjádřit souhlas s rozšířením popisků a vybrat konkrétní popisky, které chcete mít k dispozici v dosah.

Rozšířením popisků citlivosti MIP do Azure dosah mohou organizace zjišťovat, klasifikovat a získávat přehled o citlivosti v nejrůznějších zdrojích dat a minimalizovat riziko dodržování předpisů.

> [!NOTE]
> Vzhledem k tomu, že Microsoft 365 a Azure dosah jsou samostatné služby, existuje možnost, že budou nasazeny v různých oblastech. Názvy popisků a vlastní citlivé informace jsou považovány za zákaznická data a jsou ve výchozím nastavení uchovávány v rámci stejného GEOGRAFICKého umístění, aby chránily citlivost vašich dat a vyloučily GDPR zákony.
>
> Z tohoto důvodu nejsou štítky a vlastní typy citlivých informací sdíleny do Azure dosah ve výchozím nastavení a vyžadují souhlas s jejich používáním v Azure dosah.

**Chcete-li zvětšit popisky citlivosti na dosah:**

V Microsoft 365 přejděte na stránku **Information Protection** . V části **rozšiřování popisků na prostředky v Azure dosah** vyberte tlačítko **zapnout** a potom v potvrzovacím dialogovém okně, které se zobrazí, vyberte **Ano** .

Příklad:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Vyberte * * zapnout * * a rozšíříte tak popisky citlivosti na dosah." lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Jakmile rozšíříte popisky na prostředky v Azure dosah, můžete vybrat popisky, které mají být v dosah dostupné. Další informace najdete v tématech [vytváření nových popisků citlivosti nebo změna stávajících popisků](#creating-new-sensitivity-labels-or-modifying-existing-labels).
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Vytváření nových popisků citlivosti nebo změna stávajících popisků

1. Otevřete [Centrum zabezpečení Microsoft 365 a dodržování předpisů](https://protection.office.com/homepage). 

1. V části **řešení** vyberte **ochrana informací** a pak vyberte **vytvořit popisek**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Vytváření popisků citlivosti v centru zabezpečení Microsoft 365 a dodržování předpisů" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Pojmenujte popisek. Pak v části **definovat obor pro tento popisek** vyberte **soubory a e-maily** a **prostředky Azure dosah**.
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Vytvoření popisku v centru zabezpečení Microsoft 365 zabezpečení a dodržování předpisů" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Postupujte podle dalších pokynů v průvodci pro nastavení popisku. 

    Konkrétně definujte pravidla pro autoštítky pro soubory a databázové sloupce:

    - [Definování pravidel pro autoštítky pro soubory](#define-autolabeling-rules-for-files)
    - [Definování pravidel autolabeling pro sloupce databáze](#define-autolabeling-rules-for-database-columns)

    Další informace o možnostech průvodce najdete v dokumentaci k Microsoft 365, [které popisky citlivosti můžou dělat](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) .

1. Opakováním kroků uvedených výše vytvořte další popisky. 

    Chcete-li vytvořit dílčí popisek, vyberte nadřazený popisek > **...**  >  **Další akce**  >  **Přidat dílčí popisek**

1. Pokud chcete upravit existující popisky, přejděte na **Information Protection**  >  **popisky** a vyberte svůj popisek. 

    Pak vyberte **Upravit popisek** a znovu otevřete průvodce **úpravou popisku citlivosti** se všemi nastaveními, která jste definovali při vytváření popisku.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Úprava existujícího popisku citlivosti" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Až budete hotovi s vytvářením všech popisků, nezapomeňte zobrazit pořadí štítků a podle potřeby je změnit. 

    Pokud chcete změnit pořadí popisku, vyberte **...** **> další akce**  >  **nahoru** nebo dolů **.** 

    Další informace najdete v tématu [Priorita popisku (záležitosti pořadí)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) v dokumentaci Microsoft 365.

> [!IMPORTANT]
> Neodstraňujte popisek, pokud nerozumíte dopadu pro uživatele. 
>
> Další informace najdete v tématu [Odebrání a odstranění popisků](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) v dokumentaci k Microsoft 365.

Pokračujte tím, že zkontrolujete [data a použijete popisky automaticky](#scan-your-data-to-apply-labels-automatically)a potom:

- [Zobrazit popisky u prostředků](#view-labels-on-assets)
- [Zobrazení sestav Insight pro klasifikace a popisky citlivosti](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>Definování pravidel pro autoštítky pro soubory

Definujte pravidla autolabeling pro soubory v průvodci při vytváření nebo úpravě popisku. 

Na stránce **Automatické označování pro aplikace Office** povolte **Automatické označování pro aplikace Office** a pak definujte podmínky, ve kterých má být popisek automaticky použit pro vaše data.

Příklad:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Definování pravidel autolabeling pro soubory v centru zabezpečení Microsoft 365 Security a dodržování předpisů" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Další informace najdete v tématu [použití popisku citlivosti na data automaticky](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) v dokumentaci Microsoft 365. 

#### <a name="define-autolabeling-rules-for-database-columns"></a>Definování pravidel autolabeling pro sloupce databáze

V průvodci definujte pravidla autolabeling pro sloupce databáze při vytváření nebo úpravě popisku. 

V rámci možnosti **Azure dosah assets (Preview)** :

1. Vyberte posuvník **Automatické označování pro sloupce databáze** .

1. Výběrem možnosti **Vyhledat citlivé informace** zvolte typy citlivých informací, které chcete použít u popisku.

Příklad:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Definování pravidel autolabeling pro sloupce SQL v centru zabezpečení Microsoft 365 a dodržování předpisů" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Prověřování dat pro automatické použití popisků

Prohledejte data v Azure dosah a automaticky použijte štítky, které jste vytvořili, na základě pravidel automatického označování, které jste definovali. 

Další informace o tom, jak nastavit kontroly v různých assetech v Azure dosah, najdete v těchto tématech:

|Zdroj  |Reference  |
|---------|---------|
|**Azure Blob Storage**     |[Registrace a kontrola Blob Storage v Azure](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Registrovat a kontrolovat Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registrovat a kontrolovat Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Registrace a kontrola Azure SQL Database](register-scan-azure-sql-database.md) </br>[Registrace a kontrola Azure SQL Database spravované instance](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>Zobrazit popisky u prostředků

Po definování pravidel automatických popisků pro popisky v Microsoft 365 a prohledávání vašich dat v Azure dosah se štítky automaticky aplikují na vaše assety. 

**Zobrazení popisků použitých pro vaše prostředky v katalogu Azure dosah:**

V katalogu Azure dosah použijte možnosti filtrování **popisku** k zobrazení souborů s konkrétními popisky. Příklad: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Hledat prostředky podle popisku" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Příklad:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Zobrazení označení citlivosti v souboru v Azure Blob Storage" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Zobrazení sestav Insight pro klasifikace a popisky citlivosti

Projděte si přehledy o vašich klasifikovaných a označených datech ve službě Azure dosah pomocí sestav **klasifikace** a **citlivostní označování** .

> [!div class="nextstepaction"]
> [Přehledy klasifikace](./classification-insights.md)

> [!div class="nextstepaction"]
> [Přehledy označení citlivosti](sensitivity-insights.md)


