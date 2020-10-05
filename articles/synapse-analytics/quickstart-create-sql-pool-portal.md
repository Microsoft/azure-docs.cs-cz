---
title: 'Rychlý Start: vytvoření synapse fondu SQL (ve verzi Preview) pomocí Azure Portal'
description: Pomocí Azure Portal podle kroků v této příručce vytvořte nový synapse fond SQL.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c89bd01cb2e6947df5ed192d66e0dbae08cf66d5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260627"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>Rychlý Start: vytvoření synapse fondu SQL (ve verzi Preview) pomocí Azure Portal

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat a analyzovat vaše data. Fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření fondu SQL ve vašem pracovním prostoru synapse se dají data načíst, namodelovat, zpracovávat a doručovat pro rychlejší analytické poznatky.

V tomto rychlém startu se dozvíte, jak vytvořit fond SQL v pracovním prostoru synapse pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Přejděte do pracovního prostoru synapse.

1. Přejděte do pracovního prostoru synapse, kde se vytvoří fond SQL zadáním názvu služby (nebo názvu prostředku přímo) do panelu hledání.
![Azure Portal panel hledání s pracovními prostory synapse zadanými v. ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) .. 
1. V seznamu pracovních prostorů zadejte název (nebo část názvu) pracovního prostoru, který chcete otevřít. V tomto příkladu použijeme pracovní prostor s názvem **contosoanalytics**.
![Seznam pracovních prostorů synapse filtrovaných k zobrazení těch, které obsahují název contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>Vytvořit nový fond SQL

1. V pracovním prostoru synapse, kde chcete vytvořit fond SQL, vyberte v horním panelu příkaz **Nový fond SQL** .
![Přehled pracovního prostoru synapse s červeným polem kolem příkazu pro vytvoření nového fondu SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Na kartě **základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název fondu SQL** | Libovolné platné jméno | Název fondu SQL. |
    | **Úroveň výkonu** | DW100c | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |

  
    ![Fond SQL vytvořit kartu základy na základě toku](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které můžou fondy SQL použít. Názvy nesmí obsahovat speciální znaky, musí být delší než 15 znaků, nesmí obsahovat vyhrazená slova a v pracovním prostoru být jedinečné.

3. Až skončíte, vyberte **Další: Další nastavení**.
4. Pokud chcete zřídit fond SQL bez dat, vyberte **none** . Ponechte vybranou výchozí kolaci.
![Postup vytvoření fondu SQL – karta Další nastavení](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Vyberte **Zkontrolovat a vytvořit**.
6. Ujistěte se, že podrobnosti vypadají správně na základě toho, co bylo dříve zadáno. Vyberte **Vytvořit**.
![Postup vytvoření fondu SQL – karta Nastavení revize](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. V tomto okamžiku se spustí tok zřizování prostředků.
 ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí nový záznam pro nově vytvořený fond SQL.
 ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


Po vytvoření fondu SQL bude k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení ze jezera atd.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí následujících kroků odstraňte z pracovního prostoru fond SQL.
> [!WARNING]
> Odstraněním fondu SQL dojde k odebrání analytického modulu i dat uložených v databázi odstraněného fondu SQL z pracovního prostoru. Již nebude možné se připojit ke fondu SQL a všechny dotazy, kanály a poznámkové bloky, které čtou nebo zapisují do tohoto fondu SQL, již nebudou fungovat.

Pokud chcete odstranit fond SQL, proveďte následující kroky:

1. V okně pracovního prostoru přejděte do okna fondy SQL.
1. Vyberte fond SQL, který se má odstranit (v tomto případě **contosowdw**).
1. Po výběru stiskněte **Odstranit**
1. Potvrďte odstranění a stiskněte Odstranit tlačítko pro **odstranění** ![ fondu SQL – zvýrazní se potvrzení o odstranění.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Po úspěšném dokončení procesu již nebude fond SQL uveden v prostředcích pracovního prostoru.

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu rychlý Start: Vytvoření fondu Apache Spark v synapse studiu pomocí nástrojů pro web](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark pomocí Azure Portal](quickstart-create-apache-spark-pool-portal.md).
