---
title: Identifikační entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599294"
---
### <a name="financial-account-identification"></a>Identifikace finančního účtu

Tato kategorie entit zahrnuje finanční informace a oficiální formy identifikace.

#### <a name="category-aba-routing-number"></a>Kategorie: číslo směrování ABA

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Číslo směrování ABA

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Směrovací čísla asociace (ABA) pro americký bankový přenos

        Chcete-li získat tuto kategorii entit, přidejte `ABARoutingNumber` do `pii-categories` parametru. `ABARoutingNumber` bude také vrácena v odpovědi rozhraní API, pokud je zjištěna.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>Kategorie: kód SWIFT

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Kód SWIFT

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kódy SWIFT pro informace o platebních pokynech.

        Chcete-li získat tuto kategorii entit, přidejte `SWIFTCode` do `pii-categories` parametru. `SWIFTCode` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategorie: platební karta

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Platební karta

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Čísla platebních karet 

        Chcete-li získat tuto kategorii entit, přidejte `CreditCardNumber` do `pii-categories` parametru. `CreditCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.

    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategorie: číslo mezinárodního bankovního účtu (IBAN) 

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Platební karta

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kódy IBAN pro informace o platebních pokynech.

        Chcete-li získat tuto kategorii entit, přidejte `InternationlBankingAccountNumber` do `pii-categories` parametru. `InternationlBankingAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identifikace specifická pro státní správu a zemi/oblast

> [!NOTE]
> Následující finanční entity a entity specifické pro danou zemi nejsou vráceny s `domain=phi` parametrem:
> * Čísla Passport
> * Daňové identifikátory

Následující entity jsou seskupené a uvedené podle země:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entita**

        Číslo argentinské National identity (DNI)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `ARNationalIdentityNumber` do `pii-categories` parametru. `ARNationalIdentityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Rakousko

:::row:::
    :::column span="":::
        **Entita**

        Karta identity v Rakousku

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `ATIdentityCard` do `pii-categories` parametru. `ATIdentityCard` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo v Rakousku

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ATTaxIdentificationNumber` do `pii-categories` parametru. `ATTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo DPH přidané v hodnotě Rakousko

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ATValueAddedTaxNumber` do `pii-categories` parametru. `ATValueAddedTaxNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Austrálie

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu Austrálie

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `AUDriversLicenseNumber` do `pii-categories` parametru. `AUDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australské obchodní číslo

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `AUBusinessNumber` do `pii-categories` parametru. `AUBusinessNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo společnosti v Austrálii

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `AUCompanyNumber` do `pii-categories` parametru. `AUCompanyNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licence k strojvedoucímu v Austrálii  

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `AUDriversLicense` do `pii-categories` parametru. `AUDriversLicense` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo zdravotního účtu Austrálie

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `AUMedicalAccountNumber` do `pii-categories` parametru. `AUMedicalAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport v Austrálii

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ATPassportNumber` do `pii-categories` parametru. `ATPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo daňového souboru v Austrálii

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ATTaxIdentificationNumber` do `pii-categories` parametru. `ATTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belgie

:::row:::
    :::column span="":::
        **Entita**

        Národní číslo v Belgii

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `BENationalNumber` do `pii-categories` parametru. `BENationalNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo DPH přidané do přidané hodnoty v Belgii

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `BEValueAddedTaxNumber` do `pii-categories` parametru. `BEValueAddedTaxNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brazílie 

:::row:::
    :::column span="":::
        **Entita**

        Brazílie – právní číslo entity (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `BRLegalEntityNumber` do `pii-categories` parametru. `BRLegalEntityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazílie – CPF číslo

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `BRCPFNumber` do `pii-categories` parametru. `BRCPFNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Karta National ID Brazílie (RG)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `BRNationalIDRG` do `pii-categories` parametru. `BRNationalIDRG` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu v Kanadě

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `CABankAccountNumber` do `pii-categories` parametru. `CABankAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače Kanady

    :::column-end:::

    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `CADriversLicenseNumber` do `pii-categories` parametru. `CADriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Health Service pro Kanadu

        
    :::column-end:::

    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `CAHealthServiceNumber` do `pii-categories` parametru. `CAHealthServiceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport pro Kanadu

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `CAPassportNumber` do `pii-categories` parametru. `CAPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada – osobní identifikační číslo zdravotního stavu (PHIN)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `CAPersonalHealthIdentification` do `pii-categories` parametru. `CAPersonalHealthIdentification` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rodné číslo v Kanadě

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `CASocialInsuranceNumber` do `pii-categories` parametru. `CASocialInsuranceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entita**

        Číslo karty identity Chile

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `CLIdentityCardNumber` do `pii-categories` parametru. `CLIdentityCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Čína

:::row:::
    :::column span="":::
        **Entita**

        Čína – číslo rezidenta identity na kartu (ČLR)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `CNResidentIdentityCardNumber` do `pii-categories` parametru. `CNResidentIdentityCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Evropské unie (EU)

:::row:::
    :::column span="":::
        **Entita**

        Číslo debetní karty v EU

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `EUDebitCardNumber` do `pii-categories` parametru. `EUDebitCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače EU

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `EUDriversLicenseNumber` do `pii-categories` parametru. `EUDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Souřadnice GPU pro procesory EU

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `EUGPSCoordinates` do `pii-categories` parametru. `EUGPSCoordinates` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Národní identifikační číslo v EU

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `EUNationalIdentificationNumber` do `pii-categories` parametru. `EUNationalIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo Passportu EU

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `EUPassportNumber` do `pii-categories` parametru. `EUPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo sociálního pojištění (SSN) nebo ekvivalentní ID pro EU

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `EUSocialSecurityNumber` do `pii-categories` parametru. `EUSocialSecurityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo (DIČ) EU

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `EUTaxIdentificationNumber` do `pii-categories` parametru. `EUTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Francie

:::row:::
    :::column span="":::
        **Entita**

        Číslo licence ovladače Francie

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `FRDriversLicenseNumber` do `pii-categories` parametru. `FRDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo pojištění stavu Francie

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `FRHealthInsuranceNumber` do `pii-categories` parametru. `FRHealthInsuranceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Karta National ID Francie (CNI)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `FRNationalID` do `pii-categories` parametru. `FRNationalID` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francouzský pas číslo

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `FRPassportNumber` do `pii-categories` parametru. `FRPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francouzský rodné číslo (INSEE)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `FRSocialSecurityNumber` do `pii-categories` parametru. `FRSocialSecurityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo pro Francii (numéro SPI)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `FRTaxIdentificationNumber` do `pii-categories` parametru. `FRTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo daně z přidané hodnoty (DPH) pro Francii

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `FRValueAddedTaxNumber` do `pii-categories` parametru. `FRValueAddedTaxNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Německo

:::row:::
    :::column span="":::
        **Entita**

        Číslo licence německého ovladače

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `DEDriversLicenseNumber` do `pii-categories` parametru. `DEDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo karty identity v Německu

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `DEIdentityCardNumber` do `pii-categories` parametru. `DEIdentityCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo německé služby Passport

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `DEPassportNumber` do `pii-categories` parametru. `DEPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo pro Německo

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `DETaxIdentificationNumber` do `pii-categories` parametru. `DETaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové číslo přidané hodnoty v Německu

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `DEValueAddedNumber` do `pii-categories` parametru. `DEValueAddedNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entita**

        Číslo HKID (Hongkong – zvláštní identita)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `HKIdentityCardNumber` do `pii-categories` parametru. `HKIdentityCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Maďarsko

:::row:::
    :::column span="":::
        **Entita**

        Osobní identifikační číslo Maďarska

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `HUPersonalIdentificationNumber` do `pii-categories` parametru. `HUPersonalIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo v Maďarsku

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `HUTaxIdentificationNumber` do `pii-categories` parametru. `HUTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové číslo přidané hodnoty v Maďarsku

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `HUValueAddedNumber` do `pii-categories` parametru. `HUValueAddedNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Indie

:::row:::
    :::column span="":::
        **Entita**

        Indie – trvalé číslo účtu (PAN)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `INPermanentAccount` do `pii-categories` parametru. `INPermanentAccount` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Jedinečné identifikační číslo Indie (Aadhaar)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `INUniqueIdentificationNumber` do `pii-categories` parametru. `INUniqueIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésie

:::row:::
    :::column span="":::
        **Entita**

        KTP (Indonésie identity Card) – číslo

    :::column-end:::
    :::column span="2":::

        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `IDIdentityCardNumber` do `pii-categories` parametru. `IDIdentityCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irsko

:::row:::
    :::column span="":::
        **Entita**

        Číslo pro irské osobní veřejné služby (PPS)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `IEPersonalPublicServiceNumber` do `pii-categories` parametru. `IEPersonalPublicServiceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Irské osobní veřejné služby (PPS) číslo v2

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `IEPersonalPublicServiceNumberV2` do `pii-categories` parametru. `IEPersonalPublicServiceNumberV2` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Izrael

:::row:::
    :::column span="":::
        **Entita**

        Mezinárodní ID Izraele

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `ILNationalID` do `pii-categories` parametru. `ILNationalID` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo bankovního účtu Izraele

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ILBankAccountNumber` do `pii-categories` parametru. `ILBankAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Itálie

:::row:::
    :::column span="":::
        **Entita**

        ID licence ovladače Itálie

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `ITDriversLicenseNumber` do `pii-categories` parametru. `ITDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fiskální kód Itálie

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ITFiscalCode` do `pii-categories` parametru. `ITFiscalCode` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové číslo přidané hodnoty v Itálii

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ITValueAddedTaxNumber` do `pii-categories` parametru. `ITValueAddedTaxNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japonsko

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu Japonska

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `JPBankAccountNumber` do `pii-categories` parametru. `JPBankAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače Japonska

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `JPDriversLicenseNumber` do `pii-categories` parametru. `JPDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonsko "moje číslo" (osobní)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `JPMyNumberPersonal` do `pii-categories` parametru. `JPMyNumberPersonal` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonsko "moje číslo" (firemní)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `JPMyNumberCorporate` do `pii-categories` parametru. `JPMyNumberCorporate` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Registrační číslo rezidenta v japonštině

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ITValueAddedTaxNumber` do `pii-categories` parametru. `ITValueAddedTaxNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo karty pro pobyt z Japonska

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `JPResidenceCardNumber` do `pii-categories` parametru. `JPResidenceCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo sociálního pojištění Japonska (SIN)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `JPSocialInsuranceNumber` do `pii-categories` parametru. `JPSocialInsuranceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonské číslo služby Passport

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `JPPassportNumber` do `pii-categories` parametru. `JPPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Lucembursko

:::row:::
    :::column span="":::
        **Entita**

        Národní identifikační číslo v Lucemburku (fyzické osoby)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `LUNationalIdentificationNumberNatural` do `pii-categories` parametru. `LUNationalIdentificationNumberNatural` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Národní identifikační číslo v Lucemburku (nefyzické osoby)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `LUNationalIdentificationNumberNonNatural` do `pii-categories` parametru. `LUNationalIdentificationNumberNonNatural` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entita**

        Malta číslo karty identity

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `MTIdentityCardNumber` do `pii-categories` parametru. `MTIdentityCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo Malty

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `MTTaxIDNumber` do `pii-categories` parametru. `MTTaxIDNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nový Zéland

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu Nového Zélandu

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `NZBankAccountNumber` do `pii-categories` parametru. `NZBankAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače Nového Zélandu

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `NZDriversLicenseNumber` do `pii-categories` parametru. `NZDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo Nového Zélandu v vnitrozemských výnosech

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `NZInlandRevenueNumber` do `pii-categories` parametru. `NZInlandRevenueNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nové ministerstvo, které má číslo zdravotního Zélandu

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `NZMinistryOfHealthNumber` do `pii-categories` parametru. `NZMinistryOfHealthNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo sociální péče na Novém Zélandu

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `NZSocialWelfareNumber` do `pii-categories` parametru. `NZSocialWelfareNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipíny

:::row:::
    :::column span="":::
        **Entita**

        Jednotné ID pro více účelů Filipíny

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `PHUnifiedMultiPurposeIDNumber` do `pii-categories` parametru. `PHUnifiedMultiPurposeIDNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugalsko 

:::row:::
    :::column span="":::
        **Entita**

        Číslo karty občanů v Portugalsku

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `PTCitizenCardNumber` do `pii-categories` parametru. `PTCitizenCardNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Daňové identifikační číslo Portugalska

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `PTTaxIdentificationNumber` do `pii-categories` parametru. `PTTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Entita**

        Číslo karty (NRIC) Singapuru (National registration ID)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `PTTaxIdentificationNumber` do `pii-categories` parametru. `PTTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Jižní Afrika

:::row:::
    :::column span="":::
        **Entita**

        Identifikační číslo Jižní Afriky

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `ZAIdentificationNumber` do `pii-categories` parametru. `ZAIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Jižní Korea

:::row:::
    :::column span="":::
        **Entita**

        Registrační číslo pro rezidentské Jižní Korea

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `KRResidentRegistrationNumber` do `pii-categories` parametru. `KRResidentRegistrationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Španělsko

:::row:::
    :::column span="":::
        **Entita**

        Španělsko DNI

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `ESDNI` do `pii-categories` parametru. `ESDNI` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rodné číslo (sociální zabezpečení)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ESSocialSecurityNumber` do `pii-categories` parametru. `ESSocialSecurityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo pro Španělsko

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `ESTaxIdentificationNumber` do `pii-categories` parametru. `ESTaxIdentificationNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Švýcarsko

:::row:::
    :::column span="":::
        **Entita**

        Švýcarský sociální rodné číslo AHV

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `CHSocialSecurityNumber` do `pii-categories` parametru. `CHSocialSecurityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tchaj-wan 

:::row:::
    :::column span="":::
        **Entita**

        Mezinárodní ID pro Tchaj-wan

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `TWNationalID` do `pii-categories` parametru. `TWNationalID` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Rezidentský certifikát pro Tchaj-wan (oblouk/TARC)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `TWResidentCertificate` do `pii-categories` parametru. `TWResidentCertificate` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport pro Tchaj-wan

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `TWPassportNumber` do `pii-categories` parametru. `TWPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Spojené království

:::row:::
    :::column span="":::
        **Entita**

        britské Číslo řidičského průkazu

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `UKDriversLicenseNumber` do `pii-categories` parametru. `UKDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       britské Číslo Shrnutí

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `UKNationalInsuranceNumber` do `pii-categories` parametru. `UKNationalInsuranceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské Národní číslo Health Service (NHS)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `UKNationalHealthNumber` do `pii-categories` parametru. `UKNationalHealthNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské Národní pojišťovací číslo (NINO)

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `UKNationalInsuranceNumber` do `pii-categories` parametru. `UKNationalInsuranceNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské nebo číslo amerického služby Passport

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `USUKPassportNumber` do `pii-categories` parametru. `USUKPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské Jedinečné referenční číslo plátce

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `UKUniqueTaxpayerNumber` do `pii-categories` parametru. `UKUniqueTaxpayerNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>USA

:::row:::
    :::column span="":::
        **Entita**

        Číslo sociálního pojištění USA (SSN)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Chcete-li získat tuto kategorii entit, přidejte `USSocialSecurityNumber` do `pii-categories` parametru. `USSocialSecurityNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::
      **Podporované jazyky dokumentů**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo licence ovladače USA

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `USDriversLicenseNumber` do `pii-categories` parametru. `USDriversLicenseNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA nebo Spojené království Číslo Passport

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `USUKPassportNumber` do `pii-categories` parametru. `USUKPassportNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Jednotlivá daňové identifikační číslo (ITIN) USA

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `USIndividualTaxpayerIdentification` do `pii-categories` parametru. `USIndividualTaxpayerIdentification` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo DEA (USA) pro vynucovací orgány

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `DrugEnforcementAgencyNumber` do `pii-categories` parametru. `DrugEnforcementAgencyNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo účtu banky v USA

    :::column-end:::
    :::column span="2":::

        Chcete-li získat tuto kategorii entit, přidejte `USBankAccountNumber` do `pii-categories` parametru. `USBankAccountNumber` Vrátí se v odpovědi rozhraní API, pokud se zjistí.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
