---
title: Online zálohování a obnovení dat na vyžádání v Azure Cosmos DB.
description: Tento článek popisuje, jak funguje automatické zálohování dat na vyžádání. Vysvětluje také rozdíl mezi průběžnými a pravidelnými režimy zálohování.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525428"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online zálohování a obnovení dat na vyžádání ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB automaticky v pravidelných intervalech zálohuje vaše data. Automatické zálohování nemá vliv na výkon ani dostupnost databázových operací. Všechny zálohy se ukládají odděleně ve službě úložiště. Automatické zálohování je užitečné ve scénářích, kdy omylem odstraníte nebo aktualizujete svůj účet Azure Cosmos, databázi nebo kontejner a později potřebujete provést obnovení dat. Existují dva režimy zálohování:

* **Režim periodické zálohy** – tento režim je výchozím režimem zálohování pro všechny existující účty. V tomto režimu se zálohování provádí v pravidelných intervalech a data se obnoví tím, že se vytvoří žádost s týmem podpory. V tomto režimu nakonfigurujete interval zálohování a uchování pro svůj účet. Maximální doba uchování sahá na měsíc. Minimální interval zálohování může být jedna hodina.  Další informace najdete v článku [pravidelný režim zálohování](configure-periodic-backup-restore.md) .

* **Režim nepřetržitého zálohování** (aktuálně ve verzi Public Preview) – při vytváření účtu Azure Cosmos DB zvolíte tento režim. Tento režim vám umožní provést obnovení do libovolného časového okamžiku během posledních 30 dnů. Další informace najdete v tématu [Úvod do režimu nepřetržitého zálohování](continuous-backup-restore-introduction.md), konfigurace průběžného zálohování pomocí článků [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)a [Správce prostředků](continuous-backup-restore-template.md) .

  > [!NOTE]
  > Pokud nakonfigurujete nový účet pomocí průběžného zálohování, můžete provádět samoobslužné obnovení prostřednictvím Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud je váš účet nakonfigurovaný v nepřetržitém režimu, nemůžete ho přepnout zpátky do periodického režimu. Aktuálně existující účty s pravidelným režimem zálohování nelze změnit na režim nepřetržitý.  

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak nakonfigurovat a spravovat režimy pravidelného a nepřetržitého zálohování pro váš účet:

* [Nakonfigurujte a spravujte zásady pravidelného zálohování](configure-periodic-backup-restore.md) .
* Co je režim [nepřetržitého zálohování](continuous-backup-restore-introduction.md) ?
* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.
