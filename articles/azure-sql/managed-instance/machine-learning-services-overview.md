---
title: Machine Learning Services ve spravované instanci Azure SQL (Preview)
description: Tento článek poskytuje přehled nebo Machine Learning Services ve spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: b5daf283df1ef5d6b42da5bf0a4652aedf2f6284
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708735"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services ve spravované instanci Azure SQL (Preview)

Machine Learning Services je funkce spravované instance Azure SQL (Preview), která poskytuje Machine Learning v databázi, a podporuje skripty Python i R. Tato funkce zahrnuje balíčky Microsoft Pythonu a R pro vysoce výkonné prediktivní analýzy a strojové učení. Relační data lze použít ve skriptech prostřednictvím uložených procedur, skriptu T-SQL obsahujícím příkazy jazyka Python nebo R nebo kódu Python nebo R obsahujícího T-SQL.

> [!IMPORTANT]
> Machine Learning Services je funkce služby Azure SQL Managed Instance, která je aktuálně ve verzi Public Preview.
> Tato funkce ve verzi Preview je zpočátku k dispozici v omezeném počtu oblastí v USA, Asie a Austrálii s dalšími oblastmi, které se přidávají později.
>
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Zaregistrujte se do verze Preview](#signup) níže.

## <a name="what-is-machine-learning-services"></a>Co je Machine Learning Services?

Machine Learning Services ve spravované instanci Azure SQL vám umožní spouštět v databázi skripty Python a R. Můžete ji použít k přípravě a vyčištění dat, k vytváření funkcí a k výuce, vyhodnocení a nasazení modelů strojového učení v rámci databáze. Tato funkce spustí vaše skripty, ve kterých se data nacházejí, a eliminuje přenos dat napříč sítí na jiný server.

Pomocí Machine Learning Services s podporou R/Pythonu ve službě Azure SQL Managed instance můžete:

- **Spouštění skriptů r a Python pro přípravu dat a zpracování dat pro obecné účely** – nyní můžete přenést skripty R/Python do spravované instance Azure SQL, kde se nachází vaše data, a nemusíte přesouvat data na jiný server, aby bylo možné spouštět skripty R a Python. Můžete eliminovat nutnost přesunu dat a související problémy související s latencí, zabezpečením a dodržováním předpisů.

- **Naučte se modely strojového učení v databázi** . modely můžete proškolit pomocí libovolných Open Source algoritmů. Můžete snadno škálovat vaše školení na celou datovou sadu a nespoléhat se na ukázkové datové sady, které jsou z databáze vycházející.

- **Nasaďte modely a skripty do produkčního prostředí v uložených procedurách** – skripty a školené modely je možné provozovat jednoduše pouhým vložením v uložených procedurách T-SQL. Aplikace, které se připojují ke spravované instanci Azure SQL, můžou využívat výhod předpovědi a Intelligence v těchto modelech pouhým voláním uložené procedury. K zprovoznění modelů pro rychlé hodnocení ve vysoce souběžných scénářích bodování v reálném čase můžete také použít nativní funkci PREDIKTIVNÍch T-SQL.

Základní distribuce Pythonu a R jsou součástí Machine Learning Services. Kromě balíčků Microsoft pro [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) a [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) pro Python a [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler), [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml), [OLAP](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)a [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) pro R můžete nainstalovat a používat Open Source balíčky a architektury, jako jsou PyTorch, TensorFlow a scikit-učení.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrace verze Preview

Tato omezená verze Public Preview podléhá [podmínkám Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Pokud se zajímáte o připojení k programu verze Preview a přijmout tyto podmínky, můžete požádat o registraci vytvořením lístku podpory Azure na adrese [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) . U možnosti "typ problému" vyberte "Technical", "služba" vyberte "SQL Database Managed instance" a pro "typ problému" vyberte "jiné". Ve vaší žádosti uveďte, že si chcete zaregistrovat omezenou verzi Public Preview služby Machine Learning pro službu SQL Managed Instance a tyto podrobnosti: název logického serveru, oblast a ID předplatného.

Po registraci do programu vás Microsoft připojí k verzi Public Preview a povolí službu Machine Learning Services pro vaši stávající nebo novou databázi.

Ve verzi Public Preview se služba Machine Learning Services ve službě SQL Managed Instance nedoporučuje pro produkční úlohy.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [klíčové rozdíly od SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Další informace o strojovém učení na jiných platformách SQL najdete v [dokumentaci ke službě SQL Machine Learning](https://docs.microsoft.com/sql/machine-learning/).
