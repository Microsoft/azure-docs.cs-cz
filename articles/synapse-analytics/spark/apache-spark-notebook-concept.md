---
title: Přehled notebooků Azure synapse Analytics
description: Tento článek poskytuje přehled možností dostupných prostřednictvím notebooků Azure synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 5efdfcea7b6d727038bad124368179b9395bfafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093273"
---
# <a name="azure-synapse-analytics-notebooks"></a>Notebooky Azure synapse Analytics

Poznámkový blok synapse Studio je webové rozhraní, které umožňuje vytvářet soubory, které obsahují živý kód, vizualizace a mluvený text. Poznámkové bloky jsou vhodné místo pro ověřování nápadů a k získání přehledu z vašich dat můžete využít rychlé experimenty. 

Pomocí poznámkového bloku Azure synapse Studio můžete:

* Začněte s nulovým úsilím nastavení.
* Zajistěte zabezpečení dat pomocí integrovaných funkcí podnikového zabezpečení.
* Analyzujte data napříč nezpracovanými formáty (CSV, txt, JSON atd.), zpracovanými formáty souborů (Parquet, rozdílových Lake, ORC atd.) a SQL tabelárních datových souborů pro Spark a SQL.
* Získejte produktivitu s využitím vylepšených možností vytváření a integrovaných vizualizací dat.

Tato část obsahuje články o kombinování jazyků, vytváření vizualizací dat, poznámkových blocích Parametrizace, sestavování kanálů a dalších. Obsahuje také odkazy a kurzy, jak můžete začít s vývojem poznámkového bloku.

## <a name="create-manage-and-use-notebooks"></a>Vytváření, Správa a používání poznámkových bloků
Poznámkové bloky můžete spravovat pomocí uživatelského rozhraní synapse Studio. 

Další informace o tom, jak můžete vytvářet a spravovat poznámkové bloky, najdete v následujících článcích:
  - Správa poznámkových bloků
    - [Vytvoření poznámkových bloků](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Vývoj poznámkových bloků](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Přenesení dat do poznámkového bloku](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Používání více jazyků pomocí příkazů Magic a dočasných tabulek](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Použít příkazy buňky Magic](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Vývoj
    - [Konfigurace nastavení relace Spark](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Použití nástrojů Microsoft Spark](./spark/../microsoft-spark-utilities.md)
    - [Vizualizace dat pomocí poznámkových bloků a knihoven](./spark/../apache-spark-data-visualization.md)
    - [Integrace poznámkového bloku do kanálů](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Další kroky
Poznámkové bloky se také často používají při přípravě dat, vizualizaci dat, strojovém učení a dalších scénářích s velkými objemy dat. Další informace o tom, jak můžete používat poznámkové bloky pro scénáře analýzy dat a velkých objemů dat, najdete v následujících kurzech:
  - [Vytvoření poznámkového bloku](./spark/../../quickstart-apache-spark-notebook.md)
  - [Vytváření vizualizací pomocí poznámkových bloků synapse Studio](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Sestavování modelů strojového učení pomocí Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Sestavování modelů strojového učení pomocí automatizovaného ML Azure](./spark/../apache-spark-azure-machine-learning-tutorial.md)