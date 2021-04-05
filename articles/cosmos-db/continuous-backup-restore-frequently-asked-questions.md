---
title: Nejčastější dotazy týkající se funkce obnovení Azure Cosmos DBho bodu v čase.
description: V tomto článku jsou uvedeny nejčastější dotazy k funkci obnovení Azure Cosmos DBho bodu v čase, která se dosahuje pomocí režimu průběžné zálohování.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393220"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Nejčastější dotazy k funkci obnovení Azure Cosmos DBho bodu v čase (Preview)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto článku jsou uvedené Nejčastější dotazy týkající se funkcí obnovení Azure Cosmos DBho bodu v čase (Preview), které se dosahují pomocí režimu průběžné zálohování.

## <a name="how-much-time-does-it-takes-to-restore"></a>Jak dlouho trvá obnovení?
Doba obnovení závisí na velikosti vašich dat.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Můžu čas obnovení odeslat v místním čase?
K obnovení nemusí dojít v závislosti na tom, jestli v daném čase existovaly klíčové prostředky, jako jsou databáze nebo kontejnery. Můžete ověřit zadáním času a prohlížením vybrané databáze nebo kontejneru pro daný čas. Pokud se nezobrazí žádné prostředky k obnovení, proces obnovení nefunguje.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Jak se dá sledovat, jestli se účet obnovuje?
Po odeslání příkazu pro obnovení a po dokončení operace počkat na stejnou stránku se v stavovém řádku zobrazí úspěšně obnovená zpráva účtu. Můžete také vyhledat obnovený účet a [sledovat stav obnovy účtu](continuous-backup-restore-portal.md#track-restore-status). Během obnovování se stav *účtu vytvoří po* dokončení operace obnovení, stav účtu se změní na *online*.

Podobně jako PowerShell a rozhraní příkazového řádku můžete sledovat průběh operace obnovení spuštěním příkazu následujícím `az cosmosdb show` způsobem:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

ProvisioningState *se zobrazí v případě, že je* účet online.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Jak zjistím, jestli se účet obnovil z jiného účtu?
Spusťte `az cosmosdb show` příkaz ve výstupu, uvidíte, že hodnota `createMode` Vlastnosti. Pokud je hodnota nastavená na **Restore (obnovit**). indikuje, že se účet obnovil z jiného účtu. `restoreParameters`Vlastnost obsahuje další podrobnosti, jako například `restoreSource` , který má ID zdrojového účtu. Poslední identifikátor GUID v `restoreSource` parametru je InstanceId zdrojového účtu.

Například v následujícím výstupu je ID instance zdrojového účtu *7b4bb-f6a0-430e-ade1-638d781830cc*

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Co se stane po obnovení sdílené databáze propustnosti nebo kontejneru v rámci sdílené databáze propustnosti?
Celá sdílená databáze propustnosti se obnoví. Pro obnovení nelze vybrat podmnožinu kontejnerů ve sdílené databázi propustnosti.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Jaké je použití InstanceID v definici účtu?
V jakémkoli daném časovém okamžiku je vlastnost účtu Azure Cosmos DB `accountName` globálně jedinečná, zatímco je aktivní. Po odstranění účtu je však možné vytvořit jiný účet se stejným názvem, a proto "Account" již není dostačující k identifikaci instance účtu. 

ID nebo `instanceId` je vlastnost instance účtu, která se používá k jednoznačnému rozlišení mezi několika účty (živé a odstraněné), pokud mají stejný název pro obnovení. ID instance můžete získat spuštěním `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` příkazů nebo. Hodnota atributu Name označuje "InstanceID".

## <a name="next-steps"></a>Další kroky

* Co je režim [nepřetržitého zálohování](continuous-backup-restore-introduction.md) ?
* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)