---
title: Rychlý Start – vytvoření virtuálního počítače s Windows v Azure Portal
description: V tomto rychlém startu zjistíte, jak pomocí webu Azure Portal vytvořit virtuální počítač s Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 46119b9f7cdac47920d4bba8e00c3fc56b0edc78
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "87494515"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rychlý start: Vytvoření virtuálního počítače s Windows na webu Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Tato metoda poskytuje uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a jejich související prostředky. V tomto rychlém startu se dozvíte, jak použít Azure Portal k nasazení virtuálního počítače v Azure se systémem Windows Server 2019. Pak se k virtuálnímu počítači připojíte přes protokol RDP a nainstalujete na něj webový server služby IIS, abyste virtuální počítač viděli v akci.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Do vyhledávacího pole zadejte **virtuální počítače** .
1. V části **služby** vyberte **virtuální počítače**.
1. Na stránce **virtuální počítače** vyberte **Přidat**. 
1. Přesvědčte se, že je na kartě **Základy** v části **Podrobnosti o projektu** vybrané správné předplatné a pak ve Skupině prostředků vyberte **Vytvořit nový**. Jako název zadejte *myResourceGroup* . 

    ![Snímek obrazovky s oddílem Project Details s informacemi o tom, kde jste vybrali předplatné Azure a skupinu prostředků pro virtuální počítač](./media/quick-create-portal/project-details.png)

1. V **části Podrobnosti o instanci** zadejte *MyVM* pro **název virtuálního počítače** a zvolte *východní USA* pro vaši **oblast** a pak zvolte *Windows Server 2019 Datacenter* pro **bitovou kopii**. Zbytek ponechte ve výchozím nastavení.

    ![Snímek obrazovky s oddílem podrobností instance, kde můžete zadat název virtuálního počítače a vybrat jeho oblast, obrázek a velikost](./media/quick-create-portal/instance-details.png)

1. V **Účtu správce** zadejte uživatelské jméno, například *azureuser*, a heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Snímek obrazovky s oddílem účtu správce, kde můžete zadat uživatelské jméno a heslo správce](./media/quick-create-portal/administrator-account.png)

1. V části **pravidla portů pro příchozí spojení** zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** a **http (80)** .

    ![Snímek obrazovky s částí pravidla portů pro příchozí spojení, kde můžete vybrat, na kterých portech jsou příchozí připojení povolena](./media/quick-create-portal/inbound-port-rules.png)

1. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.

    ![Snímek obrazovky zobrazující tlačítko pro kontrolu a vytvoření v dolní části stránky](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Vytvořte připojení ke vzdálené ploše virtuálního počítače. Tyto pokyny popisují připojení k virtuálnímu počítači z počítače z Windows. Na počítači Mac budete potřebovat klienta protokolu RDP, jako je například tento [Klient vzdálené plochy](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) na Mac App Storu.

1. Klikněte na tlačítko **připojit** na stránce Přehled pro váš virtuální počítač. 

    ![Snímek obrazovky se stránkou s přehledem virtuálního počítače zobrazující umístění tlačítka připojit](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Na stránce **připojit k virtuálnímu počítači** ponechte výchozí možnosti připojit pomocí IP adresy, přes port 3389 a klikněte na **Stáhnout soubor RDP**.

2. Otevřete stažený soubor RDP a po zobrazení výzvy klikněte na **Připojit**. 

3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako **localhost** \\ *uživatelské jméno* hostitele, zadejte heslo, které jste pro virtuální počítač vytvořili, a pak klikněte na **OK**.

4. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a vytvořte připojení.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server služby IIS. Ve virtuálním počítači otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Jakmile budete hotovi, ukončete připojení RDP k virtuálnímu počítači.


## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Na portálu vyberte virtuální počítač a v přehledu virtuálního počítače pomocí tlačítka pro **kopírování na** pravé straně IP adresy ho zkopírujte a vložte na kartu prohlížeče. Otevře se výchozí uvítací stránka služby IIS, která by měla vypadat takto:

![Snímek obrazovky s výchozím webem služby IIS v prohlížeči](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. 

Vyberte skupinu prostředků pro virtuální počítač a pak vyberte **Odstranit**. Potvrďte název skupiny prostředků, abyste dokončili odstraňování prostředků.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, otevřeli jste síťový port pro webový provoz a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](./tutorial-manage-vm.md)
