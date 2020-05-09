---
title: 'Rychlý Start: Vytvoření fondu Apache Spark (Preview)'
description: Pomocí kroků v této příručce vytvořte nový fond Apache Spark pro pracovní prostor Azure synapse Analytics.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2501f66c42360d3ec1626f5f4ee42e0212f1967b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792237"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Rychlý Start: vytvoření nového fondu Apache Spark (Preview)

Synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat, analyzovat a obsluhovat vaše data. Fond Apache Spark nabízí Open Source výpočetní funkce pro velké objemy dat. Po vytvoření fondu Apache Spark ve vašem pracovním prostoru synapse se data dají načíst, namodelovat, zpracovávat a zpracovávat, abyste získali přehledy.

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit fond Apache Spark v pracovním prostoru synapse.

> [!IMPORTANT]
> Faktura za instance Spark se účtuje poměrnou rychlostí za minutu, ať už je používáte, nebo ne. Ujistěte se, že jste instanci Spark po dokončení používání vypnuli, nebo nastavte krátký časový limit. Další informace najdete v části **Vyčištění prostředků** tohoto článku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor analýzy synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Vytvořit nový fond Apache Spark

1. V pracovním prostoru synapse, kde chcete vytvořit fond Apache Spark klikněte na **nový Apache Spark fond**.
![Přehled pracovního prostoru synapse se červeným polem kolem příkazu pro vytvoření nového fondu Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Na kartě **základy** zadejte následující podrobnosti:

    |Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název Apache Spark fondu** | Platný název fondu | Jedná se o název, který bude mít fond Apache Spark. |
    | **Velikost uzlu** | Malý (4 vCPU/32 GB) | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |
    | **Automatické škálování** | Povoleno | Ponechat toto výchozí nastavení |
    | **Počet uzlů** | 3 - 40 | Ponechat toto výchozí nastavení |
    ||||

    ![Karta základy vytváření Apache Spark fondu](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které mohou fondy Apache Spark použít. Názvy musí obsahovat jenom písmena nebo číslice, musí být delší než 15 znaků, musí začínat písmenem, nesmí obsahovat vyhrazená slova a být v pracovním prostoru jedinečné.

3. Klikněte na **Další: Další nastavení** a zkontrolujte výchozí nastavení. Neměňte žádná výchozí nastavení.
![Apache Spark fond vytváření toku – karta Další nastavení.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Klikněte na tlačítko **Další: značky**. Nepřidávat žádné značky.
![Apache Spark fond vytváření toku – karta Další nastavení.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Klikněte na **Zkontrolovat a vytvořit**.

6. Ujistěte se, že podrobnosti vypadají správně na základě dříve zadaného obsahu, a klikněte na **vytvořit**.
![Apache Spark fond vytvoření toku – karta nastavení – přezkoumání.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. V tomto okamžiku se spustí tok zřizování prostředků, který označuje, že je dokončený ![Apache Spark fond vytváření fondů – zřizování prostředků.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí novou položku pro nově vytvořený fond Apache Spark.
 ![Vytvoření fondu Apache Sparkho toku – zřizování prostředků](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. V tuto chvíli neběží žádné prostředky, neúčtují se žádné poplatky za Spark, vytvořili jste metadata o instancích Spark, které chcete vytvořit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí následujících kroků odstraňte fond Apache Spark z pracovního prostoru.
> [!WARNING]
> Odstraněním fondu Apache Spark dojde k odebrání analytického modulu z pracovního prostoru. Již nebude možné se připojit ke fondu a všechny dotazy, kanály a poznámkové bloky, které používají tento fond Apache Spark, již nebudou fungovat.

Pokud chcete fond Apache Spark odstranit, postupujte takto:

1. Přejděte do okna fondy Apache Spark v pracovním prostoru.
2. Vyberte fond Apache, který se má odstranit (v tomto případě **contosospark**).
3. Stiskněte klávesu **Delete**.
 ![Výpis fondů Apache Spark s vybraným nedávno vytvořeným fondem.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
 ![Potvrzovací dialog pro odstranění vybraného fondu Apache Spark.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Po úspěšném dokončení procesu již nebude fond Apache Spark uveden v prostředcích pracovního prostoru.

Po vytvoření fondu SQL je k dispozici v pracovním prostoru pro načítání dat, zpracování datových proudů, čtení ze Lake a tak dále.

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [rychlý Start: vytvoření poznámkového bloku Apache Spark](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: vytvoření synapse fondu SQL pomocí Azure Portal](quickstart-create-sql-pool.md).
