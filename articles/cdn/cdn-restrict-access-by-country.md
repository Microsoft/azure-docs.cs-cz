---
title: Omezit přístup k obsahu Azure CDN podle země | Microsoft Docs
description: Zjistěte, jak omezit přístup podle země k vašemu obsahu Azure CDN pomocí funkce geograficky filtrování.
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
ms.date: 06/19/2018
ms.author: v-deasim
ms.openlocfilehash: 661356aeb2369bc1bbddd6caee57b256dd9e1212
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285011"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Omezit přístup k obsahu Azure CDN podle země

## <a name="overview"></a>Přehled
Když si uživatel vyžádá obsah, ve výchozím nastavení, je zpracovat obsah bez ohledu na polohu uživatele, který vytvořil požadavek. Ale v některých případech můžete omezit přístup k vašemu obsahu podle země. Pomocí *filtrování geograficky* funkce, můžete vytvořit pravidla v určité cesty na koncový bod CDN povolit nebo blokovat obsah ve vybraných zemích.

> [!IMPORTANT]
> **Azure CDN Standard od společnosti Microsoft** profily nepodporují filtrování geograficky na základě cesty.
> 

## <a name="standard-profiles"></a>Standardní profily
Postupy v této části jsou pro **Azure CDN Standard od společnosti Akamai** a **Azure CDN Standard od společnosti Verizon** pouze profily. 

Pro **Azure CDN Premium od společnosti Verizon** profily, je nutné použít **spravovat** portál aktivovat geograficky filtrování. Další informace najdete v tématu [Azure CDN Premium od Verizon profily](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Zadejte cestu k adresáři
Pro přístup k funkci filtrování geograficky, vyberte koncový bod CDN v rámci portálu a pak vyberte **filtrování geograficky** v části nastavení v levé nabídce. 

![Filtrování geograficky standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Z **cesta** zadejte relativní cestu k umístění, do které se uživatelům povoluje nebo odepírá přístup. 

Můžete použít geograficky filtrování pro všechny soubory s předání lomítko (/), nebo vybrat konkrétní složky zadáním cesty adresářů (například */pictures/*). Můžete taky použít filtrování geograficky do jednoho souboru (například */pictures/city.png*). Více pravidel jsou povoleny. Po zadání pravidlo, zobrazí se prázdný řádek můžete zadat další pravidla.

Například následující filtry cesta adresáře jsou platné:   
*/*                                 
*/Photos/*     
*/Photos/Štrasburku /*     
*/Photos/Strasbourg/City.PNG*

### <a name="define-the-type-of-action"></a>Zadejte typ akce

Z **akce** seznamu, vyberte **povolit** nebo **bloku**: 

- **Povolit**: pouze ze zadané zemí můžou uživatelé přístup k prostředkům, které jsou požadovány cestu rekurzivního.

- **Blok**: uživatelé ze zemí zadaný mají odepřený přístup k prostředkům vyžádaný z cestu rekurzivního. Pokud pro toto umístění nebyly nakonfigurovány žádné jiné země možnosti filtrování, pak všichni ostatní uživatelé budou mít povolený přístup.

Například filtrování geograficky pravidla pro blokování cesta */fotografie/Štrasburku/* filtry následující soubory:     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Definování zemí
Z **kódy zemí** vyberte zemích, které chcete blokovat nebo povolit pro danou cestu. 

Po dokončení výběru zemí vyberte **Uložit** aktivovat nové pravidlo filtrování geograficky. 

![Pravidla filtrování geograficky](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odstranit pravidlo, vyberte ho ze seznamu na **filtrování geograficky** stránky, a potom vyberte **odstranit**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium od Verizon profily
Pro **pro Azure CDN Premium od společnosti Verizon** profily, uživatelské rozhraní pro vytvoření pravidla filtrování geograficky se liší:

1. V hlavní nabídce ve vašem profilu Azure CDN, vyberte **spravovat**.

2. Z portálu Verizon, vybrat **HTTP velké**, pak vyberte **filtrování podle země**.

    ![Filtrování geograficky standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Vyberte **přidejte filtr země**.

    **Jeden krok:** se zobrazí stránka.

4. Zadejte cestu k adresáři, vyberte **bloku** nebo **přidat**, pak vyberte **Další**.

    **Krok dva:** se zobrazí stránka. 

5. Vyberte jeden nebo více zemích ze seznamu a pak vyberte **Dokončit** aktivujete pravidlo. 
    
    Nové pravidlo, které se objeví v tabulce na **filtrování podle země** stránky.

    ![Pravidla filtrování geograficky](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
V tabulce země filtrování pravidla vyberte ikonu odstranění vedle na pravidlo můžete odstranit nebo na ikonu pro úpravu jej upravit.

## <a name="considerations"></a>Požadavky
* Změny konfigurace filtrování geograficky se projeví okamžitě:
   * Šíření profilů **Azure CDN Standard od Microsoftu** trvá většinou 10 minut. 
   * V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   * V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
 
* Tato funkce nepodporuje zástupné znaky (například *).

* Konfigurace filtrování geograficky spojené s relativní cestou je rekurzivně této cesty.

* Pouze jedno pravidlo můžete použít stejné relativní cestu. To znamená nelze vytvořit více filtrů země, které odkazují na stejné relativní cestě. Ale protože země filtry jsou rekurzivní, složka může mít více filtrů země. Jinými slovy podsložkou dříve nakonfigurované složky lze přiřadit jiné zemi filtru.

* Funkce filtrování geograficky používá kódy zemí k definování zemí, ze kterých je povolené nebo blokované pro zabezpečené adresář žádost. Přestože Akamai a Verizon profily podporovat většinu stejné kódy zemí, existuje několik rozdílů. Další informace najdete v tématu [kódy zemí Azure CDN](https://msdn.microsoft.com/library/mt761717.aspx). 

