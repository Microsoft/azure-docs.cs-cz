---
title: Použití klíčů SSH s Windows pro virtuální počítače s Linuxem
description: Zjistěte, jak generovat a používat klíče SSH na počítači s Windows pro připojení k virtuálnímu počítači SIP v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e01fb23bbf1720f7d8df9c269373c1b8dc3ec75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034810"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Jak používat klíče SSH s Windows v Azure

Tento článek popisuje způsoby generování a používání *zabezpečených shellových* klíčů (SSH) v počítači se systémem Windows k vytvoření a připojení k virtuálnímu počítači (VM) systému Linux v Azure. Pokud chcete používat klíče SSH z klienta Linuxu nebo macOS, podívejte se na [rychlé](mac-create-ssh-keys.md) nebo [podrobné](create-ssh-keys-detailed.md) pokyny.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Balíčky systému Windows a klienti SSH
K virtuálním počítačům SSH se připojujete a spravujete je v Azure pomocí *klienta SSH*. Počítače se systémem Linux nebo macOS mají obvykle sadu příkazů SSH pro generování a správu klíčů SSH a vytváření připojení SSH. 

Počítače se systémem Windows nemají vždy nainstalovány srovnatelné příkazy SSH. Nejnovější verze Systému Windows 10 poskytují [příkazy klienta OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) k vytváření a správě klíčů SSH a vytváření připojení SSH z příkazového řádku. Nedávné verze systému Windows 10 také zahrnují [podsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/about) pro spouštění a přístup k nástrojům, jako je klient SSH nativně v rámci prostředí Bash. 

Další běžní klienti Windows SSH, které můžete nainstalovat místně, jsou zahrnuti v následujících balíčcích:

* [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Můžete také použít nástroje SSH, které jsou k dispozici v Bash v [prostředí Azure Cloud Shell](../../cloud-shell/overview.md). 

* Přístup k Cloud Shell [https://shell.azure.com](https://shell.azure.com) ve webovém prohlížeči na webu Nebo na [webu Azure Portal](https://portal.azure.com). 
* Přístup Cloud Shell jako terminál z v rámci Visual Studio code instalací [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
Následující části popisují dvě možnosti vytvoření dvojice klíčů SSH v systému Windows. Můžete použít příkaz prostředí`ssh-keygen`( ) nebo nástroj GUI (PuTTYgen). Všimněte si také, že při vytváření klíče pomocí aplikace Powershell nahrajte veřejný klíč ve formátu ssh.com(SECSH). Při použití příkazového příkazu převeďte klíč před nahráním do formátu OpenSSH. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Vytvoření klíčů SSH s ssh-keygen

Pokud spustíte příkazové prostředí v systému Windows, který podporuje klientské nástroje SSH `ssh-keygen` (nebo používáte Azure Cloud Shell), vytvořte pár klíčů SSH pomocí příkazu. Zadejte následující příkaz a odpovězte na výzvy. Pokud ve zvoleném umístění existuje dvojice klíčů SSH, budou tyto soubory přepsány. 

```bash
ssh-keygen -t rsa -b 2048
```

Další informace o pozadí a informacích naleznete v [rychlých](mac-create-ssh-keys.md) nebo [podrobných](create-ssh-keys-detailed.md) krocích k vytvoření klíčů SSH pomocí `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Vytvoření klíčů SSH pomocí PuTTYgen

Pokud dáváte přednost použití nástroje založeného na grafickém uživatelském rozhraní k vytvoření klíčů SSH, můžete použít generátor klíčů PuTTYgen, který je součástí [balíčku ke stažení PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Chcete-li vytvořit dvojici klíčů SSH RSA s PuTTYgen:

1. Začněte PuTTYgen.

2. Klepněte na **tlačítko Generovat**. Ve výchozím nastavení PuTTYgen generuje 2048bitový klíč SSH-2 RSA.

4. Pohybujte myší v prázdné oblasti, abyste zajistili náhodnost pro klíč.

5. Po vygenerování veřejného klíče volitelně zadejte a potvrďte přístupové heslo. Při ověřování na virtuálním počítači pomocí privátního klíče SSH budete vyzváni k zadání přístupové fráze. Bez přístupové fráze, pokud někdo získá váš soukromý klíč, může se přihlásit k libovolnému virtuálnímu počítači nebo službě, která tento klíč používá. Doporučujeme vytvořit přístupové heslo. Pokud však heslo zapomenete, neexistuje žádný způsob, jak jej obnovit.

6. Veřejný klíč se zobrazí v horní části okna. Celý tento veřejný klíč můžete zkopírovat a pak ho vložit do portálu Azure nebo do šablony Azure Resource Manageru při vytváření virtuálního počítače s Linuxem. Můžete také vybrat **uložit veřejný klíč** pro uložení kopie do počítače:

    ![Uložit soubor veřejného klíče PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Volitelně chcete-li uložit soukromý klíč ve formátu soukromého klíče PuTTy (soubor.ppk), vyberte **uložit soukromý klíč**. Budete potřebovat soubor PPK později použít PuTTY k navázání připojení SSH k virtuálnímu virtuálnímu provozu.

    ![Uložit soubor soukromého klíče PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Pokud chcete uložit soukromý klíč ve formátu OpenSSH, což je formát soukromého klíče používaný mnoha klienty SSH, vyberte **příkaz Převody** > **Exportovat openssh klíč**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Poskytnutí veřejného klíče SSH při nasazování virtuálního mísy

Chcete-li vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí portálu Azure nebo jiných metod.

Následující příklad ukazuje, jak byste zkopírovali a vložili tento veřejný klíč na portál Azure při vytváření virtuálního počítače s Linuxem. Veřejný klíč se obvykle pak uloží v adresáři ~/.ssh/authorized_key na novém virtuálním počítači.

   ![Použití veřejného klíče při vytváření virtuálního počítače na webu Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Jedním ze způsobů, jak vytvořit připojení SSH k virtuálnímu počítači SSH ze systému Windows, je použití klienta SSH. Toto je upřednostňovaná metoda, pokud máte v systému Windows nainstalovaný klientSSH nebo pokud používáte nástroje SSH v Bash v Azure Cloud Shell. Pokud dáváte přednost nástroji založenému na grafickém uživatelském rozhraní, můžete se připojit k PuTTY.  

### <a name="use-an-ssh-client"></a>Použití klienta SSH
S veřejným klíčem nasazeným na vašem virtuálním počítači Azure a privátním klíčem v místním systému SSH do virtuálního počítače pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu uživatelským jménem správce a plně kvalifikovaným názvem domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření dvojice klíčů nakonfigurovali přístupové heslo, zadejte je po zobrazení výzvy během procesu přihlášení.

Pokud virtuální ho správu používá zásady přístupu za chvíli, musíte požádat o přístup, než se budete moct připojit k virtuálnímu virtuálnímu zařízení. Další informace o zásadách just-in-time najdete v tématu [Správa přístupu k virtuálnímu počítači pomocí zásad just in time](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Spojte se s PuTTY

Pokud jste nainstalovali [balíček pro stažení PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) a dříve vygenerovali soubor soukromého klíče PuTTY (.ppk), můžete se připojit k virtuálnímu počítači Linux u PuTTY.

1. Začni putty.

2. Vyplňte název hostitele nebo IP adresu vašeho virtuálního počítače z webu Azure:

    ![Otevřít nové připojení PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Vyberte kategorii **Připojení** > **SSH** > **Auth.** Vyhledejte a vyberte svůj soukromý klíč PuTTY (soubor.ppk):

    ![Vyberte svůj soukromý klíč PuTTY pro ověřování](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kliknutím na **Otevřít** se připojte k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

* Podrobné kroky, možnosti a pokročilé příklady práce s klávesami SSH najdete [v tématu Podrobné kroky k vytvoření párů klíčů SSH](create-ssh-keys-detailed.md).

* PowerShell u Azure Cloud Shellu můžete taky použít ke generování klíčů SSH a k vytváření připojení SSH k virtuálním počítačům s Linuxem. Podívejte se na [rychlý start prostředí PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Pokud máte potíže s používáním SSH pro připojení k virtuálním počítačům s Linuxem, [přečtěte si článek Poradce při potížích s připojením SSH k virtuálnímu počítači Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
