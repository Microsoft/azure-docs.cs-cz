---
title: Správa životního cyklu úložiště Azure
description: Naučíte se vytvářet životního cyklu pravidla zásad k datům stárnoucích přechod z horké úrovně do studené a archivní úrovní.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 284a590a484052fdb7da2f03c6155078268b2aac
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211440"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Správa úložiště objektů Blob v Azure životního cyklu (Preview)

Datové sady mají jedinečné životního cyklu. V rané fázi životního cyklu přístup lidé často nějaká data. Ale výrazně jak stárnou zahodí potřebu přístupu. Některá data zůstanou nečinnosti v cloudu a je zřídka po uložení. Některá data vypršení platnosti dnů nebo měsíců po jeho vytvoření, zatímco jiné sady dat. aktivně číst a upravovat v celé jeho životnosti. Azure Blob storage životního cyklu správy (Preview) nabízí bohatě vybaveným a podle pravidel zásad pro účty úložiště GPv2 a Blob. Přenést data s úrovní odpovídající přístup nebo po ukončení cyklu dat pomocí zásad.

Zásady správy životního cyklu vám umožní:

- Objekty BLOB do chladnější úrovně úložiště (hot do studené, horké do archivní nebo > studená-> archivní) přechod k optimalizaci výkonu a nákladů
- Odstranění objektů blob na konci jejich životního cyklu
- Definování pravidel ke spuštění jednou za den na úrovni účtu úložiště
- Použití pravidel pro kontejnery, nebo podmnožina objektů BLOB (pomocí předpony jako filtry)

Vezměte v úvahu scénář, kde datové sady často přístup získá během raných fázích životního cyklu, ale pak jenom občas po dvou týdnech. Po prvním měsíci je zřídka datové sady. V tomto scénáři je úložiště s vrstvami hot nejlepší během raných fázích. Úložiště Cool je nejvhodnější pro občasný přístup, a úložiště archivu je nejlepší možnost úrovně po stáří dat za měsíc. Změnou úrovně úložiště z hlediska stáří dat můžete navrhnout nejlevnější možnosti úložiště pro vaše potřeby. K dosažení tohoto přechodu je, jsou k dispozici pro přesun dat stárnoucích do chladnější úrovně pravidla zásad správy životního cyklu.

## <a name="storage-account-support"></a>Podpora účtu úložiště

Zásady správy životního cyklu je k dispozici obě obecné účely v2 (GPv2) účty a účty Blob storage. Na webu Azure Portal můžete upgradovat stávající účet pro obecné účely (GPv1) na účet GPv2 prostřednictvím prostého jedním kliknutím. Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](../common/storage-account-overview.md).  

## <a name="pricing"></a>Ceny 

Funkce správy životního cyklu je zdarma ve verzi preview. Zákazníkům se poplatky účtují náklady běžném provozu [výpis objektů blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) a [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) volání rozhraní API. Další informace o cenách najdete v tématu [ceny za objekty Blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Zaregistrujte se ve verzi preview 
K registraci ve verzi public preview, budete muset odeslat žádost o registraci této funkce do vašeho předplatného. Požadavky jsou obvykle schválit během 72 hodin. Po schválení se zahrnout všechny stávající i nové účty GPv2 nebo Blob účty úložiště v těchto oblastech funkci: Západní USA 2, střed USA – Západ, USA – východ 2 a západní Evropa. Ve verzi Preview podporuje jenom objekty blob bloku. Stejně jako u většiny momentálně ve verzi Preview můžete tuto funkci neměli používat pro produkční úlohy dokud nedosáhne obecné dostupnosti.

Odeslat žádost, spusťte následující příkazy prostředí PowerShell nebo rozhraní příkazového řádku.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Odeslat žádost:

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Můžete zkontrolovat stav schválení registrace pomocí následujícího příkazu:
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Schválení a správnou registraci, zobrazí *registrované* stavu odeslání předchozích žádostech.

### <a name="azure-cli"></a>Azure CLI

Odeslat žádost: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Můžete zkontrolovat stav schválení registrace pomocí následujícího příkazu:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Schválení a správnou registraci, zobrazí *registrované* stavu odeslání předchozích žádostech.


## <a name="add-or-remove-a-policy"></a>Přidání nebo odebrání zásady 

Můžete přidat, upravit nebo odebrat zásadu pomocí webu Azure portal, [PowerShell](https://www.powershellgallery.com/packages/Az.Storage), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), [rozhraní REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate), nebo klientskými nástroji v následujících jazycích: [.NET ](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny prostředky** a pak vyberte svůj účet úložiště.

3. Vyberte **správy životního cyklu (preview)** seskupené podle služby Blob Service můžete zobrazit nebo změnit zásady.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
Pokud jsou povolená pravidla brány firewall pro váš účet úložiště může být zablokován požadavky správy životního cyklu. Tyto požadavky můžete odblokovat zadáním výjimky. Další informace najdete v části výjimky v [Konfigurace bran firewall a virtuální sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Zásada

Zásady správy životního cyklu je kolekce pravidel v dokumentu JSON:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
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


Zásada vyžaduje dva parametry:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| version        | Vyjádřený jako řetězec `x.x` | Číslo verze preview je 0,5. |
| pravidla          | Pole objektů pravidlo | V každé zásadě se vyžaduje aspoň jedno pravidlo. Ve verzi preview můžete zadat až 4 pravidla podle zásad. |

Každé pravidlo v rámci zásad vyžaduje tři parametry:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| Název           | String | Název pravidla může obsahovat libovolnou kombinaci alfanumerických znaků. Název pravidla je velká a malá písmena. Musí být jedinečný v rámci zásady. |
| type           | Hodnoty výčtu | Platná hodnota pro verzi preview je `Lifecycle`. |
| Definice     | Objekt, který definuje pravidlo životního cyklu | Každá definice se skládá sada filtru a skupinu akcí. |

## <a name="rules"></a>Pravidla

Každé pravidlo definice obsahuje sadu filtrů a skupinu akcí. [Filtrování sady](#rule-filters) omezuje akce pravidla pro určitou sadu objektů v rámci kontejneru nebo objektů, názvy. [Sadu akcí](#rule-actions) se vztahuje na úrovni nebo odstranit některé akce na filtrovanou sadu objektů.

### <a name="sample-rule"></a>Ukázka pravidla
Následující ukázkové pravidlo filtruje účet, který chcete spustit pouze na akce `container1/foo`. Spusťte následující akce pro všechny objekty, které existují v `container1` **a** začíná `foo`: 

- Úroveň objektu blob na studenou úroveň 30 dnů od poslední změny
- Úroveň objektu blob do archivní úrovně 90 dnů od poslední změny
- Odstranit objekt blob 2,555 dnů (sedm let) po poslední změny
- Odstranit snímky objektů blob 90 dnů po vytvoření snímku

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
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

Ve verzi preview platné filtry zahrnují:

| Název filtru | Typ filtru | Poznámky | Vyžaduje se |
|-------------|-------------|-------|-------------|
| blobTypes   | Pole hodnot předdefinovaných výčtu. | Vydání verze preview podporuje pouze `blockBlob`. | Ano |
| prefixMatch | Pole řetězců u předpony, jež mají být shodovat. Řetězec předpony musí začínat znakem název kontejneru. Například, pokud chcete najít všechny objekty BLOB v části "https://myaccount.blob.core.windows.net/container1/foo/..." pro pravidlo, je prefixMatch `container1/foo`. | Pokud nebudete definovat prefixMatch, pravidla se vztahují na všechny objekty BLOB v rámci účtu. | Ne |

### <a name="rule-actions"></a>Akce pravidla

Akce se použijí na filtrované objektů BLOB při splnění podmínky spuštění.

Ve verzi preview podporuje správu životního cyklu vrstvení a odstraňování objektů BLOB a odstranění snímků objektů blob. Definujte aspoň jednu akci u jednotlivých pravidel pro objekty BLOB nebo snímky objektů blob.

| Akce        | Základní objekt Blob                                   | Snímek      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Podpora objektů BLOB momentálně na vrstvu hot         | Nepodporuje se |
| tierToArchive | Podpora objektů BLOB momentálně na horké nebo studené úrovni | Nepodporuje se |
| delete        | Podporováno                                   | Podporováno     |

>[!NOTE] 
Pokud definujete více než jednu akci na stejný objekt blob, správu životního cyklu použije nejlevnější akci do objektu blob. Například akce `delete` je levnější než akce `tierToArchive`. Akce `tierToArchive` je levnější než akce `tierToCool`.

Ve verzi preview podmínky spuštění akce podle věku. Základní objekty BLOB pomocí čas poslední změny můžete sledovat stáří a objektů blob pomocí snímků čas vytvoření snímku ke sledování stáří.

| Stav provedení akce | Hodnota podmínky | Popis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Celočíselnou hodnotu označující stáří ve dnech | Platné podmínky pro akce základní objekt blob |
| daysAfterCreationGreaterThan     | Celočíselnou hodnotu označující stáří ve dnech | Platné podmínky pro akce snímku objektu blob | 

## <a name="examples"></a>Příklady
Následující příklady ukazují, jak řešit běžné scénáře s pravidly zásad životního cyklu.

### <a name="move-aging-data-to-a-cooler-tier"></a>Splatnosti přesun dat do chladnější úrovně

Tento příklad ukazuje, jak převést objekty BLOB bloku s předponou `container1/foo` nebo `container2/bar`. Zásady přechody objekty BLOB, které nebyly upraveny během více než 30 dní do studeného úložiště a objekty BLOB nebyly změněny za 90 dní do archivní úrovně:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Některá data zůstává nečinnosti v cloudu a je jen zřídka, pokud vůbec někdy jednou uložená. Archivujte data ihned po je jejich ingestování. Tyto zásady životního cyklu je nakonfigurován k archivaci dat na ingestování. Tento příklad, objekty BLOB v účtu úložiště v rámci kontejneru bloku přechody `archivecontainer` okamžitě do archivní úrovně. Okamžitý přechod provádí funguje pro objekty BLOB 0 dnů po čas poslední změny:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>Časté otázky – jsem vytvořil nové zásady, proč nejsou akce okamžité spuštění? 

Platforma se spustí jednou denně zásady životního cyklu. Jakmile jednou nastavíte nové zásady, může trvat až 24 hodin pro některé akce (například vrstvení a odstraňování) a spustíte.  

## <a name="next-steps"></a>Další postup

Zjistěte, jak obnovit data po nechtěnému odstranění:

- [Obnovitelné odstranění pro objekty BLOB služby Azure Storage ](../blobs/storage-blob-soft-delete.md)
