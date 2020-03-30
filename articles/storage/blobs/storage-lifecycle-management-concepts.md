---
title: Správa životního cyklu Azure Storage
description: Přečtěte si, jak vytvořit pravidla zásad životního cyklu pro přechod dat stárnutí z horkých na studené a archivní úrovně.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598302"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Správa životního cyklu úložiště objektů blob v Azure

Datové sady mají jedinečné životní cykly. V rané fázi životního cyklu lidé často přistupují k některým datům. Ale potřeba přístupu drasticky klesá, jak data stárne. Některá data zůstávají nečinná v cloudu a je zřídka přístupná po uložení. Některá data vyprší dny nebo měsíce po vytvoření, zatímco jiné sady dat jsou aktivně číst a upravovat po celou dobu jejich životnosti. Správa životního cyklu úložiště objektů blob Azure nabízí bohaté zásady založené na pravidlech pro účty úložiště GPv2 a Blob. Pomocí zásad můžete data převést na příslušné úrovně přístupu nebo vyprší jejich platnost na konci životního cyklu dat.

Zásady správy životního cyklu umožňují:

- Přechod objektů BLOB na úroveň úložiště chladiče (horké až studené, horké do archivu nebo chladné do archivu) pro optimalizaci výkonu a nákladů
- Odstranění objektů BLOB na konci jejich životního cyklu
- Definujte pravidla, která mají být spouštěna jednou denně na úrovni účtu úložiště
- Použití pravidel pro kontejnery nebo podmnožinu objektů BLOB (použití předpon jako filtrů)

Zvažte scénář, kdy data získají častým přístupem v raných fázích životního cyklu, ale jen příležitostně po dvou týdnech. Po uplynutí prvního měsíce je k datové sadě zřídka přístupná. V tomto scénáři horké úložiště je nejlepší v počátečních fázích. Chladné úložiště je nejvhodnější pro příležitostný přístup. Archivní úložiště je nejlepší možnost úrovně po stáří dat přes měsíc. Úpravou úrovní úložiště s ohledem na stáří dat můžete navrhnout nejlevnější možnosti úložiště pro vaše potřeby. K dosažení tohoto přechodu jsou k dispozici pravidla zásad správy životního cyklu pro přesun dat stárnutí do chladnějších úrovní.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Podpora účtu úložiště

Zásady správy životního cyklu jsou k dispozici s účty GPv2 (General Purpose v2), účty úložiště objektů Blob a účty úložiště objektů Blob premium block. Na webu Azure Portal můžete upgradovat existující účet obecného použití (GPv1) na účet GPv2. Další informace o účtech úložiště najdete v tématu [Přehled účtu úložiště Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Ceny

Funkce správy životního cyklu je zdarma. Zákazníkům se účtují běžné provozní náklady na volání [objektů BLOB seznamu](https://docs.microsoft.com/rest/api/storageservices/list-blobs) a nastavení rozhraní [API úrovně objektů blob.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) Operace odstranění je zdarma. Další informace o cenách najdete v [tématu Blokování cen objektů blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Regionální dostupnost

Funkce správy životního cyklu je dostupná ve všech oblastech Azure.

## <a name="add-or-remove-a-policy"></a>Přidání nebo odebrání zásady

Zásadu můžete přidat, upravit nebo odebrat pomocí některé z následujících metod:

* [Portál Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Rozhraní REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Zásadu lze číst nebo zapisovat v plném rozsahu. Částečné aktualizace nejsou podporovány. 

> [!NOTE]
> Pokud pro svůj účet úložiště povolíte pravidla brány firewall, mohou být požadavky na správu životního cyklu blokovány. Tyto požadavky můžete odblokovat poskytnutím výjimek pro důvěryhodné služby společnosti Microsoft. Další informace naleznete v části Výjimky v [tématu Konfigurace bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Tento článek ukazuje, jak spravovat zásady pomocí portálu a PowerShell metody.  

# <a name="portal"></a>[Portál](#tab/azure-portal)

Zásady můžete přidat prostřednictvím portálu Azure dvěma způsoby. 

* [Zobrazení seznamu portálu Azure](#azure-portal-list-view)
* [Zobrazení kódu portálu Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Zobrazení seznamu portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Na webu Azure Portal vyhledejte a vyberte svůj účet úložiště. 

3. V části **Blob Service**vyberte **Správa životního cyklu,** chcete-li zobrazit nebo změnit pravidla.

4. Vyberte kartu **Zobrazení seznamu.**

5. Vyberte **Přidat pravidlo** a vyplňte pole formuláře **sada akcí.** V následujícím příkladu objekty BLOB jsou přesunuty do chladnéúložiště, pokud nebyly změněny po dobu 30 dnů.

   ![Stránka nastavení akce správy životního cyklu na webu Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Chcete-li přidat volitelný filtr, vyberte **nastavit filtr.** Potom vyberte **Procházet** a určete kontejner a složku, podle kterých chcete filtrovat.

   ![Stránka nastavení filtru správy životního cyklu na webu Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Chcete-li zkontrolovat nastavení zásad, vyberte **možnost Zkontrolovat + přidat.**

9. Chcete-li přidat novou zásadu, vyberte **Přidat.**

#### <a name="azure-portal-code-view"></a>Zobrazení kódu portálu Azure
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Na webu Azure Portal vyhledejte a vyberte svůj účet úložiště.

3. V části **Blob Service**vyberte **Správa životního cyklu,** chcete-li zobrazit nebo změnit zásady.

4. Následující JSON je příkladem zásady, kterou lze vložit do karty **Zobrazení kódu.**

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

5. Vyberte **Uložit**.

6. Další informace o tomto příkladu JSON naleznete v části [Zásady](#policy) a [pravidla.](#rules)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Následující skript Prostředí PowerShell lze použít k přidání zásady do účtu úložiště. Proměnná `$rgname` musí být inicializována s názvem skupiny prostředků. Proměnná `$accountName` musí být inicializována s názvem účtu úložiště.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

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

# <a name="template"></a>[Šablony](#tab/template)

Správu životního cyklu můžete definovat pomocí šablon Azure Resource Manager. Tady je ukázková šablona pro nasazení účtu úložiště RA-GRS GPv2 se zásadami správy životního cyklu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

---

## <a name="policy"></a>Zásada

Zásady správy životního cyklu jsou kolekcí pravidel v dokumentu JSON:

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

Zásada je kolekce pravidel:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| `rules`        | Pole objektů pravidel | V zásadě je vyžadováno alespoň jedno pravidlo. V zásadě můžete definovat až 100 pravidel.|

Každé pravidlo v rámci zásady má několik parametrů:

| Název parametru | Typ parametru | Poznámky | Požaduje se |
|----------------|----------------|-------|----------|
| `name`         | Řetězec |Název pravidla může obsahovat až 256 alfanumerických znaků. Název pravidla rozlišuje malá a velká písmena.  Musí být jedinečný v rámci zásady. | True |
| `enabled`      | Logická hodnota | Volitelné logické hodnoty, které umožňují dočasné zakázání pravidla. Výchozí hodnota je true, pokud není nastavena. | False | 
| `type`         | Hodnota výčtu | Aktuální platný typ `Lifecycle`je . | True |
| `definition`   | Objekt, který definuje pravidlo životního cyklu | Každá definice se skládá ze sady filtrů a sady akcí. | True |

## <a name="rules"></a>Pravidla

Každá definice pravidla obsahuje sadu filtrů a sadu akcí. [Sada filtrů](#rule-filters) omezuje akce pravidel na určitou sadu objektů v rámci kontejneru nebo názvů objektů. [Sada akcí](#rule-actions) použije vrstvu nebo odstraní akce na filtrovanou sadu objektů.

### <a name="sample-rule"></a>Vzorové pravidlo

Následující ukázkové pravidlo filtruje účet tak, aby `container1` spouštěl akce s objekty, které existují uvnitř a začínají . `foo`  

>[!NOTE]
>Správa životního cyklu podporuje pouze typ objektu blob bloku.  

- Objekt blob úrovně na ochlazovat úroveň 30 dní po poslední úpravě
- Objekt blob úrovně na archivní vrstvu 90 dní po poslední úpravě
- Odstranění objektu blob 2 555 dní (sedm let) po poslední změně
- Odstranění snímků objektu blob 90 dní po vytvoření snímku

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

### <a name="rule-filters"></a>Filtry pravidel

Filtry omezují akce pravidel na podmnožinu objektů BLOB v rámci účtu úložiště. Pokud je definovánvíce než jeden `AND` filtr, logické spustí na všechny filtry.

Filtry zahrnují:

| Název filtru | Typ filtru | Poznámky | Je vyžadováno |
|-------------|-------------|-------|-------------|
| objekt blobTypes   | Pole předdefinovaných hodnot výčtu. | Aktuální verze `blockBlob`podporuje . | Ano |
| předpona Shoda | Pole řetězců pro předpony, které mají být shodovat. Každé pravidlo může definovat až 10 předpon. Řetězec předpony musí začínat názvem kontejneru. Například pokud chcete, aby odpovídaly `https://myaccount.blob.core.windows.net/container1/foo/...` všechny objekty BLOB pod `container1/foo`pro pravidlo, prefixMatch je . | Pokud nedefinujete prefixMatch, pravidlo se vztahuje na všechny objekty BLOB v rámci účtu úložiště.  | Ne |

### <a name="rule-actions"></a>Akce pravidla

Akce jsou použity na filtrované objekty BLOB při splnění podmínky spuštění.

Správa životního cyklu podporuje vrstvení a odstranění objektů BLOB a odstranění snímků objektů blob. Definujte alespoň jednu akci pro každé pravidlo na objekty blob nebo snímky objektů blob.

| Akce        | Základní objekt blob                                   | Snímek      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Podpora objektů BLOB aktuálně na horké úrovni         | Nepodporuje se |
| tierToArchive | Podpora objektů BLOB aktuálně na horké nebo studené úrovni | Nepodporuje se |
| delete        | Podporuje se                                   | Podporuje se     |

>[!NOTE]
>Pokud definujete více než jednu akci na stejný objekt blob, správa životního cyklu použije nejlevnější akce na objekt blob. Akce `delete` je například levnější `tierToArchive`než akce . Akce `tierToArchive` je levnější `tierToCool`než akce .

Podmínky spuštění jsou založeny na věku. Základní objekty BLOB používají poslední upravený čas ke sledování stáří a snímky objektů blob používají čas vytvoření snímku ke sledování stáří.

| Podmínka spuštění akce             | Hodnota podmínky                          | Popis                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Celá hodnota označující stáří ve dnech | Podmínka pro základní objekt blob akce     |
| daysAfterCreationGreaterThan     | Celá hodnota označující stáří ve dnech | Podmínka pro akce snímku objektu blob |

## <a name="examples"></a>Příklady

Následující příklady ukazují, jak řešit běžné scénáře s pravidly zásad životního cyklu.

### <a name="move-aging-data-to-a-cooler-tier"></a>Přesunutí dat stárnutí na chladnější úroveň

Tento příklad ukazuje, jak převést objekty BLOB bloku s předponou `container1/foo` nebo `container2/bar`. Objekty BLOB zásad, které nebyly změněny více než 30 dní na chladné úložiště, a objekty BLOB, které nebyly změněny za 90 dní na úroveň archivu:

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

### <a name="archive-data-after-ingest"></a>Archivovat data po požití

Některá data zůstávají nečinná v cloudu a je zřídka, pokud vůbec, přístup po uložení. Následující zásady životního cyklu jsou konfigurovány pro archivaci dat krátce po jejich požití. Tento příklad přechody bloku objekty BLOB `archivecontainer` v účtu úložiště v rámci kontejneru do vrstvy archivu. Přechod se provádí působením na objekty BLOB 0 dní po poslední změně:

> [!NOTE] 
> Doporučujeme nahrát objekty BLOB přímo na úroveň archivu, aby byla efektivnější. Můžete použít hlavičku x-ms-acess-tier pro [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) nebo [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) s REST verze 2018-11-09 a novější nebo naše nejnovější klientské knihovny úložiště objektů blob. 

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

### <a name="expire-data-based-on-age"></a>Platnost dat vyprší na základě věku

Očekává se, že platnost některých dat vyprší dny nebo měsíce po vytvoření. Zásady správy životního cyklu můžete nakonfigurovat tak, aby vypršela platnost dat odstraněním na základě stáří dat. Následující příklad ukazuje zásadu, která odstraní všechny objekty BLOB bloku starší než 365 dní.

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

Pro data, která je upravena a přístup pravidelně po celou dobu jejich životnosti, snímky se často používají ke sledování starší verze dat. Můžete vytvořit zásadu, která odstraní staré snímky na základě stáří snímku. Stáří snímku je určena vyhodnocením čas vytvoření snímku. Toto pravidlo zásad odstraní snímky objektů `activedata` blob bloku v rámci kontejneru, které jsou 90 dnů nebo starší po vytvoření snímku.

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

**Vytvořil(a) jsem novou zásadu, proč se akce nespustí okamžitě?**  
Platforma spouští zásady životního cyklu jednou denně. Po konfiguraci zásad může trvat až 24 hodin, než budou některé akce spuštěny poprvé.  

**Pokud aktualizuji existující zásady, jak dlouho trvá spuštění akcí?**  
Aktualizovaná zásada trvá až 24 hodin, než vstoupí v platnost. Jakmile je zásada v platnosti, může trvat až 24 hodin pro spuštění akcí. Proto akce zásad může trvat až 48 hodin.   

**Ručně rehydratované archivované blob, jak zabránit jeho přesunutí zpět do archivní vrstvy dočasně?**  
Při přesunutí objektu blob z jedné vrstvy přístupu do jiné, jeho poslední změna čas nezmění. Pokud ručně rehydratujete archivovaný objekt blob na horkou úroveň, bude přesunut zpět na úroveň archivace pomocí modulu pro správu životního cyklu. Zakažte pravidlo, které dočasně ovlivňuje tento objekt blob, abyste zabránili jeho archivaci znovu. Znovu povolte pravidlo, když lze objekt blob bezpečně přesunout zpět na úroveň archivace. Můžete také zkopírovat objekt blob do jiného umístění, pokud potřebuje zůstat v horké nebo chladné vrstvy trvale.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak obnovit data po náhodném odstranění:

- [Obnovitelné odstranění objektů blob služby Azure Storage](../blobs/storage-blob-soft-delete.md)
