---
title: Zobrazení instančního objektu spravované identity – Azure CLI – Azure AD
description: Podrobné pokyny pro zobrazení instančního objektu spravované identity pomocí azure cli.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298695"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Zobrazení instančního objektu spravované identity pomocí azure cli

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD bez nutnosti pověření ve vašem kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolit [systém přiřazenou identitu na virtuálním počítači](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) nebo [v aplikaci](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Chcete-li spustit příklady skriptu vykreslování jízdou po nesek, máte tři možnosti:
    - Azure [Cloud Shell](../../cloud-shell/overview.md) použijte z webu Azure Portal (viz další část).
    - Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu.
    - [Nainstalujte nejnovější verzi azure cli,](https://docs.microsoft.com/cli/azure/install-azure-cli) pokud dáváte přednost použití místní konzoly příkazového příkazového příkazu a přihlásit se k Azure pomocí`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Zobrazit instanční objekt

Tento následující příkaz ukazuje, jak zobrazit instanční objekt virtuálního zařízení nebo aplikace s povolenou spravovanou identitou. Nahraďte `<VM or application name>` vlastními hodnotami. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Další kroky

Další informace o správě instancí služby Azure AD pomocí azure cli najdete [v tématu az ad sp](/cli/azure/ad/sp).


