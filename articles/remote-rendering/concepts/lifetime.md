---
title: Životnost objektu a prostředků
description: Vysvětluje správu životnosti pro různé typy
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681867"
---
# <a name="object-and-resource-lifetime"></a>Životnost objektu a prostředků

Vzdálené vykreslování Azure rozlišuje mezi dvěma typy: **objekty** a **prostředky**.

## <a name="object-lifetime"></a>Životnost objektu

*Objekty* jsou považovány za věci, které uživatel může vytvořit, upravit a zničit podle vlastního uvážení. Objekty mohou být duplikovány volně a každá instance může v průběhu času mutovat. V důsledku toho [entity](entities.md) a [součásti](components.md) jsou objekty.

Životnost objektů je plně pod kontrolou uživatele. To však nesouvisí s životností reprezentace na straně klienta. Třídy `Entity` `Component` jako `Destroy` a mají funkci, která musí být volána navrátit objekt na vzdáleném vykreslovacím hostitele. Navíc `Entity.Destroy()` zničí entitu, její podřízené součásti a všechny součásti v této hierarchii.

## <a name="resource-lifetime"></a>Životnost zdroje

*Prostředky* jsou věci, jejichž životnost je zcela spravována hostitelem vzdáleného vykreslování. Prostředky jsou počítány interně. Rozmístí je, když na ně už nikdo nenazve.

Většinu prostředků lze vytvořit pouze nepřímo, obvykle jejich načtením ze souboru. Při načtení stejného souboru vícekrát Azure Remote Rendering vrátí stejný odkaz a není znovu načíst data.

Mnoho prostředků je neměnných, například [ok](meshes.md) a [textur .](textures.md) Některé zdroje jsou však proměnlivé, například [materiály](materials.md). Vzhledem k tomu, že prostředky jsou často sdíleny, může úprava prostředku ovlivnit více objektů. Například změna barvy materiálu změní barvu všech objektů, které používají ok, což zase odkazuje na tento materiál.

### <a name="built-in-resources"></a>Integrované prostředky

Azure Remote Rendering obsahuje některé předdefinované prostředky, které lze načíst pomocí jejich příslušného identifikátoru během `builtin://` volání `AzureSession.Actions.LoadXYZAsync()`. Dostupné předdefinované prostředky jsou uvedeny v dokumentaci pro každou příslušnou funkci. V kapitole [obloha](../overview/features/sky.md) jsou například uvedeny vestavěné textury oblohy.

## <a name="general-lifetime"></a>Obecná životnost

Životnost všech objektů a prostředků je vázána na připojení. Při odpojení je vše zahozeno. Při opětovném připojení ke stejné relaci bude graf scény prázdný a všechny prostředky budou vymazány.

V praxi načítání stejného prostředku do relace po odpojení je obvykle rychlejší než poprvé. To je případ, protože většina prostředků musí být staženy z Azure Storage poprvé, což není nutné podruhé, což šetří značné množství času.

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Komponenty](components.md)
* [Modely](models.md)
