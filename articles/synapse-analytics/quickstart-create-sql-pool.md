---
title: Úvodní příručka – vytvoření fondu SQL synapse
description: Vytvořte nový fond Synapse SQL pro pracovní prostor Azure Synapse Analytics Synapse podle kroků v této příručce.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b3cd4ed280529efbde32fb89e6bac20640940fff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423905"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Úvodní příručka: Vytvoření fondu SYNAPSE SQL

Azure Synapse Analytics nabízí různé analytické moduly, které vám pomohou ingestovat, transformovat, modelovat, analyzovat a obsluhovat vaše data. Fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření fondu SQL v synapse pracovního prostoru, data mohou být načteny, modelovány, zpracovány a slouží k získání přehledů.

V tomto rychlém startu se naučíte vytvořit fond SQL v synapse pracovního prostoru pomocí portálu Azure.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

[Synapse pracovní prostor](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlášení k [portálu Azure](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Vytvořit nový fond SQL

1. V pracovním prostoru Synapse, kde chcete vytvořit fond SQL, klikněte na příkaz **Nový fond SQL** v horním panelu.
![Přehled synapse pracovního prostoru s červeným rámečkem kolem příkazu k vytvoření nového fondu SQL.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Na kartě **Základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název fondu SQL** | Libovolné platné jméno | Název fondu SQL. |
    | **Úroveň výkonu** | DW100c | Nastavte na nejmenší velikost, abyste snížili náklady pro tento rychlý start |
    ||||
  
    ![Sql fond vytvořit tok - základy kartu.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které mohou fondy SQL používat. Názvy nesmí obsahovat speciální znaky, musí mít 15 nebo méně znaků, nesmí obsahovat vyhrazená slova a musí být v pracovním prostoru jedinečné.

3. Klikněte na **Další: Další nastavení**.
4. Chcete-li zřídit fond SQL bez dat, vyberte **možnost Žádný.** Ponechte vybranou výchozí řazení.
![Fond SQL vytvořit tok - další nastavení kartu.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Klikněte na **Zkontrolovat a vytvořit**.
6. Ujistěte se, že podrobnosti vypadají správně na základě toho, co bylo zadáno, a klepněte na **tlačítko Vytvořit**.
![Fond SQL vytvořit tok - revize nastavení kartu.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. V tomto okamžiku se spustí tok zřizování prostředků.
 ![Fond SQL vytvořit tok - zřizování prostředků.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Po dokončení zřizování se při přechodu zpět do pracovního prostoru zobrazí nová položka pro nově vytvořený fond SQL.
 ![Fond SQL vytvořit tok - zřizování prostředků.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Podle následujících kroků odstraňte fond SQL z pracovního prostoru.
> [!WARNING]
> Odstraněnífondu SQL odebere analytický modul i data uložená v databázi odstraněného fondu SQL z pracovního prostoru. Již nebude možné připojit k fondu SQL a všechny dotazy, kanály a poznámkové bloky, které čtou nebo zapisují do tohoto fondu SQL, již nebudou fungovat.

Pokud chcete fond SQL odstranit, proveďte následující kroky:

1. Přechod na okno fondů SQL v okně pracovního prostoru
1. Vyberte fond SQL, který má být odstraněn (v tomto případě **contosoedw**)
1. Vyberte ji a stiskněte **klávesu Delete**.
1. Potvrďte odstranění a stiskněte **tlačítko Odstranit.**
 ![Přehled fondu SQL – zvýraznění potvrzení odstranění.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Po úspěšném dokončení procesu nebude fond SQL již uveden v prostředcích pracovního prostoru.

Po vytvoření fondu SQL bude k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení z jezera atd.

## <a name="next-steps"></a>Další kroky

- Viz [Úvodní příručka: Vytvoření fondu Apache Spark v Synapse Studiu pomocí webových nástrojů](spark/apache-spark-notebook-create-spark-use-sql.md).
- Viz [Úvodní příručka: Vytvoření fondu Apache Spark pomocí portálu Azure](quickstart-create-apache-spark-pool.md).
