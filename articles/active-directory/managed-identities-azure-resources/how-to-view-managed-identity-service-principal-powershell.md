---
title: Zobrazení instančního objektu spravované identity pomocí prostředí PowerShell – Azure AD
description: Podrobné pokyny pro zobrazení instančního objektu spravované identity pomocí prostředí PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d958ddf977d0f6926863fa661b25e76fe031822d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009283"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Zobrazení instančního objektu spravované identity pomocí PowerShellu

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolte [identitu přiřazenou systémem na virtuálním počítači nebo v](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [aplikaci](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Nainstalovat nejnovější verzi [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Zobrazení instančního objektu

Tento příkaz ukazuje, jak zobrazit instanční objekt virtuálního počítače nebo aplikace s povolenou identitou přiřazení systému. Nahraďte `<VM or application name>` vlastními hodnotami.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Další kroky

Další informace o zobrazení objektů služby Azure AD pomocí prostředí PowerShell najdete v tématu [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


