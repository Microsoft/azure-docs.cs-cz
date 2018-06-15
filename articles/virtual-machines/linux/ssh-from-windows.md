---
title: Používat klíče SSH se systémem Windows pro virtuální počítače s Linuxem | Microsoft Docs
description: Zjistěte, jak vygenerovat a používat klíče SSH na počítači se systémem Windows pro připojení k virtuální počítač s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "31601018"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak používat klíče SSH se systémem Windows v Azure

Tento článek představuje způsoby, jak vygenerovat a používat klíče zabezpečené shell (SSH) v počítači s Windows vytvořit a připojit k Linux virtuálního počítače (VM) v Azure. Chcete-li používat klíče SSH z Linuxu nebo systému macOS klienta, přečtěte si téma [rychlé](mac-create-ssh-keys.md) nebo [podrobné](create-ssh-keys-detailed.md) pokyny.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Balíčky pro systém Windows a klientů SSH
Připojení k a spravovat virtuální počítače s Linuxem v Azure pomocí *klient SSH*. Počítače se systémem Linux nebo systému macOS obvykle mají sady příkazů SSH ke generování a správu klíčů SSH a vytvoření připojení SSH. 

Počítače se systémem Windows vždy nemají porovnatelný z hlediska SSH příkazy nainstalována. Verze Windows 10, které zahrnují [subsystému Windows pro Linux](https://docs.microsoft.com/windows/wsl/about) umožňují spouštět a přístup nástroje, jako je například klientem SSH nativně v rámci prostředí Bash. 

Pokud chcete použít něco jiného než Bash pro systém Windows, jsou běžné Windows SSH klientů, které můžete nainstalovat místně součástí následujících balíčků:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Emulaci](https://cygwin.com/)

Další možností je použít také k dispozici v Bash v SSH nástroje [prostředí cloudu Azure](../../cloud-shell/overview.md). 

* Přístup k prostředí cloudu v prohlížeči na [ https://shell.azure.com ](https://shell.azure.com) nebo [portál Azure](https://portal.azure.com). 
* Přístup k prostředí cloudu jako terminál z v rámci Visual Studio Code nainstalováním [účet Azure rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
Tato část uvádí dvě možnosti, jak vytvořit dvojici klíčů SSH v systému Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Vytvoření klíčů SSH s ssh-keygen

Pokud spustíte příkazové prostředí, například Bash pro systém Windows nebo Git Bash (nebo Bash v prostředí cloudu Azure), vytvořte dvojici klíčů SSH pomocí `ssh-keygen` příkaz. Zadejte následující příkaz a odpovědět výzvy. Pokud dvojici klíčů SSH existuje v aktuální umístění, tyto soubory jsou přepsány. 

```bash
ssh-keygen -t rsa -b 2048
```

A další informace o pozadí najdete v tématu [rychlé](mac-create-ssh-keys.md) nebo [podrobné](create-ssh-keys-detailed.md) kroky k vytvoření klíče `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Vytvoření klíčů SSH s PuTTYgen

Pokud byste radši chtěli použít nástroj využívající grafické rozhraní k vytváření klíčů SSH, můžete použít klíče generátor PuTTYgen součástí [PuTTY stažení balíčku](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Pokud chcete vytvořit pár klíče SSH RSA s PuTTYgen:

1. Spusťte PuTTYgen.

2. Klikněte na tlačítko **generovat**. Ve výchozím nastavení vygeneruje PuTTYgen klíče RSA SSH-2 2048 bitů.

4. Myší na prázdnou oblast k vygenerování některých náhodnost klíče.

5. Po vygenerování veřejný klíč, volitelně zadejte a potvrďte heslo. Jste vyzváni k heslo při ověřování k virtuálnímu počítači pomocí klíče SSH. Bez přístupové heslo Pokud někdo získá váš privátní klíč, můžete přihlášení k žádné virtuální počítač nebo služba, která používá tento klíč. Doporučujeme že vytvořit přístupové heslo. Pokud však heslo zapomenete, neexistuje žádný způsob, jak jej obnovit.

6. Veřejný klíč se zobrazí v horní části okna. Zkopírujte a vložte veřejný klíč této jednořádkové formátu do portálu Azure nebo šablonu Azure Resource Manager, když vytvoříte virtuální počítač s Linuxem. Můžete také kliknout na **uložit veřejný klíč** pro uložení kopie do počítače:

    ![Uložte soubor PuTTY veřejného klíče](./media/ssh-from-windows/save-public-key.png)

7. Volitelně můžete uložit privátní klíč ve formátu PuTTy soukromého klíče (soubor .ppk), klikněte na tlačítko **uložit privátní klíč**. Budete potřebovat soubor .ppk chcete používat PuTTY později k vytvoření připojení SSH pro virtuální počítač.

    ![Uložte soubor privátního klíče PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Pokud chcete uložit privátní klíč ve formátu OpenSSH, klikněte na tlačítko formátu soukromého klíče používají klienti SSH mnoho **převody** > **klíč OpenSSH exportovat**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače

Pokud chcete vytvořit virtuální počítač Linux, který používá klíče SSH pro ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí portálu Azure nebo jiné metody.

Následující příklad ukazuje, jak by zkopírujte a vložte tento veřejný klíč do portálu Azure při vytváření virtuálního počítače s Linuxem. Veřejný klíč je obvykle pak uloženy v `~/.ssh/authorized_keys` na nový virtuální počítač.

   ![Veřejný klíč použít při vytváření virtuálního počítače na portálu Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Jedním ze způsobů vytvoření připojení SSH k virtuálním počítačům s Linuxem ze systému Windows je používat klienta SSH. Toto je upřednostňovaná metoda, pokud máte klientem SSH v systému Windows, nebo pomocí nástrojů SSH v Bash v prostředí cloudu Azure. Pokud dáváte přednost nástroje využívající grafické rozhraní, můžete se připojit s PuTTY.  

### <a name="use-an-ssh-client"></a>Pomocí klienta SSH
S veřejný klíč nasazené na vašem virtuálním počítači Azure a privátní klíč v lokálním systému, SSH k virtuálnímu počítači pomocí IP adresu nebo název DNS vašeho virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu s uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresa):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste nakonfigurovali přístupové heslo, když jste vytvořili dvojici klíčů, zadejte heslo po zobrazení výzvy během procesu přihlášení.

### <a name="connect-with-putty"></a>Spojte se s PuTTY

Pokud jste nainstalovali [PuTTY stažení balíčku](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) a dříve vytvořila PuTTY privátní klíč (soubor .ppk), se můžete připojit k virtuálního počítače s Linuxem pomocí klienta PuTTY.

1. Spusťte PuTTy.

2. Zadejte název hostitele nebo IP adresa vašeho virtuálního počítače z portálu Azure:

    ![Otevřít nové připojení PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Před výběrem **otevřete**, klikněte na tlačítko **připojení** > **SSH** > **Auth** kartě. Vyhledejte a vyberte PuTTY privátní klíč (soubor .ppk):

    ![Vyberte PuTTY privátní klíč pro ověřování](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klikněte na tlačítko **otevřete** pro připojení k virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

* Podrobné kroky, možnosti a pokročilé příklady práce s klíčů SSH naleznete v tématu [podrobné kroky k vytvoření SSH klíče dvojice](create-ssh-keys-detailed.md).

* Pro generování klíčů SSH a vytvoření připojení SSH pro virtuální počítače s Linuxem můžete také použít PowerShell v prostředí cloudu Azure. Najdete v článku [rychlé spuštění PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Pokud máte potíže při používání SSH připojit k virtuální počítače Linux, najdete v článku [řešení SSH připojení k virtuální počítač Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
