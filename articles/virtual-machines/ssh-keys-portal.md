---
title: Vytvoření klíčů SSH v Azure Portal
description: Naučte se generovat a ukládat klíče SSH v Azure Portal pro připojení virtuálních počítačů se systémem Linux.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88929430"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Generování a ukládání klíčů SSH v Azure Portal

Pokud portál často používáte k nasazení virtuálních počítačů se systémem Linux, můžete je pomocí klíčů SSH zjednodušit tak, že je vytvoříte přímo na portálu nebo je nahrajete z počítače.

Klíče SSH můžete vytvořit při prvním vytvoření virtuálního počítače a znovu je použít pro jiné virtuální počítače. Můžete také vytvořit klíče SSH samostatně, abyste měli sadu klíčů uložených v Azure, aby vyhovovala potřebám vaší organizace. 

Pokud máte existující klíče a chcete je zjednodušit při jejich použití na portálu, můžete je nahrát a uložit v Azure pro opakované použití.

Podrobnější informace o vytváření a používání klíčů SSH s virtuálními počítači se systémem Linux najdete v tématu [použití klíčů ssh pro připojení k virtuálním počítačům se systémem Linux](./linux/ssh-from-windows.md).

## <a name="generate-new-keys"></a>Generovat nové klíče

1. Otevřete [Azure Portal](https://portal.azure.com).

1. V horní části stránky zadejte *SSH* pro hledání. V části **Marketplace** vyberte **klíče SSH**.

1. Na stránce **klíč SSH** vyberte **vytvořit**.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Vytvořte novou skupinu prostředků a vygenerujte pár klíčů SSH.":::

1. V **skupiny prostředků** vyberte **vytvořit novou** a vytvořte novou skupinu prostředků pro uložení klíčů. Zadejte název vaší skupiny prostředků a vyberte **OK**.

1. V **oblasti** vyberte oblast, do které se mají ukládat klíče. Můžete použít klíče v libovolné oblasti, jedná se jenom o oblast, kde se budou ukládat.

1. Do **názvu páru klíčů** zadejte název klíče.

1. V případě **zdroje veřejného klíče SSH** vyberte **Generovat zdroj veřejných klíčů**. 

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

1. Jakmile ověření projde, vyberte **vytvořit**.

1. Zobrazí se automaticky otevírané okno, vyberte **Stáhnout privátní klíč a vytvořit prostředek**. Tím se stáhne klíč SSH jako soubor. pem.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="Stažení privátního klíče jako souboru. pem":::

1. Po stažení souboru. pem můžete ho chtít přesunout někam do počítače, kde se snadno odkazuje na klienta SSH.


## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

V místním počítači otevřete příkazový řádek PowerShellu a zadejte:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Zadejte například: `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Nahrajte klíč SSH.

Můžete také nahrát veřejný klíč SSH pro uložení v Azure. Informace o tom, jak vytvořit pár klíčů SSH, najdete v tématu [použití klíčů ssh pro připojení k virtuálním počítačům se systémem Linux](./linux/ssh-from-windows.md).

1. Otevřete [Azure Portal](https://portal.azure.com).

1. V horní části stránky zadejte *SSH* pro hledání. V části **Marketplace* vyberte **klíče SSH**.

1. Na stránce **klíč SSH** vyberte **vytvořit**.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Nahrajte veřejný klíč SSH, který se uloží v Azure.":::

1. V **skupiny prostředků** vyberte **vytvořit novou** a vytvořte novou skupinu prostředků pro uložení klíčů. Zadejte název vaší skupiny prostředků a vyberte **OK**.

1. V **oblasti** vyberte oblast, do které se mají ukládat klíče. Můžete použít klíče v libovolné oblasti, jedná se jenom o oblast, kde se budou ukládat.

1. Do **názvu páru klíčů** zadejte název klíče.

1. V případě **zdroje veřejného klíče SSH** vyberte **Odeslat existující veřejný klíč**. 

1. Vložte úplný obsah veřejného klíče do části **nahrát klíč** a potom vyberte **zkontrolovat + vytvořit**.

1. Po dokončení ověření vyberte **vytvořit**. 

Po nahrání klíče se můžete rozhodnout použít při vytváření virtuálního počítače.

## <a name="list-keys"></a>Zobrazit seznam klíčů

Klíče SSH vytvořené na portálu se ukládají jako prostředky, takže můžete filtrovat zobrazení prostředků, abyste je viděli.

1. Na portálu vyberte **všechny prostředky**.
1. V okně Filtry vyberte **typ**, zrušte výběr možnosti **Vybrat vše** , čímž seznam vymažete.
1. Do filtru zadejte **SSH** a vyberte **klíč SSH**.

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="Snímek obrazovky s postupem, jak filtrovat seznam, aby se zobrazily všechny klíče SSH.":::

## <a name="get-the-public-key"></a>Získat veřejný klíč

Pokud potřebujete svůj veřejný klíč, můžete ho snadno zkopírovat ze stránky portálu pro tento klíč. Stačí vytvořit seznam klíčů (pomocí procesu v poslední části) a pak vybrat klíč ze seznamu. Otevře se stránka pro váš klíč a kliknutím na ikonu **Kopírovat do schránky** vedle klíče ho můžete zkopírovat.

## <a name="next-steps"></a>Další kroky

Další informace o použití klíčů SSH s virtuálními počítači Azure najdete v tématu [použití klíčů ssh pro připojení k virtuálním počítačům se systémem Linux](./linux/ssh-from-windows.md).
