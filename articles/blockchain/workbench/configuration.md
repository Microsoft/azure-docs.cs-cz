---
title: Odkaz na metadata konfigurace pracovní plochy Azure Blockchain
description: Přehled metadat konfigurace aplikace Azure Blockchain Workbench Preview.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 661e795f0e85f872b1072a8f641b8938115c5d7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252191"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referenční informace o konfiguraci pracovní plochy Azure Blockchain

Aplikace Azure Blockchain Workbench jsou vícestranné pracovní postupy definované metadaty konfigurace a inteligentním kódem smlouvy. Metadata konfigurace definují pracovní postupy na vysoké úrovni a model interakce blockchainové aplikace. Chytré kontrakty definují obchodní logiku blockchainové aplikace. Workbench používá konfiguraci a inteligentní kód smlouvy ke generování uživatelských prostředí blockchainových aplikací.

Metadata konfigurace určují následující informace pro každou aplikaci blockchain:

* Název a popis blockchainové aplikace
* Jedinečné role pro uživatele, kteří mohou jednat nebo se účastnit v rámci blockchainové aplikace
* Jeden nebo více pracovních postupů. Každý pracovní postup funguje jako stavový počítač pro řízení toku obchodní logiky. Pracovní postupy mohou být nezávislé nebo mohou vzájemně komunikovat.

Každý definovaný pracovní postup určuje následující:

* Název a popis pracovního postupu
* stavy pracovního postupu.  Každý stav je fáze toku řízení obchodní logiky. 
* Akce pro přechod do dalšího stavu
* Role uživatelů povolené k zahájení každé akce
* Inteligentní kontrakty, které představují obchodní logiku v souborech kódu

## <a name="application"></a>Aplikace

Blockchainová aplikace obsahuje metadata konfigurace, pracovní postupy a uživatelské role, které mohou v rámci aplikace jednat nebo se účastnit.

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| ApplicationName | Jedinečný název aplikace. Odpovídající inteligentní kontrakt musí používat stejný **Název_aplikace** pro příslušnou třídu smlouvy.  | Ano |
| DisplayName | Popisný zobrazovaný název aplikace. | Ano |
| Popis | Popis aplikace. | Ne |
| Role aplikace | Kolekce [ApplicationRoles](#application-roles). Role uživatelů, které mohou jednat nebo se účastnit v rámci aplikace.  | Ano |
| Pracovní postupy | Kolekce [pracovních postupů](#workflows). Každý pracovní postup funguje jako stavový počítač pro řízení toku obchodní logiky. | Ano |

Příklad naleznete v [příkladu konfiguračního souboru](#configuration-file-example).

## <a name="workflows"></a>Pracovní postupy

Obchodní logika aplikace může být modelována jako stavový počítač, kde provedení akce způsobí, že tok obchodní logiky se přesune z jednoho stavu do druhého. Pracovní postup je kolekce těchto stavů a akcí. Každý pracovní postup se skládá z jednoho nebo více inteligentních smluv, které představují obchodní logiku v souborech kódu. Spustitelná smlouva je instancí pracovního postupu.

| Pole | Popis | Požaduje se | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name (Název) | Jedinečný název pracovního postupu. Odpovídající inteligentní smlouva musí používat stejný **název** pro příslušnou třídu smlouvy. | Ano | 50 |
| DisplayName | Popisný zobrazovaný název pracovního postupu. | Ano | 255 |
| Popis | Popis pracovního postupu. | Ne | 255 |
| Iniciátory | Kolekce [ApplicationRoles](#application-roles). Role, které jsou přiřazeny uživatelům, kteří jsou oprávněni vytvářet smlouvy v pracovním postupu. | Ano | |
| StartState | Název počátečního stavu pracovního postupu. | Ano | |
| Vlastnosti | Kolekce [identifikátorů](#identifiers). Představuje data, která lze číst mimo řetězec nebo vizualizovat v nástroji uživatelského prostředí. | Ano | |
| Konstruktor | Definuje vstupní parametry pro vytvoření instance pracovního postupu. | Ano | |
| Funkce | Kolekce [funkcí,](#functions) které mohou být provedeny v pracovním postupu. | Ano | |
| Stavy | Kolekce [stavů](#states)pracovního postupu . | Ano | |

Příklad naleznete v [příkladu konfiguračního souboru](#configuration-file-example).

## <a name="type"></a>Typ

Podporované datové typy.

| Typ | Popis |
|-------|-------------|
| adresa  | Typ adresy blockchainu, například *smlouvy* nebo *uživatelé*. |
| pole    | Jednoúrovňové pole celého čísla typu, bool, peníze nebo čas. Pole mohou být statická nebo dynamická. Pomocí **funkce ElementType** určete datový typ prvků v poli. Viz [ukázková konfigurace](#example-configuration-of-type-array). |
| bool     | Logický datový typ. |
| Smlouvy | Adresa typové smlouvy. |
| enum     | Výčet sady pojmenovaných hodnot. Při použití typu výčtu zadáte také seznam EnumValues. Každá hodnota je omezena na 255 znaků. Mezi platné znaky hodnoty patří velká a malá písmena (A-Z, a-z) a čísla (0-9). Viz [příklad konfigurace a použití v Solidity](#example-configuration-of-type-enum). |
| int      | Datový typ celéčíslo. |
| Peníze    | Datový typ peněz. |
| state    | Stav pracovního postupu. |
| řetězec  | Řetězcový datový typ. Maximálně 4000 znaků. Viz [ukázková konfigurace](#example-configuration-of-type-string). |
| uživatel     | Adresa typu uživatele. |
| time     | Datový typ času. |
|`[ Application Role Name ]`| Libovolný název zadaný v roli aplikace. Omezuje uživatele, aby byli tohoto typu role. |

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

#### <a name="using-a-property-of-type-array"></a>Použití vlastnosti typu pole

Pokud definujete vlastnost jako pole typu v konfiguraci, je třeba zahrnout explicitní get funkce vrátit veřejnou vlastnost typu pole v Solidity. Například:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Příklad konfigurace řetězce typu

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

### <a name="example-configuration-of-type-enum"></a>Příklad konfigurace výčtu typu

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

#### <a name="using-enumeration-type-in-solidity"></a>Použití typu výčtu v soliditě

Jakmile výčtu je definován v konfiguraci, můžete použít typy výčtu v Solidity. Můžete například definovat výčet s názvem PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Seznam řetězců musí odpovídat mezi konfigurací a inteligentní smlouvou, aby byl platný a konzistentní prohlášení v Blockchain Workbench.

Příklad přiřazení:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Příklad parametru funkce: 

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
| Parametry | Kolekce [identifikátorů potřebných](#identifiers) k zahájení inteligentní smlouvy. | Ano |

### <a name="constructor-example"></a>Příklad konstruktoru

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

## <a name="functions"></a>Funkce

Definuje funkce, které lze spustit v pracovním postupu.

| Pole | Popis | Požaduje se | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name (Název) | Jedinečný název funkce. Odpovídající inteligentní smlouva musí používat stejný **název** pro příslušnou funkci. | Ano | 50 |
| DisplayName | Popisný zobrazovaný název funkce. | Ano | 255 |
| Popis | Popis funkce | Ne | 255 |
| Parametry | Kolekce [identifikátorů](#identifiers) odpovídajících parametrům funkce. | Ano | |

### <a name="functions-example"></a>Příklad funkcí

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

Kolekce jedinečných stavů v rámci pracovního postupu. Každý stav zachycuje krok v toku řízení obchodní logiky. 

| Pole | Popis | Požaduje se | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name (Název) | Jedinečný název státu. Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušný stav. | Ano | 50 |
| DisplayName | Popisný zobrazovaný název stavu. | Ano | 255 |
| Popis | Popis státu. | Ne | 255 |
| Procentuální dokončení | Celá hodnota zobrazená v uživatelském rozhraní Blockchain Workbench pro zobrazení průběhu v rámci toku řízení obchodní logiky. | Ano | |
| Styl | Vizuální nápověda označující, zda stav představuje stav úspěchu nebo selhání. Existují dvě platné `Success` hodnoty: nebo `Failure`. | Ano | |
| Přechody | Kolekce dostupných [přechodů](#transitions) z aktuálního stavu do další sady stavů. | Ne | |

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
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Přechody

Dostupné akce do dalšího stavu. Jedna nebo více rolí uživatelů může provést akci v každém stavu, kde akce může převést stav do jiného stavu v pracovním postupu. 

| Pole | Popis | Požaduje se |
|-------|-------------|:--------:|
| AllowedRoles | Seznam rolí aplikací, které mohou zahájit přechod. Akci mohou provádět všichni uživatelé zadané role. | Ne |
| AllowedInstanceRoles | Seznam rolí uživatelů, které se účastní nebo zadávají v inteligentní smlouvě, povolili zahájit přechod. Role instancí jsou definovány ve **vlastnostech** v rámci pracovních postupů. AllowedInstanceRoles představují uživatele, který se účastní instance inteligentní smlouvy. AllowedInstanceRoles vám umožňují omezit akci na roli uživatele v instanci smlouvy.  Například můžete chtít povolit uživateli, který vytvořil smlouvu (InstanceOwner), aby mohli ukončit, nikoli všichni uživatelé v typu role (Vlastník), pokud jste zadali roli v AllowedRoles. | Ne |
| DisplayName | Popisný zobrazovaný název přechodu. | Ano |
| Popis | Popis přechodu. | Ne |
| Funkce | Název funkce pro zahájení přechodu. | Ano |
| Další státy | Kolekce potenciálních dalších států po úspěšném přechodu. | Ano |

### <a name="transitions-example"></a>Příklad přechodů

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

Role aplikace definují sadu rolí, které lze přiřadit uživatelům, kteří chtějí jednat nebo se účastnit v rámci aplikace. Role aplikací lze použít k omezení akcí a účasti v rámci blockchainové aplikace a odpovídajících pracovních postupů. 

| Pole | Popis | Požaduje se | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name (Název) | Jedinečný název role aplikace. Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušnou roli. Názvy základních typů jsou vyhrazeny. Roli aplikace nelze pojmenovat se stejným názvem jako [Typ.](#type)| Ano | 50 |
| Popis | Popis role aplikace. | Ne | 255 |

### <a name="application-roles-example"></a>Příklad rolí aplikace

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

Identifikátory představují kolekci informací používaných k popisu vlastností pracovního postupu, konstruktoru a parametrů funkce. 

| Pole | Popis | Požaduje se | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name (Název) | Jedinečný název vlastnosti nebo parametru. Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušnou vlastnost nebo parametr. | Ano | 50 |
| DisplayName | Popisný zobrazovaný název vlastnosti nebo parametru. | Ano | 255 |
| Popis | Popis vlastnosti nebo parametru. | Ne | 255 |
| Typ | [Datový typ](#type)vlastnosti . | Ano |

### <a name="identifiers-example"></a>Příklad identifikátorů

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

## <a name="configuration-file-example"></a>Příklad konfiguračního souboru

Převod majetku je inteligentní smluvní scénář pro nákup a prodej aktiv s vysokou hodnotou, které vyžadují inspektora a odhadce. Prodejci mohou uvést svůj majetek tím, že nazákladě instance převodu majetku inteligentní smlouvy. Kupující mohou učinit nabídky tím, že podniknou kroky týkající se inteligentní smlouvy, a ostatní strany mohou podniknout kroky ke kontrole nebo hodnocení majetku. Jakmile je majetek označen jako kontrolovaný i odhadovaný, kupující a prodávající prodej znovu potvrdí před dokončením smlouvy. V každém okamžiku procesu mají všichni účastníci přehled o stavu smlouvy při jeho aktualizaci. 

Další informace včetně souborů kódu najdete v tématu [ukázka přenosu majetku pro Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Následující konfigurační soubor je určen pro ukázku přenosu majetku:

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
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

