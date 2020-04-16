---
title: Úvodní příručka k vytvoření fondu Apache Spark (preview)
description: Vytvořte nový fond Apache Spark pro pracovní prostor Azure Synapse Analytics podle kroků v této příručce.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424430"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Úvodní příručka: Vytvoření nového fondu Apache Spark (preview)

Synapse Analytics nabízí různé analytické moduly, které vám pomohou ingestovat, transformovat, modelovat, analyzovat a obsluhovat vaše data. Fond Apache Spark nabízí výpočetní funkce pro big data s otevřeným zdrojovým kódem. Po vytvoření fondu Apache Spark ve vašem pracovním prostoru Synapse lze data načíst, modelovat, zpracovat a obsluhovat, abyste získali přehledy.

V tomto rychlém startu se dozvíte, jak pomocí portálu Azure vytvořit fond Apache Spark v pracovním prostoru Synapse.

> [!IMPORTANT]
> Fakturace pro instance Spark se poměrně účtuje za minutu, ať už je používáte nebo ne. Po dokončení používání instance Spark nezapomeňte vypnout instanci Spark nebo nastavit krátký časový čas. Další informace najdete v části **Vyčištění prostředků** tohoto článku.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet, než začnete](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https:/azure.microsoft.com/free/)
- [Pracovní prostor Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlášení k [portálu Azure](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Vytvoření nového fondu Apache Spark

1. V pracovním prostoru Synapse, kde chcete vytvořit fond Apache Spark, klikněte na **Nový fond Apache Spark**.
![Přehled pracovního prostoru Synapse s červeným rámečkem kolem příkazu pro vytvoření nového fondu Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Na kartě **Základy** zadejte následující podrobnosti:

    |Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název fondu Apache Spark** | Platný název fondu | Toto je název, který bude mít fond Apache Spark. |
    | **Velikost uzlu** | Malé (4 virtuální procesory / 32 GB) | Nastavte tuto velikost na nejmenší velikost, abyste snížili náklady pro tento rychlý start |
    | **Automatické škálování** | Povoleno | Ponechat toto výchozí nastavení |
    | **Počet uzlů** | 3 - 40 | Ponechat toto výchozí nastavení |
    ||||

    ![Apache Spark fond vytvořit tok - základy kartu.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které fondy Apache Spark můžete použít. Názvy musí obsahovat pouze písmena nebo čísla, musí mít 15 nebo méně znaků, musí začínat písmenem, nesmí obsahovat vyhrazená slova a musí být v pracovním prostoru jedinečné.

3. Klikněte na **Další: další nastavení** a zkontrolujte výchozí nastavení. Neměnujte žádné výchozí nastavení.
![Fond Apache Spark vytváří tok - další karta nastavení.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Klikněte na **Další: značky**. Nepřidávejte žádné značky.
![Fond Apache Spark vytváří tok - další karta nastavení.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Klikněte na **Zkontrolovat a vytvořit**.

6. Ujistěte se, že podrobnosti vypadají správně na základě toho, co bylo zadáno, a klepněte na **tlačítko Vytvořit**.
![Fond Apache Spark vytváří tok – karta nastavení recenze.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. V tomto okamžiku se spustí tok zřizování prostředků, což znamená, že po dokončení ![fondu Apache Spark vytvořit tok – zřizování prostředků.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Po dokončení zřizování se při přechodu zpět do pracovního prostoru zobrazí nová položka pro nově vytvořený fond Apache Spark.
 ![Fond Apache Spark vytváří tok – zřizování prostředků.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. V tomto okamžiku nejsou spuštěny žádné prostředky, žádné poplatky za Spark, vytvořili jste metadata o instancích Spark, které chcete vytvořit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Podle následujících kroků odstraňte fond Apache Spark z pracovního prostoru.
> [!WARNING]
> Odstraněním fondu Apache Spark odeberete analytický modul z pracovního prostoru. Už nebude možné se připojit ke fondu a všechny dotazy, kanály a poznámkové bloky, které používají tento fond Apache Spark, už nebudou fungovat.

Pokud chcete fond Apache Spark odstranit, postupujte takto:

1. Přejděte do okna fondů Apache Spark v pracovním prostoru.
2. Vyberte fond Apache, který má být odstraněn (v tomto případě **contosospark**)
3. Stiskněte **klávesu delete**.
 ![Výpis fondů Apache Spark s vybraným nedávno vytvořeným fondem.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Potvrďte odstranění a stiskněte **tlačítko Odstranit.**
 ![Potvrzovací dialog pro odstranění vybraného fondu Apache Spark.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Po úspěšném dokončení procesu nebude fond Apache Spark již uveden v prostředcích pracovního prostoru.

Po vytvoření fondu SQL je k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení z jezera a tak.

## <a name="next-steps"></a>Další kroky

- Viz [Úvodní příručka: Vytvoření fondu Apache Spark v Synapse Studiu pomocí webových nástrojů](spark/apache-spark-notebook-create-spark-use-sql.md).
- Viz [Úvodní příručka: Vytvoření fondu synapse SQL pomocí portálu Azure](quickstart-create-sql-pool.md).
