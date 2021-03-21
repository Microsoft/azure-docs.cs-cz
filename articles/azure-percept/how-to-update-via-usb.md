---
title: Aktualizace Azure Percept DK přes připojení USB
description: Naučte se aktualizovat Azure Percept DK přes připojení USB.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662375"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Postup aktualizace služby Azure Percept DK přes připojení USB

V této příručce se dozvíte, jak provést aktualizaci USB pro nosnou desku vaší služby Azure Percept DK.

## <a name="prerequisites"></a>Předpoklady
- Hostitelský počítač s dostupným portem USB-C nebo USB-A.
- Nosná deska pro Azure Percept DK (dev Kit) a dodaná USB-C kabel USB-C. Pokud má hostitelský počítač port USB-A, ale ne port USB-C, můžete použít USB-C kabel USB (samostatně prodávaný).
- Instalace [výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (vyžaduje se přístup správce).
- Nainstalujte nástroj NXP UUU. [Stáhněte si nejnovější](https://github.com/NXPmicro/mfgtools/releases) soubor uuu.exe pro vydání (pro Windows) nebo soubor Uuu (pro Linux) na kartě assets (prostředky).
- [Nainstalujte 7 – zip](https://www.7-zip.org/). Tento software bude použit k extrakci nezpracovaného souboru bitové kopie z jeho komprimovaného souboru XZ. Stáhněte a nainstalujte příslušný soubor. exe.

## <a name="steps"></a>Postup
1.  Stáhněte si následující [tři soubory aktualizace USB](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101-***&lt; číslo &gt; verze*** UEFI. Raw. XZ
    - emmc_full.txt
    - Fast-HAB-FW. Raw
 
1. Extrahuje se do **&lt; čísla &gt; verze** pe101-UEFI-*_. Raw z komprimovaného souboru pe101-UEFI_ * _&lt; číslo &gt; verze_* *. Raw. XZ. Nevíte, jak extrahovat? Stáhněte a nainstalujte 7-zip, potom klikněte pravým tlačítkem na soubor bitové kopie **. XZ** a vyberte 7-Zip &gt; extrakce.

1. Zkopírujte následující tři soubory do složky, která obsahuje nástroj UUU:
    - Byl extrahován soubor pe101-UEFI-***&lt; Version číslo &gt;***. Raw (z kroku 2).
    - emmc_full.txt (z kroku 1).
    - Fast-HAB-FW. Raw (z kroku 1).
 
1. Zapněte sadu dev Kit.
1. [Připojení k sadě dev SDK přes SSH](./how-to-ssh-into-percept-dk.md)
1. Otevřete příkazový řádek systému Windows (Start &gt; cmd) nebo terminál pro Linux a přejděte do složky, kde jsou uloženy soubory aktualizace. Spusťte následující příkaz pro zahájení aktualizace:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Po spuštění těchto příkazů se může zobrazit zpráva oznamující, že se čeká na zařízení... na příkazovém řádku. To se očekává a měli byste přejít k dalšímu kroku.
    
1. Připojte k hostitelskému počítači desku pro vývojovou sadu pomocí kabelu USB. V závislosti na tom, které porty jsou k dispozici, se vždy připojíte od portů USB-c hostitelského počítače k portu USB-C nebo USB (USB-C pro USB-A kabel, který se prodává samostatně). 
 
1. V terminálu SSH/výstupy zadejte následující příkazy, abyste sadu dev Kit nastavili na režim USB, a pak restartujte sadu dev Kit.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Můžete získat indikaci, že hostitelský počítač rozpoznává zařízení a proces aktualizace se automaticky spustí. Chcete-li zobrazit stav, přejděte zpět na příkazový řádek. Proces bude trvat až deset minut a po úspěšném dokončení aktualizace se zobrazí zpráva s oznámením o úspěšnosti 1 – selhání 0
 
1. Po dokončení aktualizace vypněte desku dopravce. Odpojte kabel USB od počítače.  Zapojte modul Azure Percept Vision zpátky do nosné desky pomocí kabelu USB.

1. Zapněte si nosnou desku.

## <a name="next-steps"></a>Další kroky

Vaše sada dev SDK se teď úspěšně aktualizovala. V DevKit můžete pokračovat v vývoji a provozu.