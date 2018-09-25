---
title: Správa životního cyklu úložiště Azure
description: Zjistěte, jak vytvořit životního cyklu pravidla zásad k přechodu againg data z horké na studenou a archivní úrovně.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 25e6fba6ac8aa34c0c30fd61f5fe297b94720439
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983663"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Správa životního cyklu úložiště objektů Blob v Azure (Preview)

Datové sady mají jedinečné životního cyklu. Některá data se přistupuje často v rané fázi v životní cyklus, ale potřebu přístupu výrazně sníží jak stárnou. Některá data zůstat nečinná v cloudu a je zřídka po uložení. Některá data vypršení platnosti dnů nebo měsíců po vytvoření, zatímco jiné sady dat. aktivně číst a upravovat v celé jeho životnosti. Správa životního cyklu Azure Blob Storage (Preview) nabízí bohatě vybaveným a založený na pravidlech zásad, který můžete použít se převést vaše data, aby nejlepší úroveň přístupu a vypršení platnosti dat na konci svého životního cyklu.

Zásady správy životního cyklu vám pomůže:

- Přechod přes bloby až po chladnější úrovně úložiště (horké úrovně do studené, horké do archivní, nebo > studená-> archivní) k optimalizaci výkonu a nákladů
- Odstranění objektů blob na konci jejich životního cyklu
- Definovat pravidla, která provést jednou za den na úrovni účtu úložiště (podporuje účty úložiště GPv2 a Blob)
- Použití pravidel pro kontejnery, nebo podmnožina objektů BLOB (pomocí předpony jako filtry)

Vezměte v úvahu sadu dat, která se přistupuje často během v rané fázi životního cyklu, je potřeba jenom občas za dva týdny a je zřídka a novějšími verzemi za měsíc. V tomto scénáři horkého úložiště je nejvhodnější při raných fázích studeného úložiště je nejvhodnější pro občasný přístup a úložiště archivu je nejlepší možnost úrovně po stáří dat za měsíc. Změnou úrovně úložiště z hlediska stáří dat můžete navrhnout nejlevnější možnosti úložiště pro vaše potřeby. K dosažení tohoto přechodu je, jsou k dispozici pro přesun dat stárnoucích do chladnější úrovně zásad správy životního cyklu.

## <a name="storage-account-support"></a>Podpora účtu úložiště

Zásady správy životního cyklu jsou k dispozici obě obecné účely v2 (GPv2) a účet úložiště objektů Blob. Účet pro obecné účely (GPv1) můžete převést na účet GPv2 prostřednictvím prostého jedním kliknutím na webu Azure Portal. Další informace o účtech úložiště najdete v tématu [přehled účtu Azure storage](../common/storage-account-overview.md) Další informace.  

## <a name="pricing"></a>Ceny 

Funkce správy životního cyklu je zdarma ve verzi preview. Zákazníkům se poplatky účtují náklady běžném provozu [výpis objektů blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) a [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) volání rozhraní API. Zobrazit [ceny za objekty Blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) Další informace o cenách.

## <a name="register-for-preview"></a>Zaregistrujte se ve verzi preview 
K registraci ve verzi public preview, musíte odeslat žádost o registraci této funkce do vašeho předplatného. Po schválení vaší žádosti (během pár dnů), všechny stávající i nové účty GPv2 nebo Blob Storage účet v západní části USA 2, západní USA, střed USA a západní Evropa budou mít povolenou funkci. Během období preview se podporuje jenom objekty blob bloku. Stejně jako u většiny momentálně ve verzi Preview tato funkce by neměl být používat pro produkční úlohy dokud nedosáhne obecné dostupnosti.

Odeslat žádost, spusťte následující příkazy prostředí PowerShell nebo rozhraní příkazového řádku.

### <a name="powershell"></a>PowerShell

Odeslat žádost:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Můžete zkontrolovat stav schválení registrace pomocí následujícího příkazu:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Pokud tato funkce je schválena a správně registrováno, měli byste obdržet stavu "Registrováno".

### <a name="azure-cli"></a>Azure CLI

Odeslat žádost: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Můžete zkontrolovat stav schválení registrace pomocí následujícího příkazu:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Pokud tato funkce je schválena a správně registrováno, měli byste obdržet stavu "Registrováno". 


## <a name="add-or-remove-policies"></a>Přidání nebo odebrání zásady 

Můžete přidat, upravit nebo odebrat zásadu pomocí webu Azure portal, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), [rozhraní REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/createorupdatemanagementpolicies), nebo klientskými nástroji v následujících jazycích: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Klikněte na možnost Všechny prostředky a kliknutím na účet úložiště do účtu přejděte.

3. V okně nastavení klikněte na tlačítko **správy životního cyklu** seskupené podle služby Blob Service můžete zobrazit nebo změnit zásady.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Pokud jsou povolená pravidla brány firewall pro váš účet úložiště může být zablokován požadavky správy životního cyklu. Může vám ho odblokoval zadáním výjimky. Další informace najdete v části výjimky v [Konfigurace bran firewall a virtuální sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Zásady

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


V rámci zásady jsou vyžadovány dva parametry:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| verze        | Vyjádřený jako řetězec `x.x` | Číslo verze preview je 0,5 |
| pravidla          | Pole objektů pravidlo | V každé zásadě se vyžaduje aspoň jedno pravidlo. Ve verzi preview můžete zadat až 4 pravidla podle zásad. |

V pravidle požadované parametry jsou:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| Název           | Řetězec | Název pravidla může obsahovat libovolnou kombinaci alfanumerické znaky. Název pravidla je velká a malá písmena. Musí být jedinečný v rámci zásady. |
| type           | Hodnoty výčtu | Platná hodnota pro verzi preview je `Lifecycle` |
| Definice     | Objekt, který definuje pravidlo životního cyklu | Každá definice se skládá sada filtru a skupinu akcí. |

## <a name="rules"></a>Pravidla

Každé pravidlo definice obsahuje sadu filtrů a skupinu akcí. Následující ukázka pravidla se změní na úrovni objektů BLOB bloku základní s předponou `container1/foo`. V zásadách tato pravidla jsou definovány jako:

- Úroveň objektu blob na studené úložiště 30 dnů od poslední změny
- Úroveň objektu blob do úložiště archivu 90 dnů od poslední změny
- Odstranit objekt blob 2,555 dní (7 let) po poslední změny
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

Filtry omezují akce pravidla na podmnožinu objektů BLOB v účtu úložiště. Pokud jsou definovány více filtrů, logické `AND` se provádí na všechny filtry.

Ve verzi preview platné filtry zahrnují:

| Název filtru | Typ filtru | Poznámky | Vyžaduje se |
|-------------|-------------|-------|-------------|
| blobTypes   | Pole hodnot předdefinovaných výčtu. | Pro verzi preview, pouze `blockBlob` je podporována. | Ano |
| prefixMatch | Pole řetězců u předpony, jež mají být shodovat. Řetězec předpony musí začínat znakem název kontejneru. Například, pokud všechny objekty BLOB v části "https://myaccount.blob.core.windows.net/mycontainer/mydir/..." by si měly odpovídat pro pravidlo, předpona, která je "mycontainer/slozka". | Pokud není definován, toto pravidlo platí pro všechny objekty BLOB v rámci účtu. | Ne |

### <a name="rule-actions"></a>Akce pravidla

Akce se použijí na filtrované objektů BLOB při splnění podmínky spuštění.

Ve verzi preview správu životního cyklu podporuje ovládání datových vrstev a odstranění objektu blob a odstranění snímků objektů blob. Každé pravidlo musí mít alespoň jednu akci definované na objekty BLOB nebo snímky objektů blob.

| Akce        | Základní objekt Blob                                   | Snímek      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Podpora objektů BLOB momentálně na vrstvu Hot         | Nepodporuje se |
| tierToArchive | Podpora objektů BLOB momentálně na horké nebo studené vrstvy | Nepodporuje se |
| delete        | Podporováno                                   | Podporováno     |

>[!NOTE] 
Pokud na stejný objekt blob je definován více než jednu akci, správu životního cyklu použije nejlevnější akci do objektu blob. (například akce `delete` je levnější než akce `tierToArchive`. Akce `tierToArchive` je levnější než akce `tierToCool`.)

Ve verzi preview podmínky spuštění akce podle věku. Základní objekt blob používá čas ke sledování stáří a objektů blob čas vytvoření snímku snímků používá ke sledování stáří poslední změny.

| Stav provedení akce | Hodnota podmínky | Popis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Celočíselnou hodnotu označující stáří ve dnech | Platné podmínky pro akce základní objekt blob |
| daysAfterCreationGreaterThan     | Celočíselnou hodnotu označující stáří ve dnech | Platné podmínky pro akce snímku objektu blob | 

## <a name="examples"></a>Příklady
Následující příklady ukazují, jak řešit běžné scénáře s pravidly zásad životního cyklu.

### <a name="move-aging-data-to-a-cooler-tier"></a>Splatnosti přesun dat do chladnější úrovně

Následující příklad ukazuje, jak převést objekty BLOB bloku s předponou `container1/foo` nebo `container2/bar`. Zásady přechody objekty BLOB, které nebyly upraveny během více než 30 dní do studeného úložiště a objekty BLOB nebyly změněny za 90 dní do archivní úrovně:

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

Některá data se po uložení v cloudu využívají zřídka, pokud vůbec někdy. Tato data jsou nejlepší archivaci hned po zpracování. Tyto zásady životního cyklu je nakonfigurován k archivaci dat na ingestování. Tento příklad, objekty BLOB v účtu úložiště v rámci kontejneru bloku přechody `archivecontainer` okamžitě do archivní úrovně. Okamžitý přechod provádí funguje pro objekty BLOB 0 dnů po čas poslední změny:

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

Některá data se očekává vyprší dnů nebo měsíců po vytvoření snižovaly náklady nebo v souladu s předpisy státní správy. Podle odstranění podle stáří dat je můžete do vypršení platnosti dat nastavení zásad správy životního cyklu. Následující příklad ukazuje zásadu, která odstraní všechny objekty BLOB bloku (s není zadaná žádná předpona), starší než 365 dnů.

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

## <a name="next-steps"></a>Další postup

Zjistěte, jak obnovit data po nechtěnému odstranění:

- [Obnovitelné odstranění pro objekty BLOB služby Azure Storage ](../blobs/storage-blob-soft-delete.md)
