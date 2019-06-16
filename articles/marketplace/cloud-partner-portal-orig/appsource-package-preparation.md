---
title: Příprava balíčku AppSource | Azure Marketplace
description: Explanaion v tom, jak připravit a sestavovat balíčky AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ff822e87bfec5daa161172c0d47975eb06cc2808
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935647"
---
# <a name="appsource-package-preparation"></a>Příprava balíčků na AppSource

Kromě solution.zip soubor, budete potřebovat **AppSource balíčku**. Toto je soubor ZIP, který obsahuje všechny prostředky potřebné k automatizaci nasazení řešení do prostředí zákazníků CRM. **AppSource balíčku** má následující komponenty

* Balíček pro Package Deployer
* **Content_Types.xml** souboru s prostředky, které používáte
* soubor XML s daty konkrétní aplikace
* logo velikosti 32 x 32, které se zobrazí výpis v Centru pro správu
* Licenční podmínky, zásady ochrany osobních údajů

Následující postup vám pomůže vytvořit váš balíček v AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Vytvoření balíčku pro Package Deployer

Balíček pro Package Deployer je jednou ze součástí balíčku AppSource.

Chcete-li vytvořit balíček pro Package Deployer, použijte následující pokyny: [ https://msdn.microsoft.com/library/dn688182.aspx ](https://msdn.microsoft.com/library/dn688182.aspx). Jakmile budete hotovi, váš balíček bude obsahovat následující prostředky:

1. Složka balíčku: obsahuje všechna řešení, konfigurační data, plochých souborů a obsah pro balíček. _Poznámka: V následujícím příkladu budeme předpokládat, že složka balíčku se nazývá "PkgFolder"_
2. knihovny DLL: Sestavení obsahuje vlastní kód pro svůj balíček. _Poznámka: V následujícím příkladu budeme předpokládat, že tento soubor se nazývá "MicrosoftSample.dll."_

Teď, budete muset vytvořit soubor s názvem "**Content_Types.xml**" Tento soubor se zobrazí všechny, které jsou součástí vašeho balíčku rozšíření prostředků. Tady je ukázkový kód pro soubor.

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

Posledním krokem je následující kód do jednoho souboru zip. Pojmenujte ji **package.zip**. Bude obsahovat

1. PkgFolder (včetně všechno ve složce)
2. knihovny DLL
3. **Content_Types.xml**

Postup vytvoření package.zip:

1. Umístění složky balíčku **Content_Types.xml** souboru a PackageName.dll do jednoho adresáře.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Vybrat všechny položky ve složce klikněte pravým tlačítkem a vyberte složku, odeslat do komprimované (zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Změňte název na package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Vytvoření balíčku AppSource

Balíček AppSource vyžaduje několik dalších souborů.

1. JPG (s rozlišením velikosti 32 x 32)
2. HTML (soubor ve formátu HTML)
3. **Content_Types.xml** (stejně jako výše)
4. xml

Tady je ukázkový kód pro input.xml. Viz definice v níže uvedená tabulka.

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
 
**kde:**

|Pole|Podrobnosti|
|---|---|
|ProviderName|Kdo je z řešení. Pokud tým Microsoftu, je třeba Microsoft.|
|PackageFile |Balíček nástroje pro nasazení prostředků spolu s obsahem je zazipovaný\_types.xml souboru. Tento soubor zip by měl obsahovat package deployer sestavení a složku s prostředky nástroje pro nasazení balíčku. To znamená, package.zip|
|SolutionAnchorName |Název souboru zip řešení v package deployer, který se používá pro zobrazovaný název a popis prostředků řešení.|
| StartDate| Toto je datum, které bude k dispozici balíček řešení. Je ve formátu MM/DD/RRRR|
|EndDate|Toto je datum, že bude balíček řešení přestane být k dispozici. Je ve formátu MM/DD/RRRR |
|SupportedCountries |Toto je čárkami oddělený seznam zemí/oblastí, které by se měla zobrazit tento balíček. Obraťte se na seznam všech aktuální kódy zemí online služeb. V době psaní tohoto textu byl seznam: AE, AL, AM, AO, AR, AT, AUSTRÁLIE, AZ, BA, BB, BD, BÝT, BG, BH, BM, BN, BO, BRAZÍLIE, PODLE CERTIFIKAČNÍ AUTORITY, CH, PRŮBĚŽNÁ INTEGRACE, CL, CM, CO, CR, CV, SH, CY, CZ, DE, DK, PROVEĎTE, DZ, ES, EE, TŘEBA, ES, FI, FR, GB, GE, GV, GR, GT, HK , HN, HR, HU, ID, IE, IL, IN, IQ, JE HO, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, MĚ, MK, MN, MĚSÍC, MT, JEDNOTKU ZASÍLÁNÍ ZPRÁV, MX, MY, NG, NI, NL, NE, NZ, OBJEKTOVÝ MODEL, PA, PE, PH , PK, PL, ŽÁDOSTI O PŘIJETÍ ZMĚN, PS, PT, PY, DOTAZŮ A ODPOVĚDÍ, RO, RS, RU, RW, SA, SE, SG, SI, SK, SÉRIOVÉ ČÍSLO, SV, TH, TM, TN, TR, TT, TW, UŽIVATELSKÝ AGENT, USA, UY, UZ, ULOŽIT, VI, VN, ZA, ZW |
|LearnMoreLink | Adresa URL na stránku pro další informace pro tento balíček. |
|Národní prostředí|Jedna instance tohoto uzlu pro každý jazyk uživatelského prostředí, které chcete podporovat v upřednostňované řešení uživatelské prostředí Tento uzel obsahuje podřízené položky, které popisují národní prostředí, loga a podmínky pro jednotlivé jazyky|
|Národní prostředí: PackageLocale.Code|LCID jazyka pro tento uzel. Příklad: Je 1033 jazykovou verzi US English|
|Národní prostředí: PackageLocale.IsDefault|Označuje, že toto je výchozí jazyk. Toto se používá jako na podzim zpět jazyka, pokud jazyk uživatelského prostředí zákazníkem zvolené není k dispozici.|
|Národní prostředí: Logo|To pokud logo, které chcete použít pro tento balíček. Velikost ikony je 32 x 32. Povolené formáty jsou PNG a JPG|
|Národní prostředí: podmínky: PackageTerm.File|Toto je název souboru dokumentu HTML, který obsahuje licenční podmínky.|

Zde je, kde bude zobrazeno:

![CRMScreenShot5](media/CRMScreenShot5.png)

Posledním krokem je následující kód do jednoho souboru zip.

1. ZIP (vytvořenou dříve)
2. **Content_Types.xml**
3. xml
4. PNG
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Přejmenujte soubor na nějaké vhodné pro vaši aplikaci. Jsme dáváte přednost, že složku zahrnujete název společnosti a název aplikace. Příklad: **_Microsoft_SamplePackage.zip**.
