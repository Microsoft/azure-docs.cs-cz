---
title: Kotva – najít strategii
description: Přečtěte si o různých strategiích při volání rozhraní API pro hledání.
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048472"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Princip třídy AnchorLocateCriteria
V tomto článku se seznámíte s různými možnostmi, které můžete použít při dotazování kotvy. Přejdeme se na AnchorLocateCriteria třídu, její možnosti a platné kombinace možností.

## <a name="anchor-locate-criteria"></a>Umístit kritéria pro kotvu
[Třída AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) vám pomůže zadat dotaz na službu pro dříve vytvořené kotvy. Jeden objekt AnchorLocateCriteria může být v každém sledovacím procesu použit kdykoli. Každý objekt AnchorLocateCriteria musí obsahovat **přesně jednu** z následujících vlastností: [identifiers](#identifiers), [NearAnchor](#nearanchor)nebo [NearDevice](#neardevice). V případě potřeby je možné nastavit další vlastnosti, jako je například [strategie](#strategy), [BypassCache](#bypasscache)a [RequestedCategories](#requestedcategories) . 

### <a name="properties"></a>Vlastnosti
Ve sledovacím procesu definujte **přesně jednu** z těchto vlastností:
#### <a name="identifiers"></a>Identifikátory
*Výchozí hodnota: prázdné pole řetězců*

Pomocí identifikátorů můžete definovat seznam ID kotvy pro kotvy, které chcete najít. Po úspěšném vytvoření kotvy se na vás zpočátku vrátí ID kotvy. U zadaných identifikátorů AnchorLocateCriteria omezuje sadu požadovaných kotev na kotvy se shodnými ID kotvy. Tato vlastnost je určena pomocí pole řetězců. 

#### <a name="nearanchor"></a>NearAnchor
*Výchozí hodnota: Nenastaveno*

Pomocí NearAnchor můžete určit, že AnchorLocateCriteria omezuje sadu požadovaných kotev na kotvy v požadované vzdálenosti od zvoleného kotvy. Tuto zvolenou kotvu musíte zadat jako zdrojovou kotvu. Můžete také nastavit požadovanou vzdálenost od zdrojového ukotvení a maximální počet vrácených kotev k dalšímu omezení vyhledávání.
Tato vlastnost je určena pomocí objektu NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Výchozí hodnota: Nenastaveno*

Pomocí NearDevice můžete určit, že AnchorLocateCriteria omezuje sadu požadovaných kotev na ty, které se blíží fyzickému umístění zařízení. Jakékoli povolené snímače budou sloužit k odhalení kotev v zařízení. Aby bylo možné najít kotvy, měli byste nakonfigurovat SensorCapabilities a poskytnout tak relaci přístup ke všem vhodným senzorům. Další informace o nastavení a použití této vlastnosti najdete v tématu [hrubá reprostředí – prostorové kotvy Azure | Microsoft docs](./coarse-reloc.md) a *jak vytvářet a vyhledávat kotvy s využitím hrubých národních prostředí* v [jazyce C#](../how-tos/set-up-coarse-reloc-unity.md), [objektivní – C](../how-tos/set-up-coarse-reloc-unity.md), [SWIFT](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [c++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [c++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Tato vlastnost je určena pomocí objektu NearDeviceCriteria.

### <a name="additional-properties"></a>Další vlastnosti
#### <a name="bypasscache"></a>BypassCache
*Výchozí hodnota: false*

Při vytvoření nebo nalezení kotvy v relaci je také Uloženo v mezipaměti.  Když je tato vlastnost nastavená na hodnotu false, všechny následné dotazy ve stejné relaci vrátí hodnotu uloženou v mezipaměti. Není vytvořen žádný požadavek na službu ASA.

#### <a name="requestedcategories"></a>RequestedCategories
*Výchozí hodnota: vlastnosti | Klepání*

Tato vlastnost slouží k určení dat vrácených z dotazu pomocí AnchorLocateCriteria. Výchozí hodnota vrátí jak vlastnosti, tak prostorová data. Tato hodnota by neměla být změněna, pokud jsou požadovány vlastnosti a prostorová data. Tuto vlastnost lze zadat pomocí výčtu AnchorDataCategory.

Hodnota výčtu AnchorDataCategory | Vrácená data
-----|------------
Žádné | Nevrátí se žádná data.
Vlastnosti| Jsou vráceny vlastnosti kotvy včetně AppProperties.
Prostorové| Vrátí se prostorové informace o kotvě.

#### <a name="strategy"></a>Strategie
*Výchozí hodnota: AnyStrategy*

Strategie dále definuje, jak se mají umístit kotvy. Vlastnost strategie se dá zadat pomocí výčtu LocateStrategy.

Hodnota výčtu LocateStrategy | Description
---------------|------------
AnyStrategy | Tato strategie umožňuje systému využívat kombinace VisualInformation a strategií vztahů k hledání kotev. 
VisualInformation|Tato strategie se pokusí najít kotvy porovnáním vizuálních informací z aktuálního okolí s prvky vizuálního otisku kotvy. Vizuální otisky kotvy odkazují na vizuální informace, které jsou aktuálně přidruženy k ukotvení. Tyto informace o vizuálu jsou obvykle, ale nejsou shromažďovány výhradně během vytváření kotv. V současné době je tato strategie povolena pouze ve spojení s NearDevice nebo vlastnostmi identifikátorů.
Relace|Tato strategie se pokouší najít kotvy tím, že používá existující [připojené kotvy](./anchor-relationships-way-finding.md#connect-anchors). V současné době je tato strategie povolena pouze ve spojení s NearAnchor nebo vlastnostmi identifikátorů. Pokud se používá s vlastností identifiers, je nutné, aby ve stejné relaci dříve byly umístěny kotvy s již vytvořenými připojenými vztahy k kotvám, jejichž ID jsou uvedena v poli identifikátory. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Platné kombinace vlastností LocateStrategy a AnchorLocateCriteria 

Ne všechny kombinace strategií a vlastností AnchorLocateCriteria jsou aktuálně povolené systémem. V následující tabulce jsou uvedeny povolené kombinace:



Vlastnost | AnyStrategy | Relace | VisualInformation
-------- | ------------|--------------|-------------------
Identifikátory | &check;    | &check;     | &check;
NearAnchor  | &check;   (ve výchozím nastavení se jedná o relaci) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Další kroky

Další příklady použití třídy AnchorLocateCriteria najdete v tématu [jak vytvořit a najít kotvy pomocí prostorových kotev Azure](../create-locate-anchors-overview.md) .