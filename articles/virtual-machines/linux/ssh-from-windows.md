---
title: Použití klíčů SSH s Windows pro virtuální počítače s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak vygenerovat a používat klíče SSH na počítači Windows pro připojení k virtuální počítač s Linuxem v Azure.
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
ms.date: 09/12/2018
ms.author: danlep
ms.openlocfilehash: abb0ba6eace2e837ea2f74a0d919097f8801101e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407412"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Použití klíčů SSH s Windows v Azure

Tento článek popisuje, jak vygenerovat a použít *secure shell* klíče (SSH) v počítači Windows vytvořit a připojit se k virtuální počítač s Linuxem (VM) v Azure. Použití klíčů SSH z Linuxu nebo macOS klienta, najdete v článku [rychlé](mac-create-ssh-keys.md) nebo [podrobné](create-ssh-keys-detailed.md) pokyny.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Balíčky Windows a klienty SSH
Připojte se k a spravovat virtuální počítače s Linuxem v Azure s využitím *klienta SSH*. Počítače se systémem Linux nebo macOS obvykle mají sadu příkazů SSH ke vygenerovat a spravovat klíče SSH a vytvoření připojení SSH. 

Počítače Windows vždy nemají srovnatelné příkazy SSH nainstalované. Nejnovější verze Windows 10 poskytují [příkazy klient OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) vytvořit a spravovat klíče SSH a zajišťovat připojení SSH z příkazového řádku. Nejnovější verze Windows 10 zahrnují také [subsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/about) ke spuštění a přístup k nástroje, jako jsou klienta SSH nativně v rámci prostředí Bash. 

Další běžné Windows SSH, který lze nainstalovat místně jsou klienti součástí následujících balíčků:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Můžete použít také nástroje SSH, který je dostupný v Bashi ve [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Přístup ke službě Cloud Shell ve webovém prohlížeči na [ https://shell.azure.com ](https://shell.azure.com) nebo [webu Azure portal](https://portal.azure.com). 
* Přístup ke službě Cloud Shell jako k terminálu z editoru Visual Studio Code pomocí instalace [rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
V následujících částech jsou dvě možnosti k vytvoření páru klíčů SSH ve Windows. Můžete použít příkaz prostředí (`ssh-keygen`) nebo nástroje grafického rozhraní (PuTTYgen).

### <a name="create-ssh-keys-with-ssh-keygen"></a>Vytvoření klíčů SSH s ssh-keygen

Pokud spustíte příkazové okno na Windows, která podporuje klientské nástroje SSH (nebo použít Azure Cloud Shell), vytvoření páru klíčů SSH pomocí `ssh-keygen` příkazu. Zadejte následující příkaz a do zobrazených výzev. Pokud existuje pár klíčů SSH ve zvolené umístění, tyto soubory jsou přepsány. 

```bash
ssh-keygen -t rsa -b 2048
```

Další související informace a informace, najdete v článku [rychlé](mac-create-ssh-keys.md) nebo [podrobné](create-ssh-keys-detailed.md) kroky k vytvoření klíčů SSH pomocí `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Vytvoření klíčů SSH pomocí nástroje PuTTYgen

Pokud chcete použít nástroj využívající grafické rozhraní pro vytváření klíčů SSH, můžete použít Generátor klíče PuTTYgen, součástí [balíček ke stažení PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Pomocí nástroje PuTTYgen vytvoření páru klíčů SSH RSA:

1. Spuštění nástroje PuTTYgen.

2. Klikněte na tlačítko **generovat**. Ve výchozím nastavení nástroje PuTTYgen generuje 2048bitový klíč RSA SSH-2.

4. Pohybujte myši v prázdné oblasti zajistit náhodnost klíče.

5. Po vygenerování veřejný klíč, volitelně zadejte a potvrďte heslo. Zobrazí se výzva pro heslo, při ověřování pomocí privátního klíče SSH k virtuálnímu počítači. Bez přístupového hesla kdyby někdo získal váš privátní klíč, můžete přihlášení k libovolné virtuální počítač nebo službu, která používá tento klíč. Doporučujeme že vytvořit přístupové heslo. Pokud však heslo zapomenete, neexistuje žádný způsob, jak jej obnovit.

6. Veřejný klíč se zobrazí v horní části okna. Můžete zkopírovat celý veřejný klíč a vložte ho do portálu Azure portal nebo šablony Azure Resource Manageru při vytváření virtuálního počítače s Linuxem. Můžete také vybrat **uložit veřejný klíč** uložit kopii do vašeho počítače:

    ![Uložte soubor veřejného klíče PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Volitelně můžete uložit privátní klíč ve formátu PuTTy soukromého klíče (soubor .ppk), vyberte **uložit privátní klíč**. Budete potřebovat soubor .ppk později k použití klienta PuTTY k vytvořte připojení SSH k virtuálnímu počítači.

    ![Uložte soubor privátního klíče PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Pokud chcete uložit privátní klíč ve formátu OpenSSH, formátu soukromého klíče používané mnoha klientů SSH, vyberte **převody** > **Export OpenSSH key**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače

Pokud chcete vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte veřejný klíč SSH při vytváření virtuálního počítače pomocí webu Azure portal nebo jiné metody.

Následující příklad ukazuje, jak by zkopírujte a vložte veřejný klíč do portálu Azure při vytváření virtuálního počítače s Linuxem. Veřejný klíč je obvykle pak uloženy v adresáři ~/.ssh/authorized_key na nový virtuální počítač.

   ![Použít veřejný klíč při vytvoření virtuálního počítače na webu Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Připojte se ke svému virtuálnímu počítači

Jedním ze způsobů připojení SSH k virtuálním počítačům s Linuxem z Windows je pomocí klienta SSH. Toto je upřednostňovanou metodou, pokud máte klienta SSH nainstaluje do systému Windows, nebo použití nástroje SSH v Bashi ve službě Azure Cloud Shell. Pokud dáváte přednost nástroje využívající grafické rozhraní, můžete se připojit pomocí klienta PuTTY.  

### <a name="use-an-ssh-client"></a>Pomocí klienta SSH
Veřejný klíč nasazené na vašem virtuálním počítači Azure, a privátní klíč v místním systému, SSH k virtuálnímu počítači pomocí IP adresy nebo název DNS virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu se uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste nakonfigurovali přístupové heslo při vytváření páru klíčů, zadejte heslo vyzvání během procesu přihlášení.

### <a name="connect-with-putty"></a>Připojit pomocí klienta PuTTY

Pokud jste nainstalovali [balíček ke stažení PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) a dříve vygenerovaný soubor privátního klíče PuTTY (.ppk) můžete připojit k virtuálnímu počítači s Linuxem pomocí klienta PuTTY.

1. Spusťte PuTTy.

2. Zadejte název hostitele nebo IP adresu svého virtuálního počítače z portálu Azure portal:

    ![Otevřít nové připojení PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Vyberte **připojení** > **SSH** > **Auth** kategorie. Vyhledejte a vyberte váš PuTTY privátní klíč (soubor .ppk):

    ![Vyberte svůj PuTTY privátní klíč pro ověřování](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klikněte na tlačítko **otevřít** pro připojení k vašemu virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

* Podrobný postup, možnosti a pokročilé příklady práce s klíči SSH najdete v tématu [dvojic klíčů podrobný postup vytvoření SSH](create-ssh-keys-detailed.md).

* Generování klíčů SSH a připojení SSH pro virtuální počítače s Linuxem můžete také použít prostředí PowerShell ve službě Azure Cloud Shell. Zobrazit [rychlý start pro PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Pokud máte potíže s používáním SSH k připojení virtuálních počítačů s Linuxem, naleznete v tématu [řešení potíží s připojení SSH k virtuálnímu počítači Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
