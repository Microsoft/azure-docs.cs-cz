---
title: Správa životního cyklu Azure Storage
description: Naučte se vytvářet pravidla zásad životního cyklu pro přechod dat o splatnosti z horké na studenou a archivní úroveň.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/15/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: ee04ad28d6b52e63becd2991d77b453cd411f683
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309804"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Správa životního cyklu úložiště objektů blob v Azure

Datové sady mají jedinečné životní cykly. Brzy v životním cyklu lidé k často přistupuje k některým datům. Ale nutnost přístupu se v důsledku stáří dat nevýznamně hodí. Některá data v cloudu zůstala nečinná a jsou po uložení zřídka dostupná. Po vytvoření vyprší platnost některých dat nebo měsíců, zatímco jiné sady dat se aktivně čtou a upravují po celou dobu jejich životnosti. Správa životního cyklu Azure Blob Storage nabízí bohatou zásadu založenou na pravidlech pro účty GPv2 a BLOB Storage. Zásady můžete použít k převodu dat do příslušných úrovní přístupu nebo vypršení jejich platnosti na konci životního cyklu dat.

Zásady správy životního cyklu vám umožňují:

- Převeďte objekty blob ze studené na horkou hned, pokud je k dispozici pro optimalizaci výkonu. 
- Převeďte objekty blob, verze objektů BLOB a snímky objektů blob na studenou úroveň úložiště (horká na studenou, horkou pro archivaci nebo studenou pro archivaci), pokud k optimalizaci nákladů neproběhla nebo se změnila.
- Odstranění objektů blob, verzí objektů BLOB a snímků objektů blob na konci jejich životního cyklu
- Definujte pravidla, která se spustí jednou denně na úrovni účtu úložiště.
- Použití pravidel u kontejnerů nebo podmnožiny objektů BLOB (použití předpon názvů nebo [značek indexu objektů BLOB](storage-manage-find-blobs.md) jako filtrů)

Vezměte v úvahu scénář, kdy data budou často přístupná v počátečních fázích životního cyklu, ale pouze občas po dvou týdnech. Po prvním měsíci se k datové sadě používá zřídka. V tomto scénáři je horké úložiště nejlépe v počátečních fázích. Studená úložiště jsou nejvhodnější pro příležitostné přístupy. Archivní úložiště je nejlepší možností po stáří dat za měsíc. Nastavením vrstev úložiště v závislosti na stáří dat můžete navrhnout nejlevnější možnosti úložiště podle vašich potřeb. Pro dosažení tohoto přechodu jsou k dispozici pravidla zásad správy životního cyklu pro přesun dat o splatnosti do úrovní chladiče.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]
>[!NOTE]
>Pokud potřebujete, aby data zůstala čitelná, například při použití v StorSimple, nenastavte zásady pro přesun objektů blob do archivní úrovně.

## <a name="availability-and-pricing"></a>Dostupnost a ceny

Funkce správy životního cyklu je dostupná ve všech oblastech Azure pro účty Pro obecné účely v2 (GPv2), účty BLOB Storage, účty úložiště objektů blob bloku Premium a účty Azure Data Lake Storage Gen2. V Azure Portal můžete upgradovat existující účet Pro obecné účely (GPv1) na účet GPv2. Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Funkce správy životního cyklu je bezplatná. Zákazníkům se účtují běžné provozní náklady za volání rozhraní API [vrstvy objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) . Operace odstranění je zadarmo. Další informace o cenách najdete v tématu [ceny za objekty blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Přidat nebo odebrat zásadu

Zásadu můžete přidat, upravit nebo odebrat pomocí kterékoli z následujících metod:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Zásady je možné číst nebo zapisovat v plném rozsahu. Částečné aktualizace nejsou podporovány. 

> [!NOTE]
> Pokud pro svůj účet úložiště povolíte pravidla brány firewall, můžou být požadavky správy životního cyklu blokované. Tyto požadavky můžete odblokovat poskytováním výjimek pro důvěryhodné služby společnosti Microsoft. Další informace najdete v části výjimky v tématu [Konfigurace bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Tento článek popisuje, jak spravovat zásady pomocí portálu a metod PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Existují dva způsoby, jak přidat zásadu prostřednictvím Azure Portal. 

* [Zobrazení seznamu Azure Portal](#azure-portal-list-view)
* [Azure Portal zobrazení kódu](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Zobrazení seznamu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte svůj účet úložiště. 

1. V části **BLOB Service**pro zobrazení nebo změnu pravidel vyberte možnost **Správa životního cyklu** .

1. Vyberte kartu **zobrazení seznamu** .

1. Vyberte **Přidat pravidlo** a pojmenujte pravidlo na formuláři **podrobností** . Můžete také nastavit **Rozsah pravidla**, **typ objektu BLOB**a hodnoty **podtypu objektu BLOB** . Následující příklad nastaví obor pro filtrování objektů BLOB. Tím dojde k přidání karty **Sada filtrů** .

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Správa životního cyklu přidat stránku podrobností pravidla v Azure Portal":::

1. Vyberte **základní objekty blob** a nastavte podmínky pro vaše pravidlo. V následujícím příkladu jsou objekty blob přesunuté do studeného úložiště, pokud se nezměnily po dobu 30 dnů.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Správa životního cyklu přidat stránku podrobností pravidla v Azure Portal":::

   Možnost **posledního použití** je dostupná ve verzi Preview v následujících oblastech:

    - Francie – střed
    - Kanada – východ
    - Střední Kanada

   > [!IMPORTANT]
   > Čas posledního přístupu ke sledování je jenom pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.
   
   Aby bylo možné použít **poslední možnost přístupu** , vyberte na stránce **Správa životního cyklu** v Azure Portal možnost **sledování přístupu povoleno** . Další informace o možnosti **posledního použití** najdete v tématu [přesun dat na základě posledního dne otevření (Preview)](#move-data-based-on-last-accessed-date-preview).

1. Pokud jste vybrali možnost **omezit objekty BLOB s filtry** na stránce **Podrobnosti** , vyberte **Filtr sady** pro přidání volitelného filtru. Následující příklad filtruje objekty BLOB v kontejneru *mylifecyclecontainer* , které začínají na "protokol".

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Správa životního cyklu přidat stránku podrobností pravidla v Azure Portal":::

1. Pokud chcete přidat novou zásadu, vyberte **Přidat** .

#### <a name="azure-portal-code-view"></a>Azure Portal zobrazení kódu
1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte svůj účet úložiště.

1. V části **BLOB Service**pro zobrazení nebo změnu zásad vyberte možnost **Správa životního cyklu** .

1. Následující JSON je příkladem zásady, kterou lze vložit do karty **zobrazení kódu** .

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Vyberte **Uložit**.

1. Další informace o tomto příkladu JSON najdete v částech [zásady](#policy) a [pravidla](#rules) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K přidání zásad do účtu úložiště se dá použít následující skript PowerShellu. `$rgname`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště.

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
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Šablona](#tab/template)

Správu životního cyklu můžete definovat pomocí Azure Resource Manager šablon. Tady je Ukázková šablona pro nasazení účtu úložiště RA-GRS GPv2 se zásadami správy životního cyklu.

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

Zásady správy životního cyklu jsou kolekce pravidel v dokumentu JSON:

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

Zásada je kolekcí pravidel:

| Název parametru | Typ parametru | Poznámky |
|----------------|----------------|-------|
| `rules`        | Pole objektů pravidel | V zásadě je vyžadováno alespoň jedno pravidlo. V zásadách můžete definovat až 100 pravidel.|

Každé pravidlo v zásadě má několik parametrů:

| Název parametru | Typ parametru | Poznámky | Povinné |
|----------------|----------------|-------|----------|
| `name`         | Řetězec |Název pravidla může obsahovat až 256 alfanumerických znaků. Název pravidla rozlišuje velká a malá písmena. Musí být jedinečný v rámci zásad. | Pravda |
| `enabled`      | Logická hodnota | Volitelná logická hodnota, která povolí dočasné vypnutí pravidla. Výchozí hodnota je true, pokud není nastavena. | Nepravda | 
| `type`         | Hodnota výčtu | Aktuální platný typ je `Lifecycle` . | Pravda |
| `definition`   | Objekt definující pravidlo životního cyklu | Každá definice se skládá ze sady filtrů a sady akcí. | Pravda |

## <a name="rules"></a>Pravidla

Každá definice pravidla obsahuje sadu filtrů a sadu akcí. [Sada filtr](#rule-filters) omezuje akce pravidla na určitou sadu objektů v rámci názvů kontejnerů nebo objektů. [Sada akcí](#rule-actions) aplikuje akce vrstvy nebo odstranění na filtrovanou sadu objektů.

### <a name="sample-rule"></a>Ukázkové pravidlo

Následující ukázkové pravidlo filtruje účet, aby spouštěl akce na objektech, které existují uvnitř `container1` a začínají na `foo` .

>[!NOTE]
>- Správa životního cyklu podporuje typy objektů blob bloku a doplňovací objekty blob.<br>
>- Správa životního cyklu nemá vliv na systémové kontejnery, jako je $logs a $web.

- Úroveň objektu BLOB na studenou vrstvu 30 dní od poslední změny
- Úroveň objektu BLOB na archivní vrstvu 90 dní od poslední změny
- Odstranit objekt BLOB 2 555 dní (sedm let) po poslední úpravě
- Odstranit předchozí verze objektů BLOB 90 dní po vytvoření

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtry pravidla

Filtruje akce pravidla omezení na podmnožinu objektů BLOB v rámci účtu úložiště. Pokud je definován více než jeden filtr, logické `AND` spuštění na všech filtrech.

Filtry zahrnují:

| Název filtru | Typ filtru | Poznámky | Je povinné |
|-------------|-------------|-------|-------------|
| blobTypes   | Pole předdefinovaných hodnot výčtu. | Aktuální verze podporuje `blockBlob` a `appendBlob` . Pro se podporuje jenom odstranění `appendBlob` , nastavení úrovně se nepodporuje. | Ano |
| prefixMatch | Pole řetězců, pro které mají být předpony spárovány. Každé pravidlo může definovat až 10 předpon. Řetězec předpony musí začínat názvem kontejneru. Například pokud chcete, aby se všechny objekty blob shodovaly v rámci `https://myaccount.blob.core.windows.net/container1/foo/...` pravidla, prefixMatch je `container1/foo` . | Pokud prefixMatch nedefinujete, pravidlo se použije na všechny objekty BLOB v účtu úložiště. | Ne |
| blobIndexMatch | Pole hodnot slovníku sestávající z klíče značek indexu objektu BLOB a podmínky hodnoty, které mají být porovnány. Každé pravidlo může definovat až 10 stavových značek indexu objektu BLOB. Například pokud chcete, aby se všechny objekty blob shodovaly s `Project = Contoso` v rámci `https://myaccount.blob.core.windows.net/` pro pravidlo, je blobIndexMatch `{"name": "Project","op": "==","value": "Contoso"}` . | Pokud blobIndexMatch nedefinujete, pravidlo se použije na všechny objekty BLOB v účtu úložiště. | Ne |

> [!NOTE]
> Index objektu BLOB je ve verzi Public Preview a je dostupný v oblasti **Kanada – střed**, Kanada – **východ**, Francie – **střed**a Francie – **jih** . Další informace o této funkci spolu se známými problémy a omezeních najdete v tématu [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB (Preview)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Akce pravidla

Akce se aplikují na filtrované objekty BLOB při splnění podmínky spuštění.

Správa životního cyklu podporuje vrstvení a mazání objektů blob, předchozích verzí objektů BLOB a snímků objektů BLOB. Definujte alespoň jednu akci pro každé pravidlo základních objektů blob, předchozí verze objektu BLOB nebo snímky objektů BLOB.

| Akce                      | Základní objekt BLOB                                  | Snímek      | Verze
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Podporováno pro `blockBlob`                  | Podporováno     | Podporováno     |
| enableAutoTierToHotFromCool | Podporováno pro `blockBlob`                  | Nepodporováno | Nepodporováno |
| tierToArchive               | Podporováno pro `blockBlob`                  | Podporováno     | Podporováno     |
| delete                      | Podporováno pro `blockBlob` a `appendBlob` | Podporováno     | Podporováno     |

>[!NOTE]
>Pokud definujete více než jednu akci u stejného objektu blob, bude správa životního cyklu v objektu BLOB platit nejméně náročná akce. Například akce `delete` je levnější než akce `tierToArchive` . Akce `tierToArchive` je levnější než akce `tierToCool` .

Podmínky spuštění jsou založené na stáří. Základní objekty blob používají čas poslední změny, verze objektů BLOB používají čas vytvoření verze a snímky objektů BLOB používají čas vytvoření snímku ke sledování stáří.

| Podmínka spuštění akce               | Hodnota podmínky                          | Popis                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Celočíselná hodnota označující stáří ve dnech | Podmínka pro základní akce objektů BLOB                                              |
| daysAfterCreationGreaterThan       | Celočíselná hodnota označující stáří ve dnech | Podmínka pro akci snímku verze a objektu BLOB                         |
| daysAfterLastAccessTimeGreaterThan | Celočíselná hodnota označující stáří ve dnech | Tisk Podmínka pro akce základního objektu blob, pokud je povolen čas posledního otevření |

## <a name="examples"></a>Příklady

Následující příklady ukazují, jak řešit běžné scénáře s pravidly zásad životního cyklu.

### <a name="move-aging-data-to-a-cooler-tier"></a>Přesunout data o stárnutí do úrovně chladicího procesu

Tento příklad ukazuje, jak převést objekty blob bloku s předponou `container1/foo` nebo `container2/bar` . Zásady přemění objekty blob, které se nezměnily během více než 30 dní, do studeného úložiště, a objekty BLOB se v 90 dnech nezměnily na úroveň archivu:

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

### <a name="move-data-based-on-last-accessed-date-preview"></a>Přesunutí dat na základě data posledního otevření (Preview)

Sledování času posledního přístupu můžete povolit, abyste zachovali záznam o tom, kdy je váš objekt BLOB naposledy přečtený nebo zapsaný. Čas posledního přístupu můžete použít jako filtr ke správě vrstvení a uchovávání dat objektů BLOB.

Možnost **posledního použití** je dostupná ve verzi Preview v následujících oblastech:

 - Francie – střed
 - Kanada – východ
 - Střední Kanada

> [!IMPORTANT]
> Čas posledního přístupu ke sledování je jenom pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

Aby bylo možné použít **poslední možnost přístupu** , vyberte na stránce **Správa životního cyklu** v Azure Portal možnost **sledování přístupu povoleno** .

#### <a name="how-last-access-time-tracking-works"></a>Jak funguje sledování času posledního přístupu

Když je povolené sledování času posledního přístupu, `LastAccessTime` při čtení nebo zapisování objektu BLOB se aktualizuje vlastnost objektu BLOB s názvem. Operace [získání objektu BLOB](/rest/api/storageservices/get-blob) se považuje za operaci přístupu. [Získat vlastnosti objektu BLOB](/rest/api/storageservices/get-blob-properties), [získat metadata objektu BLOB](/rest/api/storageservices/get-blob-metadata)a [získat značky objektů BLOB](/rest/api/storageservices/get-blob-tags) nejsou operace přístupu, a proto neaktualizujte čas posledního přístupu.

Chcete-li minimalizovat dopad na latenci přístupu pro čtení, bude čas posledního přístupu aktualizován pouze první přečtená za posledních 24 hodin. Následná čtení ve 24hodinovém období neaktualizují čas posledního přístupu. Pokud dojde ke změně objektu BLOB mezi čteními, je posledním časem přístupu poslední z těchto dvou hodnot.

V následujícím příkladu jsou objekty blob přesunuté do studeného úložiště, pokud k nim nedošlo po dobu 30 dnů. `enableAutoTierToHotFromCool`Vlastnost je logická hodnota, která indikuje, jestli by měl být objekt BLOB automaticky vrstvený ze studené zpět na horkou, pokud k němu znovu dostanete po rozvrstvení na studenou.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Podpora účtu úložiště

Sledování času posledního přístupu je k dispozici pro následující typy účtů úložiště:

 - Účty úložiště pro obecné účely v2
 - Zablokovat účty úložiště objektů BLOB
 - Účty úložiště Blob

Pokud je váš účet úložiště účet pro obecné účely V1, použijte Azure Portal k upgradu na účet pro obecné účely v2.

Účty úložiště s hierarchickým oborem názvů povolené pro použití s Azure Data Lake Storage Gen2 ještě nejsou podporované.

#### <a name="pricing-and-billing"></a>Ceny a fakturace

Každá aktualizace času posledního přístupu je považována za [jinou operaci](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="archive-data-after-ingest"></a>Archivovat data po ingestování

Některá data v cloudu zůstanou nečinná a v případě potřeby jsou po uložení k dispozici zřídka. Následující zásady životního cyklu jsou nakonfigurovány k archivaci dat krátce po ingestování. Tento příklad přechází objekty blob bloku v účtu úložiště v kontejneru `archivecontainer` do archivní úrovně. Přechod se provádí na objektech blob 0 dní od poslední změny:

> [!NOTE] 
> Doporučujeme nahrát objekty blob přímo do archivní vrstvy, aby byly efektivnější. Můžete použít záhlaví x-MS-Access-úrovně pro [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) nebo [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) s REST verze 2018-11-09 a novějšími nebo nejnovějšími klientskými knihovnami pro úložiště objektů BLOB. 

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

### <a name="expire-data-based-on-age"></a>Vypršení platnosti dat na základě stáří

U některých dat se očekává, že vyprší platnost dnů nebo měsíců po vytvoření. Zásady správy životního cyklu můžete nakonfigurovat tak, aby data prošla odstraněním na základě stáří dat. Následující příklad ukazuje zásadu, která odstraní všechny objekty blob bloku starší než 365 dní.

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

### <a name="delete-data-with-blob-index-tags"></a>Odstranění dat pomocí značek indexu objektu BLOB
Některá data by měla být vyhodnocena pouze v případě, že jsou výslovně označeny k odstranění. Můžete nakonfigurovat zásady správy životního cyklu tak, aby prošly daty, která jsou označená atributy klíč/hodnota indexu objektu BLOB. Následující příklad ukazuje zásadu, která odstraní všechny objekty blob bloku s příznakem `Project = Contoso` . Další informace o indexu objektů BLOB najdete v tématu [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB (Preview)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Správa verzí

Pro data, která se upravují a běžně přistupovala během své životnosti, můžete povolit správu verzí služby Blob Storage a automaticky tak zachovat předchozí verze objektu. Můžete vytvořit zásadu pro vrstvu nebo odstranit předchozí verze. Stáří verze je určeno vyhodnocením času vytvoření verze. Toto pravidlo zásad nastavuje předchozí verze v rámci kontejneru `activedata` , které jsou 90 dní nebo starší po vytvoření verze do studené úrovně, a odstraní předchozí verze, které jsou 365 dní nebo starší.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Nejčastější dotazy

**Vytvořili jsem novou zásadu, proč se akce nespouštějí hned?**

Platforma spouští zásady životního cyklu jednou denně. Po nakonfigurování zásady může trvat až 24 hodin, než se některé akce poprvé spustí.

**Pokud aktualizujem existující zásady, jak dlouho trvá, než se akce spustí?**

Aktualizovaná zásada trvá až 24 hodin, než se dostanou platit. Jakmile se zásada uplatní, může trvat až 24 hodin, než se akce spustí. Proto může dokončení akcí zásad trvat až 48 hodin.

**Jak zabráním ručnímu recyklení archivovaného objektu blob, jak brání jeho přesunutí do archivní úrovně dočasně?**

Když se objekt BLOB přesune z jedné úrovně přístupu na jiný, čas poslední změny se nezmění. Pokud jste archivovaný objekt BLOB ručně znovu vypnuli do vrstvy Hot, bude se ho modul pro správu životního cyklu přesunout zpátky do archivní úrovně. Zakažte pravidlo, které bude mít dočasně vliv na tento objekt blob, aby se zabránilo jeho archivaci znovu. Znovu povolí pravidlo, když se dá objekt BLOB bezpečně přesunout zpátky do archivní úrovně. Objekt blob můžete také zkopírovat do jiného umístění, pokud je potřeba udržet se trvale na horké nebo studené úrovni.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak obnovit data po náhodném odstranění:

- [Obnovitelné odstranění objektů blob služby Azure Storage](../blobs/storage-blob-soft-delete.md)

Naučte se spravovat a vyhledávat data pomocí indexu objektů BLOB:

- [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB](storage-manage-find-blobs.md)
