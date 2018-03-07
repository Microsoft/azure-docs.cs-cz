---
title: " Správa procesu serveru běží v Azure (Resource Manager) | Microsoft Docs"
description: "Tento článek popisuje, jak nastavit procesový server navrácení služeb po obnovení (Resource Manager) v Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Správa procesu serveru běží v Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Classic](./site-recovery-vmware-setup-azure-ps-classic.md)

Během navrácení služeb po obnovení doporučujeme nasadit procesový server v Azure, když je vysoké latenci mezi virtuální síť Azure a v místní síti. Tento článek popisuje, jak můžete nastavit, konfigurovat a spravovat servery proces běží v Azure.

> [!NOTE]
> Tento článek má být použita, pokud jste použili **Resource Manager** jako model nasazení pro virtuální počítače během převzetí služeb při selhání. Pokud jste použili **Classic** jako model nasazení, postupujte podle kroků v [jak nastavit a konfigurovat procesový server navrácení služeb po obnovení (klasické)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Nasadit procesový server na platformě Azure
1. V trezoru > **infrastruktura Site Recovery** (v části "Manage") > **konfigurační servery** (pod nadpisem "Pro VMware a fyzické počítače"), vyberte konfigurační server.
2. Na stránce podrobností konfigurace serveru, které se otevře, klikněte na tlačítko "+ zpracovat serveru.

  ![Přidat server galerie procesu](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  Na **přidat procesový server** vyberte následující hodnoty

  ![Přidat položku Galerie procesového serveru](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Název pole**|**Hodnota**|
|-|-|
|Zvolte, kam chcete procesový server nasadit.|Vyberte hodnotu **nasadit procesový server navrácení služeb po obnovení v Azure** |
|Předplatné|Vyberte předplatné Azure, kde převzal virtuální počítače|
|Skupina prostředků|Můžete vytvořit skupinu prostředků a nasadit server tento proces, nebo můžete nasadit procesový server v existující skupinu prostředků.|
|Umístění|Vyberte datové centrum Azure, do kterého virtuální počítače kde převzetí služeb při selhání do|
|Síť Azure|Vyberte virtuální Network(VNet) Azure, virtuální počítače kde převzetí služeb při selhání do. Pokud se při selhání virtuálního počítače do více virtuálními sítěmi Azure budete potřebovat procesový server nasadit na virtuální síť|

4. Zadejte zbývající vlastnosti u procesového serveru

  ![Přidejte procesový server souhrn](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Název pole**|**Hodnota**|
|-|-|
|Název serveru|Zobrazovaný název a název hostitele pro virtuální počítač serveru procesu|
| Uživatelské jméno|Uživatelské jméno, který se stává správcem na tomto virtuálním počítači|
|Účet úložiště|Název účtu úložiště, kde jsou umístěny virtuální počítač virtuálního disku|
|Podsíť|Podsíť virtuální sítě Azure, ke kterému je připojený virtuální počítač|
| IP adresa|IP adresy, které chcete procesový server předpokládat, že po jeho spuštění|
5. Klikněte na tlačítko OK zahájíte nasazení proces serveru virtuálního počítače.

> [!NOTE]
> Abyste mohli použít tento procesový server navrácení služeb po obnovení, musíte zaregistrovat ji pomocí místní konfigurační server.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrace serveru procesu (spuštění v Azure) konfigurace serveru (s místní)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Upgrade na nejnovější verzi procesového serveru.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Zrušení registrace serveru procesu (spuštění v Azure) z konfigurace serveru (místní)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
