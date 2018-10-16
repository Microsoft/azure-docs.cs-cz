---
title: Referenční informace pro konfiguraci Azure Blockchain Workbench
description: Přehled konfigurace aplikace Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: caaee4cb155fc05b78bc47f1e53c79ecb0597183
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341935"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referenční informace pro konfiguraci Azure Blockchain Workbench

 Aplikace Azure Blockchain Workbench jsou více stran pracovní postupy definované konfigurace metadat a inteligentní kontraktu kódu. Konfigurace metadat definuje vysoké úrovně pracovních postupů a interakce model blockchainové aplikace. Chytrých kontraktů definovat obchodní logiku blockchainové aplikace. Aplikace Workbench použije k vygenerování uživatelského prostředí aplikace blockchain konfigurace a inteligentní kontraktu kódu.

Konfigurace metadat určuje pro každou aplikaci blockchain následující informace:

* Název a popis blockchainové aplikace
* Jedinečné role pro uživatele, kteří můžou fungovat či k účasti v rámci blockchainové aplikace
* Jeden nebo více pracovních postupů. Každý pracovní postup funguje jako stavového stroje pro řízení toku obchodní logiku. Pracovní postupy mohou být nezávislé nebo komunikovat mezi sebou.

Každý definovaný pracovní postup určuje následující:

* Název a popis pracovního postupu
* Stavy pracovního postupu.  Každý stav je úsek obchodní logiky, toku řízení. 
* Akce pro přechod do dalšího stavu
* Role uživatele povolené pro zahájení každé akce
* Chytrých kontraktů, které představují obchodní logiku v souborech kódu

## <a name="application"></a>Aplikace

Blockchainové aplikace obsahuje konfigurace metadat, pracovní postupy a uživatelských rolí, kteří můžou fungovat či k účasti v rámci aplikace.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| ApplicationName | Jedinečný název aplikace. Odpovídající inteligentní smlouvě musíte použít stejné **ApplicationName** pro třídu příslušné smlouvy.  | Ano |
| displayName | Popisný Zobrazovaný název aplikace. | Ano |
| Popis | Popis aplikace. | Ne |
| ApplicationRoles | Kolekce [ApplicationRoles](#application-roles). Role uživatelů, kteří můžou fungovat či k účasti v rámci aplikace.  | Ano |
| Pracovní postupy | Kolekce [pracovních postupů](#workflows). Každý pracovní postup funguje jako stavového stroje pro řízení toku obchodní logiku. | Ano |

Příklad najdete v tématu [příkladu konfigurační soubor](#configuration-file-example).

## <a name="workflows"></a>Pracovní postupy

Obchodní logiku aplikace může modelovat jako stavový stroj kde provádění akce způsobí, že tok obchodní logiky přesunout z jednoho stavu do jiného. Pracovní postup je kolekce tyto stavy a akce. Každý pracovní postup se skládá z jednoho nebo více chytrých kontraktů, které představují obchodní logiku v souborech kódu. Kontrakt spustitelný soubor je instance pracovního postupu.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Název pracovního postupu jedinečný. Odpovídající inteligentní smlouvě musíte použít stejné **název** pro třídu příslušné smlouvy. | Ano |
| displayName | Uživatelsky přívětivý název zobrazení pracovního postupu. | Ano |
| Popis | Popis pracovního postupu. | Ne |
| Iniciátory | Kolekce [ApplicationRoles](#application-roles). Role, které jsou přiřazené uživatelům, kteří mají oprávnění k vytvoření kontraktů v pracovním postupu. | Ano |
| StartState | Název počátečního stavu pracovního postupu. | Ano |
| Vlastnosti | Kolekce [identifikátory](#identifiers). Představuje data, které může číst mimo řetězec nebo s vizualizačního v uživatelském prostředí nástroje. | Ano |
| Konstruktor | Definuje vstupní parametry pro vytvoření instance pracovního postupu. | Ano |
| Functions | Kolekce [funkce](#functions) , které mohou být provedeny v pracovním postupu. | Ano |
| Stavy | Kolekce pracovního postupu [stavy](#states). | Ano |

Příklad najdete v tématu [příkladu konfigurační soubor](#configuration-file-example).

## <a name="type"></a>Typ

Podporované datové typy.

| Typ | Popis |
|-------|-------------|
| Adresa  | Blockchain adresy typu, jako například *kontrakty* nebo *uživatelé*. |
| pole    | Jednu úroveň pole typu integer, bool, peníze nebo čas. Pole může být statická nebo dynamická. Použití **ElementType** zadat datový typ prvků v poli. Zobrazit [Ukázková konfigurace](#example-configuration-of-type-array). |
| BOOL     | Datový typ Boolean. |
| kontrakt | Adresa Typ kontraktu. |
| Výčet     | Výčtové sadu pojmenovaných hodnot. Při použití typu výčtu, je také zadat seznam EnumKey provede. Každá hodnota je omezený na 255 znaků. Platná hodnota znaky patří velká a malá písmena (A-Z, a-z) a čísla (0-9). Zobrazit [příklad konfigurace a použili ji při Solidity](#example-configuration-of-type-enum). |
| int      | Datový typ celé číslo. |
| peníze    | Datový typ money. |
| state    | Stav pracovního postupu. |
| řetězec  | Datový typ String. maximálně 4000 znaků. Zobrazit [Ukázková konfigurace](#example-configuration-of-type-string). |
| uživatel     | Adresa Typ uživatele. |
| time     | Čas datového typu. |
|`[ Application Role Name ]`| Libovolný název zadaný v roli v aplikaci. Omezení uživatelů tohoto typu role. |

### <a name="example-configuration-of-type-array"></a>Příklad konfigurace pole typu

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Použití vlastnosti objektu pole typu

Je-li definovat vlastnost jako typ pole v konfiguraci, budete muset součástí aplikace explicitní get funkce vrátit v Solidity veřejnou vlastnost typu pole. Příklad:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Příklad konfigurace typu String

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Příklad konfigurace typu enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Použití typu výčtu v Solidity

Jakmile výčtu je definovaná v konfiguraci, můžete v Solidity výčtové typy. Například můžete definovat výčet volá PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Seznam řetězců, které se musí shodovat mezi konfigurací a inteligentní smlouvy bude deklarace platný a konzistentní vzhledem k aplikacím v Blockchain Workbench.

Příklad přiřazení:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Příklad parametr funkce: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Definuje vstupní parametry pro instanci pracovního postupu.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Parametry | Kolekce [identifikátory](#identifiers) potřebná pro zahájení inteligentní kontraktu. | Ano |

### <a name="constructor-example"></a>Příklad konstruktor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

Definuje funkce, které mohou být provedeny v pracovním postupu.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název funkce. Odpovídající inteligentní smlouvě musíte použít stejné **název** pro příslušné funkce. | Ano |
| displayName | Popisný Zobrazovaný název funkce. | Ano |
| Popis | Popis funkce | Ne |
| Parametry | Kolekce [identifikátory](#identifiers) odpovídající parametry funkce. | Ano |

### <a name="functions-example"></a>Příklad funkce

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Stavy

Kolekce stavů jedinečný v rámci pracovního postupu. Každý stav zaznamená krok v toku řízení obchodní logiku. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název stavu. Odpovídající inteligentní smlouvě musíte použít stejné **název** pro příslušný stav. | Ano |
| displayName | Uživatelsky přívětivý název zobrazení stavu. | Ano |
| Popis | Popis stavu. | Ne |
| Procento dokončení | Celočíselná hodnota zobrazí v uživatelském rozhraní Blockchain Workbench a zobrazuje průběh v rámci toku řízení obchodní logiku. | Ano |
| Styl | Vizuální nápovědou označující, zda stav představuje stav úspěch nebo neúspěch. Existují dvě platné hodnoty: `Success` nebo `Failure`. | Ano |
| Přechody | Kolekce dostupných [přechody](#transitions) z aktuálního stavu k další skupině stavů. | Ne |

### <a name="states-example"></a>Příklad stavy

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Přechody

Dostupné akce do dalšího stavu. Jeden nebo více rolí uživatelů mohou provádět akce v jednotlivých stavech, kde může akce přechodu stavu do jiného státu v pracovním postupu. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| AllowedRoles | Seznam rolí aplikace povoleno zahajovat přechodu. Všichni uživatelé zadané role může být schopen provést akci. | Ne |
| AllowedInstanceRoles | Seznam rolí uživatelů účast nebo se musí zadat v inteligentní kontraktu povoleno zahajovat přechodu. Instance rolí jsou definovány v **vlastnosti** v rámci pracovních postupů. AllowedInstanceRoles představují uživatel součástí instance inteligentní kontraktu. AllowedInstanceRoles vám umožňuje omezit provádění akce pro roli uživatele do instance kontraktu.  Například může pouze chcete povolit uživatele, který vytvořil kontraktu (objekt InstanceOwner) lze ukončit ne všichni uživatelé v roli typu (Vlastník), pokud jste zadali v AllowedRoles roli. | Ne |
| displayName | Uživatelsky přívětivý název zobrazení přechodu. | Ano |
| Popis | Popis přechodu. | Ne |
| Funkce | Název funkce k zahájení přechodu. | Ano |
| NextStates | Kolekce možných stavů další po úspěšný přechod. | Ano |

### <a name="transitions-example"></a>Příklad přechody

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Aplikační role

Aplikační role definovat sadu rolí, je možné přiřadit uživatelům, kteří chtějí pracovat či k účasti v rámci aplikace. Aplikační role je možné omezit akce a účast v blockchainu aplikaci a odpovídající pracovní postupy. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název role aplikace. Odpovídající inteligentní smlouvě musíte použít stejné **název** pro příslušné roli. Základní typ názvy jsou vyhrazené. Nelze pojmenovat roli aplikace se stejným názvem jako [typu](#type)| Ano |
| Popis | Popis role aplikace. | Ne |

### <a name="application-roles-example"></a>Role aplikace – příklad

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identifikátory

Identifikátory představují kolekci informace, které slouží k popisu vlastnosti pracovního postupu, konstruktor a parametry funkce. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název vlastnosti nebo parametr. Odpovídající inteligentní smlouvě musíte použít stejné **název** pro příslušné vlastnosti nebo parametr. | Ano |
| displayName | Uživatelsky přívětivý název zobrazení pro vlastnost nebo parametr. | Ano |
| Popis | Popis vlastnosti nebo parametr. | Ne |

### <a name="identifiers-example"></a>Příklad identifikátory

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Příklad souboru konfigurace

Přenos Asset se scénáři inteligentní smlouvy pro nákup a prodejní prostředky vysoké hodnoty, které vyžadují inspector a ohodnocovatel scampi. Prodejci můžete zobrazit seznam svých prostředků po vytvoření instance kontrakt Inteligentní přenos asset. Kupující může nabídky provedením akce na inteligentní smlouvy, a jiné strany provést akce ke kontrole nebo vyhodnotit assetu. Jakmile je označen asset obě zkontroloval a hodnotit, kupující a prodejce bude potvrďte prodej znovu před nastavením smlouvy k dokončení. V každém bodu v procesu musí všichni účastníci přehled o stavu kontrakt při její aktualizaci. 

Další informace, včetně souborů s kódem, najdete v tématu [asset přenos ukázky pro Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Následující konfigurační soubor je pro ukázku přenos prostředku:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been cancelled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

