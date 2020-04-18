---
title: Úvodní příručka – vytvoření virtuálního počítače s Windows na webu Azure Portal
description: V tomto rychlém startu zjistíte, jak pomocí webu Azure Portal vytvořit virtuální počítač s Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a304e400d193eb4b2d0c2e8ec30ea03ea41977f8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606643"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rychlý start: Vytvoření virtuálního počítače s Windows na webu Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Tato metoda poskytuje uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a jejich související prostředky. Tento rychlý start ukazuje, jak používat portál Azure k nasazení virtuálního počítače (VM) v Azure, který spouští Windows Server 2019. Pak se k virtuálnímu počítači připojíte přes protokol RDP a nainstalujete na něj webový server služby IIS, abyste virtuální počítač viděli v akci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Do hledání zadejte **virtuální počítače.**
1. V části **Služby**vyberte **Virtuální počítače**.
1. Na stránce **Virtuální počítače** vyberte **Přidat**. 
1. Přesvědčte se, že je na kartě **Základy** v části **Podrobnosti o projektu** vybrané správné předplatné a pak ve Skupině prostředků vyberte **Vytvořit nový**. Zadejte *název myResourceGroup.* 

    ![Vytvoření nové skupiny prostředků pro virtuální počítač](./media/quick-create-portal/project-details.png)

1. V **části Podrobnosti instance**zadejte *myVM* pro **název virtuálního počítače** a zvolte Východní *USA* pro **vaši oblast**a pak zvolte Windows Server *2019 Datacenter* pro **bitovou kopii**. Zbytek ponechte ve výchozím nastavení.

    ![Část podrobnosti o instancích](./media/quick-create-portal/instance-details.png)

1. V **Účtu správce** zadejte uživatelské jméno, například *azureuser*, a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Zadejte uživatelské jméno a heslo.](./media/quick-create-portal/administrator-account.png)

1. V části **Pravidla příchozího portu**zvolte **Povolit vybrané porty** a v rozevíracím souboru vyberte **protokoly RDP (3389)** a **HTTP (80).**

    ![Otevřené porty pro protokoly RDP a HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Vytvořte připojení ke vzdálené ploše virtuálního počítače. Tyto pokyny popisují připojení k virtuálnímu počítači z počítače z Windows. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) na Mac App Storu.

1. Klikněte na tlačítko **Připojit** na stránce přehledu vašeho virtuálního počítače. 

    ![Připojení k virtuálnímu počítači Azure z portálu](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na stránce **Připojit k virtuálnímu počítači** ponechejte výchozí možnosti pro připojení pomocí IP adresy přes port 3389 a klikněte na **Stáhnout soubor RDP**.

2. Otevřete stažený soubor RDP a po zobrazení výzvy klikněte na **Připojit**. 

3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako*uživatelské jméno* **localhost**\\, zadejte heslo, které jste vytvořili pro virtuální počítač, a klepněte na tlačítko **OK**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a vytvořte připojení.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server služby IIS. Ve virtuálním počítači otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Jakmile budete hotovi, ukončete připojení RDP k virtuálnímu počítači.


## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Na portálu vyberte virtuální ms a v přehledu virtuálního účtu použijte tlačítko **Kliknutím zkopírujte** vpravo od IP adresy a vložte ho na kartu prohlížeče. Otevře se výchozí uvítací stránka iis, která by měla vypadat takto:

![Výchozí web služby IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. 

Vyberte skupinu prostředků pro virtuální počítač a pak vyberte **Odstranit**. Potvrďte název skupiny prostředků, která má být dokončena odstranění prostředků.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, otevřeli jste síťový port pro webový provoz a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)