---
title: Přihlaste se k classic virtuálního počítače Azure | Microsoft Docs
description: Použití portálu Azure k přihlášení k Windows virtuální počítač vytvořený s modelem nasazení classic.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: bcbebc216dbd63acfb33cf72ba774d088149a3a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Přihlášení k virtuálnímu počítači s Windows pomocí portálu Azure
Na portálu Azure můžete použít **Connect** tlačítko spuštění relace vzdálené plochy a přihlaste se k virtuální počítač s Windows.

Chcete se připojit k virtuální počítač s Linuxem? V tématu [přihlášení do virtuálního počítače se systémem Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Informace o tom, jak se přihlásit k virtuálnímu počítači pomocí modelu Resource Manager najdete v tématu [zde](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači
1. Přihlaste se k portálu Azure.
2. Klikněte na virtuální počítač, který chcete získat přístup. Název je uveden v **všechny prostředky** podokně.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. Klikněte na tlačítko **Connect** na panelu příkazů na řídicím panelu virtuálního počítače.

    ![Připojit ikonu pro virtuální počítač](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Další postup
* Pokud **Connect** tlačítko je neaktivní nebo máte další problémy s připojením ke vzdálené ploše, zkuste resetuje se konfigurace. Klikněte na tlačítko **obnovte vzdálený přístup** na řídicím panelu virtuálního počítače.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Řešení problémů s heslo zkuste resetovat ho. Klikněte na tlačítko **resetovat heslo** podél levého okraje virtuálního počítače v části řídicího panelu, **podporu + Poradce při potížích s**.

    ![Resetování hesla](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Pokud tyto tipy nefungují nebo nejsou, co potřebujete, najdete v části [řešení potíží s vzdálené plochy připojení k systému Windows Azure virtuální počítač](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tento článek vás provede diagnostikou a řešením běžných problémů.
