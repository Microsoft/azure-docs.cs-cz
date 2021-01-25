---
title: Převod dat pro rozhraní API Azure pro FHIR
description: K převodu dat v rozhraní Azure API pro FHIR použijte $convert-data Endpoint a přizpůsobte si šablony převaděče.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: c794af26fdfe2d3706d8d8d266d0756eff391b50
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747678"
---
# <a name="how-to-convert-data-to-fhir"></a>Jak převést data na FHIR

Vlastní koncový bod $convert dat v rozhraní API Azure pro FHIR je určený pro převod dat z různých formátů na FHIR. Používá modul šablon Liquid a šablony z projektu [převaděče FHIR](https://github.com/microsoft/FHIR-Converter) jako výchozí šablony. Tyto šablony převodu můžete přizpůsobit podle potřeby. V současné době podporuje HL7v2 převod na FHIR.

## <a name="use-the-convert-data-endpoint"></a>Použití koncového bodu $convert-data

`https://<<FHIR service base URL>>/$convert-data`

$convert – data přebírají v textu požadavku prostředek [parametru](http://hl7.org/fhir/parameters.html) , jak je popsáno níže:

**Prostředek parametru:**

| Název parametru      | Popis | Přípustné hodnoty |
| ----------- | ----------- | ----------- |
| inputData      | Data, která mají být převedena. | Platná hodnota datového typu řetězce JSON|
| inputDataType   | Datový typ vstupu | ```HL7v2``` |
| templateCollectionReference | Odkaz na kolekci šablon Může se jednat o odkaz buď na **výchozí šablony**, nebo na Image vlastní šablony, která je zaregistrovaná v Azure API pro FHIR. Níže najdete informace o přizpůsobení šablon, hostování těch v ACR a registraci do rozhraní Azure API pro FHIR.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | Kořenová šablona, která se má použít při transformaci dat | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Výchozí šablony vám pomohou rychle začít. Ty ale můžou být aktualizované, když upgradujete rozhraní API Azure pro FHIR. Aby bylo možné zajistit konzistentní chování převodu dat napříč různými verzemi rozhraní Azure API pro FHIR, musíte hostovat svou vlastní kopii šablon na Azure Container Registry, zaregistrovat je do rozhraní Azure API pro FHIR a použít je v voláních rozhraní API, jak je popsáno dále.

**Vzorový požadavek:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Ukázková odpověď:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Přizpůsobení šablon

K přizpůsobení šablon podle vašich potřeb můžete použít [rozšíření FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) pro Visual Studio Code. Toto rozšíření poskytuje interaktivní možnosti úprav a usnadňuje stahování šablon a ukázkových dat, které jsou publikovány Microsoftem. Podrobnosti najdete v dokumentaci v rozšíření.

## <a name="host-and-use-templates"></a>Hostování a používání šablon

Důrazně doporučujeme hostovat svou vlastní kopii šablon na ACR. Existují čtyři kroky pro hostování vlastní kopie šablon a jejich použití v rámci operace $convert-data:

1. Nahrajte šablony do Azure Container Registry.
1. V instanci Azure API pro FHIR povolte spravovanou identitu.
1. Poskytněte přístup k ACR rozhraní API Azure pro spravovanou identitu FHIR.
1. Zaregistrujte servery ACR v rozhraní Azure API pro FHIR.

### <a name="push-templates-to-azure-container-registry"></a>Vložení šablon do Azure Container Registry

Po vytvoření instance ACR můžete pomocí příkazu _FHIR Converter: push Templates_ v [rozšíření převaděče FHIR](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) vložit přizpůsobené šablony do ACR. Alternativně můžete k tomuto účelu použít [Nástroj pro správu šablon](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) rozhraní příkazového řádku.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Povolení spravované identity v rozhraní API Azure pro FHIR

V Azure Portal přejděte do vaší instance Azure API pro službu FHIR a vyberte okno **Identita** .
Změňte stav na **zapnuto** , aby se povolila spravovaná identita v rozhraní Azure API pro FHIR.

![Povolit spravovanou identitu](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Poskytnutí přístupu k ACR rozhraní API Azure pro FHIR

V instanci ACR přejděte do okna Access Control (IAM) a vyberte _Přidat přiřazení rolí_.

![Přiřazení role ACR](media/convert-data/fhir-acr-role-assignment.png)

Udělte roli AcrPull Azure API pro instanci služby FHIR.

![Přidat roli](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Registrace serverů ACR v rozhraní Azure API pro FHIR

V rozhraní API Azure pro FHIR můžete zaregistrovat až dvacet ACR serverů.

V případě potřeby nainstalujte rozhraní příkazového řádku healthcareapis z Azure PowerShell:

```powershell
az extension add -n healthcareapis
```

Zaregistrujte servery ACR do Azure API pro FHIR podle následujících příkladů:

#### <a name="register-a-single-acr-server"></a>Registrace jednoho serveru ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>Registrace více serverů ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Ověření

Proveďte volání rozhraní $convert-data API určujícího odkaz na šablonu v parametru templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>Známé problémy a jejich řešení

- Některé výchozí soubory šablon obsahují kusovník UTF-8. V důsledku toho generované hodnoty ID budou obsahovat znak BOM. To může vytvořit problém se serverem FHIR. Alternativním řešením je vyžádat si šablony Microsoftu pomocí rozšíření VS Code a po odebrání znaků BOM z _ID nebo _procedury. Liquid_, _ID/Pro_ID _kazatelně_ PŘEdávat do _vlastních ACR._

