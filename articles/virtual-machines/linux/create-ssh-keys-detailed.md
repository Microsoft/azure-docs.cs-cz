---
title: Podrobný postup vytvoření páru klíčů SSH pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Naučíte se další postup vytvoření páru veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure společně s konkrétními certifikáty pro různé případy použití.
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
ms.date: 6/28/2017
ms.author: danlep
ms.openlocfilehash: 20d36f5e377f2d5af588319cee2be1808571f905
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Podrobný postup vytvoření páru klíčů SSH a dalších certifikátů pro virtuální počítač s Linuxem v Azure
Pomocí páru klíčů SSH můžete v Azure vytvořit službu Virtual Machines, která ve výchozím nastavení používá klíče SSH k ověřování. Není potom potřeba používat k přihlášení hesla. Hesla se dají uhodnout a jejich používání může vaše virtuální počítače vystavit útokům hrubou silou při pokusech o jejich uhodnutí. Virtuální počítače vytvořené pomocí šablon Resource Manageru nebo Azure CLI můžou jako součást svého nasazení zahrnovat veřejný klíč SSH. Po nasazení potom není potřeba provádět krok konfigurace, při kterém se zakáže přihlašování pomocí hesla pro SSH. Tento článek obsahuje podrobné pokyny a další příklady generování certifikátů, například pro použití s virtuálním počítačům s Linuxem. Pokud chcete rychle vytvořit a používat pár klíčů SSH, přečtěte si téma [Vytvoření páru veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Principy klíčů SSH

Použití veřejných a privátních klíčů SSH představuje nejjednodušší způsob, jak se přihlásit k linuxovým serverům. [Kryptografie využívající veřejný klíč](https://en.wikipedia.org/wiki/Public-key_cryptography) poskytuje mnohem bezpečnější způsob přihlašování k virtuálním počítačům s Linuxem nebo BSD v Azure než používání hesel, které je možné rozluštit (útokem hrubou silou) mnohem snadněji.

Veřejný klíč je možné s kýmkoli sdílet. Pouze vy (nebo vaše místní infrastruktura zabezpečení) ale máte privátní klíč.  Privátní klíč SSH musí mít [velmi zabezpečené heslo](https://www.xkcd.com/936/) (zdroj: [xkcd.com](https://xkcd.com)), který ho chrání.  Toto heslo se používá jenom pro přístup k souboru privátního klíče SSH a **není** to heslo k uživatelskému účtu.  Když ke klíči SSH přidáte heslo, použije se k zašifrování klíče 128bitový standard AES, aby se privátní klíč bez tohoto hesla nedal dešifrovat.  Pokud by se útočníkovi povedlo odcizit privátní klíč a tento klíč by neměl heslo, mohl by ho použít k přihlášení k vašim serverům, které mají odpovídající veřejný klíč.  Pokud je privátní klíč chráněný heslem, útočník ho nemůže použít, což znamená další úroveň zabezpečení pro vaši infrastrukturu v Azure.

V tomto článku se vytvoří pár veřejného a privátního klíče protokolu SSH verze 2 RSA (označují se také jako klíče ssh-rsa), které se doporučují pro nasazení pomocí Azure Resource Manageru. Klíče *ssh-rsa* jsou na [portálu](https://portal.azure.com) potřeba jak při klasickém nasazení, tak při nasazení Resource Manageru.

## <a name="ssh-keys-use-and-benefits"></a>Použití a výhody klíčů SSH

Azure vyžaduje minimálně 2048bitové veřejné a privátní klíče protokolu SSH verze 2 ve formátu RSA; soubor veřejného klíče má formát kontejneru `.pub`. K vytvoření páru klíčů použijeme příkaz `ssh-keygen`, který se zeptá na několik otázek a pak zapíše privátní klíč a odpovídající veřejný klíč. Jakmile se vytvoří virtuální počítač Azure, zkopíruje Azure veřejný klíč do složky `~/.ssh/authorized_keys` na virtuálním počítači. Klíče SSH v `~/.ssh/authorized_keys` vybízí klienta, aby pro přihlašovací připojení SSH použil odpovídající privátní klíč.  Při vytváření virtuálního počítače Azure s Linuxem, který používá ověřování pomocí klíčů SSH, Azure nakonfiguruje server SSHD tak, aby zakazoval přihlašování pomocí hesla a povoloval pouze klíče SSH.  Když vytvoříte virtuální počítače Azure s Linuxem a klíči SSH, zabezpečíte tím nasazené virtuální počítače a ušetříte si obvyklý konfigurační krok, který následuje po nasazení a spočívá v zakázání hesel v souboru **sshd_config**.

## <a name="using-ssh-keygen"></a>Použití příkazu ssh-keygen

Tento příkaz vytvoří pár klíčů SSH zabezpečený (zašifrovaný) heslem pomocí 2048bitového šifrování RSA a je pro snadnější identifikaci opatřený komentáři.  

Klíče SSH jsou ve výchozím nastavení v adresáři `~/.ssh`.  Pokud adresář `~/.ssh` nemáte, vytvoří ho za vás příkaz `ssh-keygen` se správnými oprávněními.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*Vysvětlení příkazu*

`ssh-keygen`= program použitý k vytvoření klíčů

`-t rsa` = Typ klíče pro vytvoření, který je [formát RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) 
 `-b 2048` = bity klíče

`-C "azureuser@myserver"`= komentář připojený na konec souboru veřejného klíče pro snadnější identifikaci.  Standardně se jako komentář používá e-mail, můžete ale použít cokoli, co je pro vaši infrastrukturu vhodné.

## <a name="classic-deploy-using-asm"></a>Klasické nasazení pomocí `asm`

Pokud používáte model nasazení classic (`asm` režimu v rozhraní příkazového řádku), můžete použít veřejný klíč SSH-RSA nebo RFC4716 naformátovaný klíče v kontejneru pem.  Veřejný klíč SSH-RSA byl vytvořen v předchozí části tohoto článku příkazem `ssh-keygen`.

Vytvoření klíče ve formátu RFC4716 z existujícího veřejného klíče SSH:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Příklad ssh-keygen

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

Uložené soubory klíčů:

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Název páru klíčů pro tento článek.  Výchozí je pár klíčů s názvem **id_rsa**, přičemž to, že se bude soubor privátního klíče jmenovat **id_rsa**, můžou očekávat i některé nástroje, takže je vhodné tento název použít. Výchozím umístěním pro páry klíčů SSH a konfigurační soubor SSH je adresář `~/.ssh/`.  Pokud nezadáte úplnou cestu, `ssh-keygen` vytvoří klíče v aktuálním pracovním adresáři, nikoli ve výchozím adresáři `~/.ssh`.

Výpis adresáře `~/.ssh`

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Heslo klíče:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` označuje heslo k souboru privátního klíče jako přístupové heslo.  *Důrazně* doporučujeme přidat si k privátnímu klíči heslo. Pokud by soubor klíče nebyl chráněný heslem, mohl by tento soubor kdokoli, kde ho má, použít k přihlášení k jakémukoli serveru s odpovídajícím veřejným klíčem. Přidání hesla (přístupového hesla) nabízí vyšší ochranu v případě, že by se někomu povedlo získat přístup k vašemu souboru privátního klíče, a poskytne vám čas změnit klíče používané k vašemu ověření.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Použití příkazu ssh-agent k uložení hesla k soukromému klíči 

Abyste při každém přihlašování přes SSH nemuseli zadávat heslo k souboru s privátním klíčem, můžete si pomocí příkazu `ssh-agent` uložit heslo k souboru s privátním klíčem do mezipaměti. Pokud používáte počítač Mac, při vyvolání příkazu `ssh-agent` se hesla k privátním klíčům bezpečně uloží v řetězci klíčů v OSX.

Ověřte a použijte příkazy ssh-agent a ssh-add, abyste informovali systém SSH o souborech s klíči. Potom se přístupové heslo nebude muset používat interaktivně.

```bash
eval "$(ssh-agent -s)"
```

Potom přidejte privátní klíč do `ssh-agent` pomocí příkazu `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Heslo privátního klíče je teď uložené v `ssh-agent`.

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Zkopírování klíče na existující virtuální počítač pomocí příkazu `ssh-copy-id`
Pokud máte virtuální počítač vytvořený, můžete do virtuálního počítače s Linuxem nainstalovat nový veřejný klíč SSH tímto příkazem:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Vytvoření a nakonfigurování konfiguračního souboru SSH

Vytvoření a konfigurace souboru `~/.ssh/config` patří mezi doporučené osvědčené postupy. Zrychluje přihlášení a optimalizuje chování klienta SSH.

V následujícím příkladu vidíte standardní konfiguraci.

### <a name="create-the-file"></a>Vytvoření souboru

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Upravením souboru přidejte novou konfiguraci SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Příklad souboru `~/.ssh/config`:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Tato konfigurace SSH obsahuje oddíly pro každý server, aby každý z nich mohl mít svůj vlastní vyhrazený pár klíčů. Výchozí nastavení (`Host *`) platí pro všechny hostitele, kteří neodpovídají žádnému konkrétních hostitelů uvedených v konfiguračním souboru výše.

### <a name="config-file-explained"></a>Vysvětlení konfiguračního souboru

`Host`= název hostitele volaného na terminálu.  `ssh fedora22` říká protokolu `SSH`, aby použil hodnoty v bloku nastavení s popiskem `Host fedora22`. POZNÁMKA: Hostitel může mít libovolný popisek, který pro vaše použití dává smysl a který nepředstavuje skutečný název hostitele žádného serveru.

`Hostname 102.160.203.241`= IP adresa nebo název DNS serveru, ke kterému přistupujete.

`User ahmet` = vzdálený uživatelský účet, který se má používat při přihlašování k serveru.

`PubKeyAuthentication yes`= říká protokolu SSH, že chcete k přihlášení použít klíč SSH.

`IdentityFile /home/ahmet/.ssh/id_id_rsa`= privátní klíč SSH a odpovídající veřejný klíč pro ověřování.

## <a name="ssh-into-linux-without-a-password"></a>Přihlášení do Linuxu bez hesla pomocí protokolu SSH

Teď máte pár klíčů SSH a nakonfigurovaný konfigurační soubor SSH a můžete se rychle a bezpečně přihlašovat ke svým virtuálním počítačům s Linuxem. Při prvním přihlášení k serveru pomocí klíče SSH vás příkaz vyzve k zadání přístupového hesla pro tento soubor klíče.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Vysvětlení příkazu

Při provádění příkazu `ssh fedora22` protokol SSH nejdříve vyhledá a načte všechna nastavení z bloku `Host fedora22` a pak načte všechna zbývající nastavení z posledního bloku, `Host *`.

## <a name="next-steps"></a>Další kroky

Dalším krokem je vytvoření virtuálního počítače Azure s Linuxem pomocí nového veřejného klíče SSH.  Vytvářené virtuální počítače Azure, pro které se v přihlašovacích údajích používá veřejný klíč SSH, jsou lépe zabezpečené než ty, které jsou vytvořené pomocí výchozí metody přihlašování s použitím hesel.  Virtuální počítače Azure vytvořené pomocí klíčů SSH jsou ve výchozím nastavení nakonfigurované se zakázaným heslem, aby se vyloučily pokusy o rozluštění hesla (útokem hrubou silou).

* [Vytvoření zabezpečeného virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření zabezpečeného virtuálního počítače s Linuxem pomocí webu Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření zabezpečeného virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
