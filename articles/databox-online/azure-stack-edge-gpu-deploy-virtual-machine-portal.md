---
title: Jak nasadit virtuální počítače na Azure Stack Edge pro přes Azure Portal
description: Naučte se vytvářet a spravovat virtuální počítače na Azure Stack Edge pro prostřednictvím Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: a48abb34a201928185130fbe8fdf9592e77492d9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944949"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro pomocí Azure Portal

Můžete vytvářet a spravovat virtuální počítače na Azure Stack hraničním zařízení pomocí Azure Portal, šablon, Azure PowerShell rutin a prostřednictvím skriptů Azure CLI/Python. Tento článek popisuje, jak vytvořit a spravovat virtuální počítač na zařízení Azure Stack Edge pomocí Azure Portal. 

Tento článek se týká Azure Stackch PROCESORů Edge pro, Azure Stack Edge pro R a Azure Stack hraničních zařízení R. 

> [!IMPORTANT] 
> Doporučujeme povolit vícefaktorové ověřování pro uživatele, který spravuje virtuální počítače nasazené v zařízení z cloudu.
        
## <a name="vm-deployment-workflow"></a>Pracovní postup nasazení virtuálních počítačů

Souhrnný přehled pracovního postupu nasazení je následující:

1. Povolte na zařízení Azure Stack Edge síťové rozhraní pro výpočty. Tím se vytvoří virtuální přepínač na zadaném síťovém rozhraní.
1. Povolte správu cloudu virtuálních počítačů z Azure Portal.
1. Nahrajte virtuální pevný disk na účet Azure Storage pomocí Průzkumník služby Storage. 
1. Pomocí nahraného virtuálního pevného disku si stáhněte VHD na zařízení a vytvořte image virtuálního počítače z virtuálního pevného disku. 
1. Použijte prostředky vytvořené v předchozích krocích:
    1. Image virtuálního počítače, kterou jste vytvořili.
    1. Virtuální přepínač přidružený k síťovému rozhraní, na kterém jste povolili výpočetní výkon.
    1. Podsíť přidružená k virtuálnímu přepínači.

    A vytvořte nebo zadejte následující vložené prostředky:
    1. Název virtuálního počítače, vyberte podporovanou velikost virtuálního počítače, přihlašovací údaje pro virtuální počítač. 
    1. Vytvořte nové datové disky nebo připojte stávající datové disky.
    1. Nakonfigurujte pro virtuální počítač statickou nebo dynamickou IP adresu. Pokud zadáváte statickou IP adresu, vyberte z volné IP adresy v rozsahu podsítě síťového rozhraní, který je povolený pro výpočetní výkon.

    Pomocí výše uvedených prostředků vytvořte virtuální počítač.


## <a name="prerequisites"></a>Požadavky

Než začnete vytvářet a spravovat virtuální počítače na svém zařízení prostřednictvím Azure Portal, ujistěte se, že:

1. Na zařízení Azure Stack Edge pro bylo dokončeno nastavení sítě, jak je popsáno v části [Krok 1: konfigurace zařízení Azure Stack Edge pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. Povolili jste síťové rozhraní pro výpočetní výkon. Tato IP adresa síťového rozhraní se používá k vytvoření virtuálního přepínače pro nasazení virtuálního počítače. V místním uživatelském rozhraní zařízení, pokračujte na **COMPUTE**. Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače.

        > [!IMPORTANT] 
        > Pro výpočetní výkon můžete nakonfigurovat jenom jeden port.

    1. Povolte výpočetní prostředky v síťovém rozhraní. Azure Stack Edge pro vytvoří a spravuje virtuální přepínač odpovídající tomuto síťovému rozhraní.

1. Máte přístup k virtuálnímu pevnému disku se systémem Windows nebo Linux, který použijete k vytvoření image virtuálního počítače pro virtuální počítač, který máte v úmyslu vytvořit.

## <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

Pomocí těchto kroků můžete na zařízení Azure Stack Edge vytvořit virtuální počítač.

### <a name="add-a-vm-image"></a>Přidání image virtuálního počítače

1. Nahrajte VHD na účet Azure Storage. Postupujte podle kroků v části [nahrání VHD pomocí Průzkumník služby Azure Storage](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. V Azure Portal pro zařízení Azure Stack Edge přejít na prostředek Azure Stack Edge. Přejít na **> výpočtů na Edge Virtual Machines**.

    ![Přidat image virtuálního počítače 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Vyberte **Virtual Machines** pro přechod na stránku **Přehled** . **Povolte** správu cloudu virtuálních počítačů.
    ![Přidat image virtuálního počítače 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Prvním krokem je přidání image virtuálního počítače. V předchozím kroku jste už nahráli VHD do účtu úložiště. Tento virtuální pevný disk použijete k vytvoření image virtuálního počítače.

    Vyberte **Přidat obrázek** a stáhněte VHD z účtu úložiště a přidejte ho do zařízení. Proces stahování trvá několik minut v závislosti na velikosti VHD a šířce pásma internetu, která je pro stažení k dispozici. 

    ![Přidat image virtuálního počítače 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. V okně **Přidat obrázek** zadejte následující parametry. Vyberte **Přidat**.


    |Parametr  |Popis  |
    |---------|---------|
    |Stáhnout z objektu BLOB úložiště    |Přejděte do umístění objektu BLOB úložiště v účtu úložiště, kam jste nahráli VHD.         |
    |Stáhnout do    | Automaticky nastaveno na aktuální zařízení, do kterého nasazujete virtuální počítač.        |
    |Uložit obrázek jako      | Název bitové kopie virtuálního počítače, kterou vytváříte z disku VHD, který jste nahráli do účtu úložiště.        |
    |Typ operačního systému     |Jako operační systém pro virtuální pevný disk, který použijete k vytvoření image virtuálního počítače, vyberte Windows nebo Linux.         |
   

    ![Přidat image virtuálního počítače 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Stáhne se virtuální pevný disk a vytvoří se image virtuálního počítače. Dokončení vytvoření bitové kopie trvá několik minut. Zobrazí se oznámení o úspěšném dokončení image virtuálního počítače.

    ![Přidat image virtuálního počítače 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Po úspěšném vytvoření image virtuálního počítače se přidá do seznamu obrázků v okně **imagí** .
    ![Přidat image virtuálního počítače 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Okno **nasazení** aktualizuje, aby označovalo stav nasazení.

    ![Přidat image virtuálního počítače 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Nově přidaný obrázek se zobrazí také na stránce **Přehled** .
    ![Přidat image virtuálního počítače 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Přidání virtuálního počítače

Pomocí těchto kroků vytvoříte virtuální počítač po vytvoření image virtuálního počítače.

1. Na stránce **Přehled** vyberte **Přidat virtuální počítač**.

    ![Přidat virtuální počítač 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Na kartě **základy** zadejte následující parametry.


    |Parametr |Popis  |
    |---------|---------|
    |Název virtuálního počítače     |         |
    |Image     | Vyberte z imagí virtuálních počítačů, které jsou k dispozici na zařízení.        |
    |Velikost     | Vyberte si z [podporovaných velikostí virtuálních počítačů](azure-stack-edge-gpu-virtual-machine-sizes.md).        |
    |Uživatelské jméno     | Použijte výchozí uživatelské jméno *azureuser*.        |
    |Typ ověřování    | Vyberte si z veřejného klíče SSH nebo pomocí uživatelsky definovaného hesla.       |
    |Heslo     | Zadejte heslo pro přihlášení k virtuálnímu počítači. Heslo musí mít délku aspoň 12 znaků a musí splňovat definované požadavky na [složitost](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).        |
    |Potvrzení hesla    | Znovu zadejte heslo.        |


    ![Přidat virtuální počítač 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Vyberte **Další: disky**.

1. Na kartě **disky** připojíte disky k virtuálnímu počítači. 
    
    1. Můžete si vybrat, že se má **vytvořit a připojit nový disk** nebo **připojit existující disk**.

        ![Přidat virtuální počítač 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Vyberte **vytvořit a připojit nový disk**. V okně **vytvořit nový disk** zadejte název disku a velikost v GIB.

        ![Přidat virtuální počítač 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Opakujte výše uvedené pro zpracování a přidejte další disky. Disky se po vytvoření zobrazí na kartě **disky** .

        ![Přidat virtuální počítač 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Až skončíte, vyberte **Další: Sítě**.

1. Na kartě **síť** nakonfigurujete připojení k síti pro svůj virtuální počítač.

    
    |Parametr  |Popis |
    |---------|---------|
    |Virtuální síť    | V rozevíracím seznamu vyberte virtuální přepínač vytvořený na zařízení Azure Stack Edge, pokud jste povolili výpočty v síťovém rozhraní.    |
    |Podsíť     | Toto pole se vyplní automaticky podsítí přidruženou k síťovému rozhraní, na kterém jste povolili výpočetní výkon.         |
    |IP adresa     | Zadejte pro svůj virtuální počítač statickou nebo dynamickou IP adresu. Statická IP adresa by měla být dostupná, volná IP adresa ze zadaného rozsahu podsítě.        |

    ![Přidat virtuální počítač 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Vyberte **Další: zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** zkontrolujte specifikace pro virtuální počítač a vyberte **vytvořit**.

    ![Přidat virtuální počítač 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Spustí se vytváření virtuálního počítače a může trvat až 20 minut. Můžete přejít na **nasazení** a monitorovat vytváření virtuálních počítačů.

    ![Přidat virtuální počítač 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Po úspěšném vytvoření virtuálního počítače se stránka **Přehled** aktualizuje a zobrazí nový virtuální počítač.

    ![Přidat virtuální počítač 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Vyberte nově vytvořený virtuální počítač, který chcete přejít na **virtuální počítače**.

    ![Přidat virtuální počítač 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Pokud si chcete zobrazit podrobnosti, vyberte virtuální počítač. 

    ![Přidat virtuální počítač 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Připojení k virtuálnímu počítači

Postup pro připojení se může lišit v závislosti na tom, jestli jste vytvořili virtuální počítač se systémem Windows nebo Linux. K virtuálním počítačům nasazeným v zařízení se nemůžete připojit prostřednictvím Azure Portal. Chcete-li se připojit k VIRTUÁLNÍmu počítači se systémem Linux nebo Windows, je nutné provést následující kroky.

### <a name="connect-to-linux-vm"></a>Připojení k virtuálnímu počítači se systémem Linux

Pomocí těchto kroků se připojte k virtuálnímu počítači se systémem Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Připojení k virtuálnímu počítači s Windows

Pomocí těchto kroků se připojte k virtuálnímu počítači s Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat zařízení Azure Stack Edge pro, najdete v tématu[použití místního webového uživatelského rozhraní pro správu Azure Stack Edge pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
