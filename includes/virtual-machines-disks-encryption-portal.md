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
ms.openlocfilehash: 3ba5d74aa245fbcd9d43f2b4398387d7f59e202c
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299488"
---
### <a name="portal"></a>Portál

Nastavení klíčů spravovaných zákazníkem pro vaše disky bude vyžadovat, abyste vytvořili prostředky v určitém pořadí, pokud je v tuto chvíli vytváříte poprvé. Nejprve budete muset vytvořit a nastavit Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Nastavení Azure Key Vault

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyhledejte Key Vault
1. Vyhledejte a vyberte **trezory klíčů**.

    [![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Váš Trezor klíčů Azure, sada šifrování disků, virtuální počítač, disky a snímky musí být všechny ve stejné oblasti a předplatném, aby nasazení bylo úspěšné.

1. Vyberte **+ Přidat** a vytvořte novou Key Vault.
1. Vytvoření nové skupiny prostředků
1. Zadejte název trezoru klíčů, vyberte oblast a vyberte cenovou úroveň.
1. Vyberte **zkontrolovat + vytvořit**, ověřte své volby a pak vyberte **vytvořit**.

    ![Snímek obrazovky s prostředím pro vytváření Azure Key Vault Zobrazení konkrétních hodnot, které vytvoříte](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Až se váš Trezor klíčů dokončí, vyberte ho.
1. V části **Nastavení**vyberte **klíče** .
1. Vybrat **vygenerovat/importovat**

    ![Snímek obrazovky s podoknem Key Vaultch nastavení prostředků Zobrazuje tlačítko pro generování/import v nastavení.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. U obou **typů klíčů** ponechte nastavenou hodnotu **RSA** a **velikost klíče RSA** na hodnotu **2080**.
1. Vyplňte zbývající výběr podle vašich pokynů a pak vyberte **vytvořit**.

    ![Snímek obrazovky okna vytvořit klíč, který se zobrazí po výběru tlačítka pro generování/import](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Nastavení sady pro šifrování disku

Chcete-li vytvořit a nakonfigurovat sady pro šifrování disků, je nutné použít následující odkaz: https://aka.ms/diskencryptionsets. Pokud jste v oblasti Microsoft Azure Government, musíte místo toho použít tento odkaz: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff). Vytvoření sady šifrování disku ještě není v globálním Azure Portal k dispozici.

1. Otevřete odkaz sady Disk Encryption Sets odpovídající vaší oblasti:

    Veřejné oblasti: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government oblasti: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Vyberte **+ Přidat**.

    ![Snímek obrazovky s hlavní obrazovkou portálu pro šifrování disků Zvýraznění tlačítka Přidat](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Vyberte skupinu prostředků, pojmenujte sadu šifrování a vyberte stejnou oblast jako Trezor klíčů.
1. Vyberte **Trezor klíčů a klíč**.
1. Vyberte Trezor klíčů a klíč, který jste vytvořili dříve, a také verzi.
1. Stiskněte **Vybrat**.
1. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**.

    ![Snímek obrazovky okna pro vytvoření šifrování disku Zobrazuje se předplatné, skupina prostředků, název sady šifrování disku, oblast a selektor klíčů a trezoru klíčů.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Jakmile se dokončí vytváření, otevřete sadu šifrování disku a vyberte výstrahu, která se objeví.

    ![Snímek obrazovky s automaticky otevírané okno výstrahy: Pokud chcete přidružit disk, obrázek nebo snímek k sadě pro šifrování disků, musíte udělit oprávnění k trezoru klíčů. Pokud chcete pokračovat, vyberte tuto výstrahu.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Měla by se zobrazit a úspěšně zobrazit dvě oznámení. Díky tomu budete moct použít sadu Disk Encryption s vaším trezorem klíčů.

![Snímek obrazovky s úspěšným oprávněním a přiřazením role pro váš Trezor klíčů](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Teď, když jste vytvořili a nastavili Trezor klíčů a sadu šifrování disků, můžete nasadit virtuální počítač pomocí šifrování.
Proces nasazení virtuálního počítače se podobá standardnímu procesu nasazení. jediným rozdílem je, že je potřeba nasadit virtuální počítač ve stejné oblasti, ve které se nacházejí vaše další prostředky, a Vy se rozhodnete použít spravovaný klíč zákazníka.

1. Otevřete odkaz sady Disk Encryption Sets odpovídající vaší oblasti:

    Veřejné oblasti: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government oblasti: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Vyhledejte **Virtual Machines** a vyberte **+ Přidat** a vytvořte virtuální počítač.
1. Na kartě **základní** vyberte stejnou oblast jako sadu šifrování disku a Azure Key Vault.
1. Zadejte další hodnoty na kartě **Basic** , jak chcete.

    ![Snímek obrazovky s možností vytváření virtuálních počítačů s zvýrazněnou hodnotou oblasti](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na kartě **disky** vyberte možnost **šifrování v klidovém kódu s klíčem spravovaným zákazníkem**.
1. V rozevíracím seznamu **sada Encryption disk** vyberte sadu šifrování disku.
1. Vyberte zbývající výběr.

    ![Snímek obrazovky s prostředím pro vytváření virtuálních počítačů, okno disky Zvýrazněný rozevírací seznam sady Disk Encryption](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Povolit na stávajícím disku

Chcete-li spravovat a konfigurovat šifrování disku na stávajících discích, je nutné použít následující odkaz: https://aka.ms/diskencryptionsets. Povolení klíčů spravovaných zákazníkem na existujících discích ještě není v globálním Azure Portal k dispozici.

> [!CAUTION]
> Povolení šifrování disku na všech discích připojených k VIRTUÁLNÍm počítačům bude vyžadovat, abyste virtuální počítač zastavili.

1. Otevřete odkaz sady Disk Encryption Sets odpovídající vaší oblasti:

    Veřejné oblasti: [https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Azure Government oblasti: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Přejděte do virtuálního počítače, který je ve stejné oblasti jako jedna ze sad pro šifrování disků.
1. Otevřete virtuální počítač a vyberte **zastavit**.

    ![Snímek obrazovky s hlavním překryvem pro ukázkový virtuální počítač Se zvýrazněným tlačítkem STOP](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Po ukončení činnosti virtuálního počítače vyberte **disky** a pak vyberte disk, který chcete zašifrovat.

    ![Snímek obrazovky s ukázkovým VIRTUÁLNÍm počítačem a otevře se okno disky. Disk s operačním systémem se zvýrazní, jako příklad disku, který chcete vybrat.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Vyberte **šifrování** a vyberte možnost **šifrování v klidovém kódu pomocí klíče spravovaného zákazníkem** a potom v rozevíracím seznamu vyberte svou sadu Disk Encryption.
1. Vyberte **Save** (Uložit).

    ![Snímek obrazovky s ukázkovým diskem s operačním systémem. Otevře se okno šifrování, vybral se šifrování v klidovém formátu s klíčem spravovaným zákazníkem a také vaše příklad Azure Key Vault. Po provedení těchto výběrů je vybráno tlačítko Uložit.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Tento postup opakujte pro všechny ostatní disky připojené k virtuálnímu počítači, který chcete zašifrovat.
1. Pokud disky dokončí přepínání na klíče spravované zákazníkem a žádné další připojené disky nechcete zašifrovat, můžete svůj virtuální počítač spustit.