---
title: Rychlý Start – vytvoření synapse fondu SQL
description: Pomocí kroků v této příručce vytvořte nový synapse fond SQL pro pracovní prostor Azure synapse Analytics synapse.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a50f203b8057dd4ea51eff1cf09fc97fb598cb3e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096431"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Rychlý Start: vytvoření synapse fondu SQL

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat, analyzovat a obsluhovat vaše data. Fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření fondu SQL ve vašem pracovním prostoru synapse se dají data načíst, namodelovat, zpracovávat a obsluhovat, aby získala přehledy.

V tomto rychlém startu se naučíte vytvořit fond SQL v pracovním prostoru synapse pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadované součásti

[Pracovní prostor synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Vytvořit nový fond SQL

1. V pracovním prostoru synapse, kde chcete vytvořit fond SQL, klikněte na horním panelu na příkaz **Nový fond SQL** .
![Přehled pracovního prostoru synapse s červeným polem kolem příkazu pro vytvoření nového fondu SQL.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Na kartě **základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název fondu SQL** | Libovolné platné jméno | Název fondu SQL. |
    | **Úroveň výkonu** | DW100c | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |
    ||||
  
    ![Fond SQL vytvořit kartu základy na základě toku](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které můžou fondy SQL použít. Názvy nesmí obsahovat speciální znaky, musí být delší než 15 znaků, nesmí obsahovat vyhrazená slova a v pracovním prostoru být jedinečné.

3. Klikněte na **Další: Další nastavení**.
4. Pokud chcete zřídit fond SQL bez dat, vyberte **none** . Ponechte vybranou výchozí kolaci.
![Postup vytvoření fondu SQL – karta Další nastavení](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Klikněte na **Zkontrolovat a vytvořit**.
6. Ujistěte se, že podrobnosti vypadají správně na základě dříve zadaného obsahu, a klikněte na **vytvořit**.
![Postup vytvoření fondu SQL – karta Nastavení revize](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. V tomto okamžiku se spustí tok zřizování prostředků.
 ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí nový záznam pro nově vytvořený fond SQL.
 ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí následujících kroků odstraňte z pracovního prostoru fond SQL.
> [!WARNING]
> Odstraněním fondu SQL dojde k odebrání analytického modulu i dat uložených v databázi odstraněného fondu SQL z pracovního prostoru. Již nebude možné se připojit ke fondu SQL a všechny dotazy, kanály a poznámkové bloky, které čtou nebo zapisují do tohoto fondu SQL, již nebudou fungovat.

Pokud chcete odstranit fond SQL, proveďte následující kroky:

1. V okně pracovního prostoru přejděte do okna fondy SQL.
1. Vyberte fond SQL, který se má odstranit (v tomto případě **contosoedw**).
1. Vyberte ji a stiskněte klávesu **Delete**.
1. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
 ![Přehled fondu SQL – zvýrazňování potvrzení odstranění.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Po úspěšném dokončení procesu již nebude fond SQL uveden v prostředcích pracovního prostoru.

Po vytvoření fondu SQL bude k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení ze jezera atd.

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu rychlý Start: Vytvoření fondu Apache Spark v synapse studiu pomocí nástrojů pro web](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark pomocí Azure Portal](quickstart-create-apache-spark-pool.md).
