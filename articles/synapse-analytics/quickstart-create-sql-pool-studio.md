---
title: 'Rychlý Start: vytvoření vyhrazeného fondu SQL pomocí synapse studia'
description: Podle kroků v této příručce Vytvořte vyhrazený fond SQL pomocí synapse studia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3644891f12a6475ec9cfec51f572df4742481e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541840"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Rychlý Start: vytvoření vyhrazeného fondu SQL pomocí synapse studia

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat a analyzovat vaše data. Vyhrazený fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření vyhrazeného fondu SQL ve vašem pracovním prostoru synapse se dají data načíst, namodelovat, zpracovávat a doručovat za účelem rychlejšího analytického přehledu.

Tento rychlý Start popisuje kroky pro vytvoření vyhrazeného fondu SQL v pracovním prostoru synapse pomocí synapse studia.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Přejděte do pracovního prostoru synapse.

1. Přejděte do pracovního prostoru synapse, kde se vytvoří vyhrazený fond SQL zadáním názvu služby (nebo názvu prostředku přímo) do panelu hledání.

    ![Azure Portal panel hledání s pracovními prostory synapse zadanými v.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. V seznamu pracovních prostorů zadejte název (nebo část názvu) pracovního prostoru, který chcete otevřít. V tomto příkladu použijeme pracovní prostor s názvem **contosoanalytics**.

    ![Seznam pracovních prostorů synapse filtrovaných k zobrazení těch, které obsahují název contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Spuštění funkce Synapse Studio

1. V přehledu pracovního prostoru vyberte **adresu URL webu pracovního prostoru** a spusťte synapse Studio.

    ![Přehled pracovního prostoru Azure Portal synapse se zvýrazněnou adresou URL webu v pracovním prostoru.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Vytvoření vyhrazeného fondu SQL v synapse studiu

1. Na domovské stránce synapse studia přejděte na **Centrum pro správu** v levém navigačním panelu tak, že vyberete ikonu **Spravovat** .

    ![Domovská stránka synapse studia s centrem správy – zvýrazněný oddíl](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. V centru pro správu přejděte do části **fondy SQL** , abyste viděli aktuální seznam fondů SQL, které jsou k dispozici v pracovním prostoru.

    ![Vybraná navigace v centru pro správu synapse studia s vybranými fondy SQL](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Vyberte **+ Nový** příkaz a zobrazí se Průvodce vytvořením nového fondu SQL. 

    ![Synapse Studio Management hub seznam fondů SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Na kartě **základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název fondu SQL** | contosoedw | Jedná se o název, který bude mít vyhrazený fond SQL. |
    | **Úroveň výkonu** | DW100c | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |

    ![Fondy SQL vytvářejí kartu základy na základě toku.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které mohou používat vyhrazené fondy SQL. Názvy nesmí obsahovat speciální znaky, musí být delší než 15 znaků, nesmí obsahovat vyhrazená slova a v pracovním prostoru být jedinečné.

4. Na další kartě **Další nastavení** vyberte možnost **žádná** a zajistěte tak fond SQL bez dat. Ponechte výchozí kolaci vybranou.

    Pokud chcete obnovit vyhrazený fond SQL z bodu obnovení, vyberte **bod obnovení**. Další informace o tom, jak provést obnovení, najdete v tématu [Postup: obnovení existujícího vyhrazeného fondu SQL.](backuprestore/restore-sql-pool.md)

    ![Postup vytvoření fondu SQL – karta Další nastavení](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Nepřidáme teď žádné značky, takže příště vyberte **zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** se ujistěte, že podrobnosti vypadají správně na základě dříve zadaného obsahu, a pak stiskněte **vytvořit**. 

    ![Postup vytvoření fondu SQL – karta Nastavení revize](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. V tomto okamžiku se spustí tok zřizování prostředků.

1. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí nový záznam pro nově vytvořený fond SQL.

    ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Po vytvoření vyhrazeného fondu SQL bude k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení ze jezera atd.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Vyčištění vyhrazeného fondu SQL pomocí synapse studia    

Použijte následující postup, chcete-li odstranit vyhrazený fond SQL z pracovního prostoru pomocí nástroje synapse Studio.
> [!WARNING]
> Odstraněním vyhrazeného fondu SQL dojde k odebrání analytického modulu z pracovního prostoru. Už se nebude možné připojit ke fondu a všechny dotazy, kanály a skripty, které používají tento vyhrazený fond SQL, už nebudou fungovat.

Pokud chcete odstranit vyhrazený fond SQL, udělejte toto:

1. Přejděte na fondy SQL v centru pro správu v synapse studiu.
1. Vyberte tři tečky ve vyhrazeném fondu SQL, které se mají odstranit (v tomto případě **contosoedw**), aby se zobrazily příkazy pro vyhrazený fond SQL:

    ![Seznam fondů SQL s vybraným nedávno vytvořeným fondem.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Stiskněte klávesu **Delete**.
1. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
1. Po úspěšném dokončení procesu nebude vyhrazený fond SQL nadále uveden v prostředcích pracovního prostoru.

## <a name="next-steps"></a>Další kroky
 
- Další informace najdete v tématu [rychlý Start: vytvoření poznámkového bloku Apache Spark](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: vytvoření vyhrazeného fondu SQL pomocí Azure Portal](quickstart-create-sql-pool-portal.md).
