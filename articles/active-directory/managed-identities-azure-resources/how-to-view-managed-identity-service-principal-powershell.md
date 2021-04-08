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
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15ccc0faa4d74a2ef95aca00a6257f27b9a209c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91611941"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Zobrazení instančního objektu spravované identity pomocí PowerShellu

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolte [identitu přiřazenou systémem na virtuálním počítači nebo v](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikaci](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Chcete-li spustit ukázkové skripty, máte dvě možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md), který můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spusťte skripty místně pomocí instalace nejnovější verze [Azure PowerShell](/powershell/azure/install-az-ps)a pak se přihlaste k Azure pomocí `Connect-AzAccount` .

## <a name="view-the-service-principal"></a>Zobrazení instančního objektu

Tento příkaz ukazuje, jak zobrazit instanční objekt virtuálního počítače nebo aplikace s povolenou identitou přiřazení systému. Nahraďte `<Azure resource name>` vlastními hodnotami.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <Azure resource name>
```

## <a name="next-steps"></a>Další kroky

Další informace o zobrazení objektů služby Azure AD pomocí prostředí PowerShell najdete v tématu [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).
