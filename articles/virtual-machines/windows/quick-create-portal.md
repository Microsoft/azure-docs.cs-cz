---
title: Rychlý start Azure – Vytvoření virtuálního počítače s Windows pomocí portálu | Dokumentace Microsoftu
description: Rychlý start Azure – Vytvoření virtuálního počítače s Windows pomocí portálu
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/11/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 42e17e2eb4ef64f8d0e80713041d22e6fe182538
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Vytvoření virtuálního počítače s Windows pomocí webu Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Tato metoda poskytuje uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet a konfigurovat virtuální počítače a všechny související prostředky. Tento Rychlý start prochází jednotlivé kroky k vytvoření virtuálního počítače a instalaci webového serveru na virtuálním počítači.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 

3. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se používá k přihlášení k virtuálnímu počítači. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm). Jakmile budete hotovi, klikněte na **OK**.

    ![Zadání základních informací o virtuálním počítači v okně portálu](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

4. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. 

    ![Snímek obrazovky zobrazující velikosti virtuálních počítačů](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

5. V části **Nastavení** ponechte výchozí nastavení a klikněte na **OK**. 

6. Na stránce Souhrn kliknutím na **Ok** spusťte nasazení virtuálního počítače.

7. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře souhrn virtuálního počítače.


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Vytvořte připojení ke vzdálené ploše virtuálního počítače.

1. Klikněte na tlačítko **Připojit** ve vlastnostech virtuálního počítače. Vytvoří a stáhne se soubor protokolu RDP (Remote Desktop Protocol) – soubor .rdp.

    ![Portál 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

3. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače, a potom klikněte na **Ok**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.


## <a name="install-iis-using-powershell"></a>Instalace služby IIS pomocí PowerShellu

Na virtuálním počítači spusťte relaci PowerShellu a následujícím příkazem nainstalujte službu IIS.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Až budete hotovi, ukončete relaci RDP a vraťte se do vlastností virtuálního počítače na webu Azure Portal.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz 

Skupina zabezpečení sítě (NSG) zabezpečuje příchozí a odchozí provoz. Když se virtuální počítač vytvoří na webu Azure Portal, pro připojení ke vzdálené ploše se vytvoří příchozí pravidlo na portu 3389. Protože je tento virtuální počítač hostitelem webového serveru, je potřeba vytvořit pravidlo NSG pro port 80.

1. Na virtuálním počítači klikněte na název **skupiny prostředků**.
2. Vyberte **skupinu zabezpečení sítě**. NSG můžete identifikovat pomocí sloupce **Typ**. 
3. V nabídce vlevo v části Nastavení klikněte na **Příchozí pravidla zabezpečení**.
4. Klikněte na **Přidat**.
5. Do pole **Název** zadejte **http**. Zkontrolujte, že **Rozsah portů** je nastavený na 80 a **Akce** je nastavená na **Povolit**. 
6. Klikněte na **OK**.


## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Když je teď služba IIS nainstalovaná a port 80 k virtuálnímu počítači otevřený, webový server je přístupný z internetu. Otevřete webový prohlížeč a zadejte veřejnou IP adresu virtuálního počítače. Veřejnou IP adresu najdete v části *Virtuální počítače* na webu Azure Portal.

![Výchozí web služby IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků příslušného virtuálního počítače a kliknete na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto Rychlém startu jste nasadili jednoduchý virtuální počítač a pravidlo skupiny zabezpečení sítě a nainstalovali jste webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
