---
title: Instalaci sady Visual Studio a připojte se k Azure zásobníku | Microsoft Docs
description: Další kroky potřebné k instalaci sady Visual Studio a připojte se k Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalaci sady Visual Studio a připojte se k Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Použijte sadu Visual Studio pro vytváření a nasazení Azure Resource Manager [šablony](azure-stack-arm-templates.md) v zásobníku Azure. Můžete použít kroků popsaných v tomto článku nainstalujte sadu Visual Studio buď z [Azure zásobníku Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo z založené na Windows externí klienta Pokud jste připojení prostřednictvím [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Tyto kroky v tomto článku jsou pro novou instalaci produktu Visual Studio 2015 Community Edition. Další informace o [koexistence](https://msdn.microsoft.com/library/ms246609.aspx) s jinými verzemi sady Visual Studio.

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

1. Stažení a spuštění [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
2. Vyhledejte **Visual Studio Community 2015 s Microsoft Azure SDK - 2.9.6**, vyberte **přidat**a potom vyberte **nainstalovat**.

    ![Snímek obrazovky WebPI nainstalujte kroků](./media/azure-stack-install-visual-studio/image1.png)

3. Odinstalace **Microsoft Azure PowerShell** který je nainstalován jako součást sady Azure SDK.

    ![Snímek obrazovky Přidat nebo odebrat programy rozhraní pro prostředí Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png)

4. [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)

5. Po dokončení instalace, restartujte operační systém.

## <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

1. Spusťte sadu Visual Studio.

2. Z **zobrazení** nabídce vyberte možnost **Průzkumník cloudu**.

3. V podokně nové vyberte **přidat účet** a přihlaste se pomocí přihlašovacích údajů Azure Active Directory.
    ![Snímek obrazovky Průzkumníka cloudu po přihlášení v a připojený k Azure zásobníku](./media/azure-stack-install-visual-studio/image6.png)

Po přihlášení, můžete [nasazení šablon](azure-stack-deploy-template-visual-studio.md) nebo vyhledejte dostupných typů prostředků a skupin prostředků, můžete vytvořit vlastní šablony.

## <a name="next-steps"></a>Další postup

* [Vývoj šablon pro Azure zásobníku](azure-stack-develop-templates.md)
