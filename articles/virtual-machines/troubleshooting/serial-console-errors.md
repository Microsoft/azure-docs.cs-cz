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
ms.openlocfilehash: 697f7267437f750bbb751239001145cb1c8af000
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806826"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Běžné chyby v konzole sériového prostředí Azure
V rámci konzole sériového prostředí Azure existuje sada známých chyb. Toto je seznam těchto chyb a jejich zmírňující kroky.

## <a name="common-errors"></a>Běžné chyby

Chyba                             |   Omezení rizik
:---------------------------------|:--------------------------------------------|
"Sériová konzola Azure vyžaduje, aby byla povolená Diagnostika spouštění. Kliknutím sem můžete nakonfigurovat diagnostiku spouštění pro virtuální počítač. | Ujistěte se, že virtuální počítač nebo sada škálování virtuálního počítače mají zapnutou [diagnostiku spouštění](boot-diagnostics.md) . Pokud používáte sériové prostředí v instanci sady škálování virtuálních počítačů, ujistěte se, že vaše instance má nejnovější model.
"Sériová konzola Azure vyžaduje, aby byl virtuální počítač spuštěný. Pro spuštění virtuálního počítače použijte tlačítko Start výše. "  | Instance virtuálního počítače nebo virtuálního počítače musí být v počátečním stavu pro přístup ke konzole sériového portu (virtuální počítač se nesmí zastavit ani zrušit jeho přidělení). Zajistěte, aby vaše virtuální počítač nebo instance sady škálování virtuálních počítačů běžely, a zkuste to znovu.
"Sériová konzola Azure není pro toto předplatné povolená, požádejte svého správce předplatného, aby povolil." | Na úrovni předplatného se dá zakázat konzola sériového rozhraní Azure. Pokud jste správcem předplatného, můžete [Povolit a zakázat službu Azure Serial Console](./serial-console-enable-disable.md). Pokud nejste správcem předplatného, měli byste se obrátit na svého správce předplatného a získat další postup.
Při přístupu k tomuto virtuálnímu počítači účet úložiště diagnostiky spouštění došlo k odpovědi "Zakázáno". | Ujistěte se, že diagnostika spouštění nemá bránu firewall účtu. Účet úložiště diagnostiky dostupné spouštěcí je nezbytné pro konzole sériového portu na funkci. Sériová konzola podle návrhu nemůžou pracovat s povolenými branami firewall účtu úložiště v účtu úložiště diagnostiky spouštění.
Nemáte potřebná oprávnění k použití tohoto virtuálního počítače se sériovou konzolou. Ujistěte se, máte alespoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje, abyste měli na svém VIRTUÁLNÍm počítači nebo v sadě škálování virtuálního počítače přístup na úrovni přispěvatele nebo novější. Další informace najdete na stránce s [přehledem](serial-console-overview.md).
Účet úložiště, který se používá pro diagnostiku spouštění na tomto virtuálním počítači, se nepovedlo najít. Ověřte, jestli je pro tento virtuální počítač povolená Diagnostika spouštění, jestli se tento účet úložiště neodstranil a máte k tomuto účtu úložiště přístup. | Dvakrát ověřte, že jste neodstranili účet úložiště diagnostiky spouštění pro váš virtuální počítač nebo sadu škálování virtuálního počítače.
V připojení k virtuálnímu počítači v sériové konzole došlo k chybě: Chybný požadavek (400) | K tomu může dojít v případě, že identifikátor URI diagnostiky spouštění není správný. Například "http://" bylo použito místo "https://". Identifikátor URI diagnostiky spouštění lze opravit pomocí tohoto příkazu: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Nemáte požadovaná oprávnění k zápisu do účtu úložiště diagnostiky spouštění tohoto virtuálního počítače. Ujistěte se prosím, že máte aspoň oprávnění přispěvatele virtuálních počítačů. | Přístup Sériová konzola vyžaduje přístup na úrovni přispěvatele v účtu úložiště diagnostiky spouštění. Další informace najdete na stránce s [přehledem](serial-console-overview.md).
Nepovedlo se určit skupinu prostředků pro účet úložiště diagnostiky spouštění  *&lt;STORAGEACCOUNTNAME&gt;* . Ověřte, že Diagnostika spouštění je povolená pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup Sériová konzola vyžaduje přístup na úrovni přispěvatele v účtu úložiště diagnostiky spouštění. Další informace najdete na stránce s [přehledem](serial-console-overview.md).
Zřizování pro tento virtuální počítač ještě nebylo úspěšné. Ujistěte se prosím, že je virtuální počítač plně nasazený, a zkuste připojení ke sériové konzole znovu. | Virtuální počítač nebo sada škálování virtuálního počítače se pořád můžou zřídit. Chvíli počkejte a pak to zkuste znovu.
Webové sokety je uzavřený nebo nelze otevřít. | Je možné, že budete muset přidat přístup k bráně firewall `*.console.azure.com`. Podrobnější, ale delší přístup je povolení přístupu brány firewall k [rozsahům IP adres Microsoft Azure datacentra](https://www.microsoft.com/download/details.aspx?id=41653), které se často mění poměrně.
Sériová konzola nepracuje s účtem úložiště pomocí Azure Data Lake Storage Gen2 s hierarchickými obory názvů. | Jedná se o známý problém s hierarchickými obory názvů. Pokud chcete zmírnit, ujistěte se, že účet úložiště diagnostiky spouštění virtuálního počítače není vytvořený pomocí Azure Data Lake Storage Gen2. Tuto možnost lze nastavit pouze při vytváření účtu úložiště. Je možné, že budete muset vytvořit samostatný účet úložiště diagnostiky spouštění bez Azure Data Lake Storage Gen2 povoleného pro zmírnění tohoto problému.


## <a name="next-steps"></a>Další kroky
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače se systémem Linux](./serial-console-linux.md)
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače s Windows](./serial-console-windows.md)