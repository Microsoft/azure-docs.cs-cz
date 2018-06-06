---
title: Správa životního cyklu úložiště Azure
description: Naučte se vytvářet životního cyklu pravidla zásad na přechod againg data z horkého úložiště do vrstev nástrojů a archivu.
services: storage
author: yzheng-msft
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.openlocfilehash: b141adc9025f2f40acdfbd1f2d7f378173463956
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34804785"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Správa životního cyklu úložiště objektů Blob v Azure (Preview)

Datové sady mít jedinečný životní cykly. Některá data se využívají často časná v životním cyklu, ale nutnosti přístupu ke zahodí výrazně jako stáří data. Některá data zůstat v nečinnosti v cloudu a je přístupný zřídka jednou uložené. Některá data vypršení platnosti dnů nebo měsíců po vytvoření, zatímco jiné sady dat. aktivně přečíst a změnit v průběhu své životnosti. Správa životního cyklu Azure Blob Storage (Preview) nabízí bohatou a založený na pravidlech zásady, které můžete použít přechodu datům na nejlepší úroveň přístupu a ukončíte platnost dat na konci tohoto životního cyklu.

Zásady správy životního cyklu vám pomůže:

- Přechod objektů BLOB do chladič vrstvy úložiště (horká Cool horká archivu, nebo Cool archivu) za účelem optimalizace výkonu a nákladů
- Odstranění objektů blob na konci jejich životní cykly
- Definuje pravidla provést jednou za den na úrovni účtu úložiště (podporuje GPv2 a objektů Blob účty úložiště)
- Použití pravidel pro kontejnery nebo podmnožina objektů BLOB (s použitím předpony jako filtry)

Vezměte v úvahu sadu dat, která přistupuje často během časná fáze životního cyklu, je potřeba jenom občas po dvou týdnech a zřídka přistupuje po měsíci a nad rámec. V tomto scénáři horkého úložiště je nejvhodnější během prvních fází studeného úložiště je nejvhodnější pro občasný přístup a úložiště archivu je nejlepší možnost vrstvy po stáří dat za měsíc. Úpravou vrstvy úložiště v ohledem na stáří dat můžete navrhnout nejlevnější možnosti úložiště pro vaše potřeby. K dosažení tento přechod, jsou k dispozici pro přesun dat stárnutí do chladič vrstev zásady správy životního cyklu.

## <a name="storage-account-support"></a>Podpora účtů úložiště

Zásady správy životního cyklu je dostupná v obou obecné účely v2 (GPv2) a účet úložiště objektů Blob. Existující účet obecné účely (GPv1) můžete převést na účet GPv2 prostřednictvím jednoduchý proces jedním kliknutím na portálu Azure. Další informace najdete v tématu [Možnosti účtu úložiště Azure](../common/storage-account-options.md).  

## <a name="pricing"></a>Ceny 

Funkce správy životního cyklu je zdarma ve verzi preview. Zákazníci budou účtovat regulární provozních nákladů pro [seznamu objektů blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) a [nastavit úroveň Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) volání rozhraní API. V tématu [objekt Blob bloku ceny](https://azure.microsoft.com/pricing/details/storage/blobs/) Další informace o cenách.

## <a name="register-for-preview"></a>Registrace pro verzi preview 
K registraci ve verzi public preview, musíte odeslat žádost o registraci tuto funkci do vašeho předplatného. Potom, co vaši žádost schválíte (do několika dní), bude mít všechny stávající a nové GPv2 nebo úložiště objektů Blob v účtu v západní USA 2 a – Západ střední USA povolena funkce. Verzi Preview je podporována pouze objekt blob bloku. Stejně jako u většiny verze Preview, tato funkce se nesmí používat k produkčním prostředí dokud nedosáhne všeobecné

Odeslat žádost o, spusťte následující příkazy prostředí PowerShell nebo rozhraní příkazového řádku.

### <a name="powershell"></a>PowerShell

Odeslat žádost:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Můžete zkontrolovat stav schválení registrace pomocí následujícího příkazu:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Pokud funkci schválení a řádně zaregistrován, měli byste obdržet stavu "Registrované".

### <a name="cli-20"></a>CLI 2.0

Odeslat žádost: 
```cli
az feature register –-namespace Microsoft.Storage –-name DLM
```
Můžete zkontrolovat stav schválení registrace pomocí následujícího příkazu:
```cli
-az feature show –-namespace Microsoft.Storage –-name DLM
```
Pokud funkci schválení a řádně zaregistrován, měli byste obdržet stavu "Registrované". 


## <a name="add-or-remove-policies"></a>Přidání nebo odebrání zásady 

Můžete přidat, upravit nebo odebrat zásadu pomocí nástroje Azure portal, prostředí PowerShell, rozhraní REST API nebo klienta v těchto jazycích: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby]( https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Klikněte na možnost Všechny prostředky a kliknutím na účet úložiště do účtu přejděte.

3. V okně nastavení klikněte na tlačítko **– Správa životního cyklu** seskupené v rámci služby objektů Blob můžete zobrazit nebo změnit zásady.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Pokud povolíte pravidla brány firewall pro váš účet úložiště, může být zablokován požadavků správy životního cyklu. Můžete ji odblokovat tím, že poskytuje výjimky. Další informace najdete v tématu v části výjimky na [konfigurace brány firewall a virtuální sítě](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Zásady

K zásadě správy životního cyklu je kolekce pravidel v dokumentu JSON:

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


V rámci zásady jsou vyžadovány dva parametry:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| verze        | Vyjádřený jako řetězec `x.x` | Číslo verze preview je 0,5 |
| pravidla          | Pole objektů pravidlo | V každé zásadě je vyžadován alespoň jedno pravidlo. Verzi Preview můžete zadat až 10 pravidla podle zásad. |

Parametry, které jsou potřeba v rámci pravidla jsou:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| Název           | Řetězec | Název pravidla může obsahovat libovolnou kombinaci speciálních znaků. Název pravidla se malá a velká písmena. Musí být jedinečný v rámci zásady. |
| type           | Hodnotu výčtu | Platná hodnota pro verzi preview je `Lifecycle` |
| Definice     | Objekt, který definuje pravidlo životního cyklu | Každá definice tvoří sadu filtrů a sadu akce. |

## <a name="rules"></a>Pravidla

Každé pravidlo definice zahrnuje sadu filtrů a sadu akce. Následující ukázka pravidla upraví vrstva pro objekty BLOB bloku základní s předponou `foo`. V zásadách tato pravidla jsou definovány jako:

- Blob úrovně cool úložiště 30 dnů od poslední změny
- Úroveň objektu blob do úložiště archivu po 90 dnech od poslední změny
- Odstranit objekt blob 2,555 dní (7 let) po poslední změny
- Odstraňte snímky blob po 90 dnech od vytvoření snímku

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
          "prefixMatch": [ "foo" ]
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

## <a name="rule-filters"></a>Pravidlo filtry

Filtry omezují pravidla akce podmnožinu objektů BLOB v rámci účtu úložiště. Pokud jsou definovány více filtrů, logickou `AND` provádí na všechny filtry.

Během ve verzi preview platné filtry zahrnují:

| Název filtru | Typ filtru | Poznámky | Je vyžadován |
|-------------|-------------|-------|-------------|
| blobTypes   | Pole hodnot předdefinované výčtu. | Ve verzi preview, pouze `blockBlob` je podporována. | Ano |
| prefixMatch | Pole řetězců pro předpony, jež mají být shodovat. | Pokud není definován, toto pravidlo platí pro všechny objekty BLOB v rámci účtu. | Ne |

### <a name="rule-actions"></a>Akce pravidla

Akce platí pro filtrovaný objekty BLOB, když je splněna podmínka spuštění.

Ve verzi preview Správa životního cyklu podporuje tvorbu vrstev a odstranění objektů blob a odstranění objektů blob snímků. Každé pravidlo musí mít alespoň jednu akci definované na objekty BLOB nebo snímky objektů blob.

| Akce        | Základní objekt Blob                                   | Snímek      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Podpora objektů BLOB aktuálně na aktivní vrstvy         | Nepodporuje se |
| tierToArchive | Podpora objektů BLOB aktuálně při horká nebo nástrojů vrstvy | Nepodporuje se |
| odstraňovat        | Podporováno                                   | Podporováno     |

Ve verzi preview jsou podmínky provádění akce založené na stáří. Základní objekt blob používá čas a sledovat stáří objektu blob čas vytvoření snímku snímky používá ke sledování stáří poslední změny.

| Stav provádění akce | Hodnota podmínky | Popis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Celočíselná hodnota, která určuje dobu stáří ve dnech | Platný podmínku pro základní objekt blob akce |
| daysAfterCreationGreaterThan     | Celočíselná hodnota, která určuje dobu stáří ve dnech | Platný podmínku pro akce snímku objektů blob | 

## <a name="examples"></a>Příklady
Následující příklady ukazují, jak řešit běžné scénáře s pravidly zásad životního cyklu.

### <a name="move-aging-data-to-a-cooler-tier"></a>Přesun dat stárnutí chladič úrovně

Následující příklad ukazuje, jak přechodu objekty BLOB bloku předponu `foo` nebo `bar`. Zásady přejde objekty BLOB, které nebyly upraveny ve více než 30 dní na studených úložiště a objekty BLOB nedojde ke změně za 90 dní k vrstvě archivu:

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
            "prefixMatch": [ "foo", "bar" ]
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

### <a name="archive-data-at-ingest"></a>Archivace dat ingestování 

Některá data se po uložení v cloudu využívají zřídka, pokud vůbec někdy. Tato data je vhodné archivaci okamžitě, jakmile je konzumována. Následující životního cyklu zásad je nakonfigurovaná k archivaci dat ingestování. Tento příklad přejde objekty BLOB bloku v účtu úložiště s předponou `archive` okamžitě do vrstvou archivu. Okamžitou přechodu provádí funguje na objekty BLOB 0 dnů po čas poslední změny:

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
            "prefixMatch": [ "archive" ]
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

### <a name="expire-data-based-on-age"></a>Vypršení platnosti dat založených na stáří

Očekává se, že některá data vypršení platnosti dnů nebo měsíců po vytvoření snižovaly náklady nebo dodržovat předpisy government. K zásadě správy životního cyklu můžete nastavit tak, aby vyprší na stáří dat na základě data pomocí odstranění. Následující příklad ukazuje zásadu, která odstraňuje všechny objekty BLOB bloku (s žádná předpona zadaná) starší než 365 dní.

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

Pro data, která je upravit a získat přístup k pravidelně v průběhu své životnosti se snímky často používají ke sledování starší verze data. Můžete vytvořit zásady, které odstraní stará snímky založené na snímku stáří. Stáří snímku je určen podle vyhodnocení čas vytvoření snímku. Toto pravidlo zásad odstranění zablokovat snímky objektů blob s předponou `activeData` 90 dnů, které jsou nebo starší po vytvoření snímku.

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
            "prefixMatch": [ "activeData" ]
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

## <a name="next-steps"></a>Další postup

Zjistěte, jak obnovit data po náhodného odstranění:

- [Soft odstranění objektů BLOB služby Azure Storage ](../blobs/storage-blob-soft-delete.md)
