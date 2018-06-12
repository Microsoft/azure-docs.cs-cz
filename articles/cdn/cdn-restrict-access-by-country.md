---
title: Omezit přístup k obsahu Azure CDN podle země | Microsoft Docs
description: Zjistěte, jak omezit přístup k vašemu obsahu Azure CDN pomocí funkce geograficky filtrování.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 93321c4c8a7f8d79835d702ca07132eed94f6493
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260748"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Omezit přístup k obsahu Azure CDN podle země

## <a name="overview"></a>Přehled
Když si uživatel vyžádá obsah, ve výchozím nastavení, je zpracovat obsah bez ohledu na to, kde uživatel provedené této žádosti z. V některých případech můžete chtít omezit přístup k vašemu obsahu podle země. Tento článek vysvětluje, jak používat *filtrování geograficky* funkci chcete-li nakonfigurovat službu, kterou chcete povolit nebo blokovat přístup podle země.

> [!IMPORTANT]
> Produkty Azure CDN všechny nabízí stejnou funkčnost geograficky filtrování, ale mají malý rozdíl v te země kódy, které podporují. Další informace najdete v tématu [kódy zemí CDN Azure](https://msdn.microsoft.com/library/mt761717.aspx).


Informace o aspektech, které platí pro konfiguraci tohoto typu omezení najdete v tématu [aspekty](cdn-restrict-access-by-country.md#considerations).  

![Filtrování podle země](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Krok 1: Definování cesta k adresáři
> [!IMPORTANT]
> **Azure CDN Standard od společnosti Microsoft** profily nepodporují filtrování geograficky na základě cesty.
>


Vyberte koncový bod služby v rámci portálu a najít na kartě filtrování geograficky v levé navigaci k vyhledání této funkce.

Při konfiguraci filtr země, zadejte relativní cestu k umístění, do které se uživatelům povoluje nebo odepírá přístup. Můžete použít geograficky filtrování pro všechny soubory s lomítkem (/) nebo vybrané složky zadáním cesty adresářů */pictures/*. Můžete taky použít filtrování geograficky do jednoho souboru zadáním soubor a ponechat si do adresy koncové lomítko */pictures/city.png*.

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
Funkce filtrování geograficky používá kódy zemí k definování zemí, ze kterých se žádost o povolené nebo blokované pro zabezpečené adresář. I když všechny produkty Azure CDN nabízí stejnou funkčnost geograficky filtrování, je malý rozdíl v zemi kódy, které podporují. Další informace najdete v tématu [kódy zemí CDN Azure](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a name="considerations"></a>Požadavky
* Změny konfigurace filtrování země se projeví okamžitě:
   * Šíření profilů **Azure CDN Standard od Microsoftu** trvá většinou 10 minut. 
   * V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   * Pro **Azure CDN Standard od společnosti Verizon** a **Azure CDN Premium od společnosti Verizon** profily, šíření obvykle se dokončí za 10 minut. 
 
* Tato funkce nepodporuje zástupné znaky (například ' *').

* Konfigurace filtrování geograficky spojené s relativní cestou bude rekurzivně této cesty.

* Pouze jedno pravidlo můžete použít stejné relativní cestu. To znamená nelze vytvořit více filtrů země, které odkazují na stejné relativní cestě. Složku však může mít více filtrů země, vzhledem k povaze rekurzivní země filtrů. Jinými slovy podsložkou dříve nakonfigurované složky lze přiřadit jiné zemi filtru.

