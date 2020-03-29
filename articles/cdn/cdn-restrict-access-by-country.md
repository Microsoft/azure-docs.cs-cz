---
title: Omezení obsahu Azure CDN podle země nebo oblasti | Dokumenty společnosti Microsoft
description: Zjistěte, jak omezit přístup podle země nebo oblasti k obsahu Azure CDN pomocí funkce geografického filtrování.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 75c422d456f2509ce478e2609a6509f78a6eb31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593433"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Omezení obsahu Azure CDN podle země nebo oblasti

## <a name="overview"></a>Přehled
Když uživatel požaduje váš obsah, ve výchozím nastavení je obsah doručen bez ohledu na umístění uživatele, který žádost podává. V některých případech však můžete chtít omezit přístup k obsahu podle země nebo oblasti. Pomocí funkce *geografického filtrování* můžete vytvořit pravidla pro konkrétní cesty v koncovém bodě CDN a povolit nebo blokovat obsah ve vybraných zemích nebo oblastech.

> [!IMPORTANT]
> **Azure CDN Standard od Microsoftu** profily nepodporují geografické filtrování založené na cestě.
> 

## <a name="standard-profiles"></a>Standardní profily
Postupy v této části jsou určeny pro **Azure CDN Standard od Akamai** a **Azure CDN Standard od Verizon** pouze profily. 

Pro **profily Azure CDN Premium od verizonu** musíte k aktivaci geografického filtrování použít portál **pro správu.** Další informace najdete [v tématu Azure CDN Premium od Verizon profily](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definování cesty k adresáři
Chcete-li získat přístup k funkci geografického filtrování, vyberte koncový bod CDN na portálu a v levé nabídce vyberte **možnost Geografické filtrování** v části NASTAVENÍ. 

![Standard geografického filtrování](./media/cdn-filtering/cdn-geo-filtering-standard.png)

V poli **CESTA** zadejte relativní cestu k umístění, ke kterému bude uživatelům povolen nebo odepřen přístup. 

Můžete použít geografické filtrování pro všechny soubory s lomítkem (/) nebo vybrat konkrétní složky zadáním cest adresáře (například */pictures/*). Můžete také použít geografické filtrování na jeden soubor (například */pictures/city.png).* Je povoleno více pravidel; Po zadání pravidla se zobrazí prázdný řádek, který můžete zadat další pravidlo.

Například všechny následující filtry cest adresáře jsou platné:   
*/*                                 
*/Fotografie/*     
*/Fotografie/Štrasburk/*     
*/Fotografie/Štrasburk/město.png*

### <a name="define-the-type-of-action"></a>Definovat typ akce

V seznamu **AKCE** vyberte **Povolit** nebo **Blokovat**: 

- **Povolit**: Pouze uživatelé z určených zemí nebo oblastí mají povolen přístup k prostředkům požadovaným z rekurzivní cesty.

- **Blok**: Uživatelům ze zadaných zemí nebo oblastí je odepřen přístup k prostředkům požadovaným z rekurzivní cesty. Pokud pro toto umístění nebyly nakonfigurovány žádné jiné možnosti filtrování zemí nebo oblastí, bude přístup povolen všem ostatním uživatelům.

Například pravidlo geografického filtrování pro blokování cesty */Photos/Strasbourg/* filtruje následující soubory:     
*\//http:\<koncový bod>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<koncový bod>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definování zemí nebo oblastí
V seznamu **KÓDY ZEMÍ** vyberte země nebo oblasti, které chcete zablokovat, nebo povolit cestu. 

Po dokončení výběru zemí nebo oblastí vyberte **Uložit,** chcete-li aktivovat nové pravidlo geografického filtrování. 

![Pravidla geografického filtrování](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
Chcete-li pravidlo odstranit, vyberte ho ze seznamu na stránce **Geografické filtrování** a pak zvolte **Odstranit**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profily Azure CDN Premium od společnosti Verizon
Pro **azure cdn premium od** verizonprofily, uživatelské rozhraní pro vytvoření geografické filtrování pravidlo se liší:

1. V horní nabídce profilu Azure CDN vyberte **Spravovat**.

2. Na portálu Verizon vyberte **možnost HTTP Large**a potom vyberte Filtrování **zemí**.

    ![Standard geografického filtrování](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Vyberte **Přidat filtr země**.

    Zobrazí se stránka **Step One:** .

4. Zadejte cestu k adresáři, vyberte **Blokovat** nebo **Přidat**a pak vyberte **Další**.

    Zobrazí se **stránka Krok dva:** 

5. Vyberte ze seznamu jednu nebo více zemí nebo oblastí a pak vyberte **Dokončit,** chcete-li pravidlo aktivovat. 
    
    Nové pravidlo se zobrazí v tabulce na stránce **Filtrování zemí.**

    ![Pravidla geografického filtrování](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Vyčištění prostředků
V tabulce pravidel filtrování zemí nebo oblastí vyberte ikonu odstranění vedle pravidla, kterou chcete odstranit, nebo ji upravit.

## <a name="considerations"></a>Požadavky
* Změny konfigurace geografického filtrování se neprojeví okamžitě:
   * U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
   * V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   * V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 10 minut. 
 
* Tato funkce nepodporuje zástupné znaky (například *).

* Konfigurace geografického filtrování přidružená k relativní cestě je na tuto cestu použita rekurzivně.

* Na stejnou relativní cestu lze použít pouze jedno pravidlo. To znamená, že nelze vytvořit více filtrů země nebo oblasti, které odkazují na stejnou relativní cestu. Protože jsou však filtry země nebo oblasti rekurzivní, může mít složka více filtrů země nebo oblasti. Jinými slovy podsložce dříve nakonfigurované složky lze přiřadit jiný filtr země nebo oblasti.

* Funkce geografického filtrování používá kódy zemí k definování zemí nebo oblastí, ze kterých je požadavek povolen nebo blokován pro zabezpečený adresář. Přestože profily Akamai a Verizon podporují většinu stejných kódů zemí, existuje několik rozdílů. Další informace naleznete v [tématu Azure CDN kódy zemí](/previous-versions/azure/mt761717(v=azure.100)). 

