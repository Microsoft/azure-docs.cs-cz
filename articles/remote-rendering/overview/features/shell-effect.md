---
title: Vykreslování prostředí
description: Vysvětluje, jak používat efekt vykreslování prostředí.
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f59c4f8225d31b61df08f30863c8b9300e20e820
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447619"
---
# <a name="shell-rendering"></a>Vykreslování prostředí

Stav prostředí [komponenty přepisu hierarchického stavu](../../overview/features/override-hierarchical-state.md) je efekt průhlednosti. Na rozdíl od vykreslování [pomocí zobrazení](../../overview/features/override-hierarchical-state.md) je viditelná pouze přední vrstva objektů, podobají se na neprůhledné vykreslování. Kromě toho je možné při vykreslování jako prostředí změnit normální vzhled objektů. Tento efekt je určený pro případy použití, kdy by měl být uživatel vizuálně provedený z nedůležitých částí a zároveň přitom udržuje prostorové povědomí pro celou scénu.

Můžete nakonfigurovat vzhled objektů vykreslených pomocí prostředí prostřednictvím `ShellRenderingSettings` globálního stavu. Všechny objekty, které používají vykreslování prostředí, budou používat stejné nastavení. Neexistují žádné parametry pro jednotlivé objekty.

## <a name="shellrenderingsettings-parameters"></a>Parametry ShellRenderingSettings

Třída `ShellRenderingSettings` obsahuje nastavení týkající se vlastností globálního vykreslování prostředí:

| Parametr      | Typ    | Popis                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | Velikost desytosti, která se má použít na obvyklou konečnou barvu objektu, v rozsahu 0 (bez sytosti) do 1 (úplné desytost) |
| `Opacity`      | float   | Neprůhlednost objektů vykreslených prostředím v rozsahu 0 (neviditelné) až 1 (zcela neprůhledné) |

Příklady efektů parametrů při použití na celou scénu najdete v následující tabulce:

|                | 0 | 0,25 | 0,5 | 0,75 | 1,0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desytost** | ![Desytost – 0,0](./media/shell-desaturation-00.png) | ![Desytost – 0,25](./media/shell-desaturation-025.png) | ![Desytost – 0,5](./media/shell-desaturation-05.png) | ![Desytost – 0,75](./media/shell-desaturation-075.png) | ![Desytost – 1,0](./media/shell-desaturation-10.png) |
| **Určující**      | ![Krytí – 0,0](./media/shell-opacity-00.png) | ![Krytí – 0,25](./media/shell-opacity-025.png) | ![Krytí – 0,5](./media/shell-opacity-05.png) | ![Krytí – 0,75](./media/shell-opacity-075.png) | ![Krytí – 1,0](./media/shell-opacity-10.png) |

Efekt prostředí se aplikuje na konečnou neprůhlednou barvu, na kterou se scéna bude vykreslovat jinak. Který zahrnuje [přepsání hierarchického stavu nádechu](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Příklad

Následující kód ukazuje příklad použití `ShellRenderingSettings` stavu prostřednictvím rozhraní API:

```cs
void SetShellSettings(AzureSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Actions.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<AzureSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Actions()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Výkon

Funkce vykreslování prostředí přináší v porovnání s standardním neprůhledným vykreslováním malé konstantní náklady. Je výrazně rychlejší než použití transparentních materiálů na objektech nebo vykreslování [pomocí zobrazení](../../overview/features/override-hierarchical-state.md) . Výkon může být snížený, pokud jsou pouze části scény přepnuty na vykreslování prostředí. K tomuto snížení může dojít v důsledku dalších objektů, které vyžadují vykreslování. V tomto ohledu se výkon chová podobně jako funkce [vyjmuté roviny](../../overview/features/cut-planes.md) .

## <a name="next-steps"></a>Další kroky

* [Součást přepisu hierarchického stavu](../../overview/features/override-hierarchical-state.md)