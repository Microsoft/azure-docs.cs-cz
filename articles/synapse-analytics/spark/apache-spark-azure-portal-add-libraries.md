---
title: Přidávání a Správa knihoven pro Apache Spark ve službě Azure synapse Analytics
description: Naučte se přidávat a spravovat knihovny používané Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870362"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Přidávání a Správa knihoven pro Apache Spark ve službě Azure synapse Analytics

Apache Spark závisí na řadě knihoven, které poskytují funkce. Tyto knihovny lze rozšířit nebo nahradit dalšími knihovnami nebo aktualizovanými verzemi starších verzí.

Balíčky Pythonu se dají přidat na úrovni fondů Spark (Preview) a balíčky založené na JAR se dají přidat na úrovni definice úlohy Spark.

## <a name="adding-or-updating-python-libraries"></a>Přidávání a aktualizace knihoven Pythonu

Apache Spark ve službě Azure synapse Analytics má úplnou instalaci Anacondas a další knihovny. Seznam úplných knihoven najdete na stránce [podpora Apache Spark verzí](apache-spark-version-support.md).

Když se spustí instance Sparku, vytvoří se v této instalaci jako základ nové virtuální prostředí. K upgradu virtuálního prostředí se navíc dá použít soubor *. txt s požadavky* (výstup z `pip freeze` příkazu). Balíčky uvedené v tomto souboru pro instalaci nebo upgrade se stáhnou z PyPi v době spuštění clusteru. Tento soubor požadavků se používá při každém vytvoření instance Spark z tohoto fondu Spark.

> [!IMPORTANT]
>
> - Pokud je balíček, který instalujete, velký nebo trvá jeho instalaci dlouhou dobu, bude to mít vliv na počáteční čas instance Spark.
> - Balíčky, které vyžadují podporu kompilátoru v době instalace, jako je třeba RSZ, se nepodporují.
> - Balíčky nemůžou být downgradované, jenom přidané nebo upgradované.

### <a name="requirements-format"></a>Formát požadavků

Následující fragment kódu ukazuje formát souboru požadavků. Název balíčku PyPi je uveden společně s přesnou verzí. Tento soubor se řídí formátem popsaným v referenční dokumentaci k [zablokování PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Tento příklad připnete konkrétní verzi. V tomto souboru můžete také zadat "ne větší než" a "menší než" verze.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Uživatelské rozhraní knihovny Pythonu

Uživatelské rozhraní pro přidávání knihoven je na kartě **Další nastavení** stránky **vytvořit fond Apache Spark** na Azure Portal.

Nahrajte konfigurační soubor prostředí pomocí voliče souborů v části **Packages (balíčky** ) na stránce.

![Přidat knihovny Pythonu](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Přidat knihovny Pythonu")

### <a name="verifying-installed-libraries"></a>Ověřují se nainstalované knihovny.

Pokud chcete ověřit, jestli jsou nainstalované správné verze správných knihoven, spusťte následující kód.

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentace k Apache Spark](https://spark.apache.org/docs/2.4.4/)
