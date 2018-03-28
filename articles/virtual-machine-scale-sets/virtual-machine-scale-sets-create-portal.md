---
title: Vytvořit sadu škálování virtuálního počítače na portálu Azure | Microsoft Docs
description: Zjistěte, jak rychle vytvořit škálování virtuálních počítačů na portálu Azure
keywords: škálovací sady virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6157f15ef471676424a2f7027c7e9299e2218d4b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2018
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Vytvořit sadu škálování virtuálního počítače na portálu Azure
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto získávání Začínáme článku vytvořit škálování virtuálních počítačů, nastavte na portálu Azure. Můžete také vytvořit škálování s [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) nebo [prostředí Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="log-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.


## <a name="create-virtual-machine-scale-set"></a>Vytvoření škálovací sadu virtuálních počítačů
Můžete nasadit nastavení bitové kopie systému Windows Server nebo Linux image, jako je například RHEL, CentOS, Ubuntu nebo SLES škálování.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyhledejte *škálovací sadu*, zvolte **škálovací sadu virtuálních počítačů**, pak vyberte **vytvořit**.
3. Zadejte název pro sadu škálování, jako například *myScaleSet*.
4. Vyberte požadovaný typ operačního systému, například *Windows Server 2016 Datacenter*.
5. Například zadejte název skupiny prostředků požadované *myResourceGroup*a umístění, jako například *východní USA*.
6. Zadejte své požadované uživatelské jméno a vyberte typ ověřování, která dáváte přednost.
    - A **heslo** musí být nejméně 12 znaků dlouhé a splňovat tři ze čtyř následujících složitost: jedno malé písmeno, jedno velké písmeno, jedna číslice a jeden speciální znak. Další informace najdete v tématu [požadavky na uživatelské jméno a heslo](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Pokud vyberete image disku operačního systému Linux, můžete místo toho zvolíte **veřejný klíč SSH**. Pouze zadejte svůj veřejný klíč, jako třeba *~/.ssh/id_rsa.pub*. Můžete použít prostředí cloudové služby Azure z portálu na [vytvořit a používat klíče SSH](../virtual-machines/linux/mac-create-ssh-keys.md).

7. Zadejte **název veřejné IP adresy**, jako například *myPublicIP*.
8. Zadejte jedinečný **Popisek názvu domény**, jako například *myuniquedns*. Tento popisek DNS tvoří základ plně kvalifikovaný název domény pro vyrovnávání zatížení před byly sadou škálování.
9. Pokud chcete potvrdit, měřítka nastavit možnosti, vyberte **vytvořit**.

    ![Vytvoření škálování virtuálních počítačů, nastavte na portálu Azure](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Připojit k virtuálnímu počítači v sadě škálování
Když vytvoříte škálování nastaveným na portálu, se vytvoří nástroj pro vyrovnávání zatížení. Pravidla pro překlad adres (NAT) sítě slouží k distribuci přenosů na instancí sady škálování pro vzdálené připojení k síti, jako je například protokolu RDP nebo SSH.

Chcete-li zobrazit tyto NAT nastavit pravidla a informace o připojení pro vaše škálování instancí:

1. Vyberte skupinu prostředků, který jste vytvořili v předchozím kroku, jako například *myResourceGroup*.
2. V seznamu zdrojů vyberte vaše **nástroj pro vyrovnávání zatížení**, jako například *myScaleSetLab*.
3. Zvolte **pravidla příchozí NAT** z nabídky na levé straně okna.

    ![Příchozí pravidla NAT umožňují připojit se k instancím sady škálování virtuálního počítače](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

Pro každý virtuální počítač ve škálovací nastavit pomocí těchto pravidel NAT můžete připojit. Každá instance virtuálních počítačů uvádí cílové IP adresy a hodnota portu TCP. Pokud cílová IP adresa je například *104.42.1.19* a TCP port je *50001*, připojit k instanci virtuálního počítače takto:

- Pro sadu škálování Windows připojte k instanci virtuálního počítače pomocí protokolu RDP na `104.42.1.19:50001`
- Pro sadu škálování Linux připojte k instanci virtuálního počítače pomocí protokolu SSH na `ssh azureuser@104.42.1.19 -p 50001`

Po zobrazení výzvy zadejte při vytváření škálovací sadu přihlašovacích údajů, které jste zadali v předchozím kroku. Instance škálovací sady jsou regulární virtuálních počítačů, které mohou komunikovat s normální. Další informace o tom, jak nasadit a spouštět aplikace na vašem škálování instancí sady najdete v tématu [nasazení aplikace na sady škálování virtuálního počítače](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již nepotřebujete, odstraňte skupinu prostředků, sady škálování virtuálního počítače a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků příslušného virtuálního počítače a kliknete na **Odstranit**.


## <a name="next-steps"></a>Další postup
V tomto získávání Začínáme článku vytvořit základní škálování nastavit na portálu Azure. Pokud chcete zlepšit škálovatelnost a automatizaci, rozšiřte svou škálovací sadu pomocí některého z těchto článků s postupy:

- [Nasazení aplikace do škálovací sady virtuálních počítačů](virtual-machine-scale-sets-deploy-app.md)
- Automatické škálování pomocí [Azure PowerShellu](virtual-machine-scale-sets-autoscale-powershell.md), [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md) nebo webu [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Použití automatických upgradů operačního systému pro instance virtuálních počítačů ve škálovací sadě](virtual-machine-scale-sets-automatic-upgrade.md)
