---
title: Chyby služby Azure Serial Console | Dokumenty společnosti Microsoft
description: Běžné chyby v rámci konzoly Azure Serial Console
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060690"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Běžné chyby v rámci konzoly Azure Serial Console
V konzole Azure Serial Console existuje sada známých chyb. Toto je seznam těchto chyb a zmírnění kroky pro ně.

## <a name="common-errors"></a>Běžné chyby

Chyba                             |   Omezení rizik
:---------------------------------|:--------------------------------------------|
"Služba Azure Serial Console vyžaduje povolení diagnostiky spouštění. Kliknutím sem nakonfigurujete diagnostiku spouštění virtuálního počítače." | Ujistěte se, že škálovací sada virtuálního počítače nebo virtuálního počítače má [povolenou diagnostiku spouštění.](boot-diagnostics.md) Pokud používáte konzolu sériového počítače v instanci škálovací sady virtuálních strojů, ujistěte se, že vaše instance má nejnovější model.
"Azure Serial Console vyžaduje spuštění virtuálního počítače. Pomocí výše uvedeného tlačítka Start můžete spustit virtuální počítač."  | Instance škálovací sady virtuálního počítače nebo virtuálního počítače musí být ve spuštěném stavu, aby bylo možné přistupovat ke sériové konzole (virtuální počítač nesmí být zastaven nebo vzdálený). Ujistěte se, že vaše instance škálovací sady virtuálního počítače nebo virtuálního počítače běží a zkuste to znovu.
"Služba Azure Serial Console není pro toto předplatné povolená, požádejte správce předplatného o povolení." | Konzolu Azure Serial Console lze zakázat na úrovni předplatného. Pokud jste správce předplatného, můžete [povolit a zakázat službu Azure Serial Console](./serial-console-enable-disable.md). Pokud nejste správce předplatného, měli byste se obraťte na správce předplatného pro další kroky.
Při přístupu k účtu diagnostického úložiště spuštění tohoto virtuálního aplikace došlo k odpovědi zakázané. | Ujistěte se, že diagnostika spouštění nemá bránu firewall účtu. Přístupný účet spouštěcího diagnostického úložiště je nezbytný pro fungování konzoly sériového zařízení. Sériová konzola podle návrhu nemůže pracovat s firewally účtů úložiště povolenými v účtu úložiště pro diagnostiku spouštění.
Nemáte požadovaná oprávnění k použití tohoto virtuálního virtuálního zařízení se sériovou konzolou. Ujistěte se, že máte alespoň oprávnění role přispěvatele virtuálního počítače.| Přístup ke konzole sériové konzole vyžaduje, abyste měli přístup na úrovni přispěvatele nebo vyšší na škálovací sadě virtuálního počítače nebo virtuálního počítače. Další informace naleznete na [stránce s přehledem](serial-console-overview.md).
Účet úložiště '' používá pro diagnostiku spouštění na tomto virtuálním počítači nebyl nalezen. Ověřte, že je pro tento virtuální virtuální účet povolená diagnostika spouštění, tento účet úložiště nebyl odstraněn a máte přístup k tomuto účtu úložiště. | Zkontrolujte, zda jste neodstranili účet úložiště diagnostiky spouštění pro sadu škálování virtuálních počítače nebo virtuálních strojů.
Připojení konzoly sériové konzole k virtuálnímu virtuálnímu ms zjistilo chybu: "Chybný požadavek" (400) | Tato k tomu může dojít, pokud je identifikátor URI diagnostiky spouštění nesprávný. Například "http://" byl použit místo "https://". Identifikátor URI diagnostiky spouštění lze opravit tímto příkazem:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Nemáte požadovaná oprávnění k zápisu do účtu úložiště diagnostiky spouštění pro tento virtuální virtuální účet. Ujistěte se, že máte alespoň oprávnění přispěvatele virtuálního viceprezidenta. | Přístup ke konzole sériové konzole vyžaduje přístup na úrovni přispěvatele k účtu úložiště pro diagnostiku spouštění. Další informace naleznete na [stránce s přehledem](serial-console-overview.md).
Nelze určit skupinu prostředků pro účet * &lt;&gt;* úložiště boot diagnostiky STORAGEACCOUNTNAME . Ověřte, že je pro tento virtuální virtuální účet povolená diagnostika spouštění a že máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériové konzole vyžaduje přístup na úrovni přispěvatele k účtu úložiště pro diagnostiku spouštění. Další informace naleznete na [stránce s přehledem](serial-console-overview.md).
Zřizování pro tento virtuální virtuální mohl ještě úspěšné. Zkontrolujte, zda je virtuální virtuální zařízení plně nasazený, a opakujte připojení konzoly sériového portu. | Vaše škálovací sada virtuálního počítače nebo virtuálního počítače může být stále zřizování. Počkejte nějaký čas a zkuste to znovu.
Webový soket je uzavřen nebo nelze otevřít. | Je možné, že bude `*.console.azure.com`nutné přidat přístup brány firewall k aplikaci . Podrobnější, ale delší přístup je umožnit bráně firewall přístup k [rozsahům IP adres Datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se mění poměrně pravidelně.
Sériová konzola nefunguje s účtem úložiště pomocí Azure Data Lake Storage Gen2 s hierarchickými obory názvů. | Jedná se o známý problém s hierarchickými obory názvů. Chcete-li zmírnit, ujistěte se, že váš účet úložiště diagnostiky spouštění virtuálního počítače se nevytvoří pomocí Azure Data Lake Storage Gen2. Tuto možnost lze nastavit pouze při vytvoření účtu úložiště. Možná budete muset vytvořit samostatný účet úložiště boot diagnostiky bez Azure Data Lake Storage Gen2 povoleno ke zmírnění tohoto problému.
Připojení sériové konzoly k virtuálnímu virtuálnímu serveru zjistilo chybu: Zakázáno(SubscriptionNotEnabled) - Název předplatného nedefinovaný, id id id \<id> je v nepovoleném stavu nedefinovaný | K tomuto problému může dojít, pokud bylo zakázáno předplatné, ve které uživatel vytvořil svůj účet úložiště cloudového prostředí. Chcete-li zmírnit, spusťte Cloud Shell a [proveďte kroky nezbytné](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) k opětovnému zřízení účtu záložního úložiště pro Cloud Shell v aktuálním předplatném.

## <a name="next-steps"></a>Další kroky
* Další informace o virtuálních [počítačích Azure Serial Console pro Linux](./serial-console-linux.md)
* Další informace o [službě Azure Serial Console pro virtuální počítače s Windows](./serial-console-windows.md)