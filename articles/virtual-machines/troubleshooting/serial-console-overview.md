---
title: Konzola Azure Serial Console | Dokumenty společnosti Microsoft
description: Konzola Azure Serial Console umožňuje připojení k virtuálnímu počítači, když není k dispozici SSH nebo RDP.
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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267011"
---
# <a name="azure-serial-console"></a>Azure Serial Console

Serial Console na portálu Azure poskytuje přístup k textové konzoli pro virtuální počítače (VM) a instance škálovací sady virtuálních počítačů se systémem Linux nebo Windows. Toto sériové připojení se připojuje k sériovému portu ttyS0 nebo COM1 instance škálovací sady virtuálního počítače nebo virtuálního počítače a poskytuje přístup nezávislý na stavu sítě nebo operačního systému. K sériové konzoli lze přistupovat jenom pomocí portálu Azure portal a je povolena jenom pro ty uživatele, kteří mají přístupovou roli přispěvatele nebo vyšší pro škálovací sadu virtuálního počítače nebo virtuálního počítače.

Serial Console funguje stejným způsobem pro virtuální počítače a instance škálovací sady virtuálních strojů. V tomto dokumentu budou všechny zmínky o virtuálních počítačích implicitně zahrnovat instance škálovací sady virtuálních strojů, pokud není uvedeno jinak.

> [!NOTE]
> Serial Console je obecně dostupná v globálních oblastech Azure a ve verzi Public Preview v Azure Government. V cloudu Azure China ještě není dostupná.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Předpoklady pro přístup k konzolové konzoli Azure
Chcete-li získat přístup k konzolové konzole na vaší instanci škálovací sady virtuálního počítače nebo virtuálního počítače, budete potřebovat následující:

- Diagnostika spouštění musí být povolená pro virtuální hod.
- V rámci virtuálního počítače musí existovat uživatelský účet, který používá ověřování heslem. Můžete vytvořit uživatele založeného na heslech s funkcí [resetování hesla](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozšíření pro přístup k virtuálnímu počítače. V části **Podpora + řešení potíží** vyberte Obnovit **heslo.**
- Účet Azure přistupující ke sériové konzoli musí mít [roli přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro účet úložiště virtuálního počítače i [pro spuštění diagnostického](boot-diagnostics.md) úložiště.

> [!NOTE]
> Klasická nasazení nejsou podporována. Instance škálovací sady virtuálních počítače nebo virtuálního počítače musí používat model nasazení Azure Resource Manageru.

## <a name="get-started-with-the-serial-console"></a>Začínáme se sériovou konzolou
Konzola Serial Console pro virtuální počítače a škálovací sadu virtuálních strojů je přístupná jenom prostřednictvím portálu Azure:

### <a name="serial-console-for-virtual-machines"></a>Sériová konzola pro virtuální počítače
Sériová konzola pro virtuální počítače je stejně jednoduchá jako kliknutí na **konzolu Serial** v části **Podpora + řešení potíží** na webu Azure Portal.
  1. Otevřete [portál Azure](https://portal.azure.com).

  1. Přejděte na **Všechny prostředky** a vyberte virtuální počítač. Otevře se stránka s přehledem pro virtuální hod.

  1. Přejděte dolů k části **Podpora + řešení potíží** a vyberte možnost **Sériová konzola**. Otevře se nové podokno se sériovou konzolou a spustí se připojení.

     ![Okno Linux Serial Console](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Sériová konzola pro škálovací sady virtuálních strojů
Konzola Serial Console je k dispozici pro škálovací sady virtuálních strojů, které jsou přístupné pro každou instanci v rámci škálovací sady. Před zobrazením tlačítka **konzoly Sériové zařízení** budete muset přejít na jednotlivé instance škálovací sady virtuálních strojů. Pokud vaše škálovací sada virtuálních strojů nemá povolenou diagnostiku spouštění, ujistěte se, že aktualizujete model škálovací sady virtuálních strojů tak, aby umožňoval diagnostiku spouštění, a potom upgradujte všechny instance na nový model, abyste získali přístup k sériové konzole.
  1. Otevřete [portál Azure](https://portal.azure.com).

  1. Přejděte na **Všechny prostředky** a vyberte škálovací sadu virtuálních strojů. Otevře se stránka s přehledem škálovací sady virtuálních strojů.

  1. Přechod na **instance**

  1. Výběr instance škálovací sady virtuálního počítače

  1. V části **Podpora + řešení potíží** vyberte možnost **Sériová konzola**. Otevře se nové podokno se sériovou konzolou a spustí se připojení.

     ![Škálovací sada virtuálních strojů v Linuxu Sériová konzola](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Pokročilá použití pro sériovou konzolu
Kromě přístupu konzole k virtuálnímu počítači můžete konzolu Azure Serial Console použít také pro následující:
* Odeslání [příkazu systémové žádosti do virtuálního počítače](./serial-console-nmi-sysrq.md)
* Odeslání [nemaskovatelného přerušení do virtuálního počítače](./serial-console-nmi-sysrq.md)
* Elegantní [restartování nebo vynucené power-cycling váš virtuální počítač](./serial-console-power-options.md)


## <a name="next-steps"></a>Další kroky
Další dokumentace konzoly Sériové konzole je k dispozici na postranním panelu.
- Další informace jsou k dispozici pro [serial console pro virtuální počítače s Linuxem](./serial-console-linux.md).
- Další informace jsou k dispozici pro [konzolu Serial Console pro virtuální servery windows](./serial-console-windows.md).
