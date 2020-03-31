---
title: Šablony tokenů Azure Blockchain
description: Šablony tokenů Azure Blockchain jsou standardizované a opakovaně použitelné šablony, které zjednodušují vytváření a nasazování tokenů založených na hlavní knize.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252204"
---
# <a name="azure-blockchain-tokens-templates"></a>Šablony tokenů Azure Blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Šablona Tokenů Blockchain Azure je standardizovaná a opakovaně použitelná šablona, která zjednodušuje vytváření a nasazování tokenů založených na hlavní knize. Šablona se skládá ze vzorce, který je založen na gramatice [Token Taxonomy Framework (TTF).](overview.md#token-taxonomy-framework) Gramatika zahrnuje typ základního tokenu a sadu chování pro token.  

Například šablona tokenu **τ{{d,m,b,r}** popisuje zaměnitelný základní token, který je sub-dělitelný, mintable, burnable a má podporu role.
  
## <a name="base-token-types"></a>Typy základních tokenů

Při definování a vytváření tokenu založeného na hlavní knize pro konkrétní majetek je důležité zvážit, jaký základní token použít.

### <a name="fungible"></a>Zaměnitelné

Zaměnitelné tokeny (τF) mají vzájemně zaměnitelnou hodnotu, pokud jsou ve stejné třídě nebo řadě. Jeden token má stejnou hodnotu jako jiný token nebo dané množství tokenů má stejnou hodnotu jako jiné stejné množství. Například dolar je zastupitelný žeton. Pokud dva lidé jsou každý drží dolarovou bankovku, mohou vyměnit tyto dolarové bankovky bez následků. Dolarové bankovky mají stejnou hodnotu. 

### <a name="non-fungible"></a>Nezastupitelné

Nezastupitelné tokeny (τN) nejsou zaměnitelné s jinými tokeny stejného typu, jako mají obvykle různé hodnoty. Například název vlastnosti je nezastupitelný token. Majetkové tituly do dvou různých bytů v bytovém komplexu nemusí mít nutně stejnou hodnotu, a to buď z důvodu umístění jednotky nebo z důvodu, na kterém patře se jednotka nachází. Vnímaná hodnota dvou tokenů název vlastnosti nejsou stejné.

### <a name="hybrid"></a>Hybridní

Hybridní tokeny jsou tokeny, které mají součásti zaměnitelné tokeny a nezastupitelné tokeny. Hybridní token je typ základního tokenu, který vlastní třídu jiného typu tokenu.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybridní nezastupitelná základna s zastupitelnými segmenty

Hybridní nezastupitelná základna s tokenem zastupitelných segmentů má nezastupitelnou základnu s zastupitelnými segmenty tokenů.
Například vstupenka na koncert je hybridní žeton, kde datum a čas koncertu je nezastupitelný základní token. Vstupenky v různých sekcích sedadel pro daný koncert jsou segmenty s zastupitelnými žetony. Vstupenky jsou vyměnitelné v jednotlivých sekcích sedadel, ale ne napříč sekcemi.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybridní zastupitelná základna s nezastupitelnými segmenty

Hybridní zastupitelná základna s tokenem nezastupitelných segmentů má zastupitelnou základnu s nezastupitelnými segmenty tokenů. Například zabezpečení s hypotékou je hybridní token, kde více vlastníků je zastupitelnou základnou, která je rozdělena mezi mnoho vlastníků. Bezpečnost je zaměnitelná. Jednotlivé hypotéky jsou nezastupitelné segmenty, které představují konkrétní hypotéku couval cenných papír.

## <a name="token-behaviors"></a>Chování tokenu

Chování tokenu definuje možnosti nebo omezení tokenu. Chování zahrnuje podpůrné vlastnosti, které jsou součástí definice tokenu. Chování lze použít napříč všemi typy tokenů nebo pouze jeden. Chování může být interní nebo externí v závislosti na tom, jaké účinky chování. Vnitřní chování umožňuje nebo omezuje vlastnosti na samotný token. Externí chování umožňuje nebo omezuje vyvolání chování z externího objektu actor.

Další informace o chování tokenů Tokeny Token taxonomy Framework (TTF) podporované Azure Blockchain tokeny, najdete v článku [slohozovací schopnost tokenu](composability.md).

## <a name="pre-built-token-templates"></a>Předem sestavené šablony tokenů

Tokeny Azure Blockchain poskytují čtyři předem sestavené šablony tokenů, které lze použít bez úprav. Můžete volat do těchto předem vytvořených šablon pro většinu případů použití a rychle začít vytvářet, nasazovat a spravovat tokeny.

### <a name="commodity-tokens"></a>Komoditní žetony

Komoditní tokeny mají konzistentní hodnotu a jsou přenositelné. Například barel ropy nebo jednotka energie.

**τF{~d,t,m,b,r}** - zastupitelné, celé, přenosné, mintable, burnable a mají podporu role

Mnoho scénářů blockchainu vyžaduje transparentnost a viditelnost v dodavatelském řetězci nebo v několika organizacích. Komoditní tokeny jsou založeny na těchto běžných případech použití. Tokeny jsou zaměnitelné a konzistentní. Šablona komoditního tokenu je flexibilní a přizpůsobitelná pomocí metadat.

### <a name="qualified-tokens"></a>Kvalifikované žetony

Kvalifikované tokeny představují něco získaného a jsou obvykle přidruženy k jedné entitě a nelze je převést. Například diplom nebo porušení parkovacích míst.

**τN{s,~t}** - nezastupitelné, singleton a nepřenosné

Různé scénáře auditu a ověřování vyžadují, aby vlastnictví tokenu nelze změnit. Existuje sada případů použití, které mají potřebu poskytnout kvalifikovaný token, zda je přidružení dobré nebo špatné.

### <a name="asset-tokens"></a>Tokeny majetku

Tokeny majetku mají jedinečnou hodnotu v závislosti na položce a nejsou komoditizovány. Například muzejní artefakt nebo název nemovitosti.

**τN{s,t}** - nezastupitelné, singleton a přenositelné

Tokeny aktiv mohou být zaměněny s komoditními tokeny. Hlavní rozdíl mezi dvěma tokeny je, že tokeny majetku jsou ze své podstaty jedinečné a hodnota je nezávislá na typu tokenu, který je. Například umělecké dílo, jako je olejomalba od zavedeného umělce, je tokenem aktiv. Nicméně, umělecký tisk Mona Lisa je považován za komoditní token. Podobně název vlastnosti je token majetku, protože hodnota existuje v subjektivní vlastnosti vlastnosti.

### <a name="ticket-tokens"></a>Tokeny vstupenek

Tokeny lístku mají konzistentní hodnotu, ale obvykle vyprší. Například letenka.

**τN{m,b,r}** - nezastupitelné, mintable, burnable a mají podporu role.

Tokeny lístků mají obvykle datum vypršení platnosti, které je odlišuje od běžného komoditního tokenu. Například letenka, vstupenka na koncert nebo sportovní vstupenka mají možnost i přiřazené sezení s konkrétními daty použití. Nemůžete snadno vyměnit vstupenky mezi termíny nebo posezením.

## <a name="next-steps"></a>Další kroky

Pokud požadujete větší flexibilitu pro váš scénář, přečtěte si informace o vytváření vlastních šablon tokenů pomocí [kompostovatelnosti tokenů](composability.md).
