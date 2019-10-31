---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199170"
---
Značky můžete použít pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Po použití značek můžete načíst všechny prostředky v předplatném s daným názvem a hodnotou značky. Značky umožňují načíst související prostředky z různých skupin prostředků. Tento přístup je užitečný v případě, že potřebujete uspořádat prostředky pro účely fakturace nebo správy.

Vaše taxonomie by měla zvážit strategii označování metadat samoobslužné služby, kromě strategie automatického tagování, která snižuje zatížení uživatelů a zvyšuje přesnost.

Na značky se vztahují následující omezení:

* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, jestli můžete pro typ prostředku použít značku, přečtěte si téma [Podpora značek pro prostředky Azure](../articles/azure-resource-manager/tag-support.md).
* Každý prostředek nebo skupina prostředků může mít maximálně 50 párů název/hodnota značky. Pokud potřebujete použít více značek, než je maximální povolený počet, použijte jako hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Skupina prostředků může obsahovat mnoho prostředků, které mají každý z nich 50 páry název-hodnota.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Generalizované virtuální počítače nepodporují značky.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.
* Značky nelze použít u klasických prostředků, jako je například Cloud Services.
* Názvy značek nesmí obsahovat tyto znaky: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > V současné době Azure DNS zóny a služby Traffic Manageru nedovolují použití mezer ve značce. 
