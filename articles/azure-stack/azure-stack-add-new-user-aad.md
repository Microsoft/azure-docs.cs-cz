---
title: Přidat nový účet klienta Azure zásobníku v Azure Active Directory | Microsoft Docs
description: Po nasazení Microsoft Azure zásobníku Development Kit, musíte vytvořit alespoň jeden klienta uživatelský účet, abyste jej mohli prozkoumat portál klienta.
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
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
ms.locfileid: "29553201"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Přidat nový účet klienta Azure zásobníku v Azure Active Directory
Po [nasazení Azure zásobníku Development Kit](azure-stack-run-powershell-script.md), budete potřebovat uživatelského účtu klienta, abyste mohli prozkoumat portál pro klienty a testování nabídky a plány. Můžete vytvořit účet klienta podle [pomocí portálu Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) nebo [pomocí prostředí PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Vytvoření účtu Azure zásobníku klienta pomocí portálu Azure
Musíte mít předplatné služby Azure pomocí webu Azure portal.

1. Přihlaste se k [Azure](https://portal.azure.com).
2. V levém navigačním panelu Microsoft Azure, klikněte na tlačítko **služby Active Directory**.
3. V seznamu adresáře klikněte na adresář, který chcete použít pro zásobník Azure nebo vytvořte novou.
4. Na této stránce adresáře klikněte na tlačítko **uživatelé**.
5. Klikněte na tlačítko **přidat uživatele**.
6. V **přidat uživatele** průvodce **typ uživatele** vyberte **nového uživatele ve vaší organizaci**.
7. V **uživatelské jméno** zadejte název pro uživatele.
8. V  **@**  , vyberte příslušnou položku.
9. Klikněte na šipku Další.
10. V **profil uživatele** v průvodci zadejte **křestní jméno**, **příjmení**, a **zobrazovaný název**.
11. V **Role** vyberte **uživatele**.
12. Klikněte na šipku Další.
13. Na **získat dočasné heslo** klikněte na tlačítko **vytvořit**.
14. Kopírování **nové heslo**.
15. Přihlaste se k Microsoft Azure se nový účet. Změňte heslo po zobrazení výzvy.
16. Přihlaste se k `https://portal.local.azurestack.external` k novému účtu zobrazíte portál klienta.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Vytvoření účtu Azure zásobníku klienta pomocí prostředí PowerShell
Pokud nemáte předplatné Azure, nelze použít portál Azure k přidání uživatelského účtu klienta. V takovém případě místo toho můžete Azure Active Directory modulu pro Windows PowerShell.

> [!NOTE]
> Pokud používáte k nasazení Azure zásobníku Development Kit Account Microsoft (Live ID), nelze použít AAD prostředí PowerShell k vytvoření účtu klienta. 
> 
> 

1. Nainstalujte [služeb Microsoft Online Services Sign-In Assistant pro IT profesionály RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Nainstalujte [Azure Active Directory modul pro prostředí Windows PowerShell (64bitová verze)](http://go.microsoft.com/fwlink/p/?linkid=236297) a otevřete ji.
3. Spusťte následující rutiny:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Přihlaste se k Microsoft Azure se nový účet. Změňte heslo po zobrazení výzvy.
2. Přihlaste se k `https://portal.local.azurestack.external` k novému účtu zobrazíte portál klienta.

