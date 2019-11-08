---
title: Příprava balíčku AppSource | Azure Marketplace
description: Explanaion se, jak připravit a sestavovat balíčky AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2c6b78e62afb43562910c872d31e2c9f564040da
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806104"
---
# <a name="appsource-package-preparation"></a>Příprava balíčku AppSource

Kromě souboru Solution. zip budete potřebovat **balíček AppSource**. Toto je soubor. zip, který obsahuje všechny prostředky potřebné k automatizaci procesu nasazení vašeho řešení do prostředí CRM zákazníků. **Balíček AppSource** má následující součásti:

* Balíček pro nástroj pro nasazení balíčků
* **Content_Types soubor. XML** s prostředky, které používáte
* soubor XML s daty konkrétní aplikace
* logo 32x32, které se zobrazí s vaším seznamem v centru pro správu
* Licenční smlouvy, zásady ochrany osobních údajů

Následující postup vám pomůže vytvořit balíček AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Vytvoření balíčku pro nástroj pro nasazení balíčku

Balíček pro nástroj pro nasazení balíčku je jednou součástí balíčku AppSource.

Chcete-li vytvořit balíček pro nástroj pro nasazení balíčku, postupujte podle následujících pokynů: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Po dokončení se balíček bude skládat z níže uvedených prostředků:

1. Složka balíčku: obsahuje všechna řešení, konfigurační data, ploché soubory a obsah vašeho balíčku. _Poznámka: v následujícím příkladu se předpokládá, že se složka Package nazývá "PkgFolder"._
2. Knihovna DLL: sestavení obsahuje vlastní kód pro váš balíček. _Poznámka: v následujícím příkladu se předpokládá, že se tento soubor nazývá "MicrosoftSample. dll"._

Nyní je třeba vytvořit soubor s názvem "**Content_Types. XML**", ve kterém se budou zobrazovat všechna rozšíření assetů, která jsou součástí vašeho balíčku. Zde je příklad kódu souboru.

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

Posledním krokem je následující soubor zip do jednoho souboru. Zavolejte na soubor **Package. zip**. Bude obsahovat

1. PkgFolder (včetně všeho uvnitř složky)
2. DLL
3. **Content_Types. XML**

Postup vytvoření souboru Package. zip:

1. Do jednoho adresáře Umístěte složku balíčku, soubor **Content_Types. XML** a balíček. dll.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Vyberte všechny položky ve složce, klikněte pravým tlačítkem a zvolte Odeslat do komprimované složky (ZIP).

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Změňte název na Package. zip.

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Vytvoření balíčku AppSource

Balíček AppSource vyžaduje několik dalších souborů.

1. jpg (rozlišení 32x32)
2. HTML (soubor ve formátu HTML)
3. **Content_Types. XML** (stejné jako výše)
4. xml

Zde je příklad kódu pro Input. XML. Viz definice v níže uvedené tabulce.

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
 
**,**

|Pole|Podrobnosti|
|---|---|
|ProviderName|Pro koho řešení pochází. Pokud je tým Microsoftu, měl by to být Microsoft.|
|PackageFile |Prostředky pro nasazení balíčku: zip společně s obsahem soubor. XML\_Types. XML. Tento soubor zip by měl obsahovat sestavení nástroje pro nasazení balíčku a složku s prostředky pro nasazení balíčku. To znamená Package. zip|
|SolutionAnchorName |Název souboru ZIP řešení v nástroji pro nasazení balíčku, který se používá pro zobrazovaný název a popis prostředků řešení.|
| StartDate| Toto je datum, kdy bude balíček řešení dostupný. Formát je MM/DD/RRRR.|
|EndDate|Toto je datum, kdy se balíček řešení zastaví. Formát je MM/DD/RRRR. |
|SupportedCountries |Toto je seznam zemí/oblastí oddělených čárkami, které by se měly zobrazit v tomto balíčku. Pokud chcete zobrazit seznam všech kódů zemí, obraťte se na online služby. V době psaní tohoto seznamu: AE, AL, AM, AO, AR, AT, AU, AZ, BA, BB, BD, BH, BG,, BM, BN, BO, BR, by, CA, CH, CI, CL, CM, DO,,,,,,, CV, ES, EE, např. , ES, FI, FR, GB, GE, GH, GR, GT, HK, HN, HR, HU, ID, IE, IL, IN, SWEETIQ, IS, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, KG, LK, LT, LU, MN, LY, MT, MÍ, MX, MY , NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PR, PS, PT, PY, QA, RO, RS, RU, RW, SA, T, SG,, A, SK, SN, SV, TH, TM, TN, TR, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | Adresa URL stránky s dalšími informacemi pro tento balíček. |
|Národní prostředí|Jedna instance tohoto uzlu pro každý jazyk uživatelského rozhraní, který chcete podporovat v uživatelském rozhraní preferovaného řešení. Tento uzel obsahuje podřízené položky, které popisují národní prostředí, logo a pojem pro jednotlivé jazyky.|
|Národní prostředí: PackageLocale. Code|LCID jazyka pro tento uzel. Příklad: US English je 1033|
|Národní prostředí: PackageLocale. výchozí|Označuje, že toto je výchozí jazyk. Tato možnost se používá jako jazyk zpětného volání, pokud není k dispozici jazyk UX vybraný zákazníkem.|
|Národní prostředí: logo|To, pokud logo, které chcete použít pro tento balíček. Velikost pro ikonu je 32x32. Povolené formáty jsou PNG a JPG.|
|Národní prostředí: terms: PackageTerm. File|Toto je název souboru HTML dokumentu, který obsahuje vaše licenční smlouvy.|

Tady je místo, kde se zobrazí logo:

![CRMScreenShot5](media/CRMScreenShot5.png)

Posledním krokem je následující soubor zip do jednoho souboru.

1. zip (vytvořeno dříve)
2. **Content_Types. XML**
3. xml
4. PNG
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Přejmenujte soubor na něco vhodného pro vaši aplikaci. Upřednostňujeme, abyste zahrnuli název vaší společnosti a název aplikace. Například: **_Microsoft_SamplePackage. zip**.
