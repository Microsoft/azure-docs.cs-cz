---
title: Co je nového ve funkci pro rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Pochopení nejnovějších změn rozhraní API pro rozpoznávání formulářů
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: cb5639dcf0e13ea03d34604816b3939085674c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456363"
---
# <a name="whats-new-in-form-recognizer"></a>Co je nového ve funkci pro rozpoznávání formulářů?

Tento článek popisuje hlavní změny, které se dodávají s novými verzemi rozhraní API pro rozpoznávání formulářů.

> [!NOTE]
> Rychlé starty a příručky v této sadě dokumentů vždycky používají nejnovější verzi rozhraní API, pokud neurčí jinak.

## <a name="form-recognizer-20-preview"></a>Rozpoznávání formulářů 2,0 (Preview)

> [!IMPORTANT]
> Nástroj pro rozpoznávání formulářů 2,0 je aktuálně k dispozici pro odběry v oblastech `West US 2` a `West Europe`. Pokud vaše předplatné není v této oblasti, použijte rozhraní 1,0 API. Rychlé starty pro školení a používání vlastního modelu jsou k dispozici pro v 1.0 i v 2.0.

### <a name="new-features"></a>Nové funkce

* **Vlastní model**
  * **Výuka s popisky** Nyní můžete vytvořit vlastní model s ručně označenými daty. Výsledkem je lepší provádění modelů a může způsobit vytváření modelů, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.
  * **Asynchronní rozhraní API** Asynchronní volání rozhraní API můžete použít ke školení a analýze velkých datových sad a souborů.
  * **Podpora souborů TIFF** Nyní můžete data z dokumentů TIFF vyškolit a extrahovat.
  * **Vylepšení přesnosti extrakce**

* **Předem sestavený model příjmu**
  * **Částky hrotu** Nyní můžete extrahovat množství tipů a další ručně psané hodnoty.
  * **Extrakce položky řádku** Můžete extrahovat hodnoty položek řádků z příjmů.
  * **Hodnoty spolehlivosti** U každé extrahované hodnoty můžete zobrazit důvěru modelu.
  * **Vylepšení přesnosti extrakce**

* **Extrakce rozložení** Rozhraní API pro rozložení teď můžete použít k extrakci textových dat a tabulkových dat z vašich formulářů.

### <a name="custom-model-api-changes"></a>Změny rozhraní API vlastního modelu

Všechna rozhraní API pro školení a používání vlastních modelů byla přejmenována a některé synchronní metody jsou nyní asynchronní. Níže jsou uvedené zásadní změny:

* Proces školení modelu je nyní asynchronní. Zahájíte školení prostřednictvím volání rozhraní **/Custom/Models** API. Toto volání vrátí ID operace, kterou můžete předat **vlastním/modelům/{modelID}** a vrátit výsledky školení.
* Extrakce klíč/hodnota je nyní iniciována voláním rozhraní API **/Custom/Models/{modelID}/Analyze** . Toto volání vrátí ID operace, kterou můžete předat **vlastním/modelům/{modelID}/analyzeResults/{hodnotu resultid}** a vrátit výsledky extrakce.
* ID operací pro operaci vlaku se nyní nacházejí v hlavičce **umístění** odpovědí HTTP, nikoli v hlavičce **umístění operace** .

### <a name="receipt-api-changes"></a>Změny rozhraní API pro příjem

Rozhraní API pro čtení prodejních příjmů bylo přejmenováno.

* Extrakce dat pro příjem se teď iniciuje voláním rozhraní API **/prebuilt/Receipt/Analyze** . Toto volání vrátí ID operace, kterou můžete předat do **/prebuilt/Receipt/analyzeResults/{resultID}** a vrátit výsledky extrakce.

### <a name="output-format-changes"></a>Změny formátu výstupu

Odpovědi JSON pro všechna volání rozhraní API mají nové formáty. Některé klíče a hodnoty byly přidány, odebrány nebo přejmenovány. Příklady aktuálních formátů JSON najdete v rychlých startech.

## <a name="next-steps"></a>Další kroky

Dokončete [rychlý Start](quickstarts/curl-train-extract.md) , abyste mohli začít s [rozhraními API pro rozpoznávání formulářů](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).