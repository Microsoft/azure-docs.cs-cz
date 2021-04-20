---
title: Funkce v Azure Monitorch dotazech protokolu
description: Tento článek popisuje, jak pomocí funkce volat dotaz z jiného dotazu protokolu v Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752839"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Funkce v Azure Monitorch dotazech protokolu
Funkce je dotaz protokolu v Azure Monitor, který se dá použít v jiných dotazech protokolu, jako by se jednalo o příkaz. Funkce umožňují vývojářům poskytovat řešení různým zákazníkům a k opakovanému použití logiky dotazů ve vlastním prostředí. Tento článek poskytuje podrobné informace o tom, jak používat funkce a jak vytvořit vlastní.

## <a name="types-of-functions"></a>Typy funkcí
V Azure Monitor existují dva typy funkcí:

- **Funkce řešení:** Předem připravené funkce, které jsou součástí Azure Monitor. Ty jsou k dispozici ve všech pracovních prostorech Log Analytics a nelze je upravovat.
- **Funkce pracovního prostoru:** Funkce nainstalované v konkrétním pracovním prostoru Log Analytics a můžou je upravovat a řídit uživatelem.

## <a name="viewing-functions"></a>Zobrazení funkcí
Funkce řešení a funkce pracovního prostoru můžete zobrazit v aktuálním pracovním prostoru na kartě **Functions (funkce** ) v levém podokně Log Analytics pracovního prostoru. Pomocí tlačítka **Filtr** můžete filtrovat funkce zahrnuté v seznamu a **Seskupit podle** změny jejich seskupení. Zadáním řetězce do **vyhledávacího** pole vyhledejte konkrétní funkci. Najeďte myší na funkci, aby se zobrazily podrobnosti, včetně popisu a parametrů.

:::image type="content" source="media/functions/view-functions.png" alt-text="Zobrazit funkci" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Použití funkce
Použijte funkci v dotazu zadáním jejího názvu s hodnotami pro všechny parametry stejně, jako byste zadali do příkazu. Výstup funkce může být vrácen jako výsledek nebo prostřednictvím kanálu k jinému příkazu.

Do aktuálního dotazu přidejte funkci dvojitým kliknutím na její název nebo přesunutím myší a výběrem možnosti **použít v editoru**. Funkce v pracovním prostoru budou také zahrnuty do technologie IntelliSense při psaní do dotazu. 

Pokud dotaz vyžaduje parametry, zadejte je pomocí syntaxe: `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Použití funkce" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Vytvoření funkce
Pokud chcete v editoru vytvořit funkci z aktuálního dotazu, vyberte **Uložit** a pak **funkci Uložit jako**. 

:::image type="content" source="media/functions/function-save.png" alt-text="Vytvoření funkce" lightbox="media/functions/function-save.png":::

Vytvořte funkci s Log Analytics v Azure Portal kliknutím na **Uložit** a zadáním informací v následující tabulce.

| Nastavení | Popis |
|:---|:---|
| Název funkce  | Název funkce Nemusí obsahovat mezeru ani žádné speciální znaky. Také nesmí začínat podtržítkem (_), protože tento znak je vyhrazen pro funkce řešení. |
| Starší kategorie | Uživatelsky definovaná kategorie, které vám pomůžou filtrovat a seskupovat funkce.   |
| Uložit jako skupinu počítačů | Uložte dotaz jako [skupinu počítačů](computer-groups.md).  |
| Parametry | Přidejte parametr pro každou proměnnou ve funkci, která při použití vyžaduje hodnotu. Podrobnosti naleznete v tématu [parametry funkce](#function-parameters) . |

:::image type="content" source="media/functions/function-details.png" alt-text="Podrobnosti funkce" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Parametry funkce 
Můžete přidat parametry do funkce, abyste při volání mohli zadat hodnoty pro určité proměnné. To umožňuje použít stejnou funkci v různých dotazech, přičemž každý z nich poskytuje různé hodnoty pro parametry. Parametry jsou definovány pomocí následujících vlastností.

| Nastavení | Popis |
|:---|:---|
| Typ  | Datový typ pro hodnotu |
| Name  | Název parametru Toto je název, který se musí použít v dotazu, který se má nahradit hodnotou parametru.  |
| Výchozí hodnota | Hodnota, která se má použít pro parametr, pokud není zadána hodnota. |

Parametry jsou seřazeny tak, jak jsou vytvořeny s parametry, které nemají žádnou výchozí hodnotu umístěnou před ovládacími prvky, které mají výchozí hodnotu.

## <a name="working-with-function-code"></a>Práce s kódem funkce
Můžete zobrazit kód funkce buď k získání přehledu o tom, jak funguje, nebo ke změně kódu pro funkci pracovního prostoru. Vyberte **načíst kód funkce** a přidejte kód funkce do aktuálního dotazu v editoru. Pokud ho přidáte do prázdného dotazu nebo do prvního řádku existujícího dotazu, přidá se název funkce na kartu. Pokud se jedná o funkci pracovního prostoru, pak tato možnost umožňuje upravit podrobnosti funkce.

:::image type="content" source="media/functions/function-code.png" alt-text="Načíst kód funkce" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Úprava funkce
Upravte vlastnosti nebo kód funkce tak, že vytvoříte nový dotaz, najedete ukazatelem myši na název funkce a vyberete **načíst kód funkce**. Proveďte jakékoli úpravy kódu a vyberte **Uložit** a pak **upravte podrobnosti o funkci**. Před kliknutím na **Uložit** proveďte změny vlastností a parametrů funkce.

:::image type="content" source="media/functions/function-edit.png" alt-text="Upravit funkci" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Příklad
Následující ukázková funkce vrátí všechny události v protokolu aktivit Azure od konkrétního data, které odpovídá určité kategorii. 

Začněte s následujícím dotazem s využitím hodnot pevně zakódované. Tím se ověří, že dotaz funguje podle očekávání.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Příklad funkce – počáteční dotaz" lightbox="media/functions/example-query.png":::

Dále nahraďte hodnoty pevně zakódované názvy parametrů a pak funkci uložte, a to tak, že vyberete **Uložit** a pak **funkci Uložit jako**.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Příklad funkce Save" lightbox="media/functions/example-save-function.png":::

 Zadejte následující hodnoty vlastností funkce.

| Vlastnost | Hodnota |
|:---|:---|
| Název funkce | AzureActivityByCategory |
| Starší kategorie | Ukázkové funkce |

Před uložením funkce definujte následující parametry.

| Typ | Name | Výchozí hodnota |
|:---|:---|:---|
| řetězec   | CategoryParam | Prav |
| datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Příklad vlastností funkce Functions" lightbox="media/functions/example-function-properties.png":::

Vytvořte nový dotaz a podívejte se na novou funkci tak, že na ni najedete myší. Všimněte si pořadí parametrů, protože se jedná o pořadí, které musí být zadáno při použití funkce.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Příklad funkce – Zobrazit podrobnosti" lightbox="media/functions/example-view-details.png":::

Pokud chcete přidat novou funkci do dotazu a potom přidat hodnoty parametrů, vyberte **použít v editoru** . Všimněte si, že nemusíte zadávat hodnotu pro CategoryParam, protože má výchozí hodnotu.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Příklad funkce use" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Další kroky
Přečtěte si další lekce pro zápis Azure Monitorch dotazů protokolu:

- [Operace s řetězci](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

