---
title: Azure Stack – rychlý Start – vytvoření virtuálního počítače pomocí portálu
description: Azure Stack rychlý Start – vytvoření virtuálního počítače s Linuxem pomocí portálu
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.openlocfilehash: 0664316d73b1473ab52b08f8abead2ec33d121b6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836742"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Rychlý start: vytvoření serveru virtuálního počítače s Linuxem pomocí portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Virtuální počítače s Ubuntu Server 16.04 LTS můžete vytvořit pomocí portálu Azure Stack. Postupujte podle kroků v tomto článku, jak vytvořit a používat virtuální počítač. Tento článek také obsahuje postup:

* Připojení k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte webový server NGINX.
* Vyčištění prostředků.

> [!NOTE]  
> Obrázky obrazovky v tomto článku jsou aktualizovány tak, aby odpovídaly změny zavedené s verzí Azure Stack. 1808. Přidá podporu pro použití. 1808 *spravované disky* kromě nespravované disky. Pokud používáte starší verzi, budou některé obrázky pro úkoly, jako je výběr disku liší od co se zobrazí v tomto článku.  


## <a name="prerequisites"></a>Požadavky

* **Image Linuxu v Tržišti Azure Stack**

   Tržiště Azure Stack nemá image Linuxu ve výchozím nastavení. Zkontrolujte, že máte Azure Stack, který obsahuje operátor **Ubuntu Server 16.04 LTS** bitové kopie v marketplace. Operátor, který můžete použít postup popsaný v [stažení položek z marketplace z Azure do služby Azure Stack](../azure-stack-download-azure-marketplace-item.md) článku.

* **Přístup k klienta SSH**

   Pokud používáte Azure Stack Development Kit (ASDK), nebudete mít přístup k klienta SSH. Pokud budete potřebovat klienta, existuje několik balíčků, které zahrnují klienta SSH. Například PuTTY zahrnuje generátor klíče SSH (puttygen.exe) a služby klienta SSH. Další informace o dostupných balíčků, přečtěte si následující článek k Azure: [jak používat klíče SSH s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Tento rychlý start využívá PuTTY generování klíčů SSH a připojte se k virtuálnímu počítači Linux serverem. Stáhněte si a nainstalujte PuTTY, přejděte na [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Potřebujete pár klíčů SSH na dokončení všech kroků v tomto článku. Pokud máte existující pár klíčů SSH, můžete tento krok přeskočit.

1. Přejděte do složky instalace PuTTY (výchozí umístění je ```C:\Program Files\PuTTY```) a spusťte ```puttygen.exe```.
2. V okně generátor klíče PuTTY, zkontrolujte **typ klíče pro generování** je nastavena na **RSA**a **počet bitů v vygenerovaný klíč** je nastavena na **2048**. Jakmile budete připraveni, klikněte na tlačítko **generovat**.

   ![Konfigurace puTTY Key Generator](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Ke generování klíče, přesuňte ukazatel myši nad náhodně v okně generátor klíče PuTTY.
4. Po dokončení generování klíčů, klikněte na tlačítko **uložit veřejný klíč** a potom klikněte na tlačítko **uložit privátní klíč** uložení klíče do souborů.

   ![PuTTY Key Generator výsledky](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlaste se k portálu Azure Stack

Přihlaste se k portálu Azure Stack. Adresa na portálu Azure Stack závisí, který produkt Azure Stack se připojujete ke:

* Pro Azure Stack Development Kit (ASDK) přejděte na: https://portal.local.azurestack.external.
* Pro systém Azure Stack integrované přejděte na adresu URL, kterou poskytuje vaší operátory Azure stacku.

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu portálu Azure Stack.

2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
   
   ![Vyberte server pro Linux](media/azure-stack-quick-linux-portal/select.png)
1. Klikněte na možnost **Vytvořit**.

4. Zadejte informace o virtuálním počítači. Jako **Typ ověřování** vyberte **Veřejný klíč SSH**. Vložte veřejný klíč SSH, který jste uložili a klikněte na **OK**.

   >[!NOTE]
 Zajistěte, aby že pro tyto klíče se odebere všechny úvodní a koncové prázdné znaky.

   ![Základní informace o panelu – Konfigurace virtuálního počítače](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Vyberte **D1** pro virtuální počítač.

   ![Velikost panelu – vyberte velikost virtuálního počítače.](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Na **nastavení** stránce, proveďte požadované změny výchozích hodnot.
   
    - Počínaje verzí Azure Stack. 1808, můžete nakonfigurovat **úložiště** kde je možné použít *spravované disky*. Starší než verze. 1808 lze použít pouze nespravované disky.    
      ![Konfigurace úložiště pro spravované disky](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
    Pokud vaše konfigurace jsou připravené, vyberte **OK** pokračujte.

7. Na **Souhrn** klikněte na **OK** spusťte nasazení virtuálního počítače.  
   ![Nasazení](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

1. Klikněte na tlačítko **připojit** na stránce virtuální počítač. Můžete najít připojovací řetězec SSH, který je potřeba připojit k virtuálnímu počítači. 

2. Otevřete PuTTY.

3. Na obrazovce konfigurace PuTTY, kterou použijete **kategorie** okno posunout nahoru nebo dolů. Přejděte dolů k položce **SSH**, rozbalte **SSH**a potom klikněte na tlačítko **Auth**. Klikněte na tlačítko **Procházet** a vyberte soubor privátního klíče, který jste uložili.
   ![Připojení virtuálního počítače](media/azure-stack-quick-linux-portal/putty03.PNG)

4. Posunout nahoru v **kategorie** okna a pak klikněte na tlačítko **relace**.
5. V **název hostitele (nebo IP adresa)** okně vložte připojovací řetězec znázorňuje na portálu Azure Stack. V tomto příkladu je řetězec ```asadmin@192.168.102.34```.

   ![Konfigurace puTTY připojovací řetězec](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Klikněte na tlačítko **otevřete** otevřít relaci. u virtuálního počítače.

   ![Linux relace](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

K aktualizaci zdrojů balíčku a nainstalujete nejnovější balíček NGINX na virtuálním počítači, použijte následující příkazy prostředí bash.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po dokončení instalace serveru NGINX, ukončete relaci SSH a otevřete stránku přehledu virtuálního počítače na portálu Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Skupina zabezpečení sítě (NSG) zabezpečuje příchozí a odchozí provoz. Když je virtuální počítač na portálu Azure Stack, se vytvoří příchozí pravidlo na portu 22 pro připojení SSH. Protože tento virtuální počítač hostitelem webového serveru, pravidlo skupiny zabezpečení sítě je potřeba vytvořit pro povolení webového provozu na portu 80.

1. Na virtuálním počítači **přehled** klikněte na název **skupiny prostředků**.
2. Vyberte **skupinu zabezpečení sítě** pro virtuální počítač. NSG můžete identifikovat pomocí sloupce **Typ**.
3. V nabídce vlevo v části **nastavení**, klikněte na tlačítko **příchozí pravidla zabezpečení**.
4. Klikněte na tlačítko **Add** (Přidat).
5. Do pole **Název** zadejte **http**. Zkontrolujte, že **Rozsah portů** je nastavený na 80 a **Akce** je nastavená na **Povolit**.
6. Klikněte na tlačítko **OK**.

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

NGINX nainstalovaný a port 80 otevřený ve vašem virtuálním počítači můžete přístup k webovému serveru pomocí veřejné IP adresy virtuálního počítače. (Veřejná IP adresa se zobrazí na stránce Přehled virtuálního počítače).

Otevřete webový prohlížeč a přejděte do ```http://<public IP address>```.

![Úvodní stránku serveru NGINX webového serveru](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Pokud chcete odstranit virtuální počítač a jeho prostředky, vyberte skupinu prostředků, na stránce virtuální počítač a potom klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní server virtuálního počítače s Linuxem s webovým serverem. Další informace o virtuálních počítačích Azure Stack, [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
