---
title: 'Rychlý Start: Vytvoření fondu Apache Spark bez serveru (Preview) pomocí synapse studia'
description: Pomocí kroků v této příručce vytvořte Apache Spark fond bez serveru s využitím synapse studia.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 313ad0c620fe06158e96c208ae265702134b58d1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324182"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-preview-using-synapse-studio"></a>Rychlý Start: Vytvoření fondu Apache Spark bez serveru (Preview) pomocí synapse studia

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat, analyzovat a obsluhovat vaše data. Fond Apache Spark nabízí Open Source výpočetní funkce pro velké objemy dat. Po vytvoření fondu Apache Spark ve vašem pracovním prostoru synapse se data dají načíst, namodelovat, zpracovávat a zpracovávat, abyste získali přehledy.  

Tento rychlý Start popisuje kroky pro vytvoření fondu Apache Spark v pracovním prostoru synapse pomocí nástroje synapse Studio.

> [!IMPORTANT]
> Faktura za instance Spark se účtuje poměrnou rychlostí za minutu, ať už je používáte, nebo ne. Ujistěte se, že jste instanci Spark po dokončení používání vypnuli, nebo nastavte krátký časový limit. Další informace najdete v části **Vyčištění prostředků** tohoto článku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Přejděte do pracovního prostoru synapse.

1. Přejděte do pracovního prostoru synapse, kde se vytvoří fond Apache Spark zadáním názvu služby (nebo názvu prostředku přímo) do panelu hledání.

    ![Azure Portal panel hledání s pracovními prostory synapse zadanými v.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. V seznamu pracovních prostorů zadejte název (nebo část názvu) pracovního prostoru, který chcete otevřít. V tomto příkladu použijeme pracovní prostor s názvem **contosoanalytics**.

    ![Seznam pracovních prostorů synapse filtrovaných k zobrazení těch, které obsahují název contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Spuštění funkce Synapse Studio 

V přehledu pracovního prostoru vyberte **adresu URL webu pracovního prostoru** a otevřete synapse Studio.

![Azure Portal synapse pracovní prostor – Přehled s zvýrazněnou možností spustit synapse Studio.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Vytvoření fondu Apache Spark v synapse studiu

1. Na domovské stránce synapse studia přejděte na **Centrum pro správu** v levém navigačním panelu tak, že vyberete ikonu **Spravovat** .

    ![Domovská stránka synapse studia s centrem správy – zvýrazněný oddíl](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. V centru pro správu přejděte do části **fondy Apache Spark** , abyste viděli aktuální seznam fondů Apache Spark, které jsou k dispozici v pracovním prostoru.
    
    ![Synapse centra pro správu studia s vybraným navigačním fondy Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Vyberte **+ Nový** a zobrazí se Průvodce vytvořením nového fondu Apache Spark. 

1. Na kartě **základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název Apache Spark fondu** | contosospark | Jedná se o název, který bude mít fond Apache Spark. |
    | **Velikost uzlu** | Malý (4 vCPU/32 GB) | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |
    | **Automatické škálování** | Zakázáno | V tomto rychlém startu nepotřebujeme automatické škálování. |
    | **Počet uzlů** | 8 | V tomto rychlém startu můžete omezit náklady pomocí malé velikosti.|
       
    ![Základy pro synapse Studio New Apache Spark fond](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které mohou fondy Apache Spark použít. Názvy musí obsahovat jenom písmena nebo číslice, musí být delší než 15 znaků, musí začínat písmenem, nesmí obsahovat vyhrazená slova a být v pracovním prostoru jedinečné.

1. Na další kartě (další nastavení) ponechte všechna nastavení výchozí.

1. Nepřidáme teď žádné značky, takže vyberte **zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** se ujistěte, že podrobnosti vypadají správně na základě dříve zadaného obsahu, a pak stiskněte **vytvořit**. 

    ![Vytvořit nový fond Apache Spark synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Apache Spark fond spustí proces zřizování.

1. Po dokončení zřizování se v seznamu objeví nový fond Apache Spark.
    
    ![Synapse Studio nový seznam fondu Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Vyčištění prostředků Apache Spark fondu pomocí aplikace synapse Studio

Podle následujících kroků odstraňte fond Apache Spark z pracovního prostoru pomocí nástroje synapse Studio.
> [!WARNING]
> Odstraněním fondu Spark dojde k odebrání analytického modulu z pracovního prostoru. Již nebude možné se připojit ke fondu a všechny dotazy, kanály a poznámkové bloky, které tento fond Spark používají, již nebudou fungovat.

Pokud chcete fond Apache Spark odstranit, postupujte takto:

1. Přejděte na fondy Apache Spark v centru pro správu v nástroji synapse Studio.
1. Vyberte tři tečky vedle fondu Apache, který se má odstranit (v tomto případě **contosospark** ), aby se zobrazily příkazy pro fond Apache Spark.

    ![Výpis fondů Apache Spark s vybraným nedávno vytvořeným fondem.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. Stiskněte klávesu **Delete**.
1. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
1. Po úspěšném dokončení procesu již nebude fond Apache Spark uveden v prostředcích pracovního prostoru. 

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu rychlý Start: Vytvoření fondu Apache Spark v synapse studiu pomocí nástrojů pro web](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark pomocí Azure Portal](quickstart-create-apache-spark-pool-portal.md).
