---
title: Podrobné kroky k vytvoření dvojice klíčů SSH
description: Naučte se podrobné kroky k vytvoření a správě dvojice veřejných a soukromých klíčů SSH pro virtuální počítače s Linuxem v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969539"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Podrobné kroky: Vytvoření a správa klíčů SSH pro ověřování na virtuálním počítači SIO v Azure 
Se zabezpečeným párem klíčů prostředí (SSH) můžete v Azure vytvořit virtuální počítač s Linuxem, který výchozí použije klíče SSH pro ověřování, čímž eliminuje potřebu hesla pro přihlášení. Virtuální počítače vytvořené pomocí portálu Azure, Azure CLI, šablony Správce prostředků nebo jiných nástrojů mohou zahrnovat váš veřejný klíč SSH jako součást nasazení, které nastavuje ověřování klíčů SSH pro připojení SSH. 

Tento článek obsahuje podrobné pozadí a kroky k vytvoření a správě dvojice souborů s veřejným a soukromým klíčem SSH RSA pro připojení klientů SSH. Pokud chcete rychlé příkazy, přečtěte [si, jak vytvořit dvojici s klíčem s veřejným a soukromým klíčem SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

Další způsoby generování a používání klíčů SSH v počítači se systémem Windows najdete v tématu [Jak používat klíče SSH s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Heslo privátního klíče
Soukromý klíč SSH by měl mít velmi bezpečné přístupové heslo k jeho zabezpečení. Toto přístupové heslo je pouze pro přístup k soukromému souboru klíče SSH a *není* heslem uživatelského účtu. Když klíči SSH přidáte přístupové heslo, použije se k zašifrování klíče 128bitový standard AES. Bez dešifrovacího hesla je privátní klíč nepoužitelný. Pokud útočník ukradl váš soukromý klíč a tento klíč neměl přístupové heslo, mohl by tento soukromý klíč použít k přihlášení k serverům, které mají odpovídající veřejný klíč. Pokud je soukromý klíč chráněný přístupovou frází, nemůže ho útočník použít a poskytne další vrstvu zabezpečení pro vaši infrastrukturu v Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Použití a výhody klíčů SSH

Když vytvoříte virtuální počítač Azure zadáním veřejného klíče, Azure `.pub` zkopíruje veřejný `~/.ssh/authorized_keys` klíč (ve formátu) do složky na virtuálním počítači. SSH klíče `~/.ssh/authorized_keys` v slouží k výzvu klienta, aby odpovídaly odpovídající soukromý klíč na připojení SSH. V virtuálním počítači Azure Linux, který používá klíče SSH pro ověřování, Azure konfiguruje server SSHD tak, aby neumožňoval přihlášení hesla, jenom klíče SSH. Proto vytvořením virtuálního počítače Azure Linux s klíči SSH můžete zabezpečit nasazení virtuálního počítače a ušetřit si typický `sshd_config` krok konfigurace po nasazení zakázání hesel v souboru.

Pokud nechcete používat klíče SSH, můžete nastavit virtuální počítač s Linuxem tak, aby používal ověřování heslem. Pokud váš virtuální počítač není vystaven internetu, použití hesel může být dostačující. Stále však potřebujete spravovat hesla pro každý virtuální počítač s Linuxem a udržovat zásady a postupy pro vpořádku hesla, jako je minimální délka hesla a pravidelné aktualizace. Použití klíčů SSH snižuje složitost správy jednotlivých přihlašovacích údajů napříč více virtuálními počítači.

## <a name="generate-keys-with-ssh-keygen"></a>Generovat klíče s ssh-keygen

Chcete-li vytvořit klíče, `ssh-keygen`upřednostňovaný příkaz je , který je k dispozici s nástroji OpenSSH v Azure Cloud Shell, hostitele macOS nebo Linux, [Podsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/about)a další nástroje. `ssh-keygen`položí řadu otázek a pak zapíše soukromý klíč a odpovídající veřejný klíč. 

Klíče SSH jsou ve výchozím nastavení v adresáři `~/.ssh`.  Pokud adresář `~/.ssh` nemáte, vytvoří ho za vás příkaz `ssh-keygen` se správnými oprávněními.

### <a name="basic-example"></a>Základní příklad

Následující `ssh-keygen` příkaz generuje 2048bitové soubory s veřejným a soukromým klíčem `~/.ssh` SSH RSA ve výchozím nastavení v adresáři. Pokud v aktuálním umístění existuje dvojice klíčů SSH, jsou tyto soubory přepsány.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Podrobný příklad
Následující příklad ukazuje další možnosti příkazu k vytvoření dvojice klíčů SSH RSA. Pokud v aktuálním umístění existuje dvojice klíčů SSH, jsou tyto soubory přepsány. 

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

`-m PEM`= formátovat klíč jako PEM

`-t rsa`= typ klíče k vytvoření, v tomto případě ve formátu RSA

`-b 4096`= počet bitů v klíči, v tomto případě 4096

`-C "azureuser@myserver"`= komentář připojený na konec souboru veřejného klíče pro snadnější identifikaci. Obvykle se jako komentář používá e-mailová adresa, ale použijte vše, co nejlépe vyhovuje vaší infrastruktuře.

`-f ~/.ssh/mykeys/myprivatekey`= název souboru soukromého klíče, pokud se rozhodnete nepoužívat výchozí název. Odpovídající soubor veřejného klíče `.pub` připojený s je generován ve stejném adresáři. Adresář musí existovat.

`-N mypassphrase`= další přístupové heslo používané pro přístup k souboru soukromého klíče. 

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

#### <a name="saved-key-files"></a>Uložené klíčové soubory

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Název páru klíčů pro tento článek. S dvojicí `id_rsa` klíčů s názvem je výchozí; některé nástroje mohou `id_rsa` očekávat název souboru soukromého klíče, takže mít jeden je dobrý nápad. Výchozím umístěním pro páry klíčů SSH a konfigurační soubor SSH je adresář `~/.ssh/`. Pokud nezadáte úplnou cestu, `ssh-keygen` vytvoří klíče v aktuálním pracovním adresáři, nikoli ve výchozím adresáři `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Seznam adresářů `~/.ssh`

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Přístupové heslo

`Enter passphrase (empty for no passphrase):`

*Důrazně* doporučujeme přidat přístupové heslo do soukromého klíče. Bez přístupového hesla k ochraně souboru klíče, kdokoli se souborem můžete použít k přihlášení k libovolnému serveru, který má odpovídající veřejný klíč. Přidání přístupového hesla nabízí větší ochranu v případě, že někdo může získat přístup k vašemu soukromému souboru klíče, což vám dává čas na změnu klíčů.

## <a name="generate-keys-automatically-during-deployment"></a>Automatické generování klíčů během nasazení

Pokud k vytvoření virtuálního počítače použijete [azure cli,](/cli/azure) můžete volitelně generovat soubory s veřejným a soukromým `--generate-ssh-keys` klíčem SSH spuštěním [příkazu az vm create](/cli/azure/vm) s možností. Klíče jsou uloženy v adresáři ~/.ssh. Všimněte si, že tato možnost příkazu nepřepíše klíče, pokud již existují v tomto umístění.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Poskytnutí veřejného klíče SSH při nasazování virtuálního mísy

Chcete-li vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí portálu Azure, CLI, šablon Resource Manager nebo jiných metod. Při použití portálu zadáte samotný veřejný klíč. Pokud použijete [Azure CLI](/cli/azure) k vytvoření virtuálního počítače s existujícím veřejným klíčem, zadejte hodnotu nebo umístění `--ssh-key-value` tohoto veřejného klíče spuštěním [příkazu az vm create](/cli/azure/vm) s možností. 

Pokud nejste obeznámeni s formátem veřejného klíče SSH, můžete `cat` zobrazit svůj veřejný `~/.ssh/id_rsa.pub` klíč následujícím spuštěním a nahrazením vlastním umístěním souboru veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Výstup je podobný následujícímu (zde redigováno):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Pokud zkopírujete a vložíte obsah souboru veřejného klíče do portálu Azure nebo šablony Správce prostředků, ujistěte se, že nekopírujete žádné další prázdné znaky nebo nezavádíte další konce řádků. Pokud například používáte macOS, můžete soubor veřejného klíče `~/.ssh/id_rsa.pub`(ve výchozím nastavení) převést do **pbcopy,** aby zkopíroval obsah (existují i jiné linuxové programy, které dělají totéž, například `xclip`).

Pokud dáváte přednost použití veřejného klíče, který je ve víceřádkovém formátu, můžete vygenerovat formátovaný klíč RFC4716 v kontejneru Pem z dříve vytvořeného veřejného klíče.

Vytvoření klíče ve formátu RFC4716 z existujícího veřejného klíče SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH do vašeho Virtuálního počítače s klientem SSH
S veřejným klíčem nasazeným na vašem virtuálním počítači Azure a privátním klíčem v místním systému SSH do virtuálního počítače pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu uživatelským jménem správce a plně kvalifikovaným názvem domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření dvojice klíčů zadali přístupové heslo, zadejte je po zobrazení výzvy během procesu přihlášení. (Server se přidá do vaší složky `~/.ssh/known_hosts` a nové připojení se nebude vyžadovat, dokud se nezmění veřejný klíč na virtuálním počítači Azure nebo se neodebere název serveru ze složky `~/.ssh/known_hosts`.)

Pokud virtuální ho správu používá zásady přístupu za chvíli, musíte požádat o přístup, než se budete moct připojit k virtuálnímu virtuálnímu zařízení. Další informace o zásadách just-in-time najdete v tématu [Správa přístupu k virtuálnímu počítači pomocí zásad just in time](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Uložení přístupového hesla soukromého klíče pomocí ssh-agenta

Chcete-li se vyhnout zadávání přístupové heslo k souboru soukromého klíče s každým přihlášením SSH, můžete použít `ssh-agent` k ukládání hesla do mezipaměti souboru soukromého klíče. Pokud používáte Mac, klíčenka macOS bezpečně ukládá soukromé přístupové `ssh-agent`heslo při vyvolání .

Ověřte `ssh-agent` `ssh-add` a používejte a informujte systém SSH o klíčových souborech, abyste nemuseli používat přístupové heslo interaktivně.

```bash
eval "$(ssh-agent -s)"
```

Potom přidejte privátní klíč do `ssh-agent` pomocí příkazu `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Přístupové heslo soukromého klíče `ssh-agent`je nyní uloženo v aplikaci .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Kopírování klíče do existujícího virtuálního virtuálního provozu pomocí ssh-copy-id
Pokud jste už vytvořili virtuální počítač, můžete nainstalovat nový veřejný klíč SSH do virtuálního počítače s Linuxem s příkazem podobným následujícímu:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Vytvoření a nakonfigurování konfiguračního souboru SSH

Můžete vytvořit a nakonfigurovat konfigurační soubor SSH (`~/.ssh/config`) pro urychlení přihlášení a optimalizaci chování klienta SSH. 

Následující příklad ukazuje jednoduchou konfiguraci, kterou můžete použít k rychlému přihlášení jako uživatel k určitému virtuálnímu počítače pomocí výchozího soukromého klíče SSH. 

### <a name="create-the-file"></a>Vytvoření souboru

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Úprava souboru pro přidání nové konfigurace SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Příklad konfigurace

Přidejte nastavení konfigurace vhodné pro váš hostitelský virtuální počítač.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Můžete přidat konfigurace pro další hostitele, aby každý mohl používat vlastní vyhrazený pár klíčů. Další pokročilejší možnosti konfigurace naleznete [v tématu Konfigurační soubor SSH.](https://www.ssh.com/ssh/config/)

Nyní, když máte pár klíčů SSH a nakonfigurovaný konfigurační soubor SSH, můžete se rychle a bezpečně přihlásit k virtuálnímu počítači Linuxu. Při spuštění následujícího příkazu SSH vyhledá a `Host myvm` načte všechna nastavení z bloku v konfiguračním souboru SSH.

```bash
ssh myvm
```

Při prvním přihlášení k serveru pomocí klíče SSH se příkaz zobrazí v příkazu pro přístupové heslo pro tento soubor klíče.

## <a name="next-steps"></a>Další kroky

Dalším krokem je vytvoření virtuálního počítače Azure s Linuxem pomocí nového veřejného klíče SSH. Virtuální počítače Azure, které jsou vytvořené s veřejným klíčem SSH jako přihlášení jsou lépe zabezpečené než virtuální počítače vytvořené pomocí výchozí metody přihlášení, hesla.

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
