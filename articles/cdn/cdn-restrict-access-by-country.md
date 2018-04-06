---
title: Omezit přístup k obsahu Azure CDN podle země | Microsoft Docs
description: Zjistěte, jak omezit přístup k vašemu obsahu Azure CDN pomocí funkce geograficky filtrování.
services: cdn
documentationcenter: ''
author: lichard
manager: akucer
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="restrict-azure-cdn-content-by-country"></a>Omezit přístup k obsahu Azure CDN podle země

## <a name="overview"></a>Přehled
Když si uživatel vyžádá obsah, ve výchozím nastavení, je zpracovat obsah bez ohledu na to, kde uživatel provedené této žádosti z. V některých případech můžete chtít omezit přístup k vašemu obsahu podle země. Toto téma vysvětluje, jak používat **filtrování geograficky** funkci chcete-li nakonfigurovat službu, kterou chcete povolit nebo blokovat přístup podle země.

> [!IMPORTANT]
> Verizon a Akamai produkty nabízí stejnou funkčnost geograficky filtrování, ale mají malý rozdíl v te země kódy, které podporují. Odkaz na rozdíly najdete v kroku 3.


Informace o aspektech, které platí pro konfiguraci tohoto typu omezení najdete v tématu [aspekty](cdn-restrict-access-by-country.md#considerations) na konci tohoto tématu.  

![Filtrování podle země](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Krok 1: Definování cesta k adresáři
Vyberte koncový bod služby v rámci portálu a najít na kartě filtrování geograficky v levé navigaci k vyhledání této funkce.

Při konfiguraci filtr země, zadejte relativní cestu k umístění, do které se uživatelům povoluje nebo odepírá přístup. Filtrování geograficky můžete použít pro všechny soubory s "/" nebo vybrané složky zadáním cesty k adresáři "/ obrázky /". Můžete taky použít filtrování geograficky do jednoho souboru zadáním soubor a ponechat si do adresy koncové lomítko "/ obrazky/Mesto.png".

Příklad filtru cesta adresáře:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Krok 2: Definování akce: blokování nebo povolení
**Blokování:** uživatelů ze zadané zemí bude odepřen přístup k prostředkům vyžádaný z této cestě rekurzivní. Pokud pro toto umístění nebyly nakonfigurovány žádné jiné země možnosti filtrování, pak všichni ostatní uživatelé budou mít povolený přístup.

**Povolit:** jen uživatelé ze zemí zadaný budou mít povolený přístup k prostředkům vyžádaný z této cestě rekurzivní.

## <a name="step-3-define-the-countries"></a>Krok 3: Definování zemí
Vyberte země, ve které chcete blokovat nebo povolit pro danou cestu. 

Například pravidlo blokování /Photos/Štrasburku/bude filtrovat soubory včetně:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Kódy zemí
**Filtrování geograficky** funkce používá k definování zemí, ze kterých se žádost o povolené nebo blokované pro zabezpečené adresář kódy zemí. Zjistíte, kódy zemí v [kódy zemí CDN Azure](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Důležité informace
* Verizon nebo několik minut s Akamai, změny ve vaší zemi filtrování k provedení změn konfigurace může trvat až 90 minut.
* Tato funkce nepodporuje zástupné znaky (například ' *').
* Konfigurace filtrování geograficky spojené s relativní cestou bude rekurzivně této cesty.
* Pouze jedno pravidlo můžete použít stejné relativní cestu (nemůžete vytvořit více filtrů země, které odkazují na stejné relativní cestě. Do složky, ale může mít více filtrů země. Toto je vzhledem k povaze rekurzivní země filtrů. Jinými slovy podsložkou dříve nakonfigurované složky lze přiřadit jiné zemi filtru.

