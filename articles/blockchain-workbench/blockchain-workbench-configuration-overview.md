---
title: Azure referenci na konfigurační Blockchain Workbench
description: Přehled konfigurace aplikace Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6318b6aafda972242239ce91abb0f67a15721e1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
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
| AllowedInstanceRoles | Seznam rolí uživatelů účasti nebo zadaný v inteligentní kontraktu povoleno zahájit přechodu. Instance role jsou definovány v **vlastnosti** v rámci pracovních postupů. Tito uživatelé představují uživatele účasti nebo zadaný v inteligentní kontraktu a všech uživatelů, typu role. | Ne |
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

V následujícím příkladu definuje základních požadavků a odpovědí aplikace, ve kterém žadatele o odešle že žádost a respondér odeslání odpovědi na požadavek.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení služby Azure Blockchain Workbench](blockchain-workbench-deploy.md)

