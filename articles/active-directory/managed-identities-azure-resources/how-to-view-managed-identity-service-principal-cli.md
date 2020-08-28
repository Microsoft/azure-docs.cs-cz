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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8b54f4d64810fbbb6a3a0fe0eb8083640376eecb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020554"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Zobrazení instančního objektu spravované identity pomocí Azure CLI

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, aniž byste museli mít přihlašovací údaje ve svém kódu. 

V tomto článku se dozvíte, jak zobrazit instanční objekt spravované identity pomocí Azure CLI.

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolte [identitu přiřazenou systémem na virtuálním počítači nebo v](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [aplikaci](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Chcete-li spustit příklady skriptu rozhraní příkazového řádku, máte tři možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal (viz další oddíl).
    - Použijte vložený Azure Cloud Shell pomocí tlačítka "vyzkoušet", které je umístěné v pravém horním rohu každého bloku kódu.
    - Pokud upřednostňujete použití místní konzoly CLI a přihlásíte se k Azure pomocí nástroje, [nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) .`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Zobrazení instančního objektu

Tento příkaz ukazuje, jak zobrazit instanční objekt virtuálního počítače nebo aplikace se zapnutou spravovanou identitou. Nahraďte `<VM or application name>` vlastními hodnotami. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Další kroky

Další informace o správě instančních objektů služby Azure AD pomocí rozhraní příkazového řádku Azure najdete v tématu [AZ AD SP](/cli/azure/ad/sp).


