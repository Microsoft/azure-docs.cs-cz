---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy s různými sadami SDK při dotazování na prostředky Azure pomocí Azure Resource graphu.
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0d783b9cb4fa30e3c3e0ff82536b4878c29c7a0d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916697"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Řešení chyb pomocí Azure Resource graphu

Při dotazování na prostředky Azure pomocí Azure Resource graphu může dojít k chybám. Tento článek popisuje různé chyby, ke kterým může dojít, a jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Většina chyb je výsledkem problému při spouštění dotazu pomocí grafu prostředků Azure. Pokud se dotaz nezdařil, sada SDK poskytuje podrobnosti o neúspěšném dotazu. Tyto informace označují problém tak, aby mohl být vyřešen a pozdější dotaz bude úspěšný.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>Scénář: omezené požadavky

#### <a name="issue"></a>Problém

Zákazníci, kteří provádějí rozsáhlé nebo časté dotazy na prostředky, mají omezení požadavků.

#### <a name="cause"></a>Příčina

Graf prostředků Azure přiděluje číslo kvóty pro každého uživatele na základě časového okna. Uživatel může například odeslat maximálně 15 dotazů v rámci každého 5 sekundového okna bez omezení. Hodnota kvóty je určena mnoha faktory a může se změnit. Další informace najdete v tématu [omezování v Azure Resource graphu](../overview.md#throttling).

#### <a name="resolution"></a>Řešení

Existuje několik metod, jak řešit omezené požadavky:

- [Seskupování dotazů](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [Rozložení dotazů](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [Paralelní dotazování](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [Stránkování](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scénář: moc velký počet předplatných

#### <a name="issue"></a>Problém

Zákazníci, kteří mají přístup k více než 1000 předplatným, včetně předplatných mezi klienty a [Azure Lighthouse](../../../lighthouse/overview.md), nemůžou v rámci všech předplatných načíst data v jednom volání do Azure Resource graphu.

#### <a name="cause"></a>Příčina

Azure CLI a PowerShell předávají jenom prvních 1000 předplatných do Azure Resource graphu. REST API pro Azure Resource Graph přijímá maximální počet odběrů, na kterých se má dotaz provést.

#### <a name="resolution"></a>Řešení

Dávkové zpracování požadavků pro dotaz s podmnožinou předplatných, které mají zůstat v rámci limitu předplatného 1000. Řešení používá parametr **předplatného** v prostředí PowerShell.

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

Zákazníci, kteří se dotazují do grafu prostředků Azure REST API obdrží odpověď _500_ (interní chyba serveru).

#### <a name="cause"></a>Příčina

Graf Azure Resource REST API podporuje jenom `Content-Type` **Application/JSON**. Některé nástroje nebo agenti REST mají výchozí hodnotu **Text/prostý**, což REST API nepodporuje.

#### <a name="resolution"></a>Řešení

Ověřte, že nástroj nebo agent, který používáte k dotazování na Azure Resource Graph, má záhlaví REST API `Content-Type` nakonfigurované pro **Application/JSON**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scénář: žádné oprávnění ke čtení pro všechna předplatná v seznamu

#### <a name="issue"></a>Problém

Zákazníci, kteří explicitně předají seznam předplatných s dotazem na Azure Resource Graph, získají odpověď _403_ (zakázáno).

#### <a name="cause"></a>Příčina

Pokud zákazník nemá oprávnění ke čtení pro všechna poskytnutá předplatná, je žádost zamítnuta z důvodu nedostatku příslušných zabezpečovacích práv.

#### <a name="resolution"></a>Řešení

V seznamu předplatných uveďte aspoň jedno předplatné, ke kterému má zákazník, který dotaz spouští, oprávnění ke čtení. Další informace najdete v tématu [oprávnění v Azure Resource graphu](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s nástrojem [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure s správnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.