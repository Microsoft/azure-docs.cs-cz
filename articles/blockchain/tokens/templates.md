---
title: Šablony tokenů Azure blockchain
description: Šablony tokenů Azure blockchain jsou standardizované a opakovaně použitelné šablony, které zjednodušují vytváření a nasazování tokenů založených na hlavní knize.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324984"
---
# <a name="azure-blockchain-tokens-templates"></a>Šablony tokenů Azure blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Šablona tokenů Azure Blockchain je standardizovaná a opakovaně použitelná šablona, která zjednodušuje vytváření a nasazování tokenů založených na hlavní knize. Šablona se skládá ze vzorce, který je založen na gramatikě [tokenů taxonomie (TTF) tokenů](overview.md#token-taxonomy-framework) . Gramatika zahrnuje základní typ tokenu a sadu chování pro token.  

Například šablona tokenu **τϜ {d, m, b, r}** popisuje fungible základní token, který je dílčí dělitelné, mintable, vypálitelné a má podporu rolí.
  
## <a name="base-token-types"></a>Základní typy tokenů

Při definování a vytváření tokenu založeného na hlavní knize pro konkrétní prostředek je důležité zvážit, jaký základní token se má použít.

### <a name="fungible"></a>Fungible

Fungible tokeny (τF) mají vzájemně zaměnitelné hodnoty, pokud jsou ve stejné třídě nebo řadách. Jeden token má stejnou hodnotu jako jiný token nebo dané množství tokenů má stejnou hodnotu jako jiné stejné množství. Například dolar je fungible token. Pokud si každý z nich drží fakturu ze dolarů, můžou si tyto poplatky za tento dolar vyměňovat bez předchozího postupu. Platby v dolarech mají stejnou hodnotu. 

### <a name="non-fungible"></a>Bez Fungible

Nefungible tokeny (τN) nejsou vzájemně zaměnitelné s jinými tokeny stejného typu, protože mají obvykle jiné hodnoty. Například název vlastnosti je token, který není fungible. Názvy vlastností dvou různých objektů Apartment v typu Apartment nejsou nutně rovny hodnotě z důvodu umístění jednotky nebo, na které je jednotka zapnuta. Vnímaná hodnota dvou tokenů názvu vlastnosti se nerovná.

### <a name="hybrid"></a>Hybridní

Hybridní tokeny jsou tokeny, které mají komponenty tokenů fungible i tokeny fungible. Hybridní token je základní typ tokenu, který vlastní třídu jiného typu tokenu.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hybridní fungible základ s fungible segmenty

Hybridní fungible základ s tokenem fungible segmentů má nefungible základnu s segmenty tokenů fungible.
Například letenka je hybridní token, kde datum a čas koshodě je základní token, který není fungible. Lístky v různých oddílech k sezení pro danou koshodě jsou segmenty s fungible tokeny. Lístky jsou v jednotlivých oddílech k disexchangi, ale ne napříč oddíly.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hybridní fungible základ s fungible segmenty

Hybridní fungible základ s tokenem fungible segmentů má fungible základnu s segmenty tokenů bez fungible. Například zabezpečení hypotéky je hybridní token, ve kterém je více vlastníků fungible základ, který je rozdělen mezi mnoho vlastníků. Zabezpečení je zaměnitelné. Jednotlivé hypotéky jsou fungible segmenty, které představují konkrétní zabezpečení hypotéky.

## <a name="token-behaviors"></a>Chování tokenu

Chování tokenu definuje možnosti nebo omezení tokenu. Chování zahrnuje podporu vlastností, které jsou součástí definice tokenu. Chování lze použít napříč všemi typy tokenů nebo pouze jedním. Chování může být interní nebo externí v závislosti na tom, jaké účinky chování. Vnitřní chování povoluje nebo omezuje vlastnosti samotného tokenu. Externí chování povoluje nebo omezuje vyvolání chování z externího objektu actor.

Další informace o tokenech služby Azure blockchain podporované tokeny pro tokeny služby taxonomie (TTF) najdete v tématu popisujícím možnosti [vytváření tokenů](composability.md).

## <a name="pre-built-token-templates"></a>Předem připravené šablony tokenů

Tokeny Azure blockchain poskytují čtyři předem připravené šablony tokenů, které je možné použít bez úprav. Do těchto předem sestavených šablon můžete zavolat pro většinu případů použití, abyste mohli rychle začít vytvářet, nasazovat a spravovat vaše tokeny.

### <a name="commodity-tokens"></a>Tokeny komodit

Tokeny komodit mají konzistentní hodnoty a jsou voditelné. Například barel oleje nebo jednotka energie.

**τF {~ d, t, m, b, r}** -fungible, celý, převoditelný, mintable, vypálitelné a mají podporu rolí.

Řada blockchainch scénářů vyžaduje transparentnost a viditelnost napříč dodavatelským řetězcem nebo více organizacemi. Tokeny komodit jsou založené na těchto běžných případech použití. Tokeny jsou vzájemně zaměnitelné a konzistentní. Šablona tokenu komodit je flexibilní a přizpůsobitelná s metadaty.

### <a name="qualified-tokens"></a>Kvalifikované tokeny

Kvalifikované tokeny představují něco získaného a jsou obvykle přidruženy k jedné entitě a nelze je přenést. Například diplom nebo narušení parkování.

**τN {s, ~ t}** -non-fungible, singleton a nepřevoditelný

Různé scénáře auditu a ověřování vyžadují, aby vlastnictví tokenu nebylo možné změnit. K dispozici je sada případů použití, která musí poskytnout kvalifikovaný token, zda je přiřazení správné nebo špatné.

### <a name="asset-tokens"></a>Tokeny assetu

Tokeny prostředků mají na položku závislé jedinečné hodnoty a nejsou commoditized. Například artefakt Museum nebo název vlastnosti.

**τN {s, t}** – non-fungible, singleton a převoditelný

Tokeny assetů se můžou zaměňovat s tokeny komodit. Hlavní rozdíl mezi těmito dvěma tokeny spočívá v tom, že tokeny prostředků jsou v podstatě jedinečné a hodnota je nezávislá na typu tokenu, který je. Například kus kresby, jako je olivový olej, je vybarvení oleje zavedený interpretem je token assetu. Obrázek Mona Lisa se však považuje za zbožový token. Podobně název vlastnosti je token assetu, protože tato hodnota existuje v subjektivních kvalitách vlastnosti.

### <a name="ticket-tokens"></a>Tokeny lístku

Tokeny lístků mají konzistentní hodnotu, ale obvykle vyprší jejich platnost. Například lístek roviny.

**τN {m, b, r}** – non-fungible, mintable, vypálitelné a mají podporu rolí.

Tokeny lístku mají obvykle datum vypršení platnosti, které se liší od běžného tokenu komodity. Například lístek v letadle, lístek koshodě nebo sportovní lístek obsahuje možnosti přiřazené k sezení s konkrétními daty o použití. Nemůžete snadno zamezit lístky mezi daty nebo místy k sezení.

## <a name="next-steps"></a>Další kroky

Pokud pro svůj scénář potřebujete větší flexibilitu, přečtěte si informace o vytváření vlastních šablon tokenů pomocí možností [vytváření tokenů](composability.md).
