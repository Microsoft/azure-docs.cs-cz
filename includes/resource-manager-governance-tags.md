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
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841491"
---
Použití značek k prostředkům Azure poskytuje metadata pro jejich logické uspořádání do taxonomie. Každá značka se skládá z názvu a hodnoty pár. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Po použití značek můžete načíst všechny prostředky v předplatném s daným názvem a hodnotou značky. Značky umožňují načíst související prostředky z různých skupin prostředků. Tento přístup je užitečný v případě, že potřebujete uspořádat prostředky pro účely fakturace nebo správy.

Taxonomii zvažte metadat samoobslužné označování strategie kromě strategie automatického označení snižují zátěž kladenou na uživatele a zvýšit přesnost.

Na značky se vztahují následující omezení:

* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, pokud použijete značku u typu prostředku, naleznete v tématu [podporu pro prostředky Azure se značkami](../articles/azure-resource-manager/tag-support.md).
* Každý prostředek nebo skupina prostředků může mít maximálně 50 párů název/hodnota značek. V současné době pro účty úložiště podporují jenom 15 značek, ale toto omezení se zvýší na 50 v budoucí verzi. Pokud potřebujete použít další značky, než maximální povolený počet, použijte pro hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Skupina prostředků může obsahovat řadu prostředků, mít 50 párů název/hodnota značek.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Zobecněný virtuální počítače s omezenou podporou značky.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.
* Značky nelze použít pro klasické prostředky, jako jsou cloudové služby.
* Názvy značek nemůže obsahovat tyto znaky: `<`, `>`, `%`, `&`, `\`, `?`, `/`
