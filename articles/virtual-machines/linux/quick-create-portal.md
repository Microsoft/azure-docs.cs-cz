---
title: Rychlý Start – vytvoření virtuálního počítače se systémem Linux v Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit virtuální počítač se systémem Linux.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/25/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c1ccd2df3a1fd6b2f1f1cb5b2d3250e8bbfcc327
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549732"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Rychlý start: Vytvoření virtuálního počítače s Linuxem na webu Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Azure Portal je uživatelské rozhraní založené na prohlížeči pro vytváření prostředků Azure. V tomto rychlém startu se dozvíte, jak použít Azure Portal k nasazení virtuálního počítače se systémem Linux se systémem Ubuntu 18,04 LTS. Také se k virtuálnímu počítači připojíte přes SSH a nainstalujete na něj webový server NGINX, abyste virtuální počítač viděli v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud jste to ještě neudělali, přihlaste se k [Azure Portal](https://portal.azure.com) .

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Do vyhledávacího pole zadejte **virtuální počítače** .
1. V části **služby** vyberte **virtuální počítače**.
1. Na stránce **virtuální počítače** vyberte **Přidat**. Otevře se stránka **vytvořit virtuální počítač** .
1. Přesvědčte se, že je na kartě **Základy** v části **Podrobnosti o projektu** vybrané správné předplatné a pak ve Skupině prostředků vyberte **Vytvořit nový**. Jako název zadejte *myResourceGroup* . *. 

    ![Snímek obrazovky s oddílem Project Details s informacemi o tom, kde jste vybrali předplatné Azure a skupinu prostředků pro virtuální počítač](./media/quick-create-portal/project-details.png)

1. V části **Podrobnosti o instanci** zadejte *MyVM* pro **název virtuálního počítače**, vyberte *východní USA* pro vaši **oblast** a jako **Obrázek** vyberte *Ubuntu 18,04 LTS* . Zbytek ponechte ve výchozím nastavení.

    ![Snímek obrazovky s oddílem podrobností instance, kde můžete zadat název virtuálního počítače a vybrat jeho oblast, obrázek a velikost](./media/quick-create-portal/instance-details.png)

1. V části **účet správce** vyberte **veřejný klíč SSH**.

1. V **uživatelské jméno** zadejte *azureuser*.

1. U **zdroje veřejného klíče SSH** ponechte výchozí hodnotu **Generovat novou dvojici klíčů** a potom jako **název páru klíčů** zadejte *myKey* .

    ![Snímek obrazovky s oddílem účtu správce, kde můžete vybrat typ ověřování a zadat přihlašovací údaje správce](./media/quick-create-portal/administrator-account.png)

1. V části **příchozí pravidla portů**  >  **veřejné příchozí porty** zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **SSH (22)** a **http (80)** . 

    ![Snímek obrazovky s částí pravidla portů pro příchozí spojení, kde můžete vybrat, na kterých portech jsou příchozí připojení povolena](./media/quick-create-portal/inbound-port-rules.png)

1. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.

1. Na stránce **Vytvoření virtuálního počítače** se zobrazí podrobnosti o virtuálním počítači, který se chystáte vytvořit. Až budete připraveni, vyberte **Vytvořit**.

1. Po otevření okna **Generovat novou dvojici klíčů** vyberte **Stáhnout privátní klíč a vytvořit prostředek**. Soubor klíče se stáhne jako **myKey. pem**. Ujistěte se, že víte, kam se `.pem` soubor stáhl, budete k němu potřebovat cestu v dalším kroku.

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

1. Na stránce nového virtuálního počítače vyberte veřejnou IP adresu a zkopírujte ji do schránky.


    ![Snímek obrazovky ukazující, jak zkopírovat IP adresu pro virtuální počítač](./media/quick-create-portal/ip-address.png)

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Vytvořte připojení SSH k virtuálnímu počítači.

1. Pokud jste na počítači se systémem Mac nebo Linux, otevřete příkazový řádek bash. Pokud jste na počítači s Windows, otevřete příkazový řádek PowerShellu. 

1. Na příkazovém řádku otevřete připojení SSH k virtuálnímu počítači. Nahraďte IP adresu některou z vašich virtuálních počítačů a nahraďte cestu k umístění, `.pem` kam se soubor klíče stáhl.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Klíč SSH, který jste vytvořili, se dá použít při příštím vytvoření virtuálního počítače v Azure. Pokud chcete příště vytvořit virtuální počítač, vyberte **použít klíč uložený v Azure** pro **zdroj veřejného klíče SSH** . V počítači už máte privátní klíč, takže nebudete muset stahovat cokoli.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server NGINX. V relaci SSH aktualizujte zdroje balíčku a pak nainstalujte nejnovější balíček NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Až budete hotovi, zadáním příkazu `exit` ukončete relaci SSH.


## <a name="view-the-web-server-in-action"></a>Zobrazení webového serveru v akci

V libovolném webovém prohlížeči zobrazte výchozí úvodní stránku serveru NGINX. Jako webovou adresu zadejte veřejnou IP adresu virtuálního počítače. Veřejnou IP adresu najdete na stránce přehledu virtuálního počítače nebo v připojovacím řetězci SSH, který jste použili dříve.

![Snímek obrazovky s výchozím webem NGINX v prohlížeči](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, vytvořili jste skupinu zabezpečení sítě a pravidlo a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Linuxem](./tutorial-manage-vm.md)
