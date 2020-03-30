---
title: Odstraňování běžných chyb
description: Zjistěte, jak řešit problémy s různými sadami SDK při dotazování prostředků Azure pomocí Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303898"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Poradce při potížích pomocí Azure Resource Graph

Můžete narazit na chyby při dotazování prostředků Azure pomocí Azure Resource Graph. Tento článek popisuje různé chyby, které mohou nastat a jak je vyřešit.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Většina chyb je výsledkem problému při spuštění dotazu pomocí Azure Resource Graph. Pokud se dotaz nezdaří, sada SDK poskytuje podrobnosti o neúspěšném dotazu. Tyto informace označují problém tak, aby jej lze opravit a pozdější dotaz úspěšné.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scénář: Příliš mnoho předplatných

#### <a name="issue"></a>Problém

Zákazníci s přístupem k více než 1000 předplatným, včetně předplatných napříč tenanty s [Azure Lighthouse](../../../lighthouse/overview.md), nemohou načíst data napříč všemi předplatnými v jediném volání do Azure Resource Graph.

#### <a name="cause"></a>Příčina

Azure CLI a PowerShell předávají jenom prvních 1000 předplatných do Azure Resource Graph. Rozhraní REST API pro Azure Resource Graph přijímá maximální počet předplatných k provedení dotazu.

#### <a name="resolution"></a>Řešení

Dávkové požadavky na dotaz s podmnožinou odběrů, aby zůstaly pod limitem 1000 předplatného. Řešení používá parametr **Subscription** v PowerShellu.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scénář: Nepodporovaná hlavička REST typu obsahu

#### <a name="issue"></a>Problém

Zákazníci dotazující se rozhraní REST rozhraní AZURE Resource Graph získají vrácenou odpověď _500_ (vnitřní chyba serveru).

#### <a name="cause"></a>Příčina

Rozhraní API REST grafu prostředků `Content-Type` Azure podporuje jenom **aplikace/json**. Některé nástroje REST nebo agenti výchozí **text/plain**, který není podporován rozhraní MATROZHRANÍ API.

#### <a name="resolution"></a>Řešení

Ověřte, zda nástroj nebo agent, který používáte k `Content-Type` dotazování na Azure Resource Graph, má hlavičku rozhraní REST API nakonfigurovanou pro **aplikaci/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scénář: Žádné oprávnění ke čtení pro všechna předplatná v seznamu

#### <a name="issue"></a>Problém

Zákazníci, kteří explicitně předat seznam předplatných s dotazem Azure Resource Graph získat _odpověď 403_ (Zakázáno).

#### <a name="cause"></a>Příčina

Pokud zákazník nemá oprávnění ke čtení všech poskytnutých předplatných, je žádost zamítnuta z důvodu nedostatku příslušných práv na zabezpečení.

#### <a name="resolution"></a>Řešení

Do seznamu odběrů zahrňte alespoň jedno předplatné, ke kterému má zákazník, který dotaz spouštěný dotaz, alespoň pro čtení. Další informace najdete [v tématu Oprávnění v Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.