---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88815466"
---
Nastavení klíčů spravovaných zákazníkem pro vaše disky bude vyžadovat, abyste vytvořili prostředky v určitém pořadí, pokud je v tuto chvíli vytváříte poprvé. Nejprve budete muset vytvořit a nastavit Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Nastavení Azure Key Vault

1. Přihlaste se k [portálu Azure Portal](https://aka.ms/diskencryptionupdates).
1. Vyhledejte a vyberte **trezory klíčů**.

    [![Snímek obrazovky Azure Portal se rozbaleným dialogovým oknem hledání](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Váš Trezor klíčů Azure, sada šifrování disků, virtuální počítač, disky a snímky musí být všechny ve stejné oblasti a předplatném, aby nasazení bylo úspěšné.

1. Vyberte **+ Přidat** a vytvořte novou Key Vault.
1. Vytvoříte novou skupinu prostředků.
1. Zadejte název trezoru klíčů, vyberte oblast a vyberte cenovou úroveň.

    > [!NOTE]
    > Při vytváření instance Key Vault musíte povolit ochranu po tichém odstranění a vyprázdnění. Obnovitelné odstranění zajistí, že Key Vault obsahuje odstraněný klíč pro dané období uchování (výchozí hodnota je 90 den). Vyčištění ochrany zajišťuje, že odstraněný klíč nebude možné trvale odstranit, dokud doba uchování neuplyne. Tato nastavení chrání před ztrátou dat kvůli náhodnému odstranění. Tato nastavení jsou povinná při použití Key Vault k šifrování spravovaných disků.

1. Vyberte **zkontrolovat + vytvořit**, ověřte své volby a pak vyberte **vytvořit**.

    ![Snímek obrazovky s prostředím pro vytváření Azure Key Vault Zobrazení konkrétních hodnot, které vytvoříte](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Až se váš Trezor klíčů dokončí, vyberte ho.
1. V části **Nastavení**vyberte **klíče** .
1. Vyberte **Generovat/importovat**.

    ![Snímek obrazovky s podoknem Key Vaultch nastavení prostředků Zobrazuje tlačítko pro generování/import v nastavení.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. U obou **typů klíčů** ponechte nastavenou hodnotu **RSA** a **velikost klíče RSA** na hodnotu **2048**.
1. Vyplňte zbývající výběr podle vašich pokynů a pak vyberte **vytvořit**.

    ![Snímek obrazovky okna vytvořit klíč, který se zobrazí po výběru tlačítka pro generování/import](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Nastavení sady pro šifrování disku

1. Vyhledejte **sady pro šifrování disků** a vyberte ji.
1. V okně **sady Disk Encryption Sets** vyberte **+ Přidat**.

    ![Snímek obrazovky s hlavní obrazovkou portálu pro šifrování disků Zvýraznění tlačítka Přidat](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Vyberte skupinu prostředků, pojmenujte sadu šifrování a vyberte stejnou oblast jako Trezor klíčů.
1. V části **typ šifrování** vyberte možnost místní **šifrování pomocí klíče spravovaného zákazníkem**.

    > [!NOTE]
    > Jakmile vytvoříte sadu šifrování disků s určitým typem šifrování, nelze ji změnit. Pokud chcete použít jiný typ šifrování, musíte vytvořit novou sadu šifrování disku.

1. Vyberte **kliknutím a vyberte klíč**.
1. Vyberte Trezor klíčů a klíč, který jste vytvořili dříve, a také verzi.
1. Stiskněte **Vybrat**.
1. Vyberte **Zkontrolovat a vytvořit** a pak **Vytvořit**.

    ![Snímek obrazovky okna pro vytvoření šifrování disku Zobrazuje se předplatné, skupina prostředků, název sady šifrování disku, oblast a selektor klíčů a trezoru klíčů.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Jakmile se dokončí vytváření, otevřete sadu šifrování disku a vyberte výstrahu, která se objeví.

    ![Snímek obrazovky s automaticky otevírané okno výstrahy: Pokud chcete přidružit disk, obrázek nebo snímek k sadě pro šifrování disků, musíte udělit oprávnění k trezoru klíčů. Pokud chcete pokračovat, vyberte tuto výstrahu.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Měla by se zobrazit a úspěšně zobrazit dvě oznámení. To vám umožní používat pro svůj Trezor klíčů sadu Disk Encryption.

    ![Snímek obrazovky s úspěšným oprávněním a přiřazením role pro váš Trezor klíčů](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)