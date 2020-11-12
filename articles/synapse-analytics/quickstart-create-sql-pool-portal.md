---
title: 'Rychlý Start: vytvoření vyhrazeného fondu SQL (ve verzi Preview) pomocí Azure Portal'
description: Pomocí následujících kroků v této příručce vytvořte nový vyhrazený fond SQL pomocí Azure Portal.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: b558e34d3f5d224515eee669dfa7b8f2c6bbb3cd
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541007"
---
# <a name="quickstart-create-a-dedicated-sql-pool-preview-using-the-azure-portal"></a>Rychlý Start: vytvoření vyhrazeného fondu SQL (ve verzi Preview) pomocí Azure Portal

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat a analyzovat vaše data. Vyhrazený fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření vyhrazeného fondu SQL ve vašem pracovním prostoru synapse se dají data načíst, namodelovat, zpracovávat a doručovat za účelem rychlejšího analytického přehledu.

V tomto rychlém startu se dozvíte, jak vytvořit vyhrazený fond SQL v pracovním prostoru synapse pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Přejděte do pracovního prostoru synapse.

1. Přejděte do pracovního prostoru synapse, kde se vytvoří vyhrazený fond SQL zadáním názvu služby (nebo názvu prostředku přímo) do panelu hledání.
![Azure Portal panel hledání s pracovními prostory synapse zadanými v. ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) .. 
1. V seznamu pracovních prostorů zadejte název (nebo část názvu) pracovního prostoru, který chcete otevřít. V tomto příkladu použijeme pracovní prostor s názvem **contosoanalytics**.
![Seznam pracovních prostorů synapse filtrovaných k zobrazení těch, které obsahují název contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-dedicated-sql-pool"></a>Vytvořit nový vyhrazený fond SQL

1. V pracovním prostoru synapse, kde chcete vytvořit vyhrazený fond SQL, vyberte v horním panelu příkaz **Nový vyhrazený fond SQL** .
![Přehled pracovního prostoru synapse se červeným polem kolem příkazu pro vytvoření nového vyhrazeného fondu SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Na kartě **základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název vyhrazeného fondu SQL** | Libovolné platné jméno | Název vyhrazeného fondu SQL. |
    | **Úroveň výkonu** | DW100c | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |

  
    ![Vyhrazený fond SQL vytvořit kartu základy na základě toku.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)

    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které mohou používat vyhrazené fondy SQL. Názvy nesmí obsahovat speciální znaky, musí být delší než 15 znaků, nesmí obsahovat vyhrazená slova a v pracovním prostoru být jedinečné.

3. Až skončíte, vyberte **Další: Další nastavení**.
4. Pokud chcete zřídit vyhrazený fond SQL bez dat, vyberte **none** . Ponechte vybranou výchozí kolaci.

    Pokud chcete obnovit vyhrazený fond SQL z bodu obnovení, vyberte **bod obnovení**. Další informace o tom, jak provést obnovení, najdete v tématu [Postup: obnovení existujícího vyhrazeného fondu SQL.](backuprestore/restore-sql-pool.md)

![vyhrazený fond SQL vytvořit tok – karta Další nastavení.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Vyberte **Zkontrolovat a vytvořit**.
6. Ujistěte se, že podrobnosti vypadají správně na základě toho, co bylo dříve zadáno. Vyberte **Vytvořit**.
![vyhrazený fond SQL vytvořit tok – karta nastavení – přezkoumání](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. V tomto okamžiku se spustí tok zřizování prostředků.
 ![Snímek obrazovky, na které se zobrazuje stránka nasazení bylo dokončeno.](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí nový záznam pro nově vytvořený vyhrazený fond SQL.
 ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)


Po vytvoření vyhrazeného fondu SQL bude k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení ze jezera atd.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí následujících kroků odstraňte vyhrazený fond SQL z pracovního prostoru.
> [!WARNING]
> Odstranění vyhrazeného fondu SQL odstraní modul Analytics a data uložená v databázi odstraněného vyhrazeného fondu SQL z pracovního prostoru. Již nebude možné se připojit k vyhrazenému fondu SQL a všechny dotazy, kanály a poznámkové bloky, které čtou nebo zapisují do tohoto vyhrazeného fondu SQL, již nebudou fungovat.

Pokud chcete odstranit vyhrazený fond SQL, proveďte následující kroky:

1. V okně pracovního prostoru přejděte do okna fondy SQL.
1. Vyberte vyhrazený fond SQL, který se má odstranit (v tomto případě **contosowdw** ).
1. Po výběru stiskněte **Odstranit**
1. Potvrďte odstranění a stisknutím tlačítka **Odstranit** ![ vyhrazený fond SQL klikněte na přehled – zvýrazňování potvrzení odstranění.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Po úspěšném dokončení procesu nebude vyhrazený fond SQL nadále uveden v prostředcích pracovního prostoru.

## <a name="next-steps"></a>Další kroky

- Informace najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark bez serveru v nástroji synapse Studio pomocí nástrojů pro web](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: Vytvoření fondu Apache Spark bez serveru pomocí Azure Portal](quickstart-create-apache-spark-pool-portal.md).
