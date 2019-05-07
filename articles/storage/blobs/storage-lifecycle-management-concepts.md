---
title: Správa životního cyklu Azure Storage
description: Naučíte se vytvářet životního cyklu pravidla zásad k datům stárnoucích přechod z horké úrovně do studené a archivní úrovní.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 4/29/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: 130eb9cc8bec4681f5c0d165735c6c3b2357576c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148325"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Správa životního cyklu úložiště objektů Blob v Azure

Datové sady mají jedinečné životního cyklu. V rané fázi životního cyklu přístup lidé často nějaká data. Ale výrazně jak stárnou zahodí potřebu přístupu. Některá data zůstanou nečinnosti v cloudu a je zřídka po uložení. Některá data vypršení platnosti dnů nebo měsíců po jeho vytvoření, zatímco jiné sady dat. aktivně číst a upravovat v celé jeho životnosti. Azure správy životního cyklu úložiště objektů Blob nabízí bohatě vybaveným a podle pravidel zásad pro účty úložiště GPv2 a Blob. Přenést data s úrovní odpovídající přístup nebo po ukončení cyklu dat pomocí zásad.

Zásady správy životního cyklu vám umožní:

- Objekty BLOB do chladnější úrovně úložiště (hot do studené, horké do archivní nebo > studená-> archivní) přechod k optimalizaci výkonu a nákladů
- Odstranění objektů blob na konci jejich životního cyklu
- Definování pravidel ke spuštění jednou za den na úrovni účtu úložiště
- Použití pravidel pro kontejnery, nebo podmnožina objektů BLOB (pomocí předpony jako filtry)

Vezměte v úvahu scénář, kde datové sady často přístup získá během raných fázích životního cyklu, ale pak jenom občas po dvou týdnech. Po prvním měsíci je zřídka datové sady. V tomto scénáři je úložiště s vrstvami hot nejlepší během raných fázích. Úložiště Cool je nejvhodnější pro občasný přístup, a úložiště archivu je nejlepší možnost úrovně po stáří dat za měsíc. Změnou úrovně úložiště z hlediska stáří dat můžete navrhnout nejlevnější možnosti úložiště pro vaše potřeby. K dosažení tohoto přechodu je, jsou k dispozici pro přesun dat stárnoucích do chladnější úrovně pravidla zásad správy životního cyklu.

## <a name="storage-account-support"></a>Podpora účtu úložiště

Zásady správy životního cyklu je k dispozici obě obecné účely v2 (GPv2) účty a účty Blob storage. Na webu Azure Portal můžete upgradovat stávající účet pro obecné účely (GPv1) na účet GPv2 prostřednictvím prostého jedním kliknutím. Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](../common/storage-account-overview.md).  

## <a name="pricing"></a>Ceny 

Funkce správy životního cyklu je zdarma. Zákazníkům se poplatky účtují náklady běžném provozu [výpis objektů blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) a [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) volání rozhraní API. Operace odstranění je zdarma. Další informace o cenách najdete v tématu [ceny za objekty Blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Regionální dostupnost 
Funkce správy životního cyklu je k dispozici ve všech veřejných oblastech Azure. 


## <a name="add-or-remove-a-policy"></a>Přidání nebo odebrání zásady 

Můžete přidat, upravit nebo odebrat zásadu pomocí webu Azure portal, [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases), rozhraní příkazového řádku Azure [rozhraní REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies), nebo klientský nástroj. Tento článek popisuje, jak ke správě zásad pomocí portálu a metod prostředí PowerShell.  

> [!NOTE]
> Pokud jsou povolená pravidla brány firewall pro váš účet úložiště může být zablokován požadavky správy životního cyklu. Tyto požadavky můžete odblokovat zadáním výjimky. Jsou požadované jednorázového přihlášení: `Logging,  Metrics,  AzureServices`. Další informace najdete v části výjimky v [Konfigurace bran firewall a virtuální sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny prostředky** a pak vyberte svůj účet úložiště.

3. V části **služby Blob Service**vyberte **správy životního cyklu** zobrazení nebo změna zásady.

### <a name="powershell"></a>PowerShell

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery 

#Create a new action object

$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd 

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy 
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1

```
## <a name="arm-template-with-lifecycle-management-policy"></a>Šablony ARM pomocí zásad správy životního cyklu

Můžete definovat a nasazovat správy životního cyklu jako součást nasazení řešení Azure pomocí šablon ARM. Dále je ukázka šablony nasazení účtu úložiště RA-GRS GPv2 pomocí zásad správy životního cyklu. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Zásada

Zásady správy životního cyklu je kolekce pravidel v dokumentu JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


Kolekce pravidel se zásada:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| pravidla          | Pole objektů pravidlo | V zásadách se vyžaduje aspoň jedno pravidlo. V zásadách můžete definovat pravidla až 100.|

Každé pravidlo v rámci zásady má několik parametrů:

| Název parametru | Typ parametru | Poznámky | Požaduje se |
|----------------|----------------|-------|----------|
| jméno           | String |Název pravidla může obsahovat až 256 znaků. Název pravidla je velká a malá písmena.  Musí být jedinečný v rámci zásady. | True |
| enabled | Boolean | Nepovinný datový typ boolean Povolit pravidlo, které se dočasné zakázán. Výchozí hodnota je hodnota true, pokud není nastaven. | False | 
| type           | Hodnoty výčtu | Je aktuální platný typ `Lifecycle`. | True |
| Definice     | Objekt, který definuje pravidlo životního cyklu | Každá definice se skládá sada filtru a skupinu akcí. | True |

## <a name="rules"></a>Pravidla

Každé pravidlo definice obsahuje sadu filtrů a skupinu akcí. [Filtrování sady](#rule-filters) omezuje akce pravidla pro určitou sadu objektů v rámci kontejneru nebo objektů, názvy. [Sadu akcí](#rule-actions) se vztahuje na úrovni nebo odstranit některé akce na filtrovanou sadu objektů.

### <a name="sample-rule"></a>Ukázka pravidla
Následující ukázkové pravidlo filtruje účet, který chcete spustit akce na objektech, které existují uvnitř `container1` **a** začínat `foo`.  

- Úroveň objektu blob na studenou úroveň 30 dnů od poslední změny
- Úroveň objektu blob do archivní úrovně 90 dnů od poslední změny
- Odstranit objekt blob 2,555 dnů (sedm let) po poslední změny
- Odstranit snímky objektů blob 90 dnů po vytvoření snímku

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>Pravidlo filtry

Filtry omezují akce pravidla na podmnožinu objektů BLOB v účtu úložiště. Pokud je definován více než jeden filtr, logické `AND` běží na všech filtrů.

Platné filtry zahrnují:

| Název filtru | Typ filtru | Poznámky | Je požadováno |
|-------------|-------------|-------|-------------|
| blobTypes   | Pole hodnot předdefinovaných výčtu. | Aktuální verze podporuje `blockBlob`. | Ano |
| prefixMatch | Pole řetězců u předpony, jež mají být shodovat. Každé pravidlo můžete definovat až 10 předpony. Řetězec předpony musí začínat znakem název kontejneru. Například, pokud chcete najít všechny objekty BLOB v části "https://myaccount.blob.core.windows.net/container1/foo/..." pro pravidlo, je prefixMatch `container1/foo`. | Pokud nebudete definovat prefixMatch, pravidlo platí pro všechny objekty BLOB v účtu úložiště.  | Ne |

### <a name="rule-actions"></a>Akce pravidla

Akce se použijí na filtrované objektů BLOB při spuštění podmínka je splněna.

Správa životního cyklu podporuje vrstvení a odstraňování objektů BLOB a odstranění snímků objektů blob. Definujte aspoň jednu akci u jednotlivých pravidel pro objekty BLOB nebo snímky objektů blob.

| Akce        | Základní objekt Blob                                   | Snímek      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Podpora objektů BLOB momentálně na vrstvu hot         | Nepodporuje se |
| tierToArchive | Podpora objektů BLOB momentálně na horké nebo studené úrovni | Nepodporuje se |
| delete        | Podporováno                                   | Podporováno     |

>[!NOTE] 
>Pokud definujete více než jednu akci na stejný objekt blob, správu životního cyklu použije nejlevnější akci do objektu blob. Například akce `delete` je levnější než akce `tierToArchive`. Akce `tierToArchive` je levnější než akce `tierToCool`.

Spuštění podmínky jsou založeny na stáří. Základní objekty BLOB pomocí čas poslední změny můžete sledovat stáří a objektů blob pomocí snímků čas vytvoření snímku ke sledování stáří.

| Akce spuštění podmínku | Hodnota podmínky | Popis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Celočíselnou hodnotu označující stáří ve dnech | Platné podmínky pro akce základní objekt blob |
| daysAfterCreationGreaterThan     | Celočíselnou hodnotu označující stáří ve dnech | Platné podmínky pro akce snímku objektu blob | 

## <a name="examples"></a>Příklady
Následující příklady ukazují, jak řešit běžné scénáře s pravidly zásad životního cyklu.

### <a name="move-aging-data-to-a-cooler-tier"></a>Splatnosti přesun dat do chladnější úrovně

Tento příklad ukazuje, jak převést objekty BLOB bloku s předponou `container1/foo` nebo `container2/bar`. Zásady přechody objekty BLOB, které nebyly upraveny během více než 30 dní do studeného úložiště a objekty BLOB nebyly změněny za 90 dní do archivní úrovně:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Archivovat data v ingestu 

Některá data zůstává nečinnosti v cloudu a je jen zřídka, pokud vůbec někdy jednou uložená. Tyto zásady životního cyklu je nakonfigurován k archivaci dat po zpracování. Tento příklad, objekty BLOB v účtu úložiště v rámci kontejneru bloku přechody `archivecontainer` do archivní úrovně. Přechodu lze dosáhnout funguje pro objekty BLOB 0 dnů po čas poslední změny:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Vypršení platnosti dat podle věku

Některá data se očekává vyprší dnů nebo měsíců po vytvoření snižovaly náklady nebo splňovat požadavky státní správy. Můžete nakonfigurovat zásady správy životního cyklu vypršení platnosti dat v odstranění podle stáří dat. Následující příklad ukazuje zásadu, která odstraní všechny objekty BLOB bloku (s není zadaná žádná předpona), starší než 365 dnů.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Odstranit staré snímky

Pro data, která upraví a získat přístup k pravidelně v průběhu svého životního cyklu se snímky často používají ke sledování starší verze data. Můžete vytvořit zásadu, která odstraní staré snímky podle věku snímku. Stáří snímku se určuje podle hodnocení čas vytvoření snímku. Toto pravidlo zásad odstraňování zablokovat snímky objektů blob v kontejneru `activedata` , které jsou 90 dní nebo starší po vytvoření snímku.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",      
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```
## <a name="faq"></a>Nejčastější dotazy 
**Můžu vytvořit novou zásadu, proč nejsou akce okamžité spuštění?**  
Platforma se spustí jednou denně zásady životního cyklu. Jakmile nakonfigurujete zásadu, může trvat až 24 hodin pro některé akce (například vrstvení a odstraňování) ke spuštění poprvé.  

## <a name="next-steps"></a>Další postup

Zjistěte, jak obnovit data po nechtěnému odstranění:

- [Obnovitelné odstranění pro objekty BLOB služby Azure Storage](../blobs/storage-blob-soft-delete.md)
