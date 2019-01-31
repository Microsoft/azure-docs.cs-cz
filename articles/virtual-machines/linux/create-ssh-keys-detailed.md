---
title: Podrobný postup - SSH páru klíčů pro Azure s Linuxem | Dokumentace Microsoftu
description: Přečtěte si podrobné pokyny k vytváření a správě veřejných a privátních pár klíčů SSH pro virtuální počítače s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 7167e31261ce029a6a0a6fe070232d1086942162
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297697"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Podrobný postup: Vytvoření a Správa klíčů SSH pro ověřování, aby virtuální počítač s Linuxem v Azure 
Pomocí páru klíčů (SSH secure shell) můžete vytvořit virtuální počítač s Linuxem v Azure, který ve výchozím nastavení používá klíče SSH pro ověřování, takže odpadá potřeba používat k přihlášení hesla. Virtuální počítače vytvořené pomocí webu Azure portal, rozhraní příkazového řádku Azure Resource Manageru šablony nebo jiné nástroje můžou zahrnovat veřejný klíč SSH jako součást svého nasazení, která nastaví ověřování pomocí klíče SSH pro připojení SSH. 

Tento článek obsahuje podrobné informace a postupy pro vytváření a správě pár souborů veřejného a privátního klíče SSH RSA pro připojení klienta SSH. Pokud chcete rychlý příkazy, přečtěte si téma [vytvoření pár veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači Windows, naleznete v tématu [klíče, jak použít SSH s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Heslo privátního klíče
Privátní klíč SSH by měl mít velmi zabezpečené heslo, který ho chrání. Toto heslo se používá pouze pro přístup k souboru privátního klíče SSH a *není* heslo k uživatelskému účtu. Pokud ke svému klíči SSH přidáte heslo, zašifruje klíče 128bitový standard AES, tak, aby privátní klíč nebylo možné bez přístupového hesla dešifrovat. Pokud útočník odcizit privátní klíč a tento klíč neobsahoval přístupové heslo, bude moct použít pro přihlášení k libovolné servery, které mají odpovídající veřejný klíč. Pokud privátní klíč chráněný heslem, nelze použít, útočník, což znamená další úroveň zabezpečení pro vaši infrastrukturu v Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Použití a výhody klíčů SSH

Při vytváření virtuálního počítače Azure tak, že zadáte veřejný klíč, zkopíruje Azure veřejný klíč (v `.pub` formátu) k `~/.ssh/authorized_keys` složky na virtuálním počítači. Klíče SSH v `~/.ssh/authorized_keys` slouží vybízí klienta, aby odpovídající privátní klíč v připojení SSH. V systému Linux virtuálního počítače Azure, který používá klíče SSH pro ověřování Azure nakonfiguruje server SSHD tak, že nepovolíte heslo přihlášení, pouze klíče SSH. Proto tak, že vytvoříte Linuxový virtuální počítač Azure s klíči SSH, vám může pomoct zabezpečit nasazením virtuálního počítače a ušetříte si obvyklý konfigurační po nasazení krok zakázání hesel v `sshd_config` souboru.

Pokud nechcete používat klíče SSH, můžete nastavit virtuální počítač s Linuxem pro použití ověřování pomocí hesla. Pokud váš virtuální počítač není přístupný z Internetu, může být dostatečné pomocí hesla. Ale pořád potřebujete spravovat hesla pro každý virtuální počítač s Linuxem a Udržovat zásady pro hesla v pořádku a postupů, jako jsou minimální délku hesla a pravidelné aktualizace. Použití klíčů SSH pro snížení složitosti správy jednotlivých přihlašovacích údajů napříč několika virtuálními počítači.

## <a name="generate-keys-with-ssh-keygen"></a>Vygenerování klíče ssh-keygen

K vytvoření klíčů, je upřednostňovanou příkaz `ssh-keygen`, která je k dispozici OpenSSH nástroje v Azure Cloud Shell, macOS nebo Linux hostitele, [subsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/about)a další nástroje. `ssh-keygen` zeptá na několik otázek a pak zapíše privátní klíč a odpovídající veřejný klíč. 

Klíče SSH jsou ve výchozím nastavení v adresáři `~/.ssh`.  Pokud adresář `~/.ssh` nemáte, vytvoří ho za vás příkaz `ssh-keygen` se správnými oprávněními.

### <a name="basic-example"></a>Základní příklad

Následující `ssh-keygen` příkaz generuje 2048bitový SSH RSA soubory veřejného a privátního klíče ve výchozím nastavení `~/.ssh` adresáře. Pokud existuje pár klíčů SSH v aktuálním umístění, tyto soubory jsou přepsány.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Podrobný příklad
Následující příklad ukazuje možnosti další příkaz k vytvoření páru klíčů SSH RSA. Pokud existuje pár klíčů SSH v aktuálním umístění, tyto soubory jsou přepsány. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Vysvětlení příkazu**

`ssh-keygen`= program použitý k vytvoření klíčů

`-t rsa` = Typ vytvářeného klíče, v tomto případě ve formátu RSA

`-b 4096` = počet bitů v klíči, v tomto případě 4096

`-C "azureuser@myserver"`= komentář připojený na konec souboru veřejného klíče pro snadnější identifikaci. Obvykle je jako komentář používá e-mailovou adresu, ale použít cokoli, co pro vaši infrastrukturu.

`-f ~/.ssh/mykeys/myprivatekey` = Název souboru privátního klíče, pokud se rozhodnete nepoužívat výchozí název. S příponou souboru odpovídající veřejného klíče `.pub` je generována ve stejném adresáři. Tento adresář musí existovat.

`-N mypassphrase` = dodatečné přístupové heslo pro přístup k souboru privátního klíče. 

### <a name="example-of-ssh-keygen"></a>Příklad ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>Uložené soubory klíčů

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Název páru klíčů pro tento článek. Pár klíčů s názvem `id_rsa` je výchozí hodnota; může očekávat některé nástroje `id_rsa` název souboru s privátním klíčem, takže je vhodné. Výchozím umístěním pro páry klíčů SSH a konfigurační soubor SSH je adresář `~/.ssh/`. Pokud nezadáte úplnou cestu, `ssh-keygen` vytvoří klíče v aktuálním pracovním adresáři, nikoli ve výchozím adresáři `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Seznam `~/.ssh` adresáře

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Heslo klíče

`Enter passphrase (empty for no passphrase):`

Je *důrazně* doporučujeme přidat si přístupové heslo k privátnímu klíči. Bez přístupového hesla k ochraně souboru s klíčem každý, kdo má soubor můžete ho použít pro přihlášení k jakémukoli serveru s odpovídajícím veřejným klíčem. Přidání přístupového hesla nabízí vyšší ochranu v případě, že někdo bude schopen získat přístup k vašemu souboru privátního klíče, poskytne vám čas změnit si klíče.

## <a name="generate-keys-automatically-during-deployment"></a>Během nasazení automaticky vygenerovat klíče

Pokud používáte [rozhraní příkazového řádku Azure](/cli/azure) k vytvoření virtuálního počítače, může volitelně Generovat soubory veřejného a privátního klíče SSH spuštěním [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazů `--generate-ssh-keys` možnost. Klíče jsou uložené v adresáři ~/.ssh. Všimněte si, že tento parametr nepřepisuje klíče Pokud již existují v dané oblasti.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače

Pokud chcete vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte veřejný klíč SSH při vytváření virtuálního počítače pomocí webu Azure portal, rozhraní příkazového řádku, šablon Resource Manageru nebo jiné metody. Během používání portálu, zadejte veřejný klíč samotný. Pokud používáte [rozhraní příkazového řádku Azure](/cli/azure) k vytvoření virtuálního počítače pomocí stávajícího veřejného klíče, zadejte hodnotu nebo umístění veřejného klíče spuštěním [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazů `--ssh-key-value` možnost. 

Pokud nejste obeznámeni s formátem veřejný klíč SSH, se zobrazí veřejný klíč spuštěním `cat` následujícím způsobem nahraďte `~/.ssh/id_rsa.pub` nahraďte vlastním umístěním souboru veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Výstup je podobný následujícímu (zde redigovaný):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Pokud zkopírujte a vložte obsah souboru veřejného klíče na webu Azure portal nebo šablony Resource Manageru, ujistěte se, že nemusíte kopírovat žádné prázdné znaky nebo zavést další zalomení řádků. Například pokud používáte macOS, můžete předat soubor veřejného klíče (ve výchozím nastavení, `~/.ssh/id_rsa.pub`) k **pbcopy** chcete zkopírovat obsah (existují další programy v systému Linux, které totéž, jako například `xclip`).

Pokud dáváte přednost použití veřejného klíče, který je ve víceřádkovém formátu, můžete generovat formátovaný klíč ve formátu RFC4716 v kontejneru pem z veřejného klíče, který jste předtím vytvořili.

Vytvoření klíče ve formátu RFC4716 z existujícího veřejného klíče SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH k virtuálnímu počítači s klientem SSH
Veřejný klíč nasazené na vašem virtuálním počítači Azure, a privátní klíč v místním systému, SSH k virtuálnímu počítači pomocí IP adresy nebo název DNS virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu se uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali přístupové heslo, zadejte heslo vyzvání během procesu přihlášení. (Server se přidá do vaší složky `~/.ssh/known_hosts` a nové připojení se nebude vyžadovat, dokud se nezmění veřejný klíč na virtuálním počítači Azure nebo se neodebere název serveru ze složky `~/.ssh/known_hosts`.)

Pokud virtuální počítač používá zásady přístupu v čase, budete muset požádat o přístup před připojením k virtuálnímu počítači. Další informace o zásadách just-in-time najdete v tématu [spravovat přístup k virtuálním počítačům podle potřeby pomocí v zásadách čas](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Použití ssh-agent k uložení vaše heslo privátního klíče

Abyste nemuseli zadávat heslo souboru s privátním klíčem každé přihlášení SSH, můžete použít `ssh-agent` na heslo souboru s privátním klíčem do mezipaměti. Pokud používáte počítač Mac, klíčenky macOS bezpečně ukládá heslo privátního klíče při vyvolání `ssh-agent`.

Ověřit a použít `ssh-agent` a `ssh-add` informovat systém SSH o souborech s klíči tak, že není potřeba použít přístupové heslo interaktivně.

```bash
eval "$(ssh-agent -s)"
```

Potom přidejte privátní klíč do `ssh-agent` pomocí příkazu `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Heslo privátního klíče jsou teď uložená v `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Použití ssh kopírování id zkopírovat klíč do existujícího virtuálního počítače
Pokud jste již vytvořili virtuální počítač, můžete nainstalovat nový veřejný klíč SSH k virtuálním počítačům s Linuxem pomocí příkazu, který je podobný následujícímu:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Vytvoření a nakonfigurování konfiguračního souboru SSH

Můžete vytvořit a nakonfigurovat konfigurační soubor SSH (`~/.ssh/config`) ke zrychlení protokolu se změnami a optimalizovat chování klienta SSH. 

Následující příklad ukazuje jednoduchou konfiguraci a že vám pomůže rychle se přihlásit jako uživatel konkrétnímu virtuálnímu počítači pomocí výchozí privátního klíče SSH. 

### <a name="create-the-file"></a>Vytvoření souboru

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Upravte soubor a přidejte novou konfiguraci SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Příklad konfigurace

Přidejte nastavení konfigurace, které jsou vhodné pro hostitele virtuálního počítače.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Můžete přidat konfigurace pro další hostitele, aby každý z nich používat svůj vlastní vyhrazený pár klíčů. Zobrazit [konfigurační soubor SSH](https://www.ssh.com/ssh/config/) pro pokročilejší možnosti konfigurace.

Teď, když máte pár klíčů SSH a nakonfigurovaný konfigurační soubor SSH, budete moct přihlásit k virtuálním počítačům s Linuxem, rychle a bezpečně. Při spuštění následujícího příkazu SSH vyhledá a načte všechna nastavení z `Host myvm` blokovat v konfigurační soubor SSH.

```bash
ssh myvm
```

Při prvním přihlášení k serveru pomocí klíče SSH vás příkaz požádá vás o přístupové heslo pro tento soubor klíče.

## <a name="next-steps"></a>Další postup

Dalším krokem je vytvoření virtuálního počítače Azure s Linuxem pomocí nového veřejného klíče SSH. Virtuální počítače Azure, které jsou vytvořeny pomocí veřejný klíč SSH jako přihlášení jsou lépe zabezpečené než virtuální počítače vytvořené pomocí výchozí přihlašovací metoda, hesla.

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
