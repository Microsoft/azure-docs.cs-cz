---
title: Podrobný postup vytvoření páru klíčů SSH
description: Přečtěte si podrobné pokyny k vytvoření a správě páru veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 34a84ed333172ea0931c529d2dbeee1b774ae8c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016356"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Podrobný postup: vytváření a Správa klíčů SSH pro ověřování na virtuálním počítači Linux v Azure

Pomocí páru klíčů SSH (Secure Shell) můžete vytvořit virtuální počítač se systémem Linux, který používá klíče SSH k ověřování. V tomto článku se dozvíte, jak vytvořit a použít dvojici souborů klíčů veřejného privátního klíče SSH RSA pro připojení klientů SSH.

Pokud chcete rychlé příkazy, přečtěte si téma [jak vytvořit pár veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux v Azure](mac-create-ssh-keys.md).

Pokud chcete vytvořit klíče SSH a použít je k připojení k počítači s **Windows** , přečtěte si téma [použití klíčů ssh s Windows v Azure](ssh-from-windows.md). Pomocí [Azure Portal](../ssh-keys-portal.md) taky můžete vytvářet a spravovat klíče SSH pro vytváření virtuálních počítačů na portálu.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Použití a výhody klíčů SSH

Když vytvoříte virtuální počítač Azure zadáním veřejného klíče, Azure zkopíruje veřejný klíč (ve `.pub` formátu) do `~/.ssh/authorized_keys` složky na virtuálním počítači. Klíče SSH v nástroji `~/.ssh/authorized_keys` slouží k výzvě klienta, aby se shodoval s odpovídajícím privátním klíčem na připojení SSH. V případě virtuálního počítače Azure Linux, který používá klíče SSH k ověřování, Azure nakonfiguruje server SSHD tak, aby nepovoloval přihlášení k heslům, jenom klíče SSH. Vytvořením virtuálního počítače Azure Linux pomocí klíčů SSH můžete zvýšit zabezpečení nasazení virtuálního počítače a uložit obvyklý krok konfigurace po nasazení, který zakazuje hesla v `sshd_config` souboru.

Pokud nechcete používat klíče SSH, můžete pro virtuální počítač se systémem Linux nastavit ověřování pomocí hesla. Pokud váš virtuální počítač není přístupný z Internetu, může stačit použití hesla. Pořád ale budete muset spravovat hesla pro každý virtuální počítač Linux a udržovat dobré zásady a postupy pro hesla, jako je například minimální délka hesla a pravidelné aktualizace. 

## <a name="generate-keys-with-ssh-keygen"></a>Generování klíčů pomocí ssh-keygen

Chcete-li vytvořit klíče, preferovaný příkaz je `ssh-keygen` , který je k dispozici u OpenSSH nástrojů v Azure Cloud Shell, na hostiteli MacOS nebo Linux a na Windows 10. `ssh-keygen` požádá o sérii otázek a pak zapíše privátní klíč a shodný veřejný klíč. 

Klíče SSH jsou ve výchozím nastavení v adresáři `~/.ssh`.  Pokud adresář `~/.ssh` nemáte, vytvoří ho za vás příkaz `ssh-keygen` se správnými oprávněními.

### <a name="basic-example"></a>Základní příklad

Následující `ssh-keygen` příkaz generuje ve výchozím nastavení v adresáři soubory veřejného a privátního klíče SSH RSA standardně 4096 `~/.ssh` . Pokud pár klíčů SSH existuje v aktuálním umístění, tyto soubory se přepíší.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Podrobný příklad
Následující příklad ukazuje další možnosti příkazu pro vytvoření páru klíčů RSA RSA. Pokud pár klíčů SSH existuje v aktuálním umístění, tyto soubory se přepíší. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Vysvětlení příkazu**

`ssh-keygen`= program použitý k vytvoření klíčů

`-m PEM` = naformátovat klíč jako PEM

`-t rsa` = typ klíče, který se má vytvořit, v tomto případě ve formátu RSA

`-b 4096` = počet bitů v klíči, v tomto případě 4096

`-C "azureuser@myserver"`= komentář připojený na konec souboru veřejného klíče pro snadnější identifikaci. Obvykle se jako komentář používá e-mailová adresa, ale je vhodná pro vaši infrastrukturu.

`-f ~/.ssh/mykeys/myprivatekey` = název souboru privátního klíče, pokud se rozhodnete nepoužívat výchozí název. Odpovídající soubor veřejného klíče připojený pomocí `.pub` je vygenerovaný ve stejném adresáři. Adresář musí existovat.

`-N mypassphrase` = Další heslo, které se používá pro přístup k souboru privátního klíče. 

### <a name="example-of-ssh-keygen"></a>Příklad ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Soubory uložených klíčů

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Název páru klíčů pro tento článek. Jako výchozí je pojmenovaná dvojice klíčů. `id_rsa` některé nástroje můžou očekávat `id_rsa` název souboru privátního klíče, takže jeden z nich je dobrý nápad. Výchozím umístěním pro páry klíčů SSH a konfigurační soubor SSH je adresář `~/.ssh/`. Pokud nezadáte úplnou cestu, `ssh-keygen` vytvoří klíče v aktuálním pracovním adresáři, nikoli ve výchozím adresáři `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Seznam `~/.ssh` adresáře

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Klíčové heslo

`Enter passphrase (empty for no passphrase):`

*Důrazně* doporučujeme přidat k privátnímu klíči přístupové heslo. Bez přístupového hesla k ochraně souboru s klíčem může kdokoli, kdo má soubor, použít ho k přihlášení k jakémukoli serveru s odpovídajícím veřejným klíčem. Přidání hesla nabízí vyšší ochranu v případě, že někdo získá přístup k vašemu souboru privátního klíče, což vám poskytne čas na změnu klíčů.

## <a name="generate-keys-automatically-during-deployment"></a>Generovat klíče automaticky během nasazení

Pokud k vytvoření virtuálního počítače použijete rozhraní příkazového [řádku Azure](/cli/azure) , můžete volitelně vygenerovat soubory veřejného a privátního klíče SSH spuštěním příkazu [AZ VM Create](/cli/azure/vm) s `--generate-ssh-keys` možností. Klíče jsou uloženy v adresáři ~/.ssh. Všimněte si, že tato možnost příkazu nepřepisuje klíče, pokud již existují v tomto umístění.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Při nasazování virtuálního počítače zadat veřejný klíč SSH

Pokud chcete vytvořit virtuální počítač se systémem Linux, který používá klíče SSH k ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí Azure Portal, CLI, Správce prostředků šablon nebo jiných metod. Při použití portálu zadáte vlastní veřejný klíč. Pokud k vytvoření virtuálního počítače pomocí rozhraní příkazového [řádku Azure](/cli/azure) použijete existující veřejný klíč, zadejte hodnotu nebo umístění tohoto veřejného klíče spuštěním příkazu [AZ VM Create](/cli/azure/vm) s `--ssh-key-value` možností. 

Pokud nejste obeznámeni s formátem veřejného klíče SSH, můžete svůj veřejný klíč zobrazit následujícím `cat` způsobem a nahradit `~/.ssh/id_rsa.pub` ho vlastním umístěním souboru veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Výstup je podobný následujícímu (zde redigováné):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Pokud zkopírujete a vložíte obsah souboru veřejného klíče do šablony Azure Portal nebo Správce prostředků, ujistěte se, že nekopírujete žádné další prázdné znaky ani nezavedete další zalomení řádků. Pokud například používáte macOS, můžete soubor veřejného klíče (ve výchozím nastavení) přesměrovat na `~/.ssh/id_rsa.pub` **pbcopy** ke zkopírování obsahu (existují i další programy pro Linux, které mají stejnou věc, například `xclip` ).

Pokud dáváte přednost použití veřejného klíče, který je ve víceřádkovém formátu, můžete vygenerovat RFC4716 formátovaný klíč v kontejneru PEM z veřejného klíče, který jste vytvořili dříve.

Vytvoření klíče ve formátu RFC4716 z existujícího veřejného klíče SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH k VIRTUÁLNÍmu počítači pomocí klienta SSH
S veřejným klíčem nasazeným na VIRTUÁLNÍm počítači Azure a privátním klíčem v místním systému se k vašemu VIRTUÁLNÍmu počítači přihlaste přes SSH pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. V následujícím příkazu nahraďte *azureuser* a *myvm.westus.cloudapp.Azure.com* uživatelským jménem správce a plně kvalifikovaným názvem domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali heslo, zadejte heslo při zobrazení výzvy během procesu přihlašování. (Server se přidá do vaší složky `~/.ssh/known_hosts` a nové připojení se nebude vyžadovat, dokud se nezmění veřejný klíč na virtuálním počítači Azure nebo se neodebere název serveru ze složky `~/.ssh/known_hosts`.)

Pokud virtuální počítač používá zásady přístupu za běhu, musíte požádat o přístup, abyste se mohli připojit k virtuálnímu počítači. Další informace o zásadách za běhu najdete v tématu [Správa přístupu k virtuálním počítačům pomocí zásad podle časových údajů](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Použití SSH-agent k uložení hesla privátního klíče

Abyste se vyhnuli psaní klíčového hesla k souboru privátního klíče u každého přihlášení pomocí protokolu SSH, můžete použít `ssh-agent` k ukládání hesla souboru privátního klíče do mezipaměti. Pokud používáte Mac, macOS řetězec klíčů bezpečně ukládá heslo k privátnímu klíči při jeho vyvolání `ssh-agent` .

Ověřte a používejte `ssh-agent` a `ssh-add` informujte systém SSH o souborech klíčů, abyste nemuseli používat přístupové heslo interaktivně.

```bash
eval "$(ssh-agent -s)"
```

Potom přidejte privátní klíč do `ssh-agent` pomocí příkazu `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Heslo privátního klíče je nyní uloženo v `ssh-agent` .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Ke zkopírování klíče do existujícího virtuálního počítače použijte SSH-Copy-ID.

Pokud jste již vytvořili virtuální počítač, můžete pomocí nástroje Přidat nový veřejný klíč SSH k VIRTUÁLNÍmu počítači se systémem Linux `ssh-copy-id` .

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Vytvoření a nakonfigurování konfiguračního souboru SSH

Můžete vytvořit a nakonfigurovat konfigurační soubor SSH ( `~/.ssh/config` ) pro urychlení přihlášení a optimalizaci chování klienta ssh. 

Následující příklad ukazuje jednoduchou konfiguraci, kterou můžete použít k rychlému přihlášení jako uživatel k určitému virtuálnímu počítači pomocí výchozího privátního klíče SSH. 

Vytvořte soubor.

```bash
touch ~/.ssh/config
```

Úpravou souboru přidejte novou konfiguraci SSH.

```bash
vim ~/.ssh/config
```

Přidejte nastavení konfigurace odpovídající vašemu hostitelskému VIRTUÁLNÍmu počítači. V tomto příkladu je název virtuálního počítače *myvm* a název účtu je *azureuser*.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Můžete přidat konfigurace pro další hostitele, aby každý mohl použít svůj vlastní vyhrazený pár klíčů. Pokročilejší možnosti konfigurace najdete v tématu [konfigurační soubor SSH](https://www.ssh.com/ssh/config/) .

Teď, když máte pár klíčů SSH a nakonfigurovaný konfigurační soubor SSH, se můžete rychle a bezpečně přihlašovat ke svému VIRTUÁLNÍmu počítači Linux. Když spustíte následující příkaz, SSH vyhledá a načte všechna nastavení z `Host myvm` bloku v konfiguračním souboru SSH.

```bash
ssh myvm
```

Při prvním přihlášení k serveru pomocí klíče SSH vás příkaz vyzve k zadání hesla pro tento soubor klíče.

## <a name="next-steps"></a>Další kroky

Dalším krokem je vytvoření virtuálního počítače Azure s Linuxem pomocí nového veřejného klíče SSH. Virtuální počítače Azure vytvořené pomocí veřejného klíče SSH jako přihlašování jsou lépe zabezpečené než virtuální počítače vytvořené pomocí výchozí metody přihlašování, hesla.

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
