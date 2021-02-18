---
title: Zlepšení výkonu pomocí komprimace souborů v Azure front-Premium Standard/Premium (Preview)
description: Přečtěte si, jak vylepšit rychlost přenosu souborů a zvýšit výkon při načítání stránek pomocí komprimace souborů v předních dveřích Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098992"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Zlepšení výkonu pomocí komprimace souborů v Azure front-Premium Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Komprese souborů je efektivní způsob, jak zlepšit rychlost přenosu souborů a zvýšit výkon při načítání stránek. Komprese zmenšuje velikost souboru před jeho odesláním serverem. Komprese souborů může snížit náklady na šířku pásma a zajistit lepší prostředí pro uživatele.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Existují dva způsoby, jak povolit kompresi souborů:

- Povoluje se komprimace na zdrojovém serveru. Přední dvířka Azure předávají podél komprimovaných souborů a doručují je klientům, kteří je požadují.
- Přímé zapínání komprese přímo na serverech POP na front-dveřích systému Azure (*Komprese za běhu*). V takovém případě Azure na předních dveřích komprimuje soubory a pošle je koncovým uživatelům.

> [!IMPORTANT]
> Změny konfigurace front-dveří Azure během celé sítě trvá až 10 minut. Pokud pro koncový bod CDN nastavujete kompresi poprvé, zvažte možnost počkat 1-2 hodin před řešením potíží, abyste zajistili, že se nastavení komprese rozšíří do všech bodů POP.

## <a name="enabling-compression"></a>Povolení komprese

> [!Note]
> V případě Azure front-dveří je komprese součástí **Povolení ukládání do mezipaměti** v trase. Jenom v případě, že **povolíte ukládání do mezipaměti**, můžete využít komprimaci v Azure front-dveřích.

Kompresi můžete povolit následujícími způsoby:
* Během rychlého vytvoření – Pokud povolíte ukládání do mezipaměti, můžete povolit kompresi.
* Při přidávání trasy můžete během vlastního vytváření ukládat do mezipaměti a komprese povolit ukládání do mezipaměti. 
* V cestě správce koncového bodu.
* Na stránce optimalizace.

### <a name="enable-compression-in-endpoint-manager"></a>Povolení komprese ve Správci koncových bodů

1. Na stránce s profilem Standard/Premium na frontě Azure klikněte na **správce koncových bodů** a vyberte koncový bod, u kterého chcete povolit kompresi.

1. Vyberte **Upravit koncový bod** a pak vyberte **trasu** , kterou chcete povolit kompresi. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Snímek úvodní stránky správce koncových bodů." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Zajistěte, aby bylo zaškrtnuto políčko **Povolit ukládání do mezipaměti** , a pak zaškrtněte políčko **Povolit kompresi**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Povolí kompresi ve Správci koncových bodů.":::   

1. Kliknutím na tlačítko **aktualizovat** uložte konfiguraci.

### <a name="enable-compression-in-optimization"></a>Povolit kompresi v optimalizaci

1. Na stránce s profilem Standard/Premium na frontě Azure můžete přejít na **optimalizace** v části nastavení. Rozbalením koncového bodu zobrazíte seznam tras. 

1. Vyberte tři tečky vedle **trasy** , která má *zakázanou* kompresi. Pak vyberte **Konfigurovat trasu**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Obrazovka povolení komprese na stránce optimalizace." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Zajistěte, aby bylo zaškrtnuto políčko **Povolit ukládání do mezipaměti** , a pak zaškrtněte políčko **Povolit kompresi**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Snímek obrazovky s povolením komprese ve Správci koncových bodů."::: 

1. Klikněte na **Aktualizovat**.

## <a name="modify-compression-content-type"></a>Upravit typ kompresního obsahu

Můžete upravit výchozí seznam typů MIME na stránce optimalizace.

1. Na stránce s profilem Standard/Premium na frontě Azure můžete přejít na **optimalizace** v části nastavení. Pak vyberte **trasu** , která má *povolenou* kompresi.

1. Vyberte tři tečky vedle **trasy** s *povolenou* kompresí. Pak vyberte **zobrazit komprimované typy souborů**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Snímek stránky optimalizace" lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Odstraňte výchozí formáty nebo vyberte **Přidat** a přidejte tak nové typy obsahu.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Snímek obrazovky přizpůsobení stránky komprese souborů"::: 

1. Vyberte **Uložit** a aktualizujte konfiguraci komprese.

## <a name="disabling-compression"></a>Zakázání komprese

Kompresi můžete vypnout následujícími způsoby:
* Zakažte kompresi v cestě správce koncových bodů.
* Zakáže kompresi na stránce optimalizace.

### <a name="disable-compression-in-endpoint-manager"></a>Zakázání komprese ve Správci koncových bodů

1. Na stránce s profilem Standard/Premium na frontě Azure klikněte v části nastavení na **správce koncových bodů** . Vyberte koncový bod, pro který chcete vypnout kompresi.

1. Vyberte **Upravit koncový bod** a pak vyberte **trasu** , kterou chcete zakázat kompresi. Zrušte zaškrtnuté políčko **Povolit kompresi** .

1. Kliknutím na tlačítko **aktualizovat** uložte konfiguraci.

### <a name="disable-compression-in-optimizations"></a>Zakázat kompresi v optimalizacích

1. Na stránce s profilem Standard/Premium na frontě Azure můžete přejít na **optimalizace** v části nastavení. Pak vyberte **trasu** , která má *povolenou* kompresi.

1. Vyberte tři tečky vedle **trasy** s *povolenou* kompresí a pak vyberte *Konfigurovat trasu*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Snímek obrazovky se zakázanou kompresí na stránce optimalizace"::: 

1. Zrušte zaškrtnuté políčko **Povolit kompresi** .

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Snímek stránky aktualizačního postupu pro vypnutí komprese"::: 

1. Kliknutím na tlačítko **aktualizovat** uložte konfiguraci.

## <a name="compression-rules"></a>Pravidla komprese

V dopředných dveřích Azure se komprimují jenom vhodné soubory. Aby měl soubor nárok na kompresi, musí mít následující:
* Být typu MIME 
* Být větší než 1 KB
* Být menší než 8 MB

Tyto profily podporují následující kódování komprese:
* gzip (GNU zip)
* brotli 

Pokud požadavek podporuje více než jeden typ komprese, má komprese brotli přednost.

Když žádost o assetu určuje kompresi gzip a výsledkem požadavku bude neúspěšné přístupy do mezipaměti, přední dveře Azure převede kompresi assetu přímo na server POP. Následně bude komprimovaný soubor obsluhován z mezipaměti.

Pokud zdroj používá pro odesílání komprimovaných dat do služby Azure front-end Encoding kódování (CTE), nepodporují se velikosti odpovědí větší než 8 MB. 

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak nakonfigurovat první [sadu pravidel](how-to-configure-rule-set.md) .
- Další informace o [podmínkách shody sady pravidel](concept-rule-set-match-conditions.md)
- Další informace o [sadě pravidel pro přední dveře Azure](concept-rule-set.md)
