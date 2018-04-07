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
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: fcc2365c3b41fb69492aa68bf7c48c2d3b8ee5f3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Postup použití SSH klíče s Windows v Azure
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Když připojíte k Linux virtuálních počítačů (VM) v Azure, měli byste použít [public key cryptography](https://wikipedia.org/wiki/Public-key_cryptography) zajistit bezpečnější způsob, jak Přihlaste se k virtuálním počítačům s Linuxem. Tento proces zahrnuje veřejné a privátní výměny klíčů pomocí příkazu zabezpečené shell (SSH) k ověření sami spíše než uživatelské jméno a heslo. Hesla se stát terčem útoků, zvláště na straně Internetu virtuálních počítačů, jako jsou třeba webové servery hrubou silou. Tento článek obsahuje přehled klíčů SSH a jak vygenerovat odpovídající klíče na počítači se systémem Windows.

## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíče
Vám umožní bezpečně přihlásit k virtuálním počítačům s Linuxem pomocí veřejného a privátního klíče:

* **Veřejný klíč** je umístěn na virtuálním počítačům s Linuxem nebo jiné služby, který chcete použít u kryptografie využívající veřejného klíče.
* **Privátní klíč** je co je k dispozici k virtuálním počítačům s Linuxem při přihlášení, ověřit vaši identitu. Chraňte tento privátní klíč. Nesdílejte ho.

Tyto veřejné a soukromé klíče lze použít na více virtuálních počítačů a služeb. Pro každý virtuální počítač nebo službu, kterou chcete pro přístup k nepotřebujete pár klíčů. Podrobnější přehled, najdete v tématu [public key cryptography](https://wikipedia.org/wiki/Public-key_cryptography).

SSH je protokol šifrované připojení, která umožňuje zabezpečený přihlášení přes nezabezpečený připojení. Je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. I když SSH, samotné poskytuje šifrované připojení, pomocí hesla s připojeními SSH stále zranitelný virtuálního počítače vůči hrubou silou útoky, nebo hádání hesel. Bezpečnější a upřednostňované metoda připojení k virtuálnímu počítači pomocí protokolu SSH je pomocí těchto veřejné a soukromé klíče, také známé jako klíče SSH.

Pokud nechcete používat klíče SSH, můžete přesto k přihlašujete vaše virtuální počítače s Linuxem pomocí hesla. Pokud není virtuální počítač přístup k Internetu, může být dostatečná pomocí hesla. Však stále potřebujete spravovat hesla pro každý virtuální počítač s Linuxem a Udržovat zásady hesel v pořádku a postupy, jako je minimální délka hesla a pravidelně aktualizuje. Použití klíče SSH snižuje složitost správy jednotlivých přihlašovacích údajů napříč více virtuálními počítači.

## <a name="windows-packages-and-ssh-clients"></a>Balíčky pro systém Windows a klientů SSH
Připojení k a spravovat virtuální počítače s Linuxem v Azure pomocí **klient SSH**. Počítače se systémem Windows obvykle nemají nainstalovaného klienta SSH. Aktualizace Windows 10 Anniversary přidána Bash pro systém Windows a nejnovější aktualizace Windows 10 Creators poskytuje další aktualizace. Tato subsystému Windows pro Linux umožňuje spouštět a přístup nástroje, jako je například klientem SSH nativně v rámci prostředí Bash. Můžete pak provedením jakéhokoliv z dokumentace Linux, například [jak vygenerovat páry klíčů SSH pro Linux](mac-create-ssh-keys.md). Bash pro Windows je stále ve vývoji a je považován za verzi beta. Další informace o Bash pro systém Windows najdete v tématu [Bash na Ubuntu v systému Windows](https://msdn.microsoft.com/commandline/wsl/about).

Pokud chcete použít něco jiného než Bash pro systém Windows, jsou běžné Windows SSH klientů, které můžete nainstalovat součástí následujících balíčků:

* [Git pro Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Klíče souborů, které je třeba vytvořit?
Azure vyžaduje minimálně 2048bitové **ssh-rsa** formátu veřejné a soukromé klíče. Pokud spravujete prostředků Azure pomocí modelu nasazení Classic, musíte taky generovat PEM (`.pem` souboru).

Zde jsou scénáře nasazení a typy souborů, které můžete použít v každém:

1. **SSH-rsa** klíče jsou požadovány pro nasazení pomocí [portál Azure](https://portal.azure.com)a nasazení Resource Manager pomocí [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md).
   * Tyto klíče jsou obvykle že potřebovat všechny většina lidí.
2. A `.pem` soubor je vyžadován pro vytvoření virtuálních počítačů pomocí nasazení Classic. Tyto klíče jsou podporovány v nasazení Classic při použití [portál Azure](https://portal.azure.com) nebo [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md).
   * Potřebujete vytvořit tyto další klíčů a certifikátů, pokud spravujete prostředky vytvořené pomocí modelu nasazení Classic.

## <a name="install-git-for-windows"></a>Instalace Gitu pro Windows
V předchozí části uveden více balíčků, které zahrnují `openssl` nástroje pro systém Windows. Tento nástroj je potřeba k vytvoření veřejné a soukromé klíče. Následující příklady jsou upřesněny postupy instalace a použití **Git pro Windows**, i když můžete podle toho, která balíček dáváte přednost. **Git pro Windows** dává vám přístup k některé další open-source softwaru ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) nástrojů a pomůcek, které mohou být užitečné při práci s virtuální počítače s Linuxem.

1. Stáhněte a nainstalujte **Git pro Windows** z následujícího umístění: [ https://git-for-windows.github.io/ ](https://git-for-windows.github.io/).
2. Pokud potřebujete konkrétně je změnit, přijměte výchozí nastavení během procesu instalace.
3. Spustit **Git Bash** z **nabídky Start** > **Git** > **Git Bash**. Konzole vypadá podobně jako v následujícím příkladu:

    ![Prostředí Git Bash pro Windows](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Vytvoření privátního klíče
1. Ve vaší **Git Bash** okně použití `openssl.exe` vytvořit privátní klíč. Následující příklad vytvoří klíč s názvem `myPrivateKey` a certifikát s názvem `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    Výstup bude vypadat podobně jako v následujícím příkladu:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Pokud se zobrazí chybová zpráva: bash, zkuste otevřít nové **Git Bash** okno se zvýšenými oprávněními. Potom spusťte znovu `openssl` příkaz.

2. Odpovězte pokynů pro název země, umístění, název organizace, atd.
3. Váš nový privátní klíč a certifikát se vytvoří v aktuální pracovní adresář. Jako bezpečnostní opatření měli byste nastavit oprávnění na váš privátní klíč tak, aby pouze můžete k němu přístup:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. [Další části](#create-a-private-key-for-putty) podrobnosti, jak zobrazit a použití veřejný klíč a vytvoření privátního klíče specifický pro použití klienta PuTTY k SSH pro virtuální počítače s Linuxem pomocí PuTTYgen. Následující příkaz vytvoří soubor veřejného klíče s názvem `myPublicKey.key` , můžete použít:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Pokud potřebujete spravovat klasické prostředky, převést `myCert.pem` k `myCert.cer` (X509, kódování DER certifikát). Tento volitelný krok proveďte jenom v případě, že budete muset konkrétně spravovat starší klasické prostředky.

    Převeďte certifikát pomocí následujícího příkazu:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Vytvoření privátní klíč pro PuTTY
PuTTY je běžné SSH klient pro systém Windows. Jste libovolného klienta SSH, který chcete používat. Prostřednictvím PuTTY, musíte vytvořit další typ klíče - privátní klíč PuTTY (PPK). Pokud nechcete, aby prostřednictvím PuTTY, tuto část přeskočte.

Následující příklad vytvoří tento další privátní klíč speciálně pro PuTTY používat:

1. Použití **Git Bash** převést svůj privátní klíč do privátní klíč RSA, který můžete porozumět PuTTYgen. Následující příklad vytvoří klíč s názvem `myPrivateKey_rsa` z existující klíč s názvem `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Jako bezpečnostní opatření měli byste nastavit oprávnění na váš privátní klíč tak, aby pouze můžete k němu přístup:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Stažení a spuštění PuTTYgen z následujícího umístění: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Klikněte na nabídku: **soubor** > **zatížení privátní klíč**
4. Najít váš privátní klíč (`myPrivateKey_rsa` v předchozím příkladu). Výchozí adresář při spuštění **Git Bash** je `C:\Users\%username%`. Změnit filtr souborů zobrazíte **všechny soubory (\*.\*)** :

    ![Načtení existující soukromý klíč do PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Klikněte na tlačítko **otevřete**. Příkazovém řádku uvedena, že klíč musí být úspěšně naimportována:

    ![Byly úspěšně importovány. klíč PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Klikněte na tlačítko **OK** k zavřete okno příkazového řádku.
7. Veřejný klíč se zobrazí v horní části **PuTTYgen** okno. Zkopírujte a vložte tento veřejný klíč do portálu Azure nebo do šablony Azure Resource Manager, když vytvoříte virtuální počítač s Linuxem. Můžete také kliknout na **uložit veřejný klíč** pro uložení kopie do počítače:

    ![Uložte soubor PuTTY veřejného klíče](./media/ssh-from-windows/save-public-key.png)

    Následující příklad ukazuje, jak by zkopírujte a vložte tento veřejný klíč do portálu Azure při vytváření virtuálního počítače s Linuxem. Veřejný klíč je obvykle pak uloženy v `~/.ssh/authorized_keys` na nový virtuální počítač.

    ![Veřejný klíč použít při vytváření virtuálního počítače na portálu Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. Zpět v **PuTTYgen**, klikněte na tlačítko **uložit privátní klíč**:

    ![Uložte soubor privátního klíče PuTTY](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > Zobrazí výzva s dotazem Pokud chcete pokračovat bez zadávání přístupové heslo klíče. Přístupové heslo je stejné jako heslo připojené k privátní klíč. I když někdo chtěli získat privátní klíč, se stále nepůjdou ověřit pomocí právě klíče. Potřebují by také heslo. Bez přístupové heslo Pokud někdo získá váš privátní klíč, můžete přihlášení k žádné virtuální počítač nebo služba, která používá tento klíč. Doporučujeme že vytvořit přístupové heslo. Pokud však heslo zapomenete, neexistuje žádný způsob, jak jej obnovit.
   >
   >

    Pokud chcete zadat přístupové heslo, klikněte na tlačítko **ne**, zadejte přístupové heslo v hlavním okně PuTTYgen a pak klikněte na tlačítko **uložit privátní klíč** znovu. Jinak, klikněte na tlačítko **Ano** pokračujte bez zadání volitelné přístupové heslo.
9. Zadejte název a umístění pro uložení souboru PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Použití klienta Putty k SSH na počítač s Linuxem
PuTTY znovu, je běžné SSH klient pro systém Windows. Jste libovolného klienta SSH, který chcete používat. Následující kroky podrobnosti, jak používat privátní klíč k ověření pomocí svého virtuálního počítače Azure pomocí protokolu SSH. Kroky se podobají v jiných SSH klíče klientů z hlediska museli načíst váš privátní klíč k ověření připojení SSH.

1. Stažení a spuštění putty z následujícího umístění: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Zadejte název hostitele nebo IP adresa vašeho virtuálního počítače z portálu Azure:

    ![Otevřít nové připojení PuTTY](./media/ssh-from-windows/putty-new-connection.png)
3. Před výběrem **otevřete**, klikněte na tlačítko **připojení** > **SSH** > **Auth** kartě. Vyhledejte a vyberte privátní klíč:

    ![Vyberte PuTTY privátní klíč pro ověřování](./media/ssh-from-windows/putty-auth-dialog.png)
4. Klikněte na tlačítko **otevřete** pro připojení k virtuálnímu počítači

## <a name="next-steps"></a>Další postup
Můžete také vygenerovat veřejné a soukromé klíče [pomocí OS X a Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Další informace o Bash pro systém Windows a o výhodách operačních systémů nástroje snadno dostupné na počítači s Windows najdete v tématu [Bash na Ubuntu v systému Windows](https://msdn.microsoft.com/commandline/wsl/about).

Pokud máte potíže při používání SSH připojit k virtuální počítače Linux, najdete v článku [řešení SSH připojení k virtuální počítač Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
