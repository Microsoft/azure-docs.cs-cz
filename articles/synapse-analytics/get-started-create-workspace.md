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
ms.openlocfilehash: c15750a7a63293994ff7c6b68cb85a5066acee90
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981132"
---
# <a name="creating-a-synapse-workspace"></a>Vytváření pracovního prostoru synapse

V tomto kurzu se dozvíte, jak vytvořit pracovní prostor synapse, vyhrazený fond SQL a fond Apache Spark bez serveru. 

## <a name="prerequisites"></a>Požadavky

K dokončení kroků tohoto kurzu potřebujete mít přístup ke skupině prostředků, ke které jste přiřadili roli **vlastníka** . V této skupině prostředků vytvořte pracovní prostor synapse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Vytvořte pracovní prostor synapse v Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby** vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor.
1. V oblasti **základy** zadejte preferované **předplatné**, **skupinu prostředků**, **oblast** a pak zvolte název pracovního prostoru. V tomto kurzu použijeme **MyWorkspace**.
1. Přejděte k **výběru Data Lake Storage Gen 2**. 
1. Klikněte na **vytvořit nový** a pojmenujte ho **contosolake**.
1. Klikněte na **systém souborů** a pojmenujte ho **Uživatelé**. Tím se vytvoří kontejner s názvem **Uživatelé** .
1. Pracovní prostor bude používat tento účet úložiště jako primární účet úložiště pro tabulky Spark a protokoly aplikací Spark.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://portal.azure.com). V horní části **přehledu** vyberte **Spustit synapse Studio**.
* Přejít na adresu `https://web.azuresynapse.net` a přihlaste se do svého pracovního prostoru.

## <a name="create-a-dedicated-sql-pool"></a>Vytvoření vyhrazeného fondu SQL

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy SQL**.
1. Vybrat **Nový**
1. Pro **název fondu SQL** vyberte **SQLPOOL1**
1. Pro **úroveň výkonu** vyberte **DW100C**
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Vyhrazený fond SQL bude připravený během několika minut. Váš vyhrazený fond SQL je přidružený k vyhrazené databázi fondu SQL, která se také označuje jako **SQLPOOL1**.

Vyhrazený fond SQL spotřebovává Fakturovatelné prostředky, pokud je aktivní. Fond můžete později pozastavit a snížit tak náklady.

## <a name="create-a-serverless-apache-spark-pool"></a>Vytvoření fondu Apache Spark bez serveru

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy Apache Spark**.
1. Vybrat **Nový** 
1. Pro **Apache Spark název fondu** zadejte **Spark1**.
1. Jako **Velikost uzlu** zadejte **malá**.
1. Pro **počet uzlů** nastavte Minimum na 3 a maximum na 3.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš fond Apache Spark bude připravený během několika sekund.

Fond Spark oznamuje, že Azure synapse, kolik prostředků Spark se má použít. Platíte jenom za prostředky, které používáte. Při aktivním zastavení fondu se prostředky automaticky vyprší a recykluje se.

## <a name="the-built-in-serverless-sql-pool"></a>Integrovaný fond SQL bez serveru

Každý pracovní prostor obsahuje předem sestavený fond s názvem **integrovaný**. Tento fond nejde odstranit. Fond SQL bez serveru umožňuje pracovat s SQL bez nutnosti vytvářet nebo zabývat se správou fondu SQL bez serveru v Azure synapse. Na rozdíl od vyhrazených fondů SQL je fakturace pro fond SQL bez serveru založená na množství dat naskenovaných pro spuštění dotazu, nikoli na počtu prostředků použitých ke spuštění dotazu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí vyhrazeného fondu SQL](get-started-analyze-sql-pool.md)
