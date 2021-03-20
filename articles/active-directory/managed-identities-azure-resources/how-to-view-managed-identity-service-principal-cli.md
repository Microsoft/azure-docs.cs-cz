---
title: Zobrazení instančního objektu spravované identity – Azure CLI – Azure AD
description: Podrobné pokyny pro zobrazení instančního objektu spravované identity pomocí Azure CLI.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92892039"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Zobrazení instančního objektu spravované identity pomocí Azure CLI

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, aniž byste museli mít přihlašovací údaje ve svém kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí Azure CLI.

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](overview.md).

- Povolte [identitu přiřazenou systémem na virtuálním počítači nebo v](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikaci](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>Zobrazení instančního objektu

Tento příkaz ukazuje, jak zobrazit instanční objekt virtuálního počítače nebo aplikace se zapnutou spravovanou identitou. Nahraďte `<Azure resource name>` vlastními hodnotami.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Další kroky

Další informace o správě instančních objektů služby Azure AD pomocí rozhraní příkazového řádku Azure najdete v tématu [AZ AD SP](/cli/azure/ad/sp).
