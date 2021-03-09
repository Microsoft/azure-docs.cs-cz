---
title: Osvědčené postupy
description: Doporučené osvědčené postupy pro dosažení lepších výsledků
author: ariye
ms.author: crtreasu
ms.date: 02/17/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: da3be6e3d97e50b27ded29ba017164fdbd9a0a5b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503041"
---
# <a name="best-practices"></a>Osvědčené postupy

Pro dosažení nejlepších výsledků doporučujeme vyzkoušet některé z těchto kroků.

## <a name="ingestion"></a>Příjem dat

- Ověřte rozměry vašich fyzických objektů. Kotvy objektů fungují nejlépe pro objekty, jejichž nejmenší rozměr je v rozsahu Doporučené 1m – 10 milionů.
- Pro následující podrobnosti si Prozkoumejte 3D model v softwaru, jako je [**MeshLab**](https://www.meshlab.net/) .
  - Ujistěte se, že 3D model má trojúhelníkovou síť a že trojúhelníky na vnějším povrchu čelí vnějším plochám. To znamená, že vrcholy by měly být orientované na to, aby normály dodržovaly pravidlo na pravé straně, a to směrem výš.
  - Ujistěte se, že prostorový model je zadán se správnými jednotkami škálování s ohledem na fyzické objekty. Jednotky musí být jedna z těchto jednotek: ***centimetry, decimeters, nohy, palce, kilometry, měřiče, milimetry, yardy***.
  - Potvrďte směr nominálních hodnot gravitace, který odpovídá skutečnému vertikálnímu otočení objektu. Pokud je svislá nebo závažnost objektu-Y, použijte ***(0,-1, 0)** _ nebo _*_ (0, 0,-1) _ * * pro-Z a podobně pro jakýkoliv jiný směr.
  - Zajistěte, aby byl 3D model kódovaný v jednom z podporovaných formátů: `.glb` , `.gltf` , `.ply` , `.fbx` , `.obj` .
- Služba převodů modelů může trvat dlouhou dobu, než se zpracuje velký model vysokého LOD (úrovně podrobností). V případě účinnosti můžete 3D model předzpracovat, aby se odstranily vnitřní obličeje.

## <a name="detection"></a>Detekce

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- Poskytovaná sada runtime SDK vyžaduje, aby uživatelem zadaná oblast vyhledávání hledala a zjišťovala fyzické objekty. Oblast hledání může být ohraničovací rámeček, koule, zobrazení frustum nebo libovolná kombinace. Chcete-li se vyhnout nepravdivé detekci, je vhodnější nastavit oblast hledání dostatečně velká pro pokrytí objektu. Při použití poskytovaných ukázkových aplikací můžete na jedné straně objektu stát asi 2 měřiče od nejbližšího povrchu a spustit aplikaci.
- Před spuštěním aplikace kotvy objektů na zařízení s HoloLens 2 odstraňte hologramy v okolí svého pracoviště, a to pomocí ***Nastavení->systémových >ch hologramů*** .

  Tento krok zajistí, že pokud se nový objekt, jako je auto, nachází ve stejném prostoru jako v minulosti, nebo když se objekt přesunul z cílového prostoru, všechny staré a nerelevantní hologramy nezůstanou zachovány a vytvoří matoucí vizualizaci pro objekt, který je aktuálně v zobrazení.
- Po odebrání hologramů a před zahájením aplikace naskenujte objekt, jako je třeba automobil, a to tak, že se podíváte na objekt, a to tak, že ho prohlížíte od přibližně 1-2 metrů a pomalu projdete všechny objekty o jeden nebo dvakrát.

  Tento krok zajistí, aby byly všechny zbytkové odhady povrchu vytvořené v daném prostoru pomocí dřívějších objektů a kontrol obnoveny pomocí povrchů aktuálního cílového objektu, se kterými budete pracovat. V opačném případě může aplikace vidět dvojité zástupné plochy, což má za chybné zarovnání 3D model a přidružených hologramů. Předběžná kontrola objektu také významně sníží latenci detekce AOA, což znamená, že od 30 sekund do 5 sekund.
- U tmavých a vysoce odrážejících objektů může být nutné skenovat objekt v rozsahu blíže a přesunout nahoru a dolů, doleva a doprava, aby zařízení viděli povrchy od více úhlů a více vzdáleností.
- Když se zobrazí nesprávná detekce objektu, jako je například převrácení orientace nebo nesprávná pozice, jako je například nakloněný model, měli byste vizualizovat prostorové mapování. Nesprávné výsledky jsou často způsobeny špatnými nebo neúplnými rekonstrukcemi povrchu. Můžete odebrat hologramy, naskenovat objekt a znovu spustit rozpoznávání objektů v aplikaci.
- Sada SDK poskytnuté modulem runtime poskytuje několik parametrů, které uživatelům umožňují vyladit detekci, jak je znázorněno v našich ukázkových aplikacích. Výchozí parametry fungují dobře pro většinu objektů. Pokud zjistíte, že je potřeba je upravit pro konkrétní objekty, tady je několik doporučení:
  - Pokud je fyzický objekt velký, tmavý nebo lesklý, použijte nižší prahovou hodnotu pokrytí povrchu.
  - Povolí změnu malého rozsahu (například 0,1) pro velký objekt jako auto.
  - Povolí u všech stupňů odchylku mezi místním svislým směrem a závažností objektu, pokud je objekt na sklonu.
