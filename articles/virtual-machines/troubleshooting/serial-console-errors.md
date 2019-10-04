---
title: Chyby služby Azure sériové konzoly | Microsoft Docs
description: Běžné chyby v konzole sériového prostředí Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e4596ae2f92e5dfd99dc7c83857e0c9874358fd4
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949710"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Běžné chyby v konzole sériového prostředí Azure
V rámci konzole sériového prostředí Azure existuje sada známých chyb. Toto je seznam těchto chyb a jejich zmírňující kroky.

## <a name="common-errors"></a>Běžné chyby

Chyba                            |   Zmírnění
:---------------------------------|:--------------------------------------------|
Nelze načíst nastavení diagnostiky spouštění pro *&lt;VMNAME @ no__t-2*. Pokud chcete použít konzolu sériového portu, ujistěte se, že je pro tento virtuální počítač povolená Diagnostika spouštění. @no__t – chyba diagnostiky 0Boot @ no__t-1 | Ujistěte se, že virtuální počítač nebo sada škálování virtuálního počítače mají zapnutou [diagnostiku spouštění](boot-diagnostics.md) . Pokud používáte sériové prostředí v instanci sady škálování virtuálních počítačů, ujistěte se, že vaše instance má nejnovější model.
Virtuální počítač je ve stavu Zastaveno (přidělení zrušeno). Spusťte virtuální počítač a zkuste připojení ke sériové konzole znovu. ![Chyba přidělení](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | Instance virtuálního počítače nebo virtuálního počítače musí být ve stavu spuštěno, aby mohla přistupovat ke konzole sériového portu. Spusťte virtuální počítač nebo instanci sady škálování virtuálního počítače a zkuste to znovu.
Při přístupu k účtu úložiště diagnostiky spouštění virtuálního počítače se zjistila odpověď "zakázaná". ![Chyba brány firewall účtu úložiště](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Ujistěte se, že diagnostika spouštění nemá bránu firewall účtu. Aby mohla konzola sériového rozhraní fungovat, je potřeba účet úložiště s přístupem k diagnostice spouštění. Sériová konzola podle návrhu nemůžou pracovat s povolenými branami firewall účtu úložiště v účtu úložiště diagnostiky spouštění.
Nemáte potřebná oprávnění k použití tohoto virtuálního počítače se sériovou konzolou. Ujistěte se, že máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje, abyste měli na svém VIRTUÁLNÍm počítači nebo v sadě škálování virtuálního počítače přístup na úrovni přispěvatele nebo novější. Další informace najdete na stránce s [přehledem](serial-console-overview.md).
Účet úložiště, který se používá pro diagnostiku spouštění na tomto virtuálním počítači, se nepovedlo najít. Ověřte, jestli je pro tento virtuální počítač povolená Diagnostika spouštění, jestli se tento účet úložiště neodstranil a máte k tomuto účtu úložiště přístup. | Dvakrát ověřte, že jste neodstranili účet úložiště diagnostiky spouštění pro váš virtuální počítač nebo sadu škálování virtuálního počítače.
Zřizování pro tento virtuální počítač ještě nebylo úspěšné. Ujistěte se prosím, že je virtuální počítač plně nasazený, a zkuste připojení ke sériové konzole znovu. | Virtuální počítač nebo sada škálování virtuálního počítače se pořád můžou zřídit. Chvíli počkejte a pak to zkuste znovu.
Nemáte požadovaná oprávnění k zápisu do účtu úložiště diagnostiky spouštění tohoto virtuálního počítače. Ujistěte se prosím, že máte aspoň oprávnění přispěvatelů virtuálních počítačů na. | Přístup Sériová konzola vyžaduje přístup na úrovni přispěvatele v účtu úložiště diagnostiky spouštění. Další informace najdete na stránce s [přehledem](serial-console-overview.md).
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění *&lt;STORAGEACCOUNTNAME @ no__t-2*. Ověřte, jestli je pro tento virtuální počítač povolená Diagnostika spouštění a máte přístup k tomuto účtu úložiště. | Přístup Sériová konzola vyžaduje přístup na úrovni přispěvatele v účtu úložiště diagnostiky spouštění. Další informace najdete na stránce s [přehledem](serial-console-overview.md).
Webový soket je uzavřený nebo ho nejde otevřít. | Je možné, že budete muset přidat přístup k bráně firewall `*.console.azure.com`. Podrobnější, ale delší přístup je povolení přístupu brány firewall k [rozsahům IP adres Microsoft Azure datacentra](https://www.microsoft.com/download/details.aspx?id=41653), které se často mění poměrně.
Sériová konzola nepracuje s účtem úložiště pomocí Azure Data Lake Storage Gen2 s hierarchickými obory názvů. | Jedná se o známý problém s hierarchickými obory názvů. Pokud chcete zmírnit, ujistěte se, že účet úložiště diagnostiky spouštění virtuálního počítače není vytvořený pomocí Azure Data Lake Storage Gen2. Tuto možnost lze nastavit pouze při vytváření účtu úložiště. Je možné, že budete muset vytvořit samostatný účet úložiště diagnostiky spouštění bez Azure Data Lake Storage Gen2 povoleného pro zmírnění tohoto problému.


## <a name="next-steps"></a>Další kroky
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače se systémem Linux](./serial-console-linux.md)
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače s Windows](./serial-console-windows.md)