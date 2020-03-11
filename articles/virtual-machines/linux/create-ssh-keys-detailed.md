---
title: Podrobný postup vytvoření páru klíčů SSH
description: Přečtěte si podrobné pokyny k vytvoření a správě páru veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969539"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Podrobný postup: vytváření a Správa klíčů SSH pro ověřování na virtuálním počítači Linux v Azure 
Pomocí páru klíčů SSH (Secure Shell) můžete vytvořit virtuální počítač se systémem Linux v Azure, který bude standardně používat klíče SSH pro ověřování. tím se eliminuje nutnost přihlášení hesel. Virtuální počítače vytvořené pomocí Azure Portal, Azure CLI, šablony Správce prostředků nebo jiné nástroje můžou jako součást nasazení zahrnovat veřejný klíč SSH, který nastavuje ověřování pomocí klíče SSH pro připojení SSH. 

Tento článek poskytuje podrobné informace a kroky k vytvoření a správě dvojice souborů veřejného privátního klíče SSH RSA pro připojení klientů SSH. Pokud chcete rychlé příkazy, přečtěte si téma [jak vytvořit pár veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux v Azure](mac-create-ssh-keys.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači s Windows, najdete v tématu [použití klíčů ssh s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Heslo privátního klíče
Privátní klíč SSH by měl mít velmi zabezpečené heslo pro ochranu. Toto heslo je jenom pro přístup k privátnímu souboru klíče SSH *a není* to heslo k uživatelskému účtu. Když do klíče SSH přidáte přístupové heslo, šifruje privátní klíč pomocí 128 AES, aby privátní klíč byl nepoužitý bez přístupového hesla k dešifrování. Pokud by útočník stole váš privátní klíč a tento klíč neobsahoval heslo, mohl by tento soukromý klíč použít k přihlášení k jakémukoli serveru s odpovídajícím veřejným klíčem. Pokud je privátní klíč chráněný heslem, nemůžete ho použít, protože poskytuje další úroveň zabezpečení pro vaši infrastrukturu v Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Použití a výhody klíčů SSH

Když vytvoříte virtuální počítač Azure zadáním veřejného klíče, Azure zkopíruje veřejný klíč (ve formátu `.pub`) do složky `~/.ssh/authorized_keys` na virtuálním počítači. Klíče SSH v `~/.ssh/authorized_keys` slouží ke výzvě klienta, aby se shodoval s odpovídajícím privátním klíčem na připojení SSH. V případě virtuálního počítače Azure Linux, který používá klíče SSH k ověřování, Azure nakonfiguruje server SSHD tak, aby nepovoloval přihlášení k heslům, jenom klíče SSH. Tím, že vytvoříte virtuální počítač Azure Linux s klíči SSH, můžete přispět k zabezpečení nasazení virtuálních počítačů a uložit obvyklý krok konfigurace po nasazení, který zakazuje hesla v souboru `sshd_config`.

Pokud nechcete používat klíče SSH, můžete pro virtuální počítač se systémem Linux nastavit ověřování pomocí hesla. Pokud váš virtuální počítač není přístupný z Internetu, může stačit použití hesla. Pořád ale budete muset spravovat hesla pro každý virtuální počítač Linux a udržovat dobré zásady a postupy pro hesla, jako je například minimální délka hesla a pravidelné aktualizace. Použití klíčů SSH snižuje složitost správy individuálních přihlašovacích údajů napříč několika virtuálními počítači.

## <a name="generate-keys-with-ssh-keygen"></a>Generování klíčů pomocí ssh-keygen

Chcete-li vytvořit klíče, preferovaný příkaz je `ssh-keygen`, který je k dispozici u nástrojů OpenSSH v Azure Cloud Shell, hostiteli macOS nebo Linux, subsystému [Windows pro Linux](https://docs.microsoft.com/windows/wsl/about)a další nástroje. `ssh-keygen` požádá o řadu otázek a pak zapíše privátní klíč a shodný veřejný klíč. 

Klíče SSH jsou ve výchozím nastavení v adresáři `~/.ssh`.  Pokud adresář `~/.ssh` nemáte, vytvoří ho za vás příkaz `ssh-keygen` se správnými oprávněními.

### <a name="basic-example"></a>Základní příklad

Následující `ssh-keygen` příkaz vygeneruje ve výchozím nastavení v adresáři `~/.ssh` soubory veřejného a privátního klíče SSH RSA standardně 2048. Pokud pár klíčů SSH existuje v aktuálním umístění, tyto soubory se přepíší.

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

`-N mypassphrase` = další heslo, které se používá pro přístup k souboru privátního klíče. 

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

Název páru klíčů pro tento článek. Má dvojici klíčů s názvem `id_rsa` výchozí hodnota; Některé nástroje mohou očekávat `id_rsa` název souboru privátního klíče, takže jeden z nich je dobrý nápad. Výchozím umístěním pro páry klíčů SSH a konfigurační soubor SSH je adresář `~/.ssh/`. Pokud nezadáte úplnou cestu, `ssh-keygen` vytvoří klíče v aktuálním pracovním adresáři, nikoli ve výchozím adresáři `~/.ssh`.

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

Pokud k vytvoření virtuálního počítače použijete rozhraní příkazového [řádku Azure](/cli/azure) , můžete volitelně vygenerovat soubory veřejného a privátního klíče SSH spuštěním příkazu [AZ VM create](/cli/azure/vm) s možností `--generate-ssh-keys`. Klíče jsou uloženy v adresáři ~/.ssh. Všimněte si, že tato možnost příkazu nepřepisuje klíče, pokud již existují v tomto umístění.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Při nasazování virtuálního počítače zadat veřejný klíč SSH

Pokud chcete vytvořit virtuální počítač se systémem Linux, který používá klíče SSH k ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí Azure Portal, CLI, Správce prostředků šablon nebo jiných metod. Při použití portálu zadáte vlastní veřejný klíč. Pokud k vytvoření virtuálního počítače s existujícím veřejným klíčem použijete rozhraní příkazového [řádku Azure](/cli/azure) , zadejte hodnotu nebo umístění tohoto veřejného klíče spuštěním příkazu [AZ VM create](/cli/azure/vm) s možností `--ssh-key-value`. 

Pokud nejste obeznámeni s formátem veřejného klíče SSH, můžete svůj veřejný klíč zobrazit spuštěním `cat` následujícím způsobem a nahrazením `~/.ssh/id_rsa.pub` vlastním umístěním souboru veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Výstup je podobný následujícímu (zde redigováné):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Pokud zkopírujete a vložíte obsah souboru veřejného klíče do šablony Azure Portal nebo Správce prostředků, ujistěte se, že nekopírujete žádné další prázdné znaky ani nezavedete další zalomení řádků. Pokud například používáte macOS, můžete soubor veřejného klíče (ve výchozím nastavení `~/.ssh/id_rsa.pub`) přesměrovat tak, aby **pbcopy** obsah zkopíroval (existují i další programy pro Linux, které mají stejnou věc, například `xclip`).

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

Abyste se vyhnuli psaní klíčového hesla k souboru privátního klíče se všemi přihlášeními přes protokol SSH, můžete použít `ssh-agent` k ukládání hesla k souboru privátního klíče do mezipaměti. Pokud používáte Mac, macOS řetězec klíčů bezpečně ukládá heslo k privátnímu klíči při vyvolání `ssh-agent`.

Ověřte a použijte `ssh-agent` a `ssh-add`, abyste informovali systém SSH o souborech klíčů, abyste nemuseli používat heslo interaktivně.

```bash
eval "$(ssh-agent -s)"
```

Potom přidejte privátní klíč do `ssh-agent` pomocí příkazu `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Heslo privátního klíče je nyní uloženo v `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Ke zkopírování klíče do existujícího virtuálního počítače použijte SSH-Copy-ID.
Pokud jste již vytvořili virtuální počítač, můžete nový veřejný klíč SSH nainstalovat do virtuálního počítače se systémem Linux pomocí příkazu podobného následujícímu:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Vytvoření a nakonfigurování konfiguračního souboru SSH

Můžete vytvořit a nakonfigurovat konfigurační soubor SSH (`~/.ssh/config`), chcete-li urychlit přihlášení a optimalizovat chování klienta SSH. 

Následující příklad ukazuje jednoduchou konfiguraci, kterou můžete použít k rychlému přihlášení jako uživatel k určitému virtuálnímu počítači pomocí výchozího privátního klíče SSH. 

### <a name="create-the-file"></a>Vytvoření souboru

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Úpravou souboru přidejte novou konfiguraci SSH.

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Příklad konfigurace

Přidejte nastavení konfigurace odpovídající vašemu hostitelskému VIRTUÁLNÍmu počítači.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Můžete přidat konfigurace pro další hostitele, aby každý mohl použít svůj vlastní vyhrazený pár klíčů. Pokročilejší možnosti konfigurace najdete v tématu [konfigurační soubor SSH](https://www.ssh.com/ssh/config/) .

Teď, když máte pár klíčů SSH a nakonfigurovaný konfigurační soubor SSH, se můžete rychle a bezpečně přihlašovat ke svému VIRTUÁLNÍmu počítači Linux. Když spustíte následující příkaz, SSH vyhledá a načte do konfiguračního souboru SSH všechna nastavení z `Host myvm`ho bloku.

```bash
ssh myvm
```

Při prvním přihlášení k serveru pomocí klíče SSH vás příkaz vyzve k zadání hesla pro tento soubor klíče.

## <a name="next-steps"></a>Další kroky

Dalším krokem je vytvoření virtuálního počítače Azure s Linuxem pomocí nového veřejného klíče SSH. Virtuální počítače Azure vytvořené pomocí veřejného klíče SSH jako přihlašování jsou lépe zabezpečené než virtuální počítače vytvořené pomocí výchozí metody přihlašování, hesla.

* [Vytvoření virtuálního počítače se systémem Linux pomocí Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače se systémem Linux pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače se systémem Linux pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
