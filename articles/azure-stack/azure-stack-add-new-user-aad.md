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
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c312658750c1e9ef024a837ccc16e5cd5be8a5ef
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35901126"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Přidat nový účet tenanta služby Azure Stack v Azure Active Directory
Po [nasazení Azure Stack Development Kit](azure-stack-run-powershell-script.md), uživatelský účet tenanta budete potřebovat, můžete zkoumat portál pro klienty a testování vaší nabídky a plány. Můžete vytvořit účet tenanta podle [pomocí webu Azure portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) nebo [pomocí prostředí PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Vytvořit účet tenanta služby Azure Stack pomocí webu Azure portal
Musíte mít předplatné Azure, pomocí webu Azure portal.

1. Přihlaste se k [Azure](https://portal.azure.com).
2. V levém navigačním panelu Microsoft Azure, klikněte na tlačítko **služby Active Directory**.
3. V seznamu adresářů klikněte na adresář, který chcete použít pro službu Azure Stack nebo vytvořte novou.
4. Na této stránce adresáře klikněte na tlačítko **uživatelé**.
5. Klikněte na **Přidat uživatele**.
6. V **přidat uživatele** průvodce **typ uživatele** klikněte na položku **nového uživatele ve vaší organizaci**.
7. V **uživatelské jméno** zadejte název pro daného uživatele.
8. V **@** vyberte příslušnou položku.
9. Klikněte na šipku Další.
10. V **profilu uživatele** stránku průvodce, zadejte **křestní jméno**, **příjmení**, a **zobrazovaný název**.
11. V **Role** klikněte na položku **uživatele**.
12. Klikněte na šipku Další.
13. Na **získat dočasné heslo** klikněte na **vytvořit**.
14. Kopírovat **nové heslo**.
15. Přihlaste se k Microsoft Azure pomocí nového účtu. Změna hesla po zobrazení výzvy.
16. Přihlaste se k `https://portal.local.azurestack.external` nový účet, chcete-li zobrazit portál pro klienty.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Vytvořit účet tenanta služby Azure Stack pomocí Powershellu
Pokud nemáte předplatné Azure, nemůžete použít na webu Azure portal k přidání uživatelského účtu tenanta. V takovém případě místo toho můžete Azure Active Directory modulu pro Windows PowerShell.

> [!NOTE]
> Pokud používáte k nasazení Azure Stack Development Kit Account Microsoft (Live ID), nelze použít AAD PowerShell k vytvoření účtu tenanta. 
> 
> 

1. Nainstalujte [Microsoft Online Services Pomocníka pro IT profesionály RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Nainstalujte [modulu Azure Active Directory pro Windows PowerShell (64bitová verze)](http://go.microsoft.com/fwlink/p/?linkid=236297) a otevřete ho.
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

