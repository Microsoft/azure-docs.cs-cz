---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005562"
---
Tento článek obsahuje pokyny pro vytváření vysoce výkonných aplikací pomocí služby Azure Premium Storage. Můžete použít pokyny uvedené v tomto dokumentu v kombinaci s doporučené postupy výkonu vztahující se na technologie používané vaší aplikací. Pro ilustraci pokynů jsme použili SQL Server spuštěný na úložiště Premium jako příklad v celém tomto dokumentu.

Zatímco řešíme scénáře výkonu pro vrstvu úložiště v tomto článku, budete muset optimalizovat aplikační vrstvu. Například pokud hostujete SharePoint Farm na Azure Premium Storage, můžete použít příklady SQL Server z tohoto článku k optimalizaci databázového serveru. Kromě toho optimalizujte webový server a aplikační server sharepointové farmy, abyste získali co největší výkon.

Tento článek vám pomůže zodpovědět následující běžné otázky týkající se optimalizace výkonu aplikací ve službě Azure Premium Storage,

* Jak měřit výkon aplikace?  
* Proč nevidíte očekávaný vysoký výkon?  
* Které faktory ovlivňují výkon vaší aplikace v úložišti Premium?  
* Jak tyto faktory ovlivňují výkon vaší aplikace v úložišti Premium?  
* Jak můžete optimalizovat pro VOPS, šířku pásma a latenci?  

Tyto pokyny jsme poskytli speciálně pro úložiště Premium, protože úlohy spuštěné v úložišti Premium jsou vysoce citlivé na výkon. V případě potřeby jsme poskytli příklady. Některé z těchto pokynů můžete použít také pro aplikace spuštěné na virtuálních počítačích IaaS s disky se standardním úložištěm.