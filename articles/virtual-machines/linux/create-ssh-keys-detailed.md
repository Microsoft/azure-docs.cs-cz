---
title: Podrobné kroky - SSH dvojice klíčů pro virtuální počítače Azure Linux | Microsoft Docs
description: Přečtěte si podrobné pokyny k vytváření a správě SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure.
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
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "31600338"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Podrobný postup: vytvoření a Správa klíčů SSH pro ověřování, aby virtuální počítač s Linuxem v Azure 
Klíče dvojice zabezpečené shell (SSH) můžete vytvořit virtuální počítač s Linuxem v Azure, který ve výchozím nastavení používá klíče SSH k ověření, což eliminuje potřebu hesla k přihlášení. Virtuální počítače vytvořené pomocí portálu Azure, rozhraní příkazového řádku Azure Resource Manager šablony nebo jiných nástrojů může obsahovat veřejný klíč SSH jako součást nasazení, která nastaví ověření pomocí klíče SSH pro připojení SSH. 

Tento článek obsahuje podrobné informace a postupy pro vytváření a správě dvojici soubor veřejného a privátního klíče SSH RSA pro připojení klientů SSH. Pokud chcete rychle příkazy, najdete v části [vytvoření páru klíčů veřejný soukromý SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači se systémem Windows, najdete v části [klíče použití SSH se systémem Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Přístupové heslo soukromého klíče
Privátní klíč SSH musí mít velmi zabezpečené přístupové heslo, aby se předešlo ho. Toto heslo se používá pouze pro přístup k souboru privátního klíče SSH a *není* heslo k uživatelskému účtu. Když ke svému klíči SSH přidáte heslo, zašifruje privátní klíč pomocí standardu AES 128-bit, tak, aby privátní klíč je nelze bez heslo ho dešifrovat. Pokud útočník stole privátní klíč a klíči neměl přístupové heslo, bude moct používat k přihlášení na všechny servery, které mají odpovídající veřejný klíč této privátní klíč. Pokud je privátní klíč chráněný modulem přístupové heslo, nelze použít, útočník, což znamená další úroveň zabezpečení pro vaši infrastrukturu v Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Použití a výhody klíčů SSH

Když vytvoříte virtuální počítač Azure zadáním veřejný klíč, Azure zkopíruje veřejný klíč (v `.pub` formátu) k `~/.ssh/authorized_keys` složky na virtuálním počítači. Klíče SSH v `~/.ssh/authorized_keys` vybízí klienta, aby pro přihlašovací připojení SSH použil odpovídající privátní klíč. V systému Linux virtuálního počítače Azure používající klíče SSH pro ověřování Azure nakonfiguruje server SSHD nepovolíte přihlášení heslo, pouze klíče SSH. Proto tak, že vytvoříte virtuální počítač Azure Linux s klíči SSH, můžete pomoct zabezpečit nasazení virtuálního počítače a uložit sami typické konfiguraci po nasazení krok zakázání hesla v `sshd_config` souboru.

Pokud nechcete používat klíče SSH, můžete nastavit virtuálním počítačům s Linuxem na použití ověřování hesla. Pokud není virtuální počítač přístup k Internetu, může být dostatečná pomocí hesla. Však stále potřebujete spravovat hesla pro každý virtuální počítač s Linuxem a Udržovat zásady hesel v pořádku a postupy, jako je minimální délka hesla a pravidelné aktualizace. Používání klíčů SSH snižuje složitost správy jednotlivých přihlašovacích údajů napříč více virtuálními počítači.

## <a name="generate-keys-with-ssh-keygen"></a>Generovat klíče ssh-keygen

K vytvoření klíčů, je upřednostňovanou příkaz `ssh-keygen`, která je k dispozici s OpenSSH nástrojů v prostředí cloudu Azure, systému macOS nebo Linux hostitele, [subsystému Windows pro Linux](https://docs.microsoft.com/windows/wsl/about)a další nástroje. `ssh-keygen` zeptá na několik otázek a pak zapíše privátní klíč a odpovídající veřejný klíč. 

Klíče SSH jsou ve výchozím nastavení v adresáři `~/.ssh`.  Pokud adresář `~/.ssh` nemáte, vytvoří ho za vás příkaz `ssh-keygen` se správnými oprávněními.

### <a name="basic-example"></a>Základní příklad

Následující `ssh-keygen` příkaz generuje 2048 bitů SSH RSA soubory veřejného a privátního klíče ve výchozím nastavení `~/.ssh` adresáře. Pokud dvojici klíčů SSH existuje v aktuální umístění, tyto soubory jsou přepsány.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Podrobný příklad
Následující příklad ukazuje další příkaz Možnosti, jak vytvořit pár klíče SSH RSA. Pokud dvojici klíčů SSH existuje v aktuální umístění, tyto soubory jsou přepsány. 

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

`-t rsa` = Typ klíče pro vytvoření v tomto případě ve formátu RSA

`-b 4096` = počet bitů v klíči, v takovém případě 4096.

`-C "azureuser@myserver"`= komentář připojený na konec souboru veřejného klíče pro snadnější identifikaci. Obvykle se používá e-mailovou adresu jako komentář, ale použít cokoli, co nejlépe pro vaši infrastrukturu.

`-f ~/.ssh/mykeys/myprivatekey` = Název souboru soubor privátního klíče, pokud se rozhodnete použít výchozí název. Odpovídající soubor veřejného klíče s příponou `.pub` se generuje ve stejném adresáři. Tento adresář musí existovat.

`-N mypassphrase` = Další heslo pro přístup k souboru privátního klíče. 

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

Název páru klíčů pro tento článek. Pár klíčů s názvem `id_rsa` je výchozí; by se dalo očekávat některé nástroje `id_rsa` soubor privátního klíče jmenovat, takže je vhodné. Výchozím umístěním pro páry klíčů SSH a konfigurační soubor SSH je adresář `~/.ssh/`. Pokud nezadáte úplnou cestu, `ssh-keygen` vytvoří klíče v aktuálním pracovním adresáři, nikoli ve výchozím adresáři `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Seznam `~/.ssh` adresáře

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Přístupové heslo klíče

`Enter passphrase (empty for no passphrase):`

Je *důrazně* doporučujeme přidat přístupové heslo pro svůj privátní klíč. Bez přístupové heslo k ochraně souboru klíče každý, kdo má soubor můžete ji použít k přihlášení k žádnému serveru, který má odpovídající veřejný klíč. Přidání přístupového hesla o nabízí další ochranu v případě, že někdo bude schopen získat přístup k svůj soubor privátního klíče, poskytnete čas změnit si klíče.

## <a name="generate-keys-automatically-during-deployment"></a>Generovat klíče automaticky během nasazení

Pokud používáte [Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače, může volitelně generovat SSH soubory veřejného a privátního klíče spuštěním [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) s `--generate-ssh-keys` možnost. Klíče jsou uložené v adresáři ~/.ssh. Poznámka: Tato možnost příkaz Pokud již existují v umístění v nepřepisuje klíče.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače

Pokud chcete vytvořit virtuální počítač Linux, který používá klíče SSH pro ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí portálu Azure, rozhraní příkazového řádku, šablony Resource Manageru nebo jiné metody. Pokud používáte portál, je třeba zadat veřejný klíč sám o sobě. Pokud používáte [Azure CLI 2.0](/cli/azure) Pokud chcete vytvořit virtuální počítač s existující veřejný klíč, zadejte hodnotu nebo umístění tohoto veřejného klíče spuštěním [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) s `--ssh-key-value` možnost. 

Pokud si nejste obeznámeni s formátem veřejný klíč SSH, zobrazí se svým veřejným klíčem spuštěním `cat` následujícím způsobem, nahraďte `~/.ssh/id_rsa.pub` s vlastní umístění soubor veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Výstup se podobá následující (v tomto poli redigovaný):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Pokud zkopírujte a vložte obsah veřejného klíče souboru do portálu Azure nebo šablony Resource Manageru, zajistěte, aby si zkopírujte všechny další prázdný znak nebo zavést další konce řádků. Například pokud použijete systému macOS, můžete předat soubor veřejného klíče (ve výchozím nastavení, `~/.ssh/id_rsa.pub`) k **pbcopy** zkopírovat obsah (existují další programy Linux, které provedou totéž, jako například **xclip**).

Pokud byste radši chtěli použít veřejný klíč, který je ve formátu, Víceřádkový, můžete vygenerovat formátovaný klíčem RFC4716 v kontejneru pem z veřejný klíč, který jste předtím vytvořili.

Vytvoření klíče ve formátu RFC4716 z existujícího veřejného klíče SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH pro virtuální počítač s klientem SSH
S veřejný klíč nasazené na vašem virtuálním počítači Azure a privátní klíč v lokálním systému, SSH k virtuálnímu počítači pomocí IP adresu nebo název DNS vašeho virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu s uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresa):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali přístupové heslo, zadejte jej na vyzvání během procesu přihlašování. (Server se přidá do vaší složky `~/.ssh/known_hosts` a nové připojení se nebude vyžadovat, dokud se nezmění veřejný klíč na virtuálním počítači Azure nebo se neodebere název serveru ze složky `~/.ssh/known_hosts`.)

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Použití ssh-agent k uložení privátního klíče heslo

Chcete-li nemuseli zadávat heslo soubor privátního klíče každém přihlašování přes SSH, můžete použít `ssh-agent` pro ukládání do mezipaměti heslo soubor privátního klíče. Pokud používáte algoritmu Mac, systému macOS řetězce klíčů bezpečně uloží privátní klíče heslo při vyvolání `ssh-agent`.

Ověřte a použít `ssh-agent` a `ssh-add` k informování o soubory klíčů SSH systému, takže nemusíte používat heslo interaktivně.

```bash
eval "$(ssh-agent -s)"
```

Potom přidejte privátní klíč do `ssh-agent` pomocí příkazu `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Heslo privátního klíče jsou teď uložená v `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Pomocí ssh kopie id, zkopírujte klíč do stávajícího virtuálního počítače
Pokud jste již vytvořili virtuální počítač, můžete nainstalovat nový veřejný klíč SSH k virtuálním počítačům s Linuxem pomocí příkazu, který je podobný následujícímu:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Vytvoření a nakonfigurování konfiguračního souboru SSH

Můžete vytvořit a nakonfigurovat konfigurační soubor SSH (`~/.ssh/config`) pro urychlení přihlašovací údaje a k optimalizaci vaše chování klienta SSH. 

Následující příklad ukazuje jednoduchý konfigurace, aby vám pomůže rychle se přihlaste jako uživatel pro konkrétní virtuální počítač pomocí výchozí privátní klíč SSH. 

### <a name="create-the-file"></a>Vytvoření souboru

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Upravením souboru přidejte novou konfiguraci SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Příklad konfigurace

Přidejte nastavení konfigurace, které jsou vhodné pro váš hostitel virtuálních počítačů.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Můžete přidat konfigurace pro další hostitele můžete povolit každou používat svůj vlastní vyhrazený pár klíčů. V tématu [konfigurační soubor SSH](https://www.ssh.com/ssh/config/) pro pokročilejší možnosti konfigurace.

Teď máte pár klíčů SSH a nakonfigurovaný konfigurační soubor SSH a můžete se rychle a bezpečně přihlašovat ke svým virtuálním počítačům s Linuxem. Když spustíte následující příkaz, SSH vyhledá a načte všechna nastavení z `Host myvm` blokovat v konfiguračním souboru SSH.

```bash
ssh myvm
```

Při prvním přihlášení k serveru pomocí klíče SSH vás příkaz požádá můžete o přístupové heslo pro tento soubor klíče.

## <a name="next-steps"></a>Další postup

Dalším krokem je vytvoření virtuálního počítače Azure s Linuxem pomocí nového veřejného klíče SSH. Vytvářené virtuální počítače Azure, pro které se v přihlašovacích údajích používá veřejný klíč SSH, jsou lépe zabezpečené než ty, které jsou vytvořené pomocí výchozí metody přihlašování s použitím hesel.

* [Vytvořit virtuální počítač s Linuxem pomocí portálu Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
