---
title: Klíčové výrazy – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Seznam klíčových pojmů používaných v článcích pro vlastní překladatele
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/02/2021
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4a0308f2b961ce9b1321fab3e202e038eb199674
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286415"
---
# <a name="custom-translator-key-terms"></a>Vlastní klíčové výrazy překladatele

Následující tabulka uvádí seznam klíčových pojmů, které se mohou při práci s [vlastním překladatelem](https://portal.customtranslator.azure.ai)najít.

| Slovo nebo fráze|Definice|
|------------------|-----------|
| Zdrojový jazyk | Zdrojový jazyk je počáteční jazyk, který chcete převést na jiný jazyk ("cíl").|
| Cílový jazyk| Cílový jazyk je jazyk, který má strojový překlad poskytnout poté, co obdrží zdrojový jazyk. |
| Soubor Monolingual | Soubor monolingual má jeden jazyk, který není spárován s jiným souborem jiného jazyka. |
| Paralelní soubory | Paralelní soubor je kombinací dvou souborů s odpovídajícím textem. Jeden soubor má zdrojový jazyk. Druhý má cílový jazyk.|
| Zarovnání věty| Paralelní datová sada musí mít zarovnaná věty na věty, které reprezentují stejný text v obou jazycích. Například ve zdrojovém paralelním souboru by první věta měla být teoreticky namapována na první větu v cílovém paralelním souboru.|
| Zarovnaný text | Jedním z nejdůležitějších kroků při ověřování souborů je zarovnání vět v paralelních dokumentech. Věci jsou v různých jazycích vyjádřené jinak. Různé jazyky mají různé wordové objednávky. Tento krok provede úlohu zarovnání vět se stejným obsahem, aby bylo možné je použít pro školení. V případě nesprávného zarovnání vět se zobrazuje něco špatného u jednoho nebo obou souborů. |
| Rozdělení nebo zrušení přerušení slova | Dělení slov je funkce označující hranice mezi slovy. Řada systémů psaní používá prostor k označení hranice mezi slovy. Zrušení přerušení slova odkazuje na odebrání viditelné značky, která může být vložena mezi slova v předchozím kroku. |
| Oddělovače   | Oddělovače jsou způsoby, kterými je věta rozdělena do segmentů nebo je ohraničena na hranici mezi větami. V anglickém prostoru například vymezují slova, dvojtečky a klauzule pro vymezení středníků a vymezené věty. |
| Školicí soubory | Školicí soubor se používá k učení systému strojového překladu, jak namapovat z jednoho jazyka (zdroje) do cílového jazyka (cíle). Větší údaje, které zadáte, tím lépe bude systém provádět. |
| Ladění souborů | Tyto soubory jsou často náhodně odvozené ze sady školení (Pokud nevyberete sadu optimalizace). Tyto věty jsou využívány pro vyladění systému a jejich správné fungování. Pokud chcete vytvořit model překladu pro obecné účely a vytvořit vlastní soubory pro ladění, ujistěte se, že se jedná o náhodnou sadu vět napříč doménami. |
| Testování souborů| Tyto soubory jsou často odvozené soubory, náhodně vybrané ze sady školení (Pokud nevyberete žádnou testovací sadu). Účelem těchto vět je vyhodnotit přesnost modelu překladu. Vzhledem k tomu, že tyto věty jsou ty, které chcete zajistit, aby systém byl přesně předaný, možná budete chtít vytvořit testovací sadu a odeslat ji do překladatele. Tím zajistíte, že se tyto věty budou používat ve vyhodnocování systému (generování skóre BLEU).   |
| Soubor se seznamem   | Typ souboru, ve kterém jsou zdrojové a přeložené věty obsaženy ve stejném souboru. Podporované formáty souborů (. TMX,. xliff,. xlf,. ici,. xlsx). |
| Archivní soubor | Soubor, který obsahuje jiné soubory. Podporované formáty souborů (zip, GZ, tgz).  |
| Skóre BLEU   | [Bleu](what-is-bleu-score.md) je standardní metoda pro vyhodnocení "přesnosti" nebo přesnosti modelu překladu. I když existují jiné metody vyhodnocení, Microsoft Translator využívá metodu BLEU, která oznamuje přesnost vlastníkům projektu.
