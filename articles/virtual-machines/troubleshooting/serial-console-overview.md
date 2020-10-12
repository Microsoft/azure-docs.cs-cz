---
title: Sériová konzola Azure | Microsoft Docs
description: Konzola sériového prostředí Azure umožňuje připojit se k VIRTUÁLNÍmu počítači, když nejsou k dispozici SSH nebo RDP.
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
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374061"
---
# <a name="azure-serial-console"></a>Sériová konzola Azure

Konzola sériového portu v Azure Portal poskytuje přístup k textové konzole pro virtuální počítače a instance sady škálování virtuálních počítačů, na kterých běží Linux nebo Windows. Toto sériové připojení se připojuje k sériovému portu ttyS0 nebo COM1 virtuálního počítače nebo instance sady škálování virtuálních počítačů a poskytuje přístup nezávisle na stavu sítě nebo operačního systému. Ke konzole sériového přístupu se dá přistupovat jenom pomocí Azure Portal a je povolená jenom pro uživatele, kteří mají roli přístupu přispěvatel nebo vyšší, k virtuálnímu počítači nebo sadě škálování virtuálních počítačů.

Sériová konzola funguje stejným způsobem pro virtuální počítače a instance sady škálování virtuálních počítačů. V tomto dokumentu budou všechny zmínky k virtuálním počítačům implicitně zahrnovat instance sady škálování virtuálních počítačů, pokud není uvedeno jinak.

Konzola sériového portu je všeobecně dostupná v globálních oblastech Azure a ve verzi Public Preview v Azure Government. Ještě není k dispozici v cloudu Azure Čína.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Předpoklady pro přístup ke konzole sériového rozhraní Azure
Pro přístup ke konzole sériového portu na virtuálním počítači nebo instanci sady škálování virtuálního počítače budete potřebovat následující:

- Pro virtuální počítač musí být povolená Diagnostika spouštění.
- Uživatelský účet, který používá ověřování heslem, musí existovat v rámci virtuálního počítače. Uživatele založené na heslech můžete vytvořit pomocí funkce [resetovat heslo](../extensions/vmaccess.md#reset-password) rozšíření pro přístup k virtuálním počítačům. V části **Podpora a řešení potíží** vyberte **resetovat heslo** .
- Účet Azure přistupující ke konzole sériového portu musí mít [roli Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač i pro účet úložiště [diagnostiky spouštění](boot-diagnostics.md) .
- Klasická nasazení nejsou podporovaná. Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí používat model nasazení Azure Resource Manager.

> [!NOTE]
> Sériová konzola je aktuálně nekompatibilní se spravovaným účtem úložiště diagnostiky spouštění. Pokud chcete použít sériovou konzolu, ujistěte se, že používáte vlastní účet úložiště.

## <a name="get-started-with-the-serial-console"></a>Začínáme s konzolou sériového portu
Konzola sériového portu pro virtuální počítače a sadu škálování virtuálního počítače je dostupná jenom prostřednictvím Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Sériová Konzola pro Virtual Machines
Sériová Konzola pro virtuální počítače je stejně jednoduchá jako při kliknutí na **sériová konzola** v části **Podpora a řešení potíží** v Azure Portal.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte na **všechny prostředky** a vyberte virtuální počítač. Otevře se stránka s přehledem pro virtuální počítač.

  1. Přejděte dolů k části **Podpora a řešení potíží** a vyberte **sériová konzola**. Otevře se nové podokno se sériovou konzolou a spustí se připojení.

     ![Okno sériové konzoly pro Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Sériová Konzola pro Virtual Machine Scale Sets
Sériová konzola je k dispozici pro virtuální počítače, které jsou dostupné na každé instanci v rámci sady škálování. Před zobrazením tlačítka **sériová konzola** budete muset přejít na jednotlivé instance sady škálování virtuálního počítače. Pokud vaše sada škálování virtuálního počítače nemá zapnutou diagnostiku spouštění, ujistěte se, že jste aktualizovali model sady škálování virtuálních počítačů, aby se aktivovala spouštění diagnostiky, a pak upgradovat všechny instance na nový model, aby bylo možné získat přístup ke konzole sériového portu.
  1. Otevřete web [Azure Portal](https://portal.azure.com).

  1. Přejděte na **všechny prostředky** a vyberte sadu škálování virtuálního počítače. Otevře se stránka s přehledem pro sadu škálování virtuálního počítače.

  1. Přejít k **instancím**

  1. Vyberte instanci sady škálování virtuálního počítače.

  1. V části **Podpora a řešení potíží** vyberte **sériová konzola**. Otevře se nové podokno se sériovou konzolou a spustí se připojení.

     ![Sériová Konzola pro sadu škálování virtuálního počítače se systémem Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1,2 ve sériové konzole
Sériová konzola používá protokol TLS 1,2 end-to-end k zabezpečení veškeré komunikace v rámci služby. Sériová konzola má závislost na účtu úložiště diagnostiky spouštění uživatelů a TLS 1,2 musí být nakonfigurovaná samostatně pro účet úložiště. Pokyny, jak to udělat, najdete [tady](../../storage/common/transport-layer-security-configure-minimum-version.md).

## <a name="advanced-uses-for-serial-console"></a>Pokročilé použití pro sériovou konzolu
Kromě přístupu ke konzole ke svému VIRTUÁLNÍmu počítači můžete také použít konzolu sériového prostředí Azure pro následující:
* Odeslání [příkazu pro požadavek na systém na virtuální počítač](./serial-console-nmi-sysrq.md)
* Odesílání [nemaskované přerušení na virtuální počítač](./serial-console-nmi-sysrq.md)
* Řádné [restartování virtuálního počítače nebo vynuceného jeho zacyklení](./serial-console-power-options.md)


## <a name="next-steps"></a>Další kroky
Další dokumentace k sériové konzole je k dispozici na bočním panelu.
- Další informace jsou k dispozici pro [sériové konzoly pro virtuální počítače se systémem Linux](./serial-console-linux.md).
- Další informace jsou k dispozici pro [sériové konzoly pro virtuální počítače s Windows](./serial-console-windows.md).
