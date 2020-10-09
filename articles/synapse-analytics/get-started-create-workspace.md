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
ms.date: 10/07/2020
ms.openlocfilehash: d74c3e42317b954a510f3276db38c0dcdf5e2362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850375"
---
# <a name="creating-a-synapse-workspace"></a>Vytváření pracovního prostoru synapse

V tomto kurzu se dozvíte, jak vytvořit synapse pracovní prostor, fond SQL a fond Apache Spark. 

## <a name="prerequisites"></a>Předpoklady

Tento krok tohoto kurzu dokončíte tak, že budete mít přístup ke skupině prostředků, ke které jste přiřadili roli **vlastníka** . V této skupině prostředků vytvořte pracovní prostor synapse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Vytvoření pracovního prostoru synapse na webu Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby**vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor.
1. V oblasti **základy**zadejte preferované **předplatné**, **skupinu prostředků**, **oblast**a pak zvolte název pracovního prostoru. V tomto kurzu použijeme **MyWorkspace**.
1. K vytvoření pracovního prostoru potřebujete účet ADLSGEN2 a kontejner v tomto účtu. Nejjednodušší volbou je vytvořit nové. Pokud chcete znovu použít stávající, musíte provést nějakou další konfiguraci. 
    1. Pracovní prostor synapse bude tento kontejner používat jako výchozí umístění pro ukládání protokolů Spark a dat pro tabulky Spark.
1. MOŽNOST 1 Vytvoření nového účtu ADLSGEN2 
    1. Přejděte k **výběru Data Lake Storage Gen 2**. 
    1. Klikněte na **vytvořit nový** a pojmenujte ho **contosolake**.
    1. Klikněte na **systém souborů** a pojmenujte ho **Uživatelé**. Tím se vytvoří kontejner s názvem **Uživatelé** .
1. MOŽNOST 2 použijte existující účet ADLSGEN2. Projděte si pokyny k **přípravě účtu úložiště ADLSGEN2** na konci tohoto dokumentu.
1. Váš pracovní prostor Azure synapse použije tento účet úložiště jako primární účet úložiště a kontejner pro uložení dat pracovního prostoru. Pracovní prostor ukládá data v Apache Sparkch tabulkách. Ukládá protokoly aplikací Spark do složky s názvem **/synapse/workspacename**.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

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
    |**Velikost uzlu**| **Malý**|
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

## <a name="preparing-a-adlsgen2-storage-account"></a>Příprava účtu úložiště ADLSGEN2

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>PŘED vytvořením pracovního prostoru proveďte následující kroky.

1. Otevřete web [Azure Portal](https://portal.azure.com).
1. Přejít na existující účet úložiště
1. V levém podokně vyberte **řízení přístupu (IAM)** . 
1. Přiřaďte následující role nebo se ujistěte, že jsou již přiřazeny:
    * Přiřaďte roli **vlastníka** sami sobě.
    * Přiřaďte se k roli **vlastníka dat objektu BLOB úložiště** .
1. V levém podokně vyberte **kontejnery** a vytvořte kontejner.
1. Kontejneru můžete dát název. V tomto dokumentu používáme jméno  **uživatele**.
1. Přijměte výchozí nastavení **úroveň veřejného přístupu**a pak vyberte **vytvořit**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Po vytvoření pracovního prostoru proveďte následující kroky.

Nakonfigurujte přístup k účtu úložiště z vašeho pracovního prostoru. Spravované identity pro váš pracovní prostor Azure synapse už můžou mít přístup k účtu úložiště. Pomocí těchto kroků se ujistěte, že:

1. Otevřete [Azure Portal](https://portal.azure.com) a primární účet úložiště, který jste zvolili pro váš pracovní prostor.
1. V levém podokně vyberte **řízení přístupu (IAM)** .
1. Přiřaďte následující role nebo se ujistěte, že jsou již přiřazeny. Pro identitu pracovního prostoru používáme stejný název a název pracovního prostoru.
    * Pro roli **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště přiřaďte **MyWorkspace** jako identitu pracovního prostoru.
    * Přiřaďte **MyWorkspace** jako název pracovního prostoru.
1. Vyberte **Uložit**.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí fondu SQL](get-started-analyze-sql-pool.md)
