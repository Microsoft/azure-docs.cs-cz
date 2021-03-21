---
title: Machine Learning Services ve spravované instanci Azure SQL
description: Tento článek poskytuje přehled nebo Machine Learning Services ve spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599557"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services ve spravované instanci Azure SQL

Machine Learning Services je funkce spravované instance Azure SQL, která poskytuje Machine Learning v databázi, podporující skripty Python i R. Tato funkce zahrnuje balíčky Microsoft Pythonu a R pro vysoce výkonné prediktivní analýzy a strojové učení. Relační data lze použít ve skriptech prostřednictvím uložených procedur, skriptu T-SQL obsahujícím příkazy jazyka Python nebo R nebo kódu Python nebo R obsahujícího T-SQL.

## <a name="what-is-machine-learning-services"></a>Co je Machine Learning Services?

Machine Learning Services ve spravované instanci Azure SQL vám umožní spouštět v databázi skripty Python a R. Můžete ji použít k přípravě a vyčištění dat, k vytváření funkcí a k výuce, vyhodnocení a nasazení modelů strojového učení v rámci databáze. Tato funkce spustí vaše skripty, ve kterých se data nacházejí, a eliminuje přenos dat napříč sítí na jiný server.

Pomocí Machine Learning Services s podporou R/Pythonu ve službě Azure SQL Managed instance můžete:

- **Spouštění skriptů r a Python pro přípravu dat a zpracování dat pro obecné účely** – nyní můžete přenést skripty R/Python do spravované instance Azure SQL, kde se nachází vaše data, a nemusíte přesouvat data na jiný server, aby bylo možné spouštět skripty R a Python. Můžete eliminovat nutnost přesunu dat a související problémy související s latencí, zabezpečením a dodržováním předpisů.

- **Naučte se modely strojového učení v databázi** . modely můžete proškolit pomocí libovolných Open Source algoritmů. Můžete snadno škálovat vaše školení na celou datovou sadu a nespoléhat se na ukázkové datové sady, které jsou z databáze vycházející.

- **Nasaďte modely a skripty do produkčního prostředí v uložených procedurách** – skripty a školené modely je možné provozovat jednoduše pouhým vložením v uložených procedurách T-SQL. Aplikace, které se připojují ke spravované instanci Azure SQL, můžou využívat výhod předpovědi a Intelligence v těchto modelech pouhým voláním uložené procedury. K zprovoznění modelů pro rychlé hodnocení ve vysoce souběžných scénářích bodování v reálném čase můžete také použít nativní funkci PREDIKTIVNÍch T-SQL.

Základní distribuce Pythonu a R jsou součástí Machine Learning Services. Kromě balíčků Microsoft pro [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) a [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) pro Python a [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [microsoftml](/sql/machine-learning/r/ref-r-microsoftml), [OLAP](/sql/machine-learning/r/ref-r-olapr)a [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) pro R můžete nainstalovat a používat Open Source balíčky a architektury, jako jsou PyTorch, TensorFlow a scikit-učení.

## <a name="how-to-enable-machine-learning-services"></a>Postup povolení Machine Learning Services

Machine Learning Services ve spravované instanci SQL Azure můžete povolit tím, že povolíte rozšiřitelnost s následujícími příkazy SQL (SQL Managed instance se restartuje a nebude k dispozici po dobu několika sekund):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Podrobnosti o tom, jak tento příkaz ovlivňuje prostředky spravované instance SQL, najdete v tématu zásady [správného řízení prostředků](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Povolení Machine Learning Services ve skupině převzetí služeb při selhání

V případě [skupiny převzetí služeb při selhání](failover-group-add-instance-tutorial.md)nejsou systémové databáze replikovány do sekundární instance (Další informace najdete v tématu [omezení skupin převzetí služeb při selhání](../database/auto-failover-group-overview.md#limitations-of-failover-groups) ).

Pokud je Managed instance, kterou používáte, součástí skupiny převzetí služeb při selhání, udělejte toto:

- Spuštěním `sp_configure` příkazů a `RECONFIGURE` v jednotlivých instancích skupiny převzetí služeb při selhání povolte Machine Learning Services.

- Nainstalujte knihovny R/Python do uživatelské databáze místo do hlavní databáze.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [klíčové rozdíly od SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Další informace o strojovém učení na jiných platformách SQL najdete v [dokumentaci ke službě SQL Machine Learning](/sql/machine-learning/index).
