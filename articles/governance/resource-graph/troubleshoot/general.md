---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy s dotazem na prostředky Azure pomocí Azure Resource graphu.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: e8f42650265b1ca400731365203408eeb22a4e4c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958526"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Řešení chyb pomocí Azure Resource graphu

Při dotazování na prostředky Azure pomocí Azure Resource graphu může dojít k chybám. Tento článek popisuje různé chyby, ke kterým může dojít, a jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Většina chyb je výsledkem problému při spouštění dotazu pomocí grafu prostředků Azure. Pokud se dotaz nezdařil, sada SDK poskytuje podrobnosti o neúspěšném dotazu. Tyto informace označují problém tak, aby mohl být vyřešen a pozdější dotaz bude úspěšný.

## <a name="general-errors"></a>Obecné chyby

### <a name="toomanysubscription"></a>Scénář: moc velký počet předplatných

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

### <a name="rest-contenttype"></a>Scénář: Nepodporovaná hlavička REST typu obsahu

#### <a name="issue"></a>Problém

Zákazníci, kteří se dotazují do grafu prostředků Azure REST API obdrží odpověď _500_ (interní chyba serveru).

#### <a name="cause"></a>Příčina

Graf prostředků Azure REST API podporuje jenom `Content-Type` **Application/JSON**. Některé nástroje nebo agenti REST mají výchozí hodnotu **Text/prostý**, což REST API nepodporuje.

#### <a name="resolution"></a>Řešení

Ověřte, že nástroj nebo agent, který používáte k dotazování na Azure Resource Graph, má REST API záhlaví `Content-Type` nakonfigurovaná pro **Application/JSON**.

### <a name="rest-403"></a>Scénář: žádné oprávnění ke čtení pro všechna předplatná v seznamu

#### <a name="issue"></a>Problém

Zákazníci, kteří explicitně předají seznam předplatných s dotazem na Azure Resource Graph, získají odpověď _403_ (zakázáno).

#### <a name="cause"></a>Příčina

Pokud zákazník nemá oprávnění ke čtení pro všechna poskytnutá předplatná, je žádost zamítnuta z důvodu nedostatku příslušných zabezpečovacích práv.

#### <a name="resolution"></a>Řešení

V seznamu předplatných uveďte aspoň jedno předplatné, ke kterému má zákazník, který dotaz spouští, oprávnění ke čtení. Další informace najdete v tématu [oprávnění v Azure Resource graphu](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
- Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.