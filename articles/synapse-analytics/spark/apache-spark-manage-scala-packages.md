---
title: Správa knihoven Java Scala & pro Apache Spark
description: Naučte se přidávat a spravovat knihovny Scala a Java v Azure synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098702"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Správa balíčků Scala a Java pro Apache Spark v Azure synapse Analytics

Knihovny poskytují opakovaně použitelný kód, který můžete chtít zahrnout do svých programů nebo projektů. 

Možná budete muset aktualizovat prostředí Apache Spark fondů bez serveru, a to z několika důvodů. Můžete například zjistit, že:
- jedna z vašich základních závislostí vydala novou verzi.
- potřebujete další balíček pro školení modelu Machine Learning nebo přípravu dat.
- našli jste lepší balíček a už nepotřebujete starší balíček.

Chcete-li zpřístupnit aplikaci třetí straně nebo místně sestavený kód, můžete nainstalovat knihovnu do jednoho z Apache Spark fondů a poznámkových bloků bez serveru. V tomto článku se dozvíte, jak můžete spravovat balíčky Scala a Java.

## <a name="default-installation"></a>Výchozí instalace
Apache Spark ve službě Azure synapse Analytics má úplnou sadu knihoven pro běžné datové inženýry, přípravu dat, strojové učení a vizualizace dat. Seznam úplných knihoven najdete na stránce [podpora Apache Spark verzí](apache-spark-version-support.md). 

Po spuštění instance Spark budou automaticky zahrnuty tyto knihovny. Další balíčky Scala/Java je možné přidat ve fondu Spark a na úrovni relace.

## <a name="workspace-packages"></a>Balíčky pracovních prostorů
Balíčky pracovního prostoru můžou být vlastní nebo soukromé soubory jar. Tyto balíčky můžete nahrát do svého pracovního prostoru a později je přiřadit ke konkrétnímu fondu Spark.

Přidání balíčků pracovního prostoru:
1. Přejděte na kartu **Spravovat**  >  **balíčky pracovních prostorů** .
2. Soubory jar nahrajte pomocí voliče souborů.
3. Po nahrání souborů do pracovního prostoru Azure synapse můžete tyto soubory jar přidat do daného fondu Apache Spark.

![Snímek obrazovky, který zvýrazní balíčky pracovních prostorů.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Zobrazit balíčky pracovních prostorů")

## <a name="pool-libraries"></a>Knihovny fondů
Po identifikaci balíčků Scala a Java, které byste chtěli použít pro aplikaci Spark, je můžete nainstalovat do fondu Spark. Knihovny na úrovni fondu jsou dostupné všem poznámkovým blokům a úlohám, které jsou ve fondu spuštěné.

Knihovny fondů Spark můžete aktualizovat tak, že přejdete na Azure synapse Studio nebo Azure Portal. Tady můžete vybrat knihovny pracovních prostorů, které chcete nainstalovat. 

Po uložení změn se spustí úloha Spark a výsledné prostředí se uloží do mezipaměti pro pozdější použití. Po dokončení úlohy budou nové úlohy Sparku nebo relace poznámkového bloku používat aktualizované knihovny fondu. 

> [!IMPORTANT]
> - Pokud je balíček, který instalujete, velký nebo trvá jeho instalaci dlouhou dobu, bude to mít vliv na počáteční čas instance Spark.
> - Změna verze PySpark, Python, Scala/Java, .NET nebo Spark se nepodporuje.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Správa balíčků z Azure synapse studia nebo Azure Portal
Knihovny fondů Spark je možné spravovat buď z Azure synapse studia, nebo Azure Portal. 

Aktualizace nebo přidání knihoven do fondu Spark:
1. Přejděte do pracovního prostoru Azure synapse Analytics z Azure Portal.

    Pokud aktualizujete z **Azure Portal**:

    - V části **prostředky synapse** vyberte kartu **fondy Apache Spark** a v seznamu vyberte fond Spark.
     
    - V části **Nastavení** ve fondu Spark vyberte **balíčky** .
  
    ![Snímek obrazovky, který zvýrazní tlačítko pro nastavení konfiguračního souboru prostředí pro odeslání.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Přidat knihovny Pythonu")
   
    Pokud aktualizujete z nástroje **synapse Studio**:
    - V hlavním navigačním panelu vyberte **Spravovat** a pak vyberte **fondy Apache Spark**.

    - Vyberte oddíl **balíčky** pro konkrétní fond Spark.
    ![Snímek obrazovky, který zvýrazní možnost konfigurace prostředí v studiu.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Přidat knihovny Pythonu z studia")
   
2. Chcete-li přidat soubory jar, přejděte do oddílu **balíčky pracovních prostorů** , které chcete přidat do fondu. 
3. Po uložení změn se aktivuje systémová úloha pro instalaci a ukládání do mezipaměti určených knihoven. Tento proces pomáhá snižovat celkovou dobu spuštění relace. 
4. Po úspěšném dokončení úlohy budou všechny nové relace vyzradit aktualizované knihovny fondu.

> [!IMPORTANT]
> Když vyberete možnost pro **vynucení nových nastavení**, ukončí se všechny aktuální relace pro vybraný fond Spark. Po ukončení relace budete muset počkat na restartování fondu. 
>
> Pokud je toto nastavení nezaškrtnuté, budete muset počkat, až aktuální relace Spark ukončí nebo zastaví ručně. Po ukončení relace budete muset povolit restart fondu.

#### <a name="track-installation-progress-preview"></a>Sledovat průběh instalace (Preview)
Systémová rezervovaná úloha Spark se iniciuje pokaždé, když se fond aktualizuje novou sadou knihoven. Tato úloha Spark pomáhá monitorovat stav instalace knihovny. Pokud se instalace nezdaří kvůli konfliktům knihoven nebo jiným problémům, fond Spark se vrátí k předchozímu nebo výchozímu stavu. 

Kromě toho mohou uživatelé také zkontrolovat protokoly instalace a identifikovat konflikty závislostí nebo zjistit, které knihovny byly během aktualizace fondu nainstalovány.

Postup zobrazení těchto protokolů:
1. Na kartě **monitorování** přejděte na seznam aplikace Spark. 
2. Vyberte úlohu systémové aplikace Spark, která odpovídá vaší aktualizaci fondu. Tyto systémové úlohy se spouštějí pod názvem *SystemReservedJob-LibraryManagement* .
   ![Snímek obrazovky, který zvýrazní rezervovanou systémovou úlohu knihovny.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Zobrazit úlohu systémové knihovny")
3. Přepněte k zobrazení protokolů **ovladače** a **stdout** . 
4. V rámci výsledků se zobrazí protokoly týkající se instalace balíčků.
    ![Snímek obrazovky, který zvýrazní výsledky úlohy rezervované pro systém.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Zobrazit průběh úlohy systémové knihovny")

## <a name="session-scoped-libraries"></a>Knihovny s rozsahem relace 
Kromě knihoven na úrovni fondu můžete také na začátku relace poznámkového bloku zadat knihovny s rozsahem relace.  Knihovny s rozsahem relace umožňují zadat a použít balíčky jar výhradně v rámci relace poznámkového bloku. 

Při použití knihoven s rozsahem relace je důležité mít na paměti následující body:
   - Při instalaci knihoven s rozsahem relace má přístup k určeným knihovnám pouze aktuální Poznámkový blok. 
   - Tyto knihovny nebudou mít vliv na ostatní relace nebo úlohy pomocí stejného fondu Spark. 
   - Tyto knihovny jsou nainstalovány nad základní modul runtime a knihovny na úrovni fondu. 
   - Knihovny poznámkových bloků budou mít nejvyšší prioritu.

Pokud chcete zadat balíčky Java nebo Scala s rozsahem relace, můžete použít ```%%configure``` možnost:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Doporučujeme na začátku poznámkového bloku spustit konfiguraci%%. Úplný seznam platných parametrů najdete v tomto [dokumentu](https://github.com/cloudera/livy#request-body) .

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)
- Řešení chyb při instalaci knihovny: [řešení chyb v knihovně](apache-spark-troubleshoot-library-errors.md)
