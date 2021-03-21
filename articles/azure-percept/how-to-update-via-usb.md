---
title: Aktualizace Azure Percept DK přes připojení USB
description: Naučte se aktualizovat Azure Percept DK přes připojení USB.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 12f6acda632b9c0fbee2db570df5293c1daf32ea
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720810"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Postup aktualizace služby Azure Percept DK přes připojení USB

I když je nejlepší způsob, jak zajistit, aby byl operační systém a firmware sady pro vývoj v sadě OTA (Over-The-Air) k dispozici, existují scénáře, kdy je potřeba aktualizovat (nebo "blikání") Vývojářská sada přes připojení USB:

- Aktualizace OTA není možná kvůli připojení nebo jiným technickým problémům.
- Zařízení se musí resetovat zpátky do svého továrního stavu.

V této příručce se dozvíte, jak úspěšně aktualizovat operační systém a firmware sady vývojových sad prostřednictvím připojení USB.

> [!WARNING]
> Aktualizace sady dev SDK přes USB odstraní všechna stávající data v zařízení, včetně modelů a kontejnerů AI.
>
> Postupujte podle všech instrukcí v uvedeném pořadí. Přeskočení kroků může být vaše sada pro vývoj v nepoužitelném stavu.

## <a name="prerequisites"></a>Předpoklady

- Azure Percept DK
- Hostitelský počítač se systémem Windows, Linux nebo OS X s možností Wi-Fi a dostupným portem USB-C nebo USB-A
- Kabel USB-C pro USB-A kabel (volitelné, prodávané samostatně)
- Přihlášení SSH vytvořené během [prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Stažení softwarových nástrojů a souborů aktualizace

1. [Nástroj NXP Uuu](https://github.com/NXPmicro/mfgtools/releases). Stáhněte si **nejnovější** soubor uuu.exe pro vydání (pro Windows) nebo soubor Uuu (pro Linux) na kartě **assets (prostředky** ).

1. [7 – zip](https://www.7-zip.org/). Tento software bude použit k extrakci nezpracovaného souboru bitové kopie z jeho komprimovaného souboru XZ. Stáhněte a nainstalujte příslušný soubor. exe.

1. [Stáhněte si soubory aktualizace](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Zajistěte, aby byly k dispozici všechny tři artefakty sestavení:
    - Azure-Percept-DK-*&lt; číslo &gt; verze*. Raw. XZ
    - Fast-HAB-FW. Raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Nastavení prostředí

1. Vytvořte složku nebo adresář v hostitelském počítači v umístění, které je snadno přístupné prostřednictvím příkazového řádku.

1. Zkopírujte nástroj UUU (**uuu.exe** nebo **Uuu**) do nové složky.

1. Kliknutím pravým tlačítkem na ***&lt; číslo &gt; verze* Azure-Percept-DK-Version** z komprimovaného souboru rozbalte soubor **Azure-Percept-DK-*&lt; &gt; Version*** Number. Raw. XZ a vyberte **7-zip** &gt; **extrakce**.

1. Přesuňte extrahovaný soubor s ***&lt; číslem &gt; verze* Azure-Percept-DK-Version** , **Fast-HAB-FW. Raw** a **emmc_full.txt** do složky obsahující nástroj Uuu.

## <a name="update-your-device"></a>Aktualizace zařízení

1. [Do sady dev Kit SSH](./how-to-ssh-into-percept-dk.md).

1. Dále otevřete příkazový řádek systému Windows (**Start**  >  **cmd**) nebo terminál pro Linux a přejděte do složky, kde jsou uloženy soubory aktualizace a nástroj Uuu. Do příkazového řádku nebo terminálu zadejte následující příkaz, který připraví počítač pro příjem zařízení s možností blikání:

    - Windows:

        ```bash
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Odpojte zařízení se službou Azure Percept Vision z portu USB-C nosné desky.

1. Připojte dodaný kabel USB-C k portu USB-C kabiny nosné desky a portu USB-C hostitelského počítače. Pokud má váš počítač pouze port USB-A, připojte USB-C kabel USB-A kabelem (samostatně prodávaný) na desku a hostitelský počítač.

1. Na příkazovém řádku klienta SSH zadejte následující příkazy:

    1. Nastavte režim aktualizace zařízení na USB:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Restartujte zařízení. Spustí se instalace aktualizace.

        ```bash
        sudo reboot -f
        ```

1. Přejděte zpět na další příkazový řádek nebo terminál. Po dokončení aktualizace se zobrazí zpráva s tímto ```Success 1    Failure 0``` :

    > [!NOTE]
    > Po aktualizaci se vaše zařízení obnoví do továrního nastavení a ztratí se Wi-Fi připojení a přihlášení SSH.

1. Po dokončení aktualizace vypněte desku dopravce. Odpojte kabel USB od počítače.  

## <a name="next-steps"></a>Další kroky

Projděte si [prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) a překonfigurujte vaše zařízení.