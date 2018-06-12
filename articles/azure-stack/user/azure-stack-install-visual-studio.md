---
title: Instalaci sady Visual Studio a připojte se k Azure zásobníku | Microsoft Docs
description: Další kroky potřebné k instalaci sady Visual Studio a připojte se k Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295026"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Instalaci sady Visual Studio a připojte se k Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Visual Studio můžete použít k zápisu a nasazení Azure Resource Manager [šablony](azure-stack-arm-templates.md) do protokolů Azure. Kroky v tomto článku vás provede procesem instalace sady Visual Studio na [zásobník Azure](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo externího počítače, pokud máte v úmyslu zásobník Azure prostřednictvím [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Instalace sady Visual Studio

1. Stažení a spuštění [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Otevřete **webové platformy Microsoft**.

3. Vyhledejte **Visual Studio Community 2015 s Microsoft Azure SDK - 2.9.6**. Klikněte na tlačítko **přidat**, a **nainstalovat**.

4. Odinstalace **Microsoft Azure PowerShell** který je nainstalován jako součást sady Azure SDK.

    ![Postup instalace – snímek obrazovky WebPI](./media/azure-stack-install-visual-studio/image1.png) 

5. [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)

6. Po dokončení instalace, restartujte operační systém.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Připojení k Azure zásobníku s Azure AD

1. Spusťte sadu Visual Studio.

2. Z **zobrazení** nabídce vyberte možnost **Průzkumník cloudu**.

3. V podokně nové vyberte **přidat účet** a přihlaste se pomocí přihlašovacích údajů Azure Active Directory (Azure AD).  

    ![Průzkumník cloudu snímek obrazovky po přihlášení a připojení k Azure zásobníku](./media/azure-stack-install-visual-studio/image2.png)

Po přihlášení, můžete [nasazení šablon](azure-stack-deploy-template-visual-studio.md) nebo vyhledejte dostupných typů prostředků a skupin prostředků, můžete vytvořit vlastní šablony.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Připojení k Azure zásobníku se službou AD FS

1. Spusťte sadu Visual Studio.

2. Z **nástroje**, vyberte **možnosti**.

3. Rozbalte položku **prostředí** v **navigačním podokně** a vyberte **účty**.

4. Vyberte **přidat**a zadejte koncový bod uživatele Azure Resource Manager.  
  Pro Azure zásobníku Development kit, že je adresa URL: `https://management.local.azurestack/external`.  
  Pro Azure zásobníku integrované systémy adresu URL: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Vyberte **Přidat**.  

    Visual Studio volá správce prostředků Azure a zjišťuje koncové body, včetně koncový bod ověřování Azure Directory federovaný Services (AD FS).

    ![Průzkumník cloudu snímek obrazovky po přihlášení a připojení k Azure zásobníku](./media/azure-stack-install-visual-studio/image6.png)

6. Vyberte **Průzkumník cloudu** z **zobrazení** nabídky.
7. Vyberte **přidat účet** a přihlaste se pomocí přihlašovacích údajů služby AD FS.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Průzkumník cloudu dotazuje dostupných předplatných. Můžete vybrat jednu předplatné k dispozici ke správě.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Procházení stávajícími prostředky, skupiny prostředků nebo nasazení šablon.

## <a name="next-steps"></a>Další postup

 - Další informace o [koexistence](https://msdn.microsoft.com/library/ms246609.aspx) s jinými verzemi sady Visual Studio.
 - [Vývoj šablon pro Azure zásobníku](azure-stack-develop-templates.md)