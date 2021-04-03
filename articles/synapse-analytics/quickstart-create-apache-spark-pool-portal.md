---
title: 'Rychlý Start: Vytvoření fondu Apache Spark bez serveru pomocí Azure Portal'
description: Pomocí pokynů v této příručce vytvořte fond Apache Spark bez serveru pomocí Azure Portal.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fc7a7f137701590e4bddd791a3fdd2d1eaeca4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461610"
---
# <a name="quickstart-create-a-new-serverless-apache-spark-pool-using-the-azure-portal"></a>Rychlý Start: vytvoření nového fondu Apache Spark bez serveru pomocí Azure Portal

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat, analyzovat a distribuovat data. Fond Apache Spark nabízí Open Source funkce pro výpočet velkých objemů dat. Po vytvoření fondu Apache Spark v pracovním prostoru synapse se data dají načíst, namodelovat, zpracovávat a distribuovat pro rychlejší analytické poznatky.

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit fond Apache Spark v pracovním prostoru synapse.

> [!IMPORTANT]
> Faktura za instance Spark se účtuje poměrnou rychlostí za minutu, ať už je používáte, nebo ne. Ujistěte se, že jste instanci Spark po dokončení používání vypnuli, nebo nastavte krátký časový limit. Další informace najdete v části **Vyčištění prostředků** tohoto článku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor analýzy synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Přejděte do pracovního prostoru synapse. 
1. Přejděte do pracovního prostoru synapse, kde se vytvoří fond Apache Spark zadáním názvu služby (nebo názvu prostředku přímo) do panelu hledání.
![Azure Portal panel hledání s pracovními prostory synapse zadanými v.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. V seznamu pracovních prostorů zadejte název (nebo část názvu) pracovního prostoru, který chcete otevřít. V tomto příkladu použijeme pracovní prostor s názvem **contosoanalytics**.
![Seznam pracovních prostorů synapse filtrovaných k zobrazení těch, které obsahují název contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Vytvořit nový fond Apache Spark

1. V pracovním prostoru synapse, kde chcete vytvořit fond Apache Spark, vyberte **Nový fond Apache Spark**.
    ![Přehled pracovního prostoru synapse se červeným polem kolem příkazu pro vytvoření nového fondu Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Na kartě **základy** zadejte následující podrobnosti:

    |Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název Apache Spark fondu** | Platný název fondu | Jedná se o název, který bude mít fond Apache Spark. |
    | **Velikost uzlu** | Malý (4 vCPU/32 GB) | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |
    | **Automatické škálování** | Zakázáno | Pro tento rychlý Start nepotřebujeme automatické škálování. |
    | **Počet uzlů** | 5 | Pro tento rychlý Start můžete omezit náklady pomocí malé velikosti. |


    ![Karta základy vytváření Apache Spark fondu](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které mohou fondy Apache Spark použít. Názvy musí obsahovat jenom písmena nebo číslice, musí být delší než 15 znaků, musí začínat písmenem, nesmí obsahovat vyhrazená slova a být v pracovním prostoru jedinečné.

3. Vyberte **Další: Další nastavení** a zkontrolujte výchozí nastavení. Neměňte žádná výchozí nastavení.
    ![Snímek obrazovky zobrazující stránku vytvořit fond Apache Spark s vybranou kartou Další nastavení.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Vyberte **Další: značky**. Nepřidávejte žádné značky.
    ![Apache Spark fond vytváření toku – karta Další nastavení.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Vyberte **Zkontrolovat a vytvořit**.

6. Zajistěte, aby podrobnosti vypadaly správně na základě dříve zadaného obsahu, a vyberte **vytvořit**.
    ![Apache Spark fond vytvoření toku – karta nastavení – přezkoumání.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. V tomto okamžiku se spustí tok zřizování prostředků, který indikuje, že je celý.
    ![Snímek obrazovky zobrazující stránku "Přehled" se zobrazenou zprávou "vaše nasazení je dokončeno".](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí novou položku pro nově vytvořený fond Apache Spark.
    ![Vytvoření fondu Apache Sparkho toku – zřizování prostředků](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. V tuto chvíli neběží žádné prostředky, neúčtují se žádné poplatky za Spark, vytvořili jste metadata o instancích Spark, které chcete vytvořit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí následujících kroků odstraňte fond Apache Spark z pracovního prostoru.
> [!WARNING]
> Odstraněním fondu Apache Spark dojde k odebrání analytického modulu z pracovního prostoru. Již nebude možné se připojit ke fondu a všechny dotazy, kanály a poznámkové bloky, které používají tento fond Apache Spark, již nebudou fungovat.

Pokud chcete fond Apache Spark odstranit, postupujte takto:

1. Přejděte do okna fondy Apache Spark v pracovním prostoru.
2. Vyberte fond Apache Spark, který se má odstranit (v tomto případě **contosospark**).
3. Stiskněte klávesu **Delete**.
 ![Výpis fondů Apache Spark s vybraným nedávno vytvořeným fondem.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
 ![Potvrzovací dialog pro odstranění vybraného fondu Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Po úspěšném dokončení procesu již nebude fond Apache Spark uveden v prostředcích pracovního prostoru.

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [rychlý Start: vytvoření poznámkového bloku Apache Spark](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: vytvoření vyhrazeného fondu SQL pomocí Azure Portal](quickstart-create-sql-pool-portal.md).
