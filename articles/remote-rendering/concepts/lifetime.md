---
title: Životnost objektů a prostředků
description: Vysvětluje správu životnosti pro různé typy
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681867"
---
# <a name="object-and-resource-lifetime"></a>Životnost objektů a prostředků

Vzdálené vykreslování Azure rozlišuje mezi dvěma typy: **objekty** a **prostředky**.

## <a name="object-lifetime"></a>Doba života objektu

*Objekty* se považují za to, že uživatel může vytvářet, upravovat a zničit vlastní rozhodnutí. Objekty mohou být volně duplikovány a každá instance může být v průběhu času. [Entity](entities.md) a [komponenty](components.md) jsou tedy objekty.

Doba života objektů je plně v rámci uživatelského ovládacího prvku. Nesouvisí s životností reprezentace na straně klienta, i když. Třídy jako `Entity` a `Component` mají `Destroy` funkci, která musí být volána k navrácení objektu na vzdáleném hostiteli vykreslování. Navíc `Entity.Destroy()` odstraní entitu, její podřízené položky a všechny komponenty v této hierarchii.

## <a name="resource-lifetime"></a>Doba života prostředku

*Prostředky* jsou věci, jejichž životnost je zcela spravovaná hostitelem vzdáleného vykreslování. Odkazy na prostředky jsou vypočítány interně. Budou se navrátit, pokud už žádné z nich neodkazují.

Většinu prostředků lze vytvořit pouze nepřímo, obvykle jejich načtením ze souboru. Když se stejný soubor načte víckrát, vzdálené vykreslování Azure vrátí stejný odkaz a data znovu nenačte.

Mnoho prostředků je neměnné, a to pro [sítě](meshes.md) a [textury](textures.md)pro instance. Některé prostředky jsou proměnlivé, ale například [materiály](materials.md). Vzhledem k tomu, že prostředky jsou často sdíleny, může změna prostředku ovlivnit více objektů. Například změna barvy materiálu změní barvu všech objektů, které používají sítě, které zase odkazují na tento materiál.

### <a name="built-in-resources"></a>Integrované prostředky

Vzdálené vykreslování Azure obsahuje některé integrované prostředky, které je možné načíst pomocí předpřipravenosti jejich příslušného identifikátoru v `builtin://` průběhu volání `AzureSession.Actions.LoadXYZAsync()` . Dostupné integrované prostředky jsou uvedeny v dokumentaci pro každou příslušnou funkci. Například [Kapitola nebe](../overview/features/sky.md) uvádí vestavěné textury nebe.

## <a name="general-lifetime"></a>Obecná doba platnosti

Doba života všech objektů a prostředků je vázána na připojení. Při odpojení všeho se zruší. Při opětovném připojení ke stejné relaci bude graf scény prázdný a všechny prostředky budou smazány.

V praxi je po odpojení obvykle rychlejší, když se v relaci načítají stejný prostředek, většinou rychleji než první. Důvodem je to, že většina prostředků musí být stažena z Azure Storage poprvé, což není nutné podruhé, ušetříte tak velké množství času.

## <a name="next-steps"></a>Další kroky

* [Entity](entities.md)
* [Komponenty](components.md)
* [Modely](models.md)
