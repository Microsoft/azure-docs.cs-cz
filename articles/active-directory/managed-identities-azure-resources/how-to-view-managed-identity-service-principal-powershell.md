---
title: Postup zobrazení instanční objekt spravovanou identitu pomocí Powershellu
description: Podrobné pokyny pro zobrazení instanční objekt spravovanou identitu pomocí Powershellu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0ad3a52b837a5f79c9976c4c509e0a8516de1e7d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714102"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Zobrazení instanční objekt spravovanou identitu pomocí Powershellu

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak chcete-li zobrazit instanční objekt spravovanou identitu pomocí Powershellu.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolit [identitou přiřazenou systémem ve virtuálním počítači](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) nebo [aplikace](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Pokud používáte PowerShell místně, je potřeba provést také následující: 
    - Spuštěním příkazu `Login-AzureRmAccount` vytvořte připojení k Azure.
    - Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Spuštěním rutiny `Install-Module -Name PowerShellGet -AllowPrerelease` získejte předběžnou verzi modulu `PowerShellGet` (po spuštění tohoto příkazu možná budete muset pomocí příkazu `Exit` ukončit aktuální relaci PowerShellu, aby se modul `AzureRM.ManagedServiceIdentity` nainstaloval).
    - Spustit `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` nainstalovat zkušební verzi `AzureRM.ManagedServiceIdentity` modulu provádět uživatel přiřazenou spravované identity operace v tomto článku.

## <a name="view-the-service-principal"></a>Zobrazení instanční objekt služby

Tento následující příkaz ukazuje, jak zobrazit instanční objekt služby virtuálního počítače nebo aplikace s identitou přiřazenou systémem povolena. Nahraďte `<VM or application name>` vlastními hodnotami.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Další postup

Další informace o zobrazení objektů služby Azure AD pomocí prostředí PowerShell najdete v tématu [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


