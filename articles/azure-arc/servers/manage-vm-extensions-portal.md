---
title: Povolit rozšíření virtuálního počítače z Azure Portal
description: Tento článek popisuje, jak nasadit rozšíření virtuálních počítačů na servery s podporou ARC Azure běžícími v hybridních cloudových prostředích z Azure Portal.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 48d7d4085dce893d94436fe0c6be32cfeea9cda3
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359081"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Povolit rozšíření virtuálních počítačů Azure z Azure Portal

V tomto článku se dozvíte, jak nasadit a odinstalovat rozšíření virtuálních počítačů Azure, které podporují servery s podporou ARC Azure, do hybridního počítače se systémem Linux nebo Windows prostřednictvím Azure Portal.

> [!NOTE]
> Rozšíření virtuálního počítače Key Vault (Preview) nepodporuje nasazení z Azure Portal, jenom pomocí rozhraní příkazového řádku Azure, Azure PowerShell nebo pomocí šablony Azure Resource Manager.

## <a name="enable-extensions-from-the-portal"></a>Povolení rozšíření z portálu

Rozšíření virtuálních počítačů můžete použít k použití ARC pro serverový počítač spravovaný pomocí Azure Portal.

1. V prohlížeči přejdete na [Azure Portal](https://portal.azure.com).

2. Na portálu přejděte na **servery – Azure ARC** a ze seznamu vyberte svůj hybridní počítač.

3. Vyberte **rozšíření** a pak **Přidat**. V seznamu dostupných rozšíření vyberte požadované rozšíření a postupujte podle pokynů v průvodci. V tomto příkladu nasadíme rozšíření Log Analytics VM.

    ![Vybrat rozšíření virtuálního počítače pro vybraný počítač](./media/manage-vm-extensions/add-vm-extensions.png)

    Následující příklad ukazuje instalaci rozšíření Log Analytics virtuálního počítače z Azure Portal:

    ![Nainstalovat rozšíření virtuálního počítače Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    K dokončení instalace je nutné zadat ID a primární klíč pracovního prostoru. Pokud nejste obeznámeni s tím, jak tyto informace najít, přečtěte si téma [získání ID a klíče pracovního prostoru](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Po potvrzení požadovaných informací vyberte **vytvořit**. Zobrazí se souhrn nasazení a můžete zkontrolovat stav nasazení.

>[!NOTE]
>I když je možné dávkovat více rozšíření a zpracovat je, nainstalují se sériově. Po dokončení první instalace rozšíření dojde k pokusu o instalaci dalšího rozšíření.

## <a name="uninstall-extension"></a>Odinstalace rozšíření

Z Azure Portal můžete odebrat jednu nebo více rozšíření ze serveru s povoleným ARC. Chcete-li odebrat rozšíření, proveďte následující kroky.

1. V prohlížeči přejdete na [Azure Portal](https://portal.azure.com).

2. Na portálu přejděte na **servery – Azure ARC** a ze seznamu vyberte svůj hybridní počítač.

3. Zvolte **rozšíření** a pak vyberte rozšíření ze seznamu nainstalovaných rozšíření.

4. Vyberte možnost **odinstalovat** a když se zobrazí výzva k ověření, vyberte **Ano** a pokračujte.

## <a name="next-steps"></a>Další kroky

- Rozšíření virtuálních počítačů můžete nasadit, spravovat a odebírat pomocí šablon [Azure CLI](manage-vm-extensions-cli.md), [powershellu](manage-vm-extensions-powershell.md)nebo [Azure Resource Manager](manage-vm-extensions-template.md).

- Informace o řešení potíží najdete v [Průvodci pro řešení potíží s rozšířeními virtuálních počítačů](troubleshoot-vm-extensions.md).