---
title: 'Kurz: Začínáme vytvořit pracovní prostor synapse'
description: V tomto kurzu se dozvíte, jak vytvořit synapse pracovní prostor, fond SQL a fond Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b4d48dcc8f09ae8e2ec3bb198f8864de1c945682
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101502"
---
# <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru synapse

V tomto kurzu se dozvíte, jak vytvořit synapse pracovní prostor, fond SQL a fond Apache Spark. 

## <a name="prepare-a-storage-account"></a>Příprava účtu úložiště

1. Otevřete web [Azure Portal](https://portal.azure.com).
1. Vytvořte nový účet úložiště s následujícími nastaveními:

    |Karta|Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|---|
    |Základy|**Název účtu úložiště**| Vyberte libovolný název.| V tomto dokumentu použijeme název **contosolake**.|
    |Základy|**Druh účtu**| **StorageV2** ||
    |Základy|**Umístění**|Vyberte libovolné umístění.| Doporučujeme, aby byl váš pracovní prostor Azure synapse Analytics a účet Azure Data Lake Storage Gen2 ve stejné oblasti.|
    |Upřesnit|**Data Lake Storage Gen2**|**Povoleno**| Azure synapse funguje jenom s účty úložiště, které mají povolené toto nastavení.|
    |||||

1. Po vytvoření účtu úložiště v levém podokně vyberte **řízení přístupu (IAM)** . Pak přiřaďte následující role nebo ověřte, zda jsou již přiřazeny:
    * Přiřaďte roli **vlastníka** sami sobě.
    * Přiřaďte se k roli **vlastníka dat objektu BLOB úložiště** .
1. V levém podokně vyberte **kontejnery** a vytvořte kontejner.
1. Kontejneru můžete dát libovolný název. V tomto dokumentu budeme pojmenovat **uživatele**kontejneru.
1. Přijměte výchozí nastavení **úroveň veřejného přístupu**a pak vyberte **vytvořit**.

V následujícím kroku nakonfigurujete pracovní prostor Azure synapse tak, aby používal tento účet úložiště jako primární účet úložiště a kontejner pro ukládání dat pracovního prostoru. Pracovní prostor ukládá data v Apache Sparkch tabulkách. Ukládá protokoly aplikací Spark do složky s názvem **/synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru synapse

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby**vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor pomocí těchto nastavení:

    |Karta|Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|---|
    |Základy|**Název pracovního prostoru**|Můžete si je pojmenovat.| V tomto dokumentu budeme používat **MyWorkspace**.|
    |Základy|**Oblast**|Porovnává s oblastí účtu úložiště.|

1. V části **vybrat data Lake Storage Gen 2**vyberte účet a kontejner, který jste vytvořili dříve.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

## <a name="verify-access-to-the-storage-account"></a>Ověřte přístup k účtu úložiště.

Spravované identity pro váš pracovní prostor Azure synapse už můžou mít přístup k účtu úložiště. Pomocí těchto kroků se ujistěte, že:

1. Otevřete [Azure Portal](https://portal.azure.com) a primární účet úložiště, který jste zvolili pro váš pracovní prostor.
1. V levém podokně vyberte **řízení přístupu (IAM)** .
1. Přiřaďte následující role nebo se ujistěte, že jsou již přiřazeny. Pro identitu pracovního prostoru používáme stejný název a název pracovního prostoru.
    * Pro roli **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště přiřaďte **MyWorkspace** jako identitu pracovního prostoru.
    * Přiřaďte **MyWorkspace** jako název pracovního prostoru.

1. Vyberte **Uložit**.

## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://portal.azure.com). V horní části **přehledu** vyberte **Spustit synapse Studio**.
* Přejít na adresu `https://web.azuresynapse.net` a přihlaste se do svého pracovního prostoru.

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy SQL**.
1. Vyberte **nové** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název fondu SQL**| **SQLDB1**|
    |**Úroveň výkonu**|**DW100C**|
    |||

1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš fond SQL bude připravený během několika minut. Váš fond SQL je přidružen k databázi fondu SQL, která se také označuje jako **SQLDB1**.

Fond SQL spotřebovává Fakturovatelné prostředky, pokud je aktivní. Fond můžete později pozastavit a snížit tak náklady.

## <a name="create-an-apache-spark-pool"></a>Vytvoření fondu Apache Spark

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy Apache Spark**.
1. Vyberte **nové** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název Apache Spark fondu**|**Spark1**
    |**Velikost uzlu**| **Malá**|
    |**Počet uzlů**| Nastavte minimum na 3 a maximum na 3.|

1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš fond Apache Spark bude připravený během několika sekund.

> [!NOTE]
> Bez ohledu na název Apache Spark fond nevypadá jako fond SQL. Jedná se o jenom některá základní metadata, která používáte k tomu, abyste synapse pracovnímu prostoru Azure, jak pracovat s Sparkem.

Vzhledem k tomu, že se jedná o metadata, nelze spustit ani zastavit fondy Spark.

Při provádění aktivity Sparku v Azure synapse určíte fond Spark, který se má použít. Tento fond oznamuje službě Azure synapse, kolik prostředků Spark se má použít. Platíte jenom za prostředky, které používáte. Při aktivním zastavení fondu se prostředky automaticky vyprší a recykluje se.

> [!NOTE]
> Databáze Spark jsou nezávisle vytvořené z fondů Spark. Pracovní prostor má vždy databázi Spark s názvem **Default**. Můžete vytvořit další databáze Spark.

## <a name="the-sql-on-demand-pool"></a>Fond na vyžádání SQL

Každý pracovní prostor obsahuje předem sestavený fond s názvem **SQL na vyžádání**. Tento fond nejde odstranit. Fond na vyžádání SQL umožňuje pracovat s SQL bez nutnosti vytvářet nebo považovat za správu fondu SQL ve službě Azure synapse.

Na rozdíl od jiných druhů fondů je fakturace za SQL na vyžádání založená na množství dat naskenovaných pro spuštění dotazu, nikoli na počtu prostředků použitých ke spuštění dotazu.

* SQL na vyžádání má vlastní databáze SQL na vyžádání, které existují nezávisle na jakémkoli fondu SQL na vyžádání.
* Pracovní prostor má vždy přesně jeden fond SQL na vyžádání s názvem **SQL na vyžádání**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí fondu SQL](get-started-analyze-sql-pool.md)
