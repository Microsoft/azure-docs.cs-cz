---
title: 'Rychlý Start: Začínáme – vytvoření pracovního prostoru synapse'
description: V tomto kurzu se dozvíte, jak vytvořit pracovní prostor synapse, vyhrazený fond SQL a fond Apache Spark bez serveru.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 303ed66b7d268a5bab33d0d857399e907764d152
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378852"
---
# <a name="creating-a-synapse-workspace"></a>Vytváření pracovního prostoru synapse

V tomto kurzu se dozvíte, jak vytvořit pracovní prostor synapse, vyhrazený fond SQL a fond Apache Spark bez serveru. 

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků tohoto kurzu potřebujete mít přístup ke skupině prostředků, ke které jste přiřadili roli **vlastníka** . V této skupině prostředků vytvořte pracovní prostor synapse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Vytvořte pracovní prostor synapse v Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby** vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor.
1. V oblasti **základy** zadejte preferované **předplatné** , **skupinu prostředků** , **oblast** a pak zvolte název pracovního prostoru. V tomto kurzu použijeme **MyWorkspace**.
1. K vytvoření pracovního prostoru potřebujete účet ADLSGEN2 a kontejner v tomto účtu. Pracovní prostor synapse bude tento kontejner používat jako výchozí umístění pro ukládání protokolů Spark a dat pro tabulky Spark.
    1. Přejděte k **výběru Data Lake Storage Gen 2**. 
    1. Klikněte na **vytvořit nový** a pojmenujte ho **contosolake**.
    1. Klikněte na **systém souborů** a pojmenujte ho **Uživatelé**. Tím se vytvoří kontejner s názvem **Uživatelé** .
1. Váš pracovní prostor Azure synapse použije tento účet úložiště jako primární účet úložiště a kontejner pro uložení dat pracovního prostoru. Pracovní prostor ukládá data v Apache Sparkch tabulkách. Ukládá protokoly aplikací Spark do složky s názvem **/synapse/workspacename**.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://portal.azure.com). V horní části **přehledu** vyberte **Spustit synapse Studio**.
* Přejít na adresu `https://web.azuresynapse.net` a přihlaste se do svého pracovního prostoru.

## <a name="create-a-dedicated-sql-pool"></a>Vytvoření vyhrazeného fondu SQL

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy SQL**.
1. Vyberte **nové** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název fondu SQL**| **SQLDB1**|
    |**Úroveň výkonu**|**DW100C**|
    |||

1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Vyhrazený fond SQL bude připravený během několika minut. Váš vyhrazený fond SQL je přidružený k vyhrazené databázi fondu SQL, která se také označuje jako **SQLDB1**.

Vyhrazený fond SQL spotřebovává Fakturovatelné prostředky, pokud je aktivní. Fond můžete později pozastavit a snížit tak náklady.

## <a name="create-a-serverless-apache-spark-pool"></a>Vytvoření fondu Apache Spark bez serveru

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy Apache Spark**.
1. Vyberte **nové** a zadejte tato nastavení:

    |Nastavení | Navrhovaná hodnota | 
    |---|---|---|
    |**Název Apache Spark fondu**|**Spark1**
    |**Velikost uzlu**| **Malý**|
    |**Počet uzlů**| Nastavte minimum na 3 a maximum na 3.|

1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš fond Apache Spark bude připravený během několika sekund.

Při provádění aktivity Sparku v Azure synapse určíte fond Spark, který se má použít. Tento fond oznamuje službě Azure synapse, kolik prostředků Spark se má použít. Platíte jenom za prostředky, které používáte. Při aktivním zastavení fondu se prostředky automaticky vyprší a recykluje se.

> [!NOTE]
> Databáze Spark jsou nezávisle vytvořené z fondů Spark. Pracovní prostor má vždy databázi Spark s názvem **Default**. Můžete vytvořit další databáze Spark.

## <a name="the-serverless-sql-pool"></a>Fond SQL bez serveru

Každý pracovní prostor obsahuje předem sestavený fond s názvem **integrovaný**. Tento fond nejde odstranit. Fond SQL bez serveru umožňuje pracovat s SQL bez nutnosti vytvářet nebo zabývat se správou fondu SQL bez serveru v Azure synapse. Na rozdíl od vyhrazených fondů SQL je fakturace pro fond SQL bez serveru založená na množství dat naskenovaných pro spuštění dotazu, nikoli na počtu prostředků použitých ke spuštění dotazu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí vyhrazeného fondu SQL](get-started-analyze-sql-pool.md)
