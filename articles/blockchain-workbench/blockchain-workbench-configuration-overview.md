---
title: Azure referenci na konfigurační Blockchain Workbench
description: Přehled konfigurace aplikace Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 178c2c1d4f727241338d6d933cd5eecbbffe65bb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303810"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure referenci na konfigurační Blockchain Workbench

 Aplikace Azure Blockchain Workbench jsou více stran pracovní postupy definované konfigurace metadata a kód inteligentní smluv. Konfigurace metadata definuje vysoké úrovně pracovní postupy a interakce modelu blockchain aplikace. Inteligentní kontrakty definovat obchodní logiku aplikace blockchain. Workbench používá ke generování koncových uživatelů aplikace blockchain konfigurací a kódem inteligentní kontrakt.

Konfigurace metadata určuje pro každou aplikaci blockchain tyto informace: 

* Název a popis blockchain aplikace
* Jedinečných rolí pro uživatele, kteří mohou fungovat nebo účast v rámci aplikace blockchain
* Jeden nebo více pracovních postupů. Každý pracovní postup funguje jako stavový stroj k řízení toku obchodní logiku. Pracovní postupy může být nezávislá nebo vzájemné interakce.

Každý definovaný pracovní postup určuje následující:

* Název a popis pracovního postupu
* Stavy pracovního postupu.  Každý stav je úsek tok řízení obchodní logiku. 
* Akce pro přechod na další stav
* Role uživatele povolené zahájíte každá akce
* Inteligentní smlouvy, které představují obchodní logiky v soubory kódu

## <a name="application"></a>Aplikace

Blockchain aplikace obsahuje metadata, pracovní postupy a uživatelské role konfigurace, které může fungovat nebo účast v aplikaci.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| ApplicationName | Jedinečný název aplikace. Odpovídající inteligentní kontrakt musíte použít stejné **ApplicationName** pro třídu použít kontrakt.  | Ano |
| Zobrazovaný název | Popisný Zobrazovaný název aplikace. | Ano |
| Popis | Popis aplikace. | Ne |
| ApplicationRoles | Kolekce [ApplicationRoles](#application-roles). Role uživatele, kteří mohou fungovat nebo účast v aplikaci.  | Ano |
| Pracovní postupy | Kolekce [pracovních](#workflows). Každý pracovní postup funguje jako stavový stroj k řízení toku obchodní logiku. | Ano |

Příklad, naleznete v části [příklad souboru konfigurace](#configuration-file-example).

## <a name="workflows"></a>Pracovní postupy

Obchodní logiku aplikace je možné modelovat jako stavu počítače, kde akce způsobí, že tok obchodní logiku pro přesun z jednoho stavu do jiného. Pracovní postup je kolekce tyto stavy a akce. Každý pracovní postup se skládá z jedné nebo více inteligentní smluv, které představují obchodní logiku v soubory kódu. Spustitelný soubor kontrakt je instance pracovního postupu.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Název jedinečný pracovního postupu. Odpovídající inteligentní kontrakt musíte použít stejné **název** pro třídu použít kontrakt. | Ano |
| Zobrazovaný název | Popisný Zobrazovaný název pracovního postupu. | Ano |
| Popis | Popis pracovního postupu. | Ne |
| Iniciátory | Kolekce [ApplicationRoles](#application-roles). Role, které jsou přiřazené k uživatelům, kteří mají oprávnění k vytvoření kontraktů v pracovním postupu. | Ano |
| StartState | Název počátečního stavu pracovního postupu. | Ano |
| Vlastnosti | Kolekce [identifikátory](#identifiers). Představuje data, která může číst vypnout řetězec nebo vizualizovaných v uživatelském prostředí nástroj. | Ano |
| Konstruktor | Definuje vstupní parametry pro vytvoření instance pracovního postupu. | Ano |
| Functions | Kolekce [funkce](#functions) , mohou být provedeny v pracovním postupu. | Ano |
| stavy | Kolekce pracovního postupu [stavy](#states). | Ano |

Příklad, naleznete v části [příklad souboru konfigurace](#configuration-file-example).

## <a name="type"></a>Typ

Podporované datové typy.

| Typ | Popis |
|-------|-------------|
| Adresa  | Blockchain typy, jako například adres *kontrakty* nebo *uživatelů* |
| BOOL     | Boolean – datový typ |
| kontrakt | Adresa typu kontraktu |
| celá čísla      | Integer – datový typ |
| peníze    | Datový typ money |
| state    | Stav pracovního postupu |
| řetězec   | String – datový typ |
| uživatel     | Adresu typu uživatele |
| time     | Čas datový typ |
|`[ Application Role Name ]`| Libovolný název zadaný v roli aplikace. Omezení uživatelů na pozici daného typu role. |

## <a name="constructor"></a>Konstruktor

Definuje vstupní parametry pro instanci pracovního postupu.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Parametry | Kolekce [identifikátory](#identifiers) požadován k zahájení inteligentní kontrakt. | Ano |

### <a name="constructor-example"></a>Příklad – konstruktor

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

Definuje funkce, které mohou být provedeny pro pracovní postup.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název funkce. Odpovídající inteligentní kontrakt musíte použít stejné **název** pro příslušné funkce. | Ano |
| Zobrazovaný název | Popisný Zobrazovaný název funkce. | Ano |
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

## <a name="states"></a>stavy

Kolekce jedinečné stavy v pracovním postupu. Každý stav zaznamená krok v toku řízení obchodní logiku. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název stavu. Odpovídající inteligentní kontrakt musíte použít stejné **název** pro příslušné stavu. | Ano |
| Zobrazovaný název | Uživatelsky přívětivý název zobrazení stavu. | Ano |
| Popis | Popis stavu. | Ne |
| Procento dokončení | Celočíselná hodnota zobrazí v uživatelském rozhraní Blockchain Workbench zobrazíte průběh v rámci toku řízení obchodní logiku. | Ano |
| Styl | Pomocný parametr Visual označující, zda stav představuje úspěch nebo neúspěch stavu. Existují dvě platné hodnoty: `Success` nebo `Failure`. | Ano |
| Přechody | Kolekce dostupných [přechody](#transitions) z aktuálního stavu k dalším stavů. | Ne |

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

Dostupné akce pro další stav. Jeden nebo více rolí uživatele, může provést akce v každém stavu, kde může akce přechod stavu na jiný stav v pracovním postupu. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| AllowedRoles | Seznam aplikací rolí povoleno zahájit přechodu. Všichni uživatelé v roli možné provést akci. | Ne |
| AllowedInstanceRoles | Seznam rolí uživatelů účasti nebo zadaný v inteligentní kontraktu povoleno zahájit přechodu. Instance role jsou definovány v **vlastnosti** v rámci pracovních postupů. AllowedInstanceRoles představují uživatele účastní instanci inteligentní kontrakt. AllowedInstanceRoles vám umožňuje omezit trvá akci, která má roli uživatele v instanci kontrakt.  Například může jenom chcete povolit uživatele, který vytvořil kontrakt (InstanceOwner) mohli ukončit místo všichni uživatelé v Typ role (Vlastník), pokud jste zadali v AllowedRoles roli. | Ne |
| Zobrazovaný název | Uživatelsky přívětivý název zobrazení přechodu. | Ano |
| Popis | Popis přechodu. | Ne |
| Funkce | Název funkce zahájíte přechodu. | Ano |
| NextStates | Kolekce možných stavů další po úspěšném převodu. | Ano |

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

Aplikační role definovat sadu rolí, které můžete přiřadit uživatelům, kteří chtějí fungovat nebo účast v aplikaci. Aplikačních rolí lze omezit akce a účast v rámci blockchain aplikace a odpovídající pracovních postupů. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| Název | Jedinečný název role aplikace. Odpovídající inteligentní kontrakt musíte použít stejné **název** pro příslušné roli. Základní typ názvy jsou vyhrazené. Nelze pojmenovat aplikační role se stejným názvem jako [typu](#type)| Ano |
| Popis | Popis role aplikace. | Ne |

### <a name="application-roles-example"></a>Aplikace – příklad rolí

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
| Název | Jedinečný název vlastnosti nebo parametr. Odpovídající inteligentní kontrakt musíte použít stejné **název** pro příslušné vlastnosti nebo parametr. | Ano |
| Zobrazovaný název | Popisný Zobrazovaný název vlastnosti nebo parametr. | Ano |
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

Převod majetku je inteligentní kontrakt scénář pro nákupu a prodeji aktivům s vysokou hodnotou, které vyžadují služby inspector a appraiser. Prodejci můžete po vytvoření instance asset kontrakt Inteligentní přenos seznamu jejich prostředky. Kupující provádět nabízí provedením akce na inteligentní kontrakt a jiných stran jde provádět akce zkontrolovat nebo vyhodnotit asset. Po asset je označena i prověřovány a hodnocena, kupujících a prodejce bude potvrďte prodeji znovu před kontrakt nastavena na dokončení. U každého bodu v procesu všichni účastníci mít přehled o stavu kontrakt při její aktualizaci. 

Další informace včetně soubory kódu najdete v tématu [asset přenos vzorek pro Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Následující konfigurační soubor je pro přenos vzorovou asset:

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
> [Azure Blockchain Workbench REST API – referenční informace](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

