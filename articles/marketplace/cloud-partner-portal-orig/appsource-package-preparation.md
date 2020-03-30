---
title: Příprava balíčku AppSource | Azure Marketplace
description: Explanaion v tom, jak připravit a vytvořit AppSource balíčky .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280605"
---
# <a name="appsource-package-preparation"></a>Příprava balíčku AppSource

Kromě souboru solution.zip budete potřebovat **balíček AppSource**. Jedná se o soubor ZIP, který obsahuje všechny prostředky potřebné k automatizaci procesu nasazení řešení do prostředí CRM zákazníků. **Balíček AppSource** má následující součásti.

* Balíček pro nasaditebalík balíčku
* **Content_Types.xml** s datovými zdroji, které používáte
* xml soubor s daty specifickými pro aplikaci
* 32x32 logo, které se zobrazí s vaším zápisem v Centru pro správu
* Licenční podmínky, zásady ochrany osobních údajů

Níže uvedené kroky vám pomohou vytvořit balíček AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Vytvoření balíčku pro nasaditého balíku

Balíček pro nasazení balíčku je součástí balíčku AppSource.

Chcete-li vytvořit balíček pro pomocníka pro [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)nasazení balíčku, postupujte podle následujících pokynů: . Po dokončení se váš balíček bude skládat z níže uvedených aktiv:

1. Složka balíčku: obsahuje všechna řešení, konfigurační data, ploché soubory a obsah balíčku. _Poznámka: V následujícím příkladu budeme předpokládat, že složka balíčku se nazývá "PkgFolder"_
2. DLL: Sestavení obsahuje vlastní kód pro váš balíček. _Poznámka: V následujícím příkladu budeme předpokládat, že tento soubor se nazývá "MicrosoftSample.dll."_

Nyní je třeba vytvořit soubor s názvem "**Content_Types.xml**" Tento soubor zobrazí seznam všech přípon datových zdrojů, které jsou součástí vašeho balíčku. Zde je příklad kódu souboru.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Posledním krokem je zip následující do jednoho souboru. Nazvěme to **package.zip**. Bude obsahovat

1. PkgFolder (včetně všeho uvnitř složky)
2. Knihovny dll
3. **Content_Types.xml**

Kroky k vytvoření package.zip:

1. Vložte složku balíčku, soubor **Content_Types.xml** a soubor PackageName.dll do jednoho adresáře.

![CrmScreenShot2](media/CRMScreenShot2.png)

1. Vyberte všechny položky ve složce, klikněte pravým tlačítkem myši a zvolte Odeslat komprimovované (zip) složky

![CrmScreenShot3](media/CRMScreenShot3.png)

1. Změnit název na package.zip

![CrmScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Vytvoření balíčku AppSource

Balíček AppSource vyžaduje několik dalších souborů.

1. jpg (rozlišení 32x32)
2. html (soubor ve formátu HTML)
3. **Content_Types.xml** (stejné jako výše)
4. xml

Zde je příklad kódu input.xml. Viz definice v následující tabulce.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Kde:**

|Pole|Podrobnosti|
|---|---|
|Providername|Od koho to řešení přichází. Pokud tým společnosti Microsoft, by to měl být Microsoft.|
|Soubor balíčků |Prostředky Package Deployer jsou zazipovány společně se souborem content\_types.xml. Tento soubor zip by měl obsahovat sestavení nasadit balíček a složku s prostředky nasazené balíček. To znamená, že package.zip|
|Název_řešeníAnchorName |Název souboru zip řešení v nasaditebalíku balíčku, který se používá pro zobrazovaný název a popis prostředků řešení.|
| PočátečníDatum| Toto je datum, kdy bude k dispozici balíček řešení. Formát je MM/DD/YYYY|
|Enddate|Toto je datum, kdy balíček řešení přestane být k dispozici. Formát je MM/DD/YYYY |
|Podporované země |Toto je seznam zemí nebo oblastí oddělených čárkami, které by měly vidět tento balíček. Seznam všech aktuálních kódů zemí získáte od online služeb. V té době, tohoto psaní seznamu bylo: AE, AL, AM, AO, AR, AT, AU,AZ,BA, BB, BD, BE,BG,BH,BM, BN, BO, BR, BY, CA, CH, CI, CL, CM, CO, CR, CV, CW, CY,CZ,DE, DK, DO, DZ,EC,EE, EG ,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY ,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW |
|LearnMoreLink | URL na stránku více informací pro tento balíček. |
|Národní prostředí|Jedna instance tohoto uzlu pro každý jazyk uživatelského rozhraní, který chcete podporovat v uživatelském rozhraní upřednostňovaného řešení. Tento uzel obsahuje podřízené položky, které popisují národní prostředí, logo a termíny pro každý jazyk|
|Národní prostředí: PackageLocale.Code|LCID jazyka pro tento uzel. Příklad: Americká angličtina je 1033|
|Národní prostředí: PackageLocale.IsDefault|Označuje, že se jedná o výchozí jazyk. Používá se jako záložní jazyk, pokud jazyk uživatelského rozhraní zvolený zákazníkem není k dispozici.|
|Národní prostředí: Logo|To to v případě, že logo, které chcete použít pro tento balíček. Velikost ikony je 32x32. Povolené formáty jsou PNG a JPG|
|Národní prostředí:Termíny: PackageTerm.File|Toto je název souboru dokumentu HTML, který obsahuje licenční podmínky.|

Zde je místo, kde se zobrazí logo:

![CrmScreenShot5](media/CRMScreenShot5.png)

Posledním krokem je zip následující do jednoho souboru.

1. zip (vytvořeno dříve)
2. **Content_Types.xml**
3. xml
4. png
5. html

![CrmScreenShot6](media/CRMScreenShot6.png)

Přejmenujte soubor na něco vhodného pro vaši aplikaci. Upřednostňujeme, abyste uvedei název společnosti a název aplikace. Například: **_Microsoft_SamplePackage.zip**.
