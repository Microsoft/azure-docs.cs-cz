---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 17247b6dfac18a841a35e97496ec8f84f0cdeb8f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827922"
---
Použití značek k prostředkům Azure poskytuje metadata pro jejich logické uspořádání do taxonomie. Každá značka se skládá z názvu a hodnoty pár. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Po použití značek můžete načíst všechny prostředky v předplatném s daným názvem a hodnotou značky. Značky umožňují načíst související prostředky z různých skupin prostředků. Tento přístup je užitečný v případě, že potřebujete uspořádat prostředky pro účely fakturace nebo správy.

Taxonomii zvažte metadat samoobslužné označování strategie kromě strategie automatického označení snižují zátěž kladenou na uživatele a zvýšit přesnost.

Na značky se vztahují následující omezení:

* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, pokud použijete značku u typu prostředku, naleznete v tématu [podporu pro prostředky Azure se značkami](../articles/azure-resource-manager/tag-support.md).
* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek. Pokud k prostředku potřebujete přidružit více než 15 hodnot, použijte jako hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Tento článek ukazuje příklad přiřazení řetězce JSON ke značce.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Zobecněný virtuální počítače s omezenou podporou značky.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.
* Značky nelze použít pro klasické prostředky, jako jsou cloudové služby.
* Názvy značek nemůže obsahovat tyto znaky: `<`, `>`, `%`, `&`, `\`, `?`, `/`
