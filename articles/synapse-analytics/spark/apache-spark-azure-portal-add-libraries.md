---
title: Správa knihovny
description: Naučte se přidávat a spravovat knihovny používané Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 955d7f8c2d2ce5ea126d4cce67b0e4e55152ac72
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695086"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Správa knihoven pro Apache Spark ve službě Azure synapse Analytics
Knihovny poskytují opakovaně použitelný kód, který můžete chtít zahrnout do svých programů nebo projektů. 

Pro různé důvody možná budete muset aktualizovat prostředí Apache Spark fondů bez serveru. Můžete například zjistit, že:
- jedna z vašich základních závislostí vydala novou verzi.
- potřebujete další balíček pro školení modelu Machine Learning nebo přípravu dat.
- našli jste lepší balíček a už nepotřebujete starší balíček.
- Váš tým vytvořil vlastní balíček, který potřebujete k dispozici ve vašem fondu Apache Spark.

Chcete-li zpřístupnit aplikaci třetí straně nebo místně sestavený kód, můžete nainstalovat knihovnu do jednoho z Apache Spark fondů a poznámkových bloků bez serveru.
  
## <a name="default-installation"></a>Výchozí instalace
Apache Spark ve službě Azure synapse Analytics má úplnou Anacondas instalaci a další knihovny. Seznam úplných knihoven najdete na stránce [podpora Apache Spark verzí](apache-spark-version-support.md). 

Po spuštění instance Spark budou automaticky zahrnuty tyto knihovny. Další balíčky lze přidat na úrovni fondu Spark nebo na úrovni relace.

## <a name="workspace-packages"></a>Balíčky pracovních prostorů
Při vývoji vlastních aplikací nebo modelů může váš tým vyvíjet různé artefakty kódu, jako jsou soubory kolo nebo JAR pro zabalení kódu. 

V synapse mohou být balíčky pracovních prostorů vlastními nebo soukromými soubory nebo soubory jar. Tyto balíčky můžete nahrát do svého pracovního prostoru a později je přiřadit ke konkrétnímu fondu Spark. Po přiřazení se tyto balíčky pracovního prostoru automaticky nainstalují na všechny relace fondu Spark.

Další informace o správě knihoven pracovních prostorů najdete v následujících průvodcích:
- [Balíčky pracovního prostoru Pythonu: ](./apache-spark-manage-python-packages.md#Install-wheel-files) Nahrajte soubory kolečka Pythonu jako balíček pracovního prostoru a později tyto balíčky přidejte do konkrétních Apache Sparkch fondů bez serveru.
- [Balíčky pracovních prostorů Scala/Java (Preview): ](./apache-spark-manage-scala-packages.md#Workspace-packages) Nahrajte soubory Scala a Java jar jako balíček pracovního prostoru a později tyto balíčky přidejte do konkrétních Apache Sparkch fondů bez serveru.

## <a name="pool-management"></a>Správa fondu
V některých případech můžete chtít standardizovat sadu balíčků, které se používají v daném fondu Apache Spark. Tato standardizace může být užitečná, pokud jsou stejné balíčky běžně nainstalovány více lidmi v týmu. 

Pomocí možností správy fondu Azure synapse Analytics můžete nakonfigurovat výchozí sadu knihoven, které byste chtěli nainstalovat na daný server Apache Spark fond. Tyto knihovny jsou nainstalovány nad [základním modulem runtime](./apache-spark-version-support.md). 

Správa fondu se v současné době podporuje jenom pro Python. Pro Python synapse fondy Spark používají conda pro instalaci a správu závislostí balíčku Pythonu. Když zadáváte knihovny na úrovni fondu, můžete teď zadat requirements.txt nebo prostředí. yml. Tento konfigurační soubor prostředí se používá při každém vytvoření instance Spark z tohoto fondu Spark. 

Další informace o těchto možnostech najdete v dokumentaci ke [správě fondů Pythonu](./apache-spark-manage-python-packages.md#Pool-libraries).

> [!IMPORTANT]
> - Pokud je balíček, který instalujete, velký nebo trvá jeho instalaci dlouhou dobu, bude to mít vliv na počáteční čas instance Spark.
> - Změna verze PySpark, Python, Scala/Java, .NET nebo Spark se nepodporuje.
> - Instalace balíčků z PyPI není v pracovních prostorech povolených pro DEP podporována.

## <a name="session-scoped-packages"></a>Balíčky s rozsahem relace
Při provádění interaktivní analýzy dat nebo strojového učení často zjistíte, že chcete vyzkoušet novější balíčky, nebo budete možná potřebovat balíčky, které ještě nejsou ve vašem Apache Spark fondu dostupné. Místo aktualizace konfigurace fondu můžou teď uživatelé používat balíčky s rozsahem relace k přidávání, správě a aktualizaci závislostí relací.

Balíčky s rozsahem relace umožňují uživatelům definovat závislosti balíčků na začátku relace. Při instalaci balíčku s rozsahem relace má přístup k určeným balíčkům pouze aktuální relace. V důsledku toho tyto balíčky s rozsahem relace nebudou mít vliv na ostatní relace nebo úlohy pomocí stejného fondu Apache Spark. Kromě toho jsou tyto knihovny nainstalovány nad základními balíčky modulu runtime a na úrovni fondu. 

Další informace o tom, jak spravovat balíčky s rozsahem relace, najdete v následujících průvodcích:
- [Balíčky relací Pythonu (Preview):](./apache-spark-manage-python-packages.md#Session-scoped-libraries-(preview)) Na začátku relace poskytněte *prostředí conda. yml* pro instalaci dalších balíčků Pythonu z oblíbených úložišť. 
- [Balíčky relací Scala/Java: ](./apache-spark-manage-scala-packages.md#Workspace-packages) Na začátku relace zadejte seznam souborů JAR, které se mají nainstalovat pomocí nástroje ```%%configure``` .

## <a name="next-steps"></a>Další kroky
- Zobrazení výchozích knihoven: [podpora Apache Spark verzí](apache-spark-version-support.md)
