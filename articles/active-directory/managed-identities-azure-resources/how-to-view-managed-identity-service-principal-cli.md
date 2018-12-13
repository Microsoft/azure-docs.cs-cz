---
title: Postup zobrazení instanční objekt spravovanou identitu pomocí Azure CLI
description: Podrobné pokyny pro zobrazení instanční objekt spravovanou identitu pomocí Azure CLI.
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
ms.openlocfilehash: c1e09ac91055cc02fa0ab5c2f4576d6ed6a6d3e7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882477"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Zobrazení instanční objekt spravovanou identitu pomocí Azure CLI

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak chcete-li zobrazit instanční objekt spravovanou identitu pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Povolit [identitou přiřazenou systémem ve virtuálním počítači](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) nebo [aplikace](/azure/app-service/app-service-managed-service-identity#adding-a-system-assigned-identity).
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Pokud budete chtít použít místní konzoly příkazového řádku a přihlášení do Azure s využitím `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Zobrazení instanční objekt služby

Tento následující příkaz ukazuje, jak zobrazit instanční objekt služby virtuálního počítače nebo aplikace s využitím spravované identity povolené. Nahraďte `<VM or application name>` vlastními hodnotami. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Další postup

Další informace o správě instančních objektů Azure AD pomocí Azure CLI najdete v tématu [az ad sp](/cli/azure/ad/sp).


