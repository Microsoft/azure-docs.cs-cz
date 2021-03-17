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
ms.date: 12/31/2020
ms.openlocfilehash: 94d069a283249f2880743ba911c32bf3821d28c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171479"
---
# <a name="creating-a-synapse-workspace"></a>Vytváření pracovního prostoru synapse

V tomto kurzu se dozvíte, jak vytvořit pracovní prostor synapse, vyhrazený fond SQL a fond Apache Spark bez serveru. 

## <a name="prerequisites"></a>Požadavky

K dokončení kroků tohoto kurzu potřebujete mít přístup ke skupině prostředků, ke které jste přiřadili roli **vlastníka** . V této skupině prostředků vytvořte pracovní prostor synapse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Vytvořte pracovní prostor synapse v Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com)a na panelu hledání zadejte **synapse** , aniž byste museli vstoupit.
1. Ve výsledcích hledání v části **služby** vyberte **Azure synapse Analytics**.
1. Vyberte **Přidat** a vytvořte pracovní prostor.
1. Karta **základy** v části **Project Details (podrobnosti projektu**) vyplňte následující pole:
      1. **Předplatné** – vyberte nějaké předplatné.
      2. **Skupina prostředků** – použijte jakoukoli skupinu prostředků.
      3. **Skupina prostředků** – ponechte toto pole prázdné.
1. Na kartě **základy** v části **Podrobnosti o pracovním prostoru** vyplňte následující pole:
      1. **Název pracovního prostoru** – vyberte libovolný globálně jedinečný název. V tomto kurzu použijeme **MyWorkspace**.
      1. **Oblast** – vyberte libovolnou oblast.
      1. **Vybrat Data Lake Storage Gen 2**
        1. Klikněte na tlačítko **z předplatného**.
        1. Podle **názvu účtu** klikněte na **vytvořit nový** a pojmenujte nový účet úložiště **contosolake** nebo podobný, protože tento název musí být jedinečný.
        1. Podle **názvu systému souborů** klikněte na **vytvořit nový** a pojmenujte **uživatele** IT. Tím se vytvoří kontejner úložiště s názvem **Uživatelé**. Pracovní prostor bude používat tento účet úložiště jako primární účet úložiště pro tabulky Spark a protokoly aplikací Spark.
        1. Zaškrtněte políčko přiřadit roli Přispěvatel dat objektů BLOB úložiště v poli Data Lake Storage Gen2 účet. 
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

> [!NOTE]
> Pokud chcete povolit funkce pracovního prostoru z existujícího vyhrazeného fondu SQL (dřív SQL DW), přečtěte si, [Jak povolit pracovní prostor pro vyhrazený fond SQL (dřív SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse v [Azure Portal](https://portal.azure.com)v části **Přehled** v pracovním prostoru synapse vyberte **otevřít** v dialogovém okně Otevřít synapse Studio.
* Přejít na adresu `https://web.azuresynapse.net` a přihlaste se do svého pracovního prostoru.


## <a name="the-built-in-serverless-sql-pool"></a>Integrovaný fond SQL bez serveru

Každý pracovní prostor obsahuje předem sestavený fond SQL bez serveru s názvem **integrovaný**. Tento fond nejde odstranit. Neserverové fondy SQL umožňují používat SQL bez nutnosti rezervovat kapacitu pomocí vyhrazených fondů SQL. Na rozdíl od vyhrazených fondů SQL je fakturace pro fond SQL bez serveru založená na množství dat naskenovaných pro spuštění dotazu, nikoli na počtu kapacit přidělených do fondu.


## <a name="create-a-dedicated-sql-pool"></a>Vytvoření vyhrazeného fondu SQL

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy SQL**.
1. Vybrat **Nový**
1. Pro **název fondu SQL** vyberte **SQLPOOL1**
1. Pro **úroveň výkonu** vyberte **DW100C**
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Vyhrazený fond SQL bude připravený během několika minut. Váš vyhrazený fond SQL je přidružený k vyhrazené databázi fondu SQL, která se také označuje jako **SQLPOOL1**.

Vyhrazený fond SQL spotřebovává Fakturovatelné prostředky, pokud je aktivní. Fond můžete později pozastavit a snížit tak náklady.

> [!NOTE] 
> Při vytváření nového vyhrazeného fondu SQL (dříve SQL DW) ve vašem pracovním prostoru se otevře vyhrazená stránka pro zřizování fondu SQL. Zřizování bude provedeno na logickém SQL serveru.


## <a name="create-a-serverless-apache-spark-pool"></a>Vytvoření fondu Apache Spark bez serveru

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy Apache Spark**.
1. Vybrat **Nový** 
1. Pro **Apache Spark název fondu** zadejte **Spark1**.
1. Jako **Velikost uzlu** zadejte **malá**.
1. Pro **počet uzlů** nastavte Minimum na 3 a maximum na 3.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš fond Apache Spark bude připravený během několika sekund.

Fond Spark oznamuje, že Azure synapse, kolik prostředků Spark se má použít. Platíte jenom za prostředky, které používáte. Při aktivním zastavení fondu se prostředky automaticky vyprší a recykluje se.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí vyhrazeného fondu SQL](get-started-analyze-sql-pool.md)
