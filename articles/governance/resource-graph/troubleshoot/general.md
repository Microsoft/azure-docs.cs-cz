---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy s dotazem na prostředky Azure pomocí Azure Resource graphu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480550"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Řešení chyb pomocí Azure Resource graphu

Při dotazování na prostředky Azure pomocí Azure Resource graphu může dojít k chybám. Tento článek popisuje různé chyby, ke kterým může dojít, a jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Většina chyb je výsledkem problému při spouštění dotazu pomocí grafu prostředků Azure. Pokud se dotaz nezdařil, sada SDK poskytuje podrobnosti o neúspěšném dotazu. Tyto informace označují problém tak, aby mohl být vyřešen a pozdější dotaz bude úspěšný.

## <a name="general-errors"></a>Obecné chyby

### <a name="toomanysubscription"></a>Případě Příliš mnoho předplatných

#### <a name="issue"></a>Problém

Zákazníci, kteří mají přístup k více než 1000 předplatným, včetně předplatných mezi klienty a [Azure Lighthouse](../../../lighthouse/overview.md), nemůžou v rámci všech předplatných načíst data v jednom volání do Azure Resource graphu.

#### <a name="cause"></a>Příčina

Azure CLI a PowerShell předávají jenom prvních 1000 předplatných do Azure Resource graphu. REST API pro Azure Resource Graph přijímá maximální počet odběrů, na kterých se má dotaz provést.

#### <a name="resolution"></a>Řešení

Dávkové zpracování požadavků pro dotaz s podmnožinou předplatných, které mají zůstat v rámci limitu předplatného 1000. Řešení používá parametr předplatného v prostředí PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.