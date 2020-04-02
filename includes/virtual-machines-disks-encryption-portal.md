---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6077db0a09b09f7e4bfb859902da53b173845e55
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520770"
---
### <a name="portal"></a>Portál

Nastavení klíčů spravovaných zákazníkem pro vaše disky bude vyžadovat vytvoření prostředků v určitém pořadí, pokud to děláte poprvé. Nejprve budete muset vytvořit a nastavit azure key vault.

#### <a name="setting-up-your-azure-key-vault"></a>Nastavení trezoru klíčů Azure

1. Přihlášení k [portálu Azure](https://portal.azure.com/) a hledání trezoru klíčů
1. Vyhledejte a vyberte **trezory klíčů**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Trezor klíčů Azure, sada šifrování disku, virtuální počítač, disky a snímky musí být ve stejné oblasti a předplatné, aby nasazení bylo úspěšné.

1. Výběrem **možnosti +Přidat** vytvoříte nový trezor klíčů.
1. Vytvořte novou skupinu prostředků
1. Zadejte název trezoru klíčů, vyberte oblast a vyberte cenovou úroveň.
1. Vyberte **Zkontrolovat + Vytvořit**, ověřte své volby a pak vyberte **Vytvořit**.

    ![Snímek obrazovky s prostředím pro vytváření úložiště klíčů Azure Zobrazení konkrétních hodnot, které vytvoříte](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Jakmile trezor klíčů dokončí nasazení, vyberte ho.
1. V části **Nastavení** vyberte **Klávesy**.
1. Vybrat **Generovat/importovat**

    ![Snímek obrazovky podokna Nastavení prostředků trezoru klíčů Zobrazí nastavení generovat/importovat.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Ponechte **typ klíče** nastavený na **RSA** a **velikost klíče RSA** nastavenou na **2048**.
1. Vyplňte zbývající výběry podle potřeby a pak vyberte **Vytvořit**.

    ![Snímek obrazovky s vytvořením klíče, které se zobrazí po výběru tlačítka generovat nebo importovat](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Nastavení sady šifrování disku

Chcete-li vytvořit a konfigurovat sady šifrování disku, je nutné použít následující odkaz: https://aka.ms/diskencryptionsets. Pokud se nacházejí v oblastech Microsoft Azure Government, [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)musíte místo toho použít tento odkaz: . Vytvoření sady šifrování disku ještě není na globálním portálu Azure k dispozici.

1. Otevřete propojení sady šifrování disku vhodné pro vaši oblast:

    Veřejné regiony:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Oblasti Azure Government:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Vyberte **možnost +Přidat**.

    ![Snímek obrazovky hlavní obrazovky portálu pro šifrování disku Zvýraznění tlačítka Přidat](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Vyberte skupinu prostředků, pojmenujte sadu šifrování a vyberte stejnou oblast jako trezor klíčů.
1. Vyberte **trezor klíčů a klíč**.
1. Vyberte trezor klíčů a klíč, které jste vytvořili dříve, a také verzi.
1. Stiskněte **klávesu Select**.
1. Vyberte **Zkontrolovat + Vytvořit** a potom **Vytvořit**.

    ![Snímek obrazovky s rozhraním pro vytváření šifrování disku Zobrazení předplatného, skupiny prostředků, názvu sady šifrování disku, oblasti a trezoru klíčů + voliče klíčů.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Po dokončení vytváření otevřete sadu šifrování disku a vyberte výstrahu, která se zobrazí.

    ![Snímek obrazovky s vyskakovacím panelem výstrah: "Chcete-li přidružit disk, obrázek nebo snímek k sadě šifrování disku, musíte udělit oprávnění k trezoru klíčů". Chcete-li pokračovat, vyberte tuto výstrahu.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Dvě oznámení by se měla objevit a uspět. To vám umožní použít sadu šifrování disku s trezorem klíčů.

![Snímek obrazovky s úspěšným oprávněním a přiřazením role pro trezor klíčů](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Teď, když jste vytvořili a nastavili trezor klíčů a sadu šifrování disku, můžete nasadit virtuální počítač pomocí šifrování.
Proces nasazení virtuálního počítače je podobný standardnímu procesu nasazení, jedinými rozdíly je, že potřebujete nasadit virtuální počítač ve stejné oblasti jako ostatní prostředky a rozhodnete se použít klíč spravovaný zákazníkem.

1. Otevřete propojení sady šifrování disku vhodné pro vaši oblast:

    Veřejné regiony:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Oblasti Azure Government:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Vyhledejte **virtuální počítače** a vyberte + **Přidat** k vytvoření virtuálního počítače.
1. Na kartě **Basic** vyberte stejnou oblast jako sada šifrování disku a Azure Key Vault.
1. Vyplňte ostatní hodnoty na kartě **Základní,** jak chcete.

    ![Snímek obrazovky s prostředím pro vytváření virtuálních bodů se zvýrazněnou hodnotou oblasti](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na kartě **Disky** vyberte **Možnost Šifrování v klidovém stavu pomocí klíče spravovaného zákazníkem**.
1. V rozevíracím souboru Sada šifrování disku vyberte sadu **šifrování disku.**
1. Proveďte zbývající výběry, jak se vám líbí.

    ![Snímek obrazovky s prostředím pro vytváření virtuálních počítačů, disky blade. Se zvýrazněným rozevíracím souborem sady šifrování disku.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Povolení na existujícím disku

Chcete-li spravovat a konfigurovat šifrování disku na existujících https://aka.ms/diskencryptionsetsdiscích, je nutné použít následující odkaz: . Povolení klíčů spravovaných zákazníky na existujících discích ještě není dostupné na globálním portálu Azure.

> [!CAUTION]
> Povolení šifrování disku na všech discích připojených k virtuálnímu počítači bude vyžadovat zastavení virtuálního počítače.

1. Otevřete propojení sady šifrování disku vhodné pro vaši oblast:

    Veřejné regiony:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Oblasti Azure Government:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Přejděte na virtuální počítač, který je ve stejné oblasti jako jedna ze sad šifrování disku.
1. Otevřete virtuální hod a vyberte **Zastavit**.

    ![Snímek obrazovky s hlavním překrytím pro ukázkový virtuální počítač Se zvýrazněným tlačítkem Stop](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Po dokončení zastavení virtuálního počítače vyberte **Disky** a pak vyberte disk, který chcete zašifrovat.

    ![Snímek obrazovky s ukázkovým virtuálním počítačem s otevřeným diskovým polem Disk operačního systému je zvýrazněn jako ukázkový disk, který můžete vybrat.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Vyberte **Šifrování** a **vyberte Šifrování v klidovém stavu pomocí klíče spravovaného zákazníkem** a v rozevíracím seznamu vyberte sadu šifrování disku.
1. Vyberte **Uložit**.

    ![Snímek obrazovky s ukázkovým diskem operačního systému Šifrovací okno je otevřené, šifrování v klidovém stavu pomocí klíče spravovaného zákazníkem je vybráno, stejně jako příklad Azure Key Vault. Po provedení těchto výběrů je vybráno tlačítko uložit.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Tento postup opakujte pro všechny ostatní disky připojené k virtuálnímu počítače, které chcete zašifrovat.
1. Když vaše disky dokončí přepnutí na klíče spravované zákazníkem, pokud neexistují žádné jiné připojené disky, které chcete zašifrovat, můžete začít virtuální počítač.
