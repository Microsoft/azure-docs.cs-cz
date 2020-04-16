---
title: Přidání a správa knihoven pro Apache Spark v Azure Synapse Analytics
description: Přečtěte si, jak přidávat a spravovat knihovny používané Apache Spark v Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427832"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Přidání a správa knihoven pro Apache Spark v Azure Synapse Analytics

Apache Spark závisí na mnoha knihovnách, které poskytují funkce. Tyto knihovny mohou být rozšířeny nebo nahrazeny dalšími knihovnami nebo aktualizovanými verzemi starších knihoven.

Balíčky pythonu lze přidat na úrovni fondu Spark (preview) a balíčky založené na .jar lze přidat na úrovni definice úlohy Spark.

## <a name="adding-or-updating-python-libraries"></a>Přidání nebo aktualizace knihoven Pythonu

Apache Spark v Azure Synapse Analytics má plnou instalaci Anacondas plus další knihovny. Úplný seznam knihoven naleznete na [adrese Podpora verzí Apache Spark](apache-spark-version-support.md).

Při spuštění instance Spark se vytvoří nové virtuální prostředí pomocí této instalace jako základu. Kromě toho *soubor requirements.txt* (výstup `pip freeze` z příkazu) lze použít k upgradu virtuálního prostředí. Balíčky uvedené v tomto souboru pro instalaci nebo upgrade jsou staženy z PyPi v době spuštění clusteru. Tento soubor požadavků se používá při každém vytvoření instance Spark z tohoto fondu Spark.

> [!IMPORTANT]
>
> - Pokud je balíček, který instalujete, velký nebo instalace trvá dlouho, ovlivní to dobu spuštění instance Spark.
> - Balíčky, které vyžadují podporu kompilátoru v době instalace, jako je například GCC, nejsou podporovány.
> - Balíčky nelze snížit, pouze přidat nebo upgradovat.

### <a name="requirements-format"></a>Formát požadavků

Následující úryvek zobrazuje formát souboru požadavků. Název balíčku PyPi je uveden spolu s přesnou verzí. Tento soubor se řídí formátem popsaným v referenční dokumentaci [pro zmrazení pipu.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Tento příklad připne určitou verzi. V tomto souboru můžete také zadat verze "ne větší než" a "menší než".

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Uživatelské rozhraní knihovny Pythonu

Uživatelské tlačítko pro přidávání knihoven je na kartě **Další nastavení** na stránce **fondu Vytvořit Apache Spark** na webu Azure Portal.

Nahrajte konfigurační soubor prostředí pomocí voliče souborů v části **Balíčky** na stránce.

![Přidání knihoven Pythonu](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Přidání knihoven Pythonu")

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentace Apache Spark](https://spark.apache.org/docs/2.4.4/)
