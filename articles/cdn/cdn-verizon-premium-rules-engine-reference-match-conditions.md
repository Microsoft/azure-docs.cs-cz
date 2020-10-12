---
title: Azure CDN z podmínek shody modulu pravidel Verizon Premium
description: Referenční dokumentace k Azure Content Delivery Network z podmínek shody stroje Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323310"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN z podmínek shody modulu pravidel Verizon Premium

V tomto článku jsou uvedené podrobné popisy dostupných podmínek shody pro Azure Content Delivery Network (CDN) z [modulu pravidel](cdn-verizon-premium-rules-engine.md)Verizon Premium.

Druhá část pravidla je podmínka shody. Podmínka shody identifikuje konkrétní typy požadavků, pro které budou provedeny sady funkcí.

Podmínku shody můžete například použít k těmto akcím:

- Filtrovat požadavky na obsah v určitém umístění.
- Vyfiltruje požadavky vygenerované z konkrétní IP adresy nebo země nebo oblasti.
- Vyfiltruje požadavky podle informací v záhlaví.

## <a name="match-conditions"></a><a name="top"></a>Podmínky shody

* [Vždy](#always)
* [Zařízení](#device)
* [Umístění](#location)
* [Zdroji](#origin)
* [Žádost](#request)
* [Adresa URL](#url)

### <a name="always"></a><a name="always"></a>Vždy

[Podmínka vždycky se shodou](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) je navržená tak, aby na všechny požadavky mohla používat výchozí sadu funkcí.

### <a name="device"></a><a name="device"></a>Zařízení

Tyto podmínky shody jsou navržené k identifikaci požadavků na základě uživatelského agenta klienta.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| Název značky | Identifikuje požadavky podle toho, jestli název značky zařízení odpovídá: <br> **-** Konkrétní hodnota ([literální název značky](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Regulární výraz ([název značky – regulární](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)výraz) <br> **-** Konkrétní vzor ([zástupný znak názvu značky](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Operační systém zařízení | Identifikuje požadavky podle toho, jestli operační systém zařízení odpovídá: <br> **-** Konkrétní hodnota ([LITERÁL systému zařízení](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Regulární výraz ([regulární výraz pro operační systém zařízení](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak operačního systému zařízení](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Verze operačního systému zařízení | Identifikuje požadavky podle toho, jestli verze operačního systému zařízení odpovídá: <br> **-** Konkrétní hodnota ([literál verze operačního systému zařízení](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Regulární výraz ([systém zařízení – regulární výraz verze](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak verze operačního systému zařízení](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Dvojitá orientace?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identifikuje požadavky podle toho, jestli zařízení podporuje dvojí orientaci. |
| Preferovaná deklarace DTD HTML | Identifikuje požadavky podle toho, jestli preferovaná deklarace DTD (HTML) zařízení odpovídá: <br> **-** Konkrétní hodnota ([preferovaný LITERÁL DTD jazyka HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Regulární výraz ([HTML preferovaný regulární výraz DTD](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Konkrétní vzor ([HTML preferovaný zástupný znak DTD](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Vkládání obrázku?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identifikuje požadavky podle toho, jestli zařízení podporuje obrázky kódované v kódování Base64. |
| [Je Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identifikuje požadavky podle toho, jestli zařízení používá operační systém Android. |
| [Je aplikace?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identifikuje požadavky podle toho, zda nativní aplikace požadovala obsah. |
| [Je úplná plocha?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identifikuje požadavky podle toho, jestli zařízení nabízí plnohodnotné možnosti práce s počítačem. |
| [Je iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identifikuje požadavky podle toho, jestli zařízení používá iOS. |
| [Je robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identifikuje požadavky podle toho, jestli se zařízení považuje za automatizovaného klienta HTTP (například crawler robota). |
| [Je inteligentní TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identifikuje požadavky podle toho, jestli je zařízení inteligentní TV. |
| [Je smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identifikuje požadavky podle toho, jestli je zařízení smartphone.
| [Je tablet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identifikuje požadavky podle toho, jestli je zařízení tabletem. |
| [Je dotyková obrazovka?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identifikuje požadavky podle toho, zda je primární polohovací zařízení zařízení dotykovou obrazovkou. |
| [Je Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identifikuje požadavky podle toho, jestli je zařízení Windows Mobile 6.5/Windows Phone 7 nebo novější. |
| [Je bezdrátové zařízení?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identifikuje požadavky podle toho, jestli je zařízení bezdrátové. 
| Název marketingu | Identifikuje požadavky podle toho, jestli název marketingu zařízení odpovídá: <br> **-** Konkrétní hodnota ([literál názvu marketingu](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Regulární výraz ([název marketingového regulárního](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)výrazu) <br> **-** Konkrétní vzor ([zástupný znak názvu marketingu](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Mobilní prohlížeč | Identifikuje požadavky podle toho, jestli prohlížeč zařízení odpovídá: <br> **-** Konkrétní hodnota ([literál v mobilním prohlížeči](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Regulární výraz ([regulární výraz pro mobilní prohlížeče](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak mobilního prohlížeče](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Verze mobilního prohlížeče | Identifikuje požadavky podle toho, jestli verze prohlížeče zařízení odpovídá: <br> **-** Konkrétní hodnota ([literál verze v mobilním prohlížeči](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Regulární výraz ([verze regulárního prohlížeče v mobilním prohlížeči](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak verze mobilního prohlížeče](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Název modelu | Identifikuje požadavky podle toho, jestli název modelu zařízení odpovídá: <br> **-** Konkrétní hodnota ([literál názvu modelu](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Regulární výraz ([název modelu regulárního](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)výrazu) <br> **-** Konkrétní vzor ([zástupný znak názvu modelu](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Progresivní stahování?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identifikuje požadavky podle toho, jestli zařízení podporuje progresivní stahování. |
| Datum vydání | Identifikuje požadavky podle toho, jestli datum vydání zařízení odpovídá: <br> **-** Konkrétní hodnota ([literály data vydání](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Regulární výraz ([regulární výraz datum vydání](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak pro datum vydání](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Výška rozlišení](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identifikuje požadavky na výšku zařízení. |
| [Rozlišení šířky](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identifikuje požadavky na šířku zařízení. |

**[Zpět na začátek](#top)**

### <a name="location"></a><a name="location"></a>Oblasti

Tyto podmínky shody jsou navržené k identifikaci požadavků na základě umístění žadatele.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [JAKO číslo](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identifikuje požadavky, které pocházejí z konkrétní sítě. |
| Název města | Identifikuje požadavky podle toho, zda pocházejí z města, jehož název odpovídá: <br> **-** Konkrétní hodnota ([název města, literál](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Regulární výraz ([město Name Regex](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Kontinent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identifikuje požadavky, které pocházejí ze zadaných kontinentů. |
| [Země](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identifikuje požadavky, které pocházejí ze zadaných zemí. |
| [DMA – kód](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identifikuje požadavky, které pocházejí ze zadaného METROS (určených oblastí trhu). |
| [Šířce](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identifikuje požadavky, které pocházejí ze zadaného Latitudes. |
| [Bodu](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identifikuje žádosti, které pocházejí od zadaných zeměpisných zeměpisných délky. |
| [Kód Metro](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identifikuje požadavky, které pocházejí ze zadaného METROS (určených oblastí trhu). |
| [Poštovní směrovací číslo](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identifikuje požadavky, které pocházejí ze zadaných poštovních kódů. |
| [Kód oblasti](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identifikuje požadavky, které pocházejí ze zadaných oblastí. |

> [!NOTE]
> **Mám použít kód metro nebo kód DMA?** <br>
Obě tyto podmínky shody poskytují stejnou funkci. Nicméně doporučujeme, abyste pomocí podmínky shody kódů Metro identifikovali požadavky na DMA.

**[Zpět na začátek](#top)**

### <a name="origin"></a><a name="origin"></a>Zdroji

Tyto podmínky shody jsou navržené k identifikaci požadavků, které odkazují na úložiště CDN nebo na server původu zákazníka.

| Name       | Účel                                                           |
|------------|-------------------------------------------------------------------|
| [Zdroj CDN](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identifikuje požadavky na obsah uložený v úložišti CDN. |
| [Původ zákazníka](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identifikuje požadavky na obsah uložený na konkrétním zákaznickém serveru. |

**[Zpět na začátek](#top)**

### <a name="request"></a><a name="request"></a>Žádost

Tyto podmínky shody jsou navržené k identifikaci požadavků na základě jejich vlastností.

| Name              | Účel                                                                |
|-------------------|------------------------------------------------------------------------|
| [Client IP Address](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identifikuje požadavky, které pocházejí z konkrétní IP adresy. |
| Parametr souboru cookie  | Identifikuje požadavek tím, že obsahuje soubor cookie, který odpovídá: <br> **-** Konkrétní hodnota ([literál parametru cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Regulární výraz ([parametr cookie regulárního](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) výrazu <br> **-** Specifický vzor ([zástupný znak parametru souboru cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Záznam CNAME okraje](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identifikuje požadavky, které ukazují na konkrétní hraniční záznam CNAME. |
| Odkazující doména | Identifikuje požadavek podle toho, zda odkazoval na název hostitele, který odpovídá: <br> **-** Konkrétní hodnota ([odkazující doménový literál](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Konkrétní vzor ([odkazující na zástupný znak domény](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Hlavička požadavku | Identifikuje požadavek tím, že obsahuje hlavičku, která odpovídá: <br> **-** Konkrétní hodnota ([literál hlavičky požadavku](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Regulární výraz ([Request Header Regex](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak hlavičky žádosti](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Request – metoda](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identifikuje požadavky metodou HTTP. |
| [Schéma žádosti](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identifikuje požadavky podle jejich protokolu HTTP. |

**[Zpět na začátek](#top)**

### <a name="url"></a><a name="url"></a>URL

| Name              | Účel                                                                |
|-------------------|------------------------------------------------------------------------|
| Cesta adresy URL | Identifikuje požadavky podle toho, zda jejich relativní cesta, včetně názvu souboru, odpovídá: <br> **-** Konkrétní hodnota ([literál cesty URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Regulární výraz ([výraz URL cesty](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Konkrétní vzor ([zástupný znak adresy URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| Adresář cesty URL | Identifikuje požadavky podle toho, zda jejich relativní cesta odpovídá: <br> **-** Konkrétní hodnota ([literál adresáře cesty URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Konkrétní vzor ([zástupný znak adresáře cesty URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| Přípona cesty URL | Identifikuje požadavky podle toho, jestli jejich Přípona souboru odpovídá: <br> **-** Konkrétní hodnota ([literál rozšíření cesty URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Konkrétní vzor ([zástupný znak rozšíření cesty URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| Název cesty URL | Identifikuje požadavky podle toho, zda název souboru odpovídá: <br> **-** Konkrétní hodnota ([literál cesta k souboru URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Konkrétní vzor ([zástupný znak názvu souboru cesty URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| Dotaz na adresu URL | Identifikuje požadavky podle toho, zda řetězec dotazu odpovídá: <br> **-** Konkrétní hodnota ([literál dotazu URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Regulární výraz ([výraz pro dotaz na adresu URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Specifický vzor ([zástupný znak URL pro dotaz](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| Parametr dotazu adresy URL | Identifikuje požadavky podle toho, jestli obsahují parametr řetězce dotazu nastaveného na hodnotu, která odpovídá: <br> **-** Konkrétní hodnota ([literál parametru dotazu URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Konkrétní vzor ([zástupný znak parametru dotazu URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Zpět na začátek](#top)**

Poslední podmínky shody najdete v [dokumentaci k modulu pravidel Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Další kroky

- [Přehled služby Azure Content Delivery Network](cdn-overview.md)
- [Odkazy na modul pravidel](cdn-verizon-premium-rules-engine-reference.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání výchozího chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
