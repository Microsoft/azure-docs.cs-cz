---
title: Přidat nový účet tenanta služby Azure Stack v Azure Active Directory | Dokumentace Microsoftu
description: Po nasazení Microsoft Azure Stack Development Kit, musíte vytvořit uživatelský účet alespoň jednoho tenanta, abyste mohli zkoumat portál pro klienty.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: fa8c8da34a687edd1bd92c1d516183ee5d3e1bd0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240116"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Přidat nový účet tenanta služby Azure Stack v Azure Active Directory

Po [nasazení Azure Stack Development Kit](azure-stack-run-powershell-script.md), uživatelský účet tenanta budete potřebovat, můžete zkoumat portál pro klienty a testování vaší nabídky a plány. Můžete vytvořit účet tenanta podle [pomocí webu Azure portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) nebo [pomocí prostředí PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Vytvořit účet tenanta služby Azure Stack pomocí webu Azure portal

Musíte mít předplatné Azure, pomocí webu Azure portal.

1. Přihlaste se k [Azure](https://portal.azure.com).
2. V levém navigačním panelu vyberte **služby Active Directory** a přejděte do adresáře, který chcete použít pro službu Azure Stack, nebo vytvořte novou.
3. Vyberte **Azure Active Directory** > **uživatelé** > **nového uživatele**.

    ![Uživatelé – všichni uživatelé stránce zvýrazněnou nového uživatele](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Na **uživatele** stránce, vyplňte požadované informace.

    ![Přidání nového uživatele, uživatel stránka s informace o uživateli](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Název (povinné).** První a poslední název nového uživatele. Například Mary Parker.
    - **Uživatelské jméno (povinné).** Uživatelské jméno nového uživatele. Například, mary@contoso.com.
        Součást domény uživatelské jméno musí používat buď počáteční výchozí název domény, <_názevvašídomény_>. onmicrosoft.com, nebo vlastní název domény, třeba contoso.com. Další informace o tom, jak vytvořit vlastního názvu domény najdete v tématu [přidání vlastního názvu domény do Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Profil.** Volitelně můžete přidat další informace o uživateli. Později můžete také přidat informace o uživateli. Další informace o přidání informace o uživateli, naleznete v tématu [postup přidání nebo změně informací profilu uživatele](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Role adresáře.**  Zvolte **uživatele**.

5. Zkontrolujte **zobrazit heslo** a zkopírujte automaticky vytvořené heslo součástí **heslo** pole. Toto heslo budete potřebovat pro počáteční proces přihlašování.

6. Vyberte **Vytvořit**.

    Uživatel je vytvořen a přidán do vašeho tenanta Azure AD.

7. Přihlaste se k portálu Microsoft Azure nový účet. Změna hesla po zobrazení výzvy.
8. Přihlaste se k `https://portal.local.azurestack.external` nový účet, chcete-li zobrazit portál pro klienty.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Vytvořit účet tenanta služby Azure Stack pomocí Powershellu

Pokud nemáte předplatné Azure, nemůžete použít na webu Azure portal k přidání uživatelského účtu tenanta. V takovém případě místo toho můžete Azure Active Directory modulu pro Windows PowerShell.

> [!NOTE]
> Pokud používáte k nasazení Azure Stack Development Kit Account Microsoft (Live ID), nelze použít AAD PowerShell k vytvoření účtu tenanta. 
> 
> 

1. Nainstalujte [Microsoft Online Services Pomocníka pro IT profesionály RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Nainstalujte [modulu Azure Active Directory pro Windows PowerShell (64bitová verze)](https://go.microsoft.com/fwlink/p/?linkid=236297) a otevřete ho.
3. Spuštěním následující rutiny:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Přihlaste se k Microsoft Azure pomocí nového účtu. Změna hesla po zobrazení výzvy.
2. Přihlaste se k `https://portal.local.azurestack.external` nový účet, chcete-li zobrazit portál pro klienty.

