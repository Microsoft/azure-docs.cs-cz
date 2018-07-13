---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 69c67f437d2f0b7bd6c1f5311eb5ba1d962d889a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38738875"
---
Použijte značky na prostředky Azure pro jejich logické uspořádání podle kategorií. Každá značka se skládá z názvu a hodnoty. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Po použití značek můžete načíst všechny prostředky v předplatném s daným názvem a hodnotou značky. Značky umožňují načíst související prostředky z různých skupin prostředků. Tento přístup je užitečný v případě, že potřebujete uspořádat prostředky pro účely fakturace nebo správy.

Na značky se vztahují následující omezení:

* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek. Pokud k prostředku potřebujete přidružit více než 15 hodnot, použijte jako hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Tento článek ukazuje příklad přiřazení řetězce JSON ke značce.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.
* Značky nelze použít pro klasické prostředky, jako jsou cloudové služby.
* Názvy značek nemůže obsahovat tyto znaky: `<`, `>`, `%`, `&`, `\`, `?`, `/`
