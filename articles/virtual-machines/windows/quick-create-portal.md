---
title: Rychlý start – Vytvoření virtuálního počítače s Windows na webu Azure Portal | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak pomocí webu Azure Portal vytvořit virtuální počítač s Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 692d8c0a1a427fa65a94d474f78792b1a071de46
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219715"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rychlý start: Vytvoření virtuálního počítače s Windows na webu Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Tato metoda poskytuje uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a jejich související prostředky. V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal nasadit do Azure virtuální počítač s Windows Serverem 2016. Pak se k virtuálnímu počítači připojíte přes protokol RDP a nainstalujete na něj webový server služby IIS, abyste virtuální počítač viděli v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.

1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte a vyberte **Windows Server 2016 Datacenter** a pak zvolte **Vytvořit**.

1. Přesvědčte se, že je na kartě **Základy** v části **Podrobnosti o projektu** vybrané správné předplatné a pak ve Skupině prostředků vyberte **Vytvořit nový**. Jako název zadejte *myResourceGroup*. 

    ![Vytvoření nové skupiny prostředků pro virtuální počítač](./media/quick-create-portal/project-details.png)

1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Umístění** zvolte *USA – východ*. Zbytek ponechte ve výchozím nastavení.

    ![Část podrobností o instancích](./media/quick-create-portal/instance-details.png)

1. V **Účtu správce** zadejte uživatelské jméno, například *azureuser*, a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Zadejte uživatelské jméno a heslo.](./media/quick-create-portal/administrator-account.png)

1. V části **Pravidla portů pro příchozí spojení** vyberte **Povolit vybrané porty** a pak z rozevíracího seznamu vyberte **RDP (3389)** a **HTTP**.

    ![Otevřené porty pro protokoly RDP a HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Zbytek ponechte ve výchozím nastavení a pak v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Vytvořte připojení ke vzdálené ploše virtuálního počítače. Tyto pokyny popisují připojení k virtuálnímu počítači z počítače z Windows. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) na Mac App Storu.

1. Na stránce vlastností virtuálního počítače klikněte na tlačítko **Připojit**. 

    ![Připojení k virtuálnímu počítači Azure z portálu](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na stránce **Připojení k virtuálnímu počítači** ponechte výchozí výběr možností pro připojení podle názvu DNS přes port 3389 a klikněte na **Stáhnout soubor RDP**.

2. Otevřete stažený soubor RDP a po zobrazení výzvy klikněte na **Připojit**. 

3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno ve formátu *název_virtuálního_počítače*\\*uživatelské_jméno* a heslo, které jste pro virtuální počítač vytvořili, a pak klikněte na **OK**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a vytvořte připojení.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server služby IIS. Ve virtuálním počítači otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Jakmile budete hotovi, ukončete připojení RDP k virtuálnímu počítači.


## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Na portálu vyberte virtuální počítač a v přehledu virtuálního počítače klikněte na tlačítko **Kopírování kliknutím** napravo od IP adresy, abyste ji mohli zkopírovat a vložit na kartu prohlížeče. Otevře se výchozí úvodní stránka služby IIS, která by měla vypadat takto:

![Výchozí web služby IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, otevřeli jste síťový port pro webový provoz a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
