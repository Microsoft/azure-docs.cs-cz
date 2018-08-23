---
title: Instalace sady Visual Studio a připojte se ke službě Azure Stack | Dokumentace Microsoftu
description: Další kroky potřebné k instalaci sady Visual Studio a připojte se ke službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 2afbea68c017805e9bd7db43b03face0705608b7
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42358743"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalace sady Visual Studio a připojte se ke službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Visual Studio můžete použít k zápisu a nasazení Azure Resource Manageru [šablony](azure-stack-arm-templates.md) do služby Azure Stack. Kroky v tomto článku vás provede instalací sady Visual Studio na [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo externí počítače, pokud máte v plánu služby Azure Stack prostřednictvím [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

1. Stáhněte a spusťte [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Otevřít **instalace webové platformy Microsoft**.

3. Vyhledejte **Visual Studio Community 2015 s Microsoft Azure SDK – 2.9.6**. Klikněte na tlačítko **přidat**, a **nainstalovat**.

4. Odinstalace **prostředí Azure PowerShell** , který je nainstalován jako součást sady Azure SDK.

    ![Postup instalace – snímek obrazovky z instalace webové platformy](./media/azure-stack-install-visual-studio/image1.png) 

5. [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)

6. Po dokončení instalace restartujte operační systém.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Připojení k Azure Stack s Azure AD

1. Spusťte sadu Visual Studio.

2. Z **zobrazení** nabídce vyberte možnost **Průzkumníka cloudu**.

3. V novém podokně, vyberte **přidat účet** a přihlaste se pomocí přihlašovacích údajů Azure Active Directory (Azure AD).  

    ![Snímek obrazovky Průzkumníka cloudu jednou přihlášeni a připojeni ke službě Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Po přihlášení můžete [nasazení šablon](azure-stack-deploy-template-visual-studio.md) nebo procházení dostupných typů prostředků a skupin prostředků, můžete vytvořit vlastní šablony.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Připojení k Azure Stack se službou AD FS

1. Spusťte sadu Visual Studio.

2. Z **nástroje**vyberte **možnosti**.

3. Rozbalte **prostředí** v **navigačním podokně** a vyberte **účty**.

4. Vyberte **přidat**a zadejte koncový bod uživatele Azure Resource Manageru.  
  Pro Azure Stack Development kit, adresa URL je: `https://management.local.azurestack/external`.  
  Pro Azure Stack integrované systémy pro adresu URL je: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Vyberte **Přidat**.  

    Visual Studio volá správce prostředků Azure a zjistí koncových bodů, včetně koncový bod ověřování pro Azure Directory Federated Services (AD FS).

    ![Snímek obrazovky Průzkumníka cloudu jednou přihlášeni a připojeni ke službě Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Vyberte **Průzkumníka cloudu** z **zobrazení** nabídky.
7. Vyberte **přidat účet** a přihlaste se pomocí svých přihlašovacích údajů služby AD FS.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Průzkumník cloudu se dotazuje dostupná předplatná. Můžete vybrat jednu předplatné k dispozici pro správu.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Procházení stávající prostředky, skupiny prostředků nebo nasazení šablon.

## <a name="next-steps"></a>Další postup

 - Další informace o [koexistence](https://msdn.microsoft.com/library/ms246609.aspx) s jinými verzemi sady Visual Studio.
 - [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)