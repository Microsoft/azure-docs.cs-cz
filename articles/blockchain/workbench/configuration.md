---
title: Reference k metadatům konfigurace Azure blockchain Workbench
description: Přehled metadat konfigurace aplikace Azure blockchain Workbench Preview
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: f0ba19bf1d7fdf05014ac199fae9392b5c3249d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073080"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referenční informace o konfiguraci Azure blockchain Workbench

Aplikace Azure blockchain Workbench jsou pracovní postupy s více stranami, které jsou definovány pomocí metadat konfigurace a kódu inteligentního kontraktu. Metadata konfigurace definují pracovní postupy vysoké úrovně a model interakce aplikace blockchain. Inteligentní kontrakty definují obchodní logiku aplikace blockchain. Workbench používá ke generování uživatelského prostředí aplikace blockchain kód konfigurace a inteligentního kontraktu.

Metadata konfigurace určují pro každou aplikaci blockchain následující informace:

* Název a popis aplikace blockchain
* Jedinečné role pro uživatele, kteří můžou pracovat s aplikací blockchain nebo se do ní zúčastnit
* Jeden nebo více pracovních postupů. Každý pracovní postup funguje jako Stavový počítač pro řízení toku obchodní logiky. Pracovní postupy mohou být nezávislé nebo vzájemně spolupracují.

Každý definovaný pracovní postup určuje následující:

* Název a popis pracovního postupu
* Stavy pracovního postupu.  Každý stav je fáze v toku řízení obchodní logiky. 
* Akce přechodu do dalšího stavu
* Role uživatelů, kterým se povoluje zahájení každé akce
* Inteligentní kontrakty, které reprezentují obchodní logiku v souborech kódu

## <a name="application"></a>Aplikace

Aplikace blockchain obsahuje metadata konfigurace, pracovní postupy a role uživatelů, kteří se můžou chovat nebo se zúčastnit v rámci aplikace.

| Pole | Popis | Povinné |
|-------|-------------|:--------:|
| ApplicationName | Jedinečný název aplikace Odpovídající inteligentní kontrakt musí používat stejný atribut **ApplicationName** pro příslušnou třídu smlouvy.  | Yes |
| DisplayName | Popisný zobrazovaný název aplikace | Yes |
| Description | Popis aplikace | No |
| ApplicationRoles | Kolekce [ApplicationRoles](#application-roles) Role uživatelů, kteří můžou pracovat v rámci aplikace nebo se do ní zúčastnit  | Yes |
| Pracovní postupy | Kolekce  [pracovních postupů](#workflows). Každý pracovní postup funguje jako Stavový počítač pro řízení toku obchodní logiky. | Yes |

Příklad najdete v tématu [příklad konfiguračního souboru](#configuration-file-example).

## <a name="workflows"></a>Pracovní postupy

Obchodní logika aplikace může být modelována jako Stavový počítač, který přijímá akci způsobí, že tok obchodní logiky se přesune z jednoho stavu do druhého. Pracovní postup je kolekce takových stavů a akcí. Každý pracovní postup se skládá z jedné nebo více inteligentních kontraktů, které reprezentují obchodní logiku v souborech kódu. Spustitelný kontrakt je instancí pracovního postupu.

| Pole | Popis | Povinné | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name | Jedinečný název pracovního postupu Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušnou třídu smlouvy. | Yes | 50 |
| DisplayName | Popisný zobrazovaný název pracovního postupu | Yes | 255 |
| Description | Popis pracovního postupu | No | 255 |
| Iniciátory | Kolekce [ApplicationRoles](#application-roles) Role, které jsou přiřazeny uživatelům, kteří mají oprávnění k vytváření kontraktů v pracovním postupu. | Yes | |
| StartState | Název počátečního stavu pracovního postupu | Yes | |
| Vlastnosti | Kolekce [identifikátorů](#identifiers). Představuje data, která lze číst z řetězce nebo vizuálního použití v nástroji uživatelského prostředí. | Yes | |
| Konstruktor | Definuje vstupní parametry pro vytvoření instance pracovního postupu. | Yes | |
| Functions | Kolekce [funkcí](#functions) , které lze spustit v pracovním postupu. | Yes | |
| Stavy | Kolekce [stavů](#states)pracovního postupu. | Yes | |

Příklad najdete v tématu [příklad konfiguračního souboru](#configuration-file-example).

## <a name="type"></a>Typ

Podporované datové typy.

| Typ | Description |
|-------|-------------|
| adresa  | Typ adresy blockchain, například *smlouvy* nebo *Uživatelé*. |
| array    | Pole s jednou úrovní typu celé číslo, bool, peníze nebo čas. Pole mohou být statická nebo dynamická. Použijte **ElementType** k určení datového typu prvků v poli. Viz [příklad konfigurace](#example-configuration-of-type-array). |
| bool     | Logický datový typ. |
| dodavatele | Adresa typu kontraktu. |
| enum     | Byla vytvořena Výčtová Sada pojmenovaných hodnot. Při použití typu výčtu zadáte také seznam EnumValues. Každá hodnota je omezena na 255 znaků. Mezi platné znaky hodnoty patří velká a malá písmena (A-Z, a-z) a číslice (0-9). Podívejte [se na příklad konfigurace a použití v hustotě](#example-configuration-of-type-enum). |
| int      | Celočíselný datový typ. |
| papír    | Datový typ Money. |
| state    | Stav pracovního postupu. |
| řetězec  | Řetězcový datový typ. maximální počet znaků: 4000. Viz [příklad konfigurace](#example-configuration-of-type-string). |
| uživatel     | Adresa typu uživatel. |
| time     | Čas – datový typ |
|`[ Application Role Name ]`| Libovolný název zadaný v aplikační roli. Omezuje uživatele na daný typ role. |

### <a name="example-configuration-of-type-array"></a>Příklad konfigurace typu Array

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

#### <a name="using-a-property-of-type-array"></a>Použití vlastnosti Array typu

Definujete-li vlastnost jako pole typu v konfiguraci, je nutné zahrnout funkci get, která vrátí veřejnou vlastnost typu pole v hustotě. Například:

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

### <a name="example-configuration-of-type-enum"></a>Příklad konfigurace typu Enum

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

#### <a name="using-enumeration-type-in-solidity"></a>Použití typu výčtu v hustotě

Po definování výčtu v konfiguraci můžete použít typy výčtu v hustotě. Můžete například definovat výčet s názvem PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Seznam řetězců musí být v rámci konfigurace a inteligentních kontraktů shodný s platnými a konzistentními deklaracemi v blockchain Workbench.

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

| Pole | Popis | Povinné |
|-------|-------------|:--------:|
| Parametry | Kolekce [identifikátorů](#identifiers) vyžadovaných pro zahájení inteligentního kontraktu. | Yes |

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

## <a name="functions"></a>Functions

Definuje funkce, které lze spustit v pracovním postupu.

| Pole | Popis | Povinné | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name | Jedinečný název funkce. Odpovídající inteligentní kontrakt musí používat stejný **název** pro platnou funkci. | Yes | 50 |
| DisplayName | Popisný zobrazovaný název funkce | Yes | 255 |
| Description | Popis funkce | No | 255 |
| Parametry | Kolekce [identifikátorů](#identifiers) odpovídajících parametrům funkce. | Yes | |

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

| Pole | Popis | Povinné | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name | Jedinečný název stavu. Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušný stav. | Yes | 50 |
| DisplayName | Popisný zobrazovaný název stavu | Yes | 255 |
| Description | Popis stavu | No | 255 |
| PercentComplete | Celočíselná hodnota zobrazená v uživatelském rozhraní blockchain Workbench pro zobrazení pokroku v rámci toku řízení obchodní logiky. | Yes | |
| Styl | Vizuální pomocný parametr označující, zda stav představuje stav úspěch nebo selhání. Existují dvě platné hodnoty: `Success` nebo `Failure` . | Yes | |
| Přechody | Kolekce dostupných [přechodů](#transitions) z aktuálního stavu do další množiny stavů. | No | |

### <a name="states-example"></a>Příklad stavů

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

Dostupné akce do dalšího stavu. Jedna nebo více rolí uživatele může provádět akce v každém stavu, kde může akce převést stav do jiného stavu pracovního postupu. 

| Pole | Popis | Povinné |
|-------|-------------|:--------:|
| AllowedRoles | Seznam rolí aplikací povolujících zahájení přechodu Tuto akci mohou provádět pouze všichni uživatelé zadané role. | No |
| AllowedInstanceRoles | Seznam rolí uživatele zúčastněných nebo zadaných ve službě inteligentních kontraktů povolených k zahájení přechodu. Role instancí jsou definované ve **vlastnostech** v rámci pracovních postupů. AllowedInstanceRoles představuje uživatele, který se účastní instance inteligentního kontraktu. AllowedInstanceRoles vám dává možnost omezit provedení akce pro roli uživatele v instanci kontraktu.  Například můžete chtít, aby uživatel, který vytvořil kontrakt (objekt InstanceOwner), mohl ukončit místo všech uživatelů v typu role (Vlastník), pokud jste zadali roli v AllowedRoles. | No |
| DisplayName | Popisný zobrazovaný název přechodu | Yes |
| Description | Popis přechodu | No |
| Funkce | Název funkce pro zahájení přechodu. | Yes |
| NextStates | Kolekce možných dalších stavů po úspěšném přechodu. | Yes |

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

Aplikační role definují sadu rolí, které mohou být přiřazeny uživatelům, kteří chtějí v rámci aplikace jednat nebo se do ní zúčastnit. Aplikační role lze použít k omezení akcí a účasti v rámci aplikace blockchain a odpovídajících pracovních postupů. 

| Pole | Popis | Povinné | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name | Jedinečný název aplikační role. Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušnou roli. Názvy základních typů jsou vyhrazené. Nemůžete pojmenovat aplikační roli se stejným názvem jako [typ](#type) .| Yes | 50 |
| Description | Popis aplikační role | No | 255 |

### <a name="application-roles-example"></a>Příklad aplikačních rolí

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

Identifikátory představuje kolekci informací, které slouží k popisu vlastností pracovního postupu, konstruktoru a parametrů funkce. 

| Pole | Popis | Povinné | Maximální délka |
|-------|-------------|:--------:|-----------:|
| Name | Jedinečný název vlastnosti nebo parametru. Odpovídající inteligentní kontrakt musí používat stejný **název** pro příslušnou vlastnost nebo parametr. | Yes | 50 |
| DisplayName | Popisný zobrazovaný název vlastnosti nebo parametru. | Yes | 255 |
| Description | Popis vlastnosti nebo parametru | No | 255 |
| Typ | [Datový typ](#type)vlastnosti. | Yes |

### <a name="identifiers-example"></a>Příklady identifikátorů

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

Přenos prostředků je scénář inteligentního kontraktu pro nákup a prodej prostředků s vysokou hodnotou, který vyžaduje inspektora a hodnocení. Prodejci můžou vypsat své prostředky vytvořením instance inteligentního kontraktu přenosu prostředků. Účastníci můžou nabídky dělat na základě akce v rámci inteligentních smluv a další strany mohou podniknout kroky ke kontrole nebo posouzení assetu. Jakmile je Asset označený jako zkontrolovaný a vyhodnocený, kupující a prodávající si tento prodej znovu potvrdí, než se smlouva nastaví na dokončeno. V každém okamžiku procesu mají všichni účastníci přehled o stavu kontraktu, jak je aktualizován. 

Další informace, včetně souborů s kódem, najdete v tématu [Ukázka přenosu assetu pro Azure blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer) .

Následující konfigurační soubor slouží jako ukázka přenosu assetu:

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
> [Rozhraní REST API služby Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench)
