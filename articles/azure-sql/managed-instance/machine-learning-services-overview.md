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
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: c5392c822c42c2e0ee3f6ce9edffc6229d572165
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782412"
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

Základní distribuce Pythonu a R jsou součástí Machine Learning Services. Kromě balíčků Microsoft pro [revoscalepy](/sql/advanced-analytics/python/ref-py-revoscalepy) a [microsoftml](/sql/advanced-analytics/python/ref-py-microsoftml) pro Python a [RevoScaleR](/sql/advanced-analytics/r/ref-r-revoscaler), [microsoftml](/sql/advanced-analytics/r/ref-r-microsoftml), [OLAP](/sql/advanced-analytics/r/ref-r-olapr)a [sqlrutils](/sql/advanced-analytics/r/ref-r-sqlrutils) pro R můžete nainstalovat a používat Open Source balíčky a architektury, jako jsou PyTorch, TensorFlow a scikit-učení.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrace verze Preview

Tato omezená verze Public Preview podléhá [podmínkám Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Pokud se zajímáte o připojení k programu verze Preview a přijmout tyto podmínky, můžete požádat o registraci vytvořením lístku podpory Azure na adrese [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. Na stránce **vytvořit lístek podpory** klikněte na **vytvořit incident** .

1. Na stránce **pomoc a podpora** klikněte na **Nová žádost o podporu** a vytvořte novou lístek.

1. Vyberte následující možnosti:
   - Typ problému – **technický**
   - Předplatné – *Vyberte své předplatné* .
   - Služba – **spravovaná instance SQL**
   - Prostředek – *Výběr spravované instance*
   - Shrnutí – *Zadejte stručný popis vaší žádosti* .
   - Typ problému – **Machine Learning Services pro spravovanou instanci SQL (Preview)**
   - Problémový podtyp – **jiný problém nebo "How to" (otázky** )

1. Klikněte na **Další: řešení** .

1. Přečtěte si informace o verzi Preview a potom klikněte na **Další: podrobnosti** .

1. Na této stránce:
   - U otázky, **kterou se pokoušíte zaregistrovat ve verzi Preview** , vyberte **Ano** . 
   - Jako **Popis** zadejte konkrétní požadavek, včetně názvu logického serveru, oblasti a ID předplatného, které chcete zaregistrovat ve verzi Preview. Podle potřeby zadejte další podrobnosti.
   - Vyberte preferovanou metodu kontaktu. 

1. Až skončíte, klikněte na **Další: Zkontrolujte + vytvořit** a pak klikněte na **vytvořit** .

Po registraci do programu vás Microsoft připojí k verzi Public Preview a povolí službu Machine Learning Services pro vaši stávající nebo novou databázi.

Ve verzi Public Preview se služba Machine Learning Services ve službě SQL Managed Instance nedoporučuje pro produkční úlohy.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [klíčové rozdíly od SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Informace o použití Pythonu v Machine Learning Services najdete v tématu [spuštění skriptů Pythonu](/sql/machine-learning/tutorials/quickstart-python-create-script?context=%252fazure%252fazure-sql%252fmanaged-instance%252fcontext%252fml-context&view=sql-server-ver15).
- Informace o použití R v Machine Learning Services najdete v tématu [spuštění skriptů r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%252fazure%252fazure-sql%252fmanaged-instance%252fcontext%252fml-context&view=sql-server-ver15).
- Další informace o strojovém učení na jiných platformách SQL najdete v [dokumentaci ke službě SQL Machine Learning](/sql/machine-learning/).