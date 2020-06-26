---
title: Použití klíčů SSH s Windows pro virtuální počítače s Linuxem
description: Naučte se generovat a používat klíče SSH na počítači s Windows pro připojení k virtuálnímu počítači se systémem Linux v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 81dfac2a1623253a110833a96fddd1b41bd11b26
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85390223"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Použití klíčů SSH s Windows v Azure

Tento článek popisuje způsoby generování a použití klíčů protokolu SSH ( *Secure Shell* ) na počítači s Windows k vytvoření a připojení k virtuálnímu počítači se systémem Linux v Azure. Postup použití klíčů SSH z klienta se systémem Linux nebo macOS naleznete v tématu [rychlé](mac-create-ssh-keys.md) a [podrobné](create-ssh-keys-detailed.md) pokyny.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Balíčky Windows a klienti SSH
Pomocí *klienta ssh*se připojujete k virtuálním počítačům Linux a spravujete je v Azure. Počítače se systémem Linux nebo macOS obvykle mají sadu příkazů SSH pro generování a správu klíčů SSH a k vytváření připojení SSH. 

Na počítačích s Windows nejsou vždycky nainstalované srovnatelné příkazy SSH. Poslední verze Windows 10 poskytují [příkazy klienta OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) k vytváření a správě klíčů ssh a vytvářejí připojení SSH z příkazového řádku. Poslední verze Windows 10 také zahrnují [podsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/about) pro spouštění a přístup k nástrojům, jako je klient SSH nativně v rámci prostředí bash. 

Další běžné klienty Windows SSH, které můžete nainstalovat místně, jsou zahrnuté v následujících balíčcích:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Můžete také použít nástroje SSH dostupné v bash v [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Přístup k Cloud Shell ve webovém prohlížeči na [https://shell.azure.com](https://shell.azure.com) nebo v [Azure Portal](https://portal.azure.com). 
* Když nainstalujete [rozšíření účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), Cloud Shell v rámci Visual Studio Code přístup k tomuto terminálu.

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
Následující části popisují dvě možnosti vytvoření páru klíčů SSH ve Windows. Můžete použít příkaz Shell ( `ssh-keygen` ) nebo nástroj grafického uživatelského rozhraní (PuTTYgen). Všimněte si také, že při použití PowerShellu k vytvoření klíče nahrajte veřejný klíč jako formát SSH. com (SECSH). Při použití rozhraní příkazového řádku převeďte klíč do formátu OpenSSH před odesláním. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Vytvoření klíčů SSH pomocí ssh-keygen

Pokud spustíte příkazové prostředí ve Windows, které podporuje nástroje klienta SSH (nebo používáte Azure Cloud Shell), vytvořte pár klíčů SSH pomocí `ssh-keygen` příkazu. Zadejte následující příkaz a odpovězte na výzvy. Pokud ve zvoleném umístění existuje pár klíčů SSH, tyto soubory se přepíší. 

```bash
ssh-keygen -t rsa -b 2048
```

Další informace a informace najdete v tématu [rychlé](mac-create-ssh-keys.md) nebo [podrobné](create-ssh-keys-detailed.md) kroky k vytvoření klíčů ssh pomocí `ssh-keygen` .

### <a name="create-ssh-keys-with-puttygen"></a>Vytvoření klíčů SSH pomocí PuTTYgen

Pokud upřednostňujete použití nástroje založeného na grafickém uživatelském rozhraní k vytváření klíčů SSH, můžete použít generátor klíčů PuTTYgen, který je zahrnutý v [balíčku pro stažení výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Vytvoření páru klíčů SSH RSA pomocí PuTTYgen:

1. Spusťte PuTTYgen.

2. Klikněte na **Generovat**. Ve výchozím nastavení PuTTYgen generuje 2048 klíč RSA-2 pro SSH.

3. Přesunutím myši kolem prázdné oblasti zadáte náhodnost pro klíč.

4. Po vygenerování veřejného klíče můžete zadat a Potvrdit heslo. Po ověření pro virtuální počítač s privátním klíčem SSH se zobrazí výzva k zadání hesla. Bez přístupového hesla, pokud někdo získá privátní klíč, se může přihlásit k libovolnému virtuálnímu počítači nebo službě, která tento klíč používá. Doporučujeme vytvořit přístupové heslo. Pokud však heslo zapomenete, neexistuje žádný způsob, jak jej obnovit.

5. Veřejný klíč se zobrazí v horní části okna. Můžete zkopírovat celý veřejný klíč a pak ho vložit do Azure Portal nebo do Azure Resource Manager šablony při vytváření virtuálního počítače se systémem Linux. Můžete také vybrat možnost **Uložit veřejný klíč** a uložit kopii do počítače. Všimněte si, že při ukládání do souboru převede výstupy veřejný klíč na jiný formát [RFC4716](https://tools.ietf.org/html/rfc4716). Formát RFC4716 nemusí být kompatibilní se všemi rozhraními API. Takže pokud chcete použít v Azure Portal, doporučujeme zkopírovat veřejný klíč, který se zobrazí v okně výstupu.

    ![Uložit soubor s veřejným klíčem výstupu](./media/ssh-from-windows/save-public-key.png)

6. Pokud chcete uložit privátní klíč ve formátu RTF (soubor. ppk), vyberte **Uložit privátní klíč**. K vytvoření připojení SSH k virtuálnímu počítači budete potřebovat soubor. ppk později.

    ![Uložit soubor privátního klíče na výstupu](./media/ssh-from-windows/save-ppk-file.png)

    Pokud chcete uložit privátní klíč ve formátu OpenSSH, použije se formát privátního klíče, který používá mnoho klientů SSH, a pak vyberte **převody**  >  **exportovat OpenSSH Key**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Při nasazování virtuálního počítače zadejte veřejný klíč SSH.

Pokud chcete vytvořit virtuální počítač se systémem Linux, který používá klíče SSH k ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí Azure Portal nebo jiných metod.

Následující příklad ukazuje, jak byste při vytváření virtuálního počítače se systémem Linux zkopírovali a vložili tento veřejný klíč do Azure Portal. Veřejný klíč se obvykle uloží do složky ~/.ssh/authorized_key v novém VIRTUÁLNÍm počítači.

   ![Při vytváření virtuálního počítače v Azure Portal použít veřejný klíč](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Jedním ze způsobů, jak vytvořit připojení SSH k VIRTUÁLNÍmu počítači Linux z Windows, je použití klienta SSH. Toto je upřednostňovaná metoda, pokud máte nainstalovaného klienta SSH v systému Windows nebo pokud používáte nástroje SSH v bash v Azure Cloud Shell. Pokud dáváte přednost nástroji založenému na grafickém uživatelském rozhraní, můžete se připojit pomocí výstupu.  

### <a name="use-an-ssh-client"></a>Použití klienta SSH
S veřejným klíčem nasazeným na VIRTUÁLNÍm počítači Azure a privátním klíčem v místním systému se k vašemu VIRTUÁLNÍmu počítači přihlaste přes SSH pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. V následujícím příkazu nahraďte *azureuser* a *myvm.westus.cloudapp.Azure.com* uživatelským jménem správce a plně kvalifikovaným názvem domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů nakonfigurovali heslo, zadejte ho při zobrazení výzvy během procesu přihlašování.

Pokud virtuální počítač používá zásady přístupu za běhu, musíte požádat o přístup, abyste se mohli připojit k virtuálnímu počítači. Další informace o zásadách za běhu najdete v tématu [Správa přístupu k virtuálním počítačům pomocí zásad podle časových údajů](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Připojení pomocí výstupu

Pokud jste nainstalovali [balíček pro stažení](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) a dříve vygenerovali soubor privátního klíče (. ppk), můžete se připojit k virtuálnímu počítači se systémem Linux pomocí výstupu.

1. Počáteční výstup.

2. Do Azure Portal zadejte název hostitele nebo IP adresu vašeho virtuálního počítače:

    ![Otevřít nové připojení k výstupu](./media/ssh-from-windows/putty-new-connection.png)

3. Vyberte **Connection**  >  **SSH**  >  kategorii**ověřování** SSH připojení. Vyhledejte a vyberte svůj privátní klíč pro zadané datové soubory (soubor. ppk):

    ![Vyberte pro ověřování privátní klíč poštovního výstupu.](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kliknutím na **otevřít** se připojte k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

* Podrobný postup, možnosti a rozšířené příklady práce s klíči SSH najdete v tématu [podrobný postup vytvoření párů klíčů ssh](create-ssh-keys-detailed.md).

* PowerShell můžete také použít v Azure Cloud Shell k vygenerování klíčů SSH a k vytvoření připojení SSH k virtuálním počítačům se systémem Linux. Další informace najdete v tématu [rychlý Start prostředí PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Pokud máte potíže s použitím protokolu SSH pro připojení k virtuálním počítačům se systémem Linux, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
