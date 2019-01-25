---
title: Rychlý start – Vytvoření škálovací sady virtuálních počítačů na webu Azure Portal | Microsoft Docs
description: Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů na webu Azure Portal.
keywords: škálovací sady virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 03/27/18
ms.author: cynthn
ms.openlocfilehash: 7644940418b7322e5ec4b02b793219b44ae3aa97
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885145"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Rychlý start: Vytvoření škálovací sady virtuálních počítačů na webu Azure Portal
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Počet virtuálních počítačů ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. Nástroj pro vyrovnávání zatížení Azure pak bude distribuovat provoz do instancí virtuálních počítačů ve škálovací sadě. V tomto rychlém startu vytvoříte škálovací sadu virtuálních počítačů na webu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.


## <a name="create-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů
Škálovací sadu můžete nasadit s použitím image Windows Serveru nebo image Linuxu, jako je například RHEL, CentOS, Ubuntu nebo SLES.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte *škálovací sada*, zvolte **Škálovací sada virtuálních počítačů** a pak vyberte **Vytvořit**.
3. Zadejte název škálovací sady, například *myScaleSet*.
4. Vyberte požadovaný typ operačního systému, například *Windows Server 2016 Datacenter*.
5. Zadejte požadovaný název skupiny prostředků, například *myResourceGroup*, a umístění, například *USA – východ*.
6. Zadejte požadované uživatelské jméno a vyberte upřednostňovaný typ ověřování.
    - **Heslo** musí mít alespoň 12 znaků a musí splňovat tři ze čtyř bezpečnostních požadavků: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Další informace najdete v [požadavcích na uživatelské jméno a heslo](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Pokud vyberete image disku s operačním systémem Linux, můžete místo toho zvolit **Veřejný klíč SSH**. Stačí zadat veřejný klíč, například *~/.ssh/id_rsa.pub*. K [vytvoření a použití klíčů SSH](../virtual-machines/linux/mac-create-ssh-keys.md) můžete použít Azure Cloud Shell na portálu.

7. Zadejte **Název veřejné IP adresy**, například *myPublicIP*.
8. Zadejte jedinečný **Popisek názvu domény**, například *myuniquedns*. Tento popisek DNS tvoří základ plně kvalifikovaného názvu domény nástroje pro vyrovnávání zatížení před škálovací sadou.
9. Potvrďte možnosti škálovací sady výběrem možnosti **Vytvořit**.

    ![Vytvoření škálovací sady virtuálních počítačů na webu Azure Portal](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Připojení k virtuálnímu počítači ve škálovací sadě
Když vytvoříte škálovací sadu na portálu, vytvoří se i nástroj pro vyrovnávání zatížení. K distribuci provozu do instancí škálovací sady kvůli zajištění možnosti vzdáleného připojení, například přes protokol RDP nebo SSH, se používají pravidla překladu adres (NAT).

Zobrazení těchto pravidel překladu adres a informací o připojení pro instance škálovací sady:

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozím kroku, například *myResourceGroup*.
2. V seznamu prostředků vyberte svůj **Nástroj pro vyrovnávání zatížení**, například *myScaleSetLab*.
3. V nabídce na levé straně okna zvolte **Pravidla příchozího překladu adres (NAT)**.

    ![Pravidla příchozího překladu adres umožňují připojení k instancím virtuálních počítačů ve škálovací sadě](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

Pomocí těchto pravidel překladu adres se můžete připojit ke všem virtuálním počítačům ve škálovací sadě. Každá instance virtuálního počítače vypíše cílovou IP adresu a hodnotu portu TCP. Pokud je například cílová IP adresa *104.42.1.19* a port TCP *50001*, připojíte se k instanci virtuálního počítače následujícím způsobem:

- V případě škálovací sady pro Windows se k instanci virtuálního počítače připojte přes protokol RDP na adrese `104.42.1.19:50001`.
- V případě škálovací sady pro Linux se k instanci virtuálního počítače připojte přes SSH pomocí příkazu `ssh azureuser@104.42.1.19 -p 50001`.

Po zobrazení výzvy zadejte přihlašovací údaje, které jste zadali při vytváření škálovací sady v předchozím kroku. Instance škálovací sady jsou normální virtuální počítače, se kterými můžete pracovat normálním způsobem. Další informace o nasazování a spouštění aplikací na instancích škálovací sady najdete v tématu [Nasazení aplikace do škálovací sady virtuálních počítačů](virtual-machine-scale-sets-deploy-app.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte skupinu prostředků, škálovací sadu a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků příslušného virtuálního počítače a kliknete na **Odstranit**.


## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili základní škálovací sadu na webu Azure Portal. Další informace najdete v kurzu věnovaném vytváření a správě škálovacích sad virtuálních počítačů Azure.

> [!div class="nextstepaction"]
> [Vytváření a správa škálovacích sad virtuálních počítačů Azure](tutorial-create-and-manage-powershell.md)