---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521284"
---
Zobrazení a zkopírování přístupových klíčů účtu úložiště nebo připojovacího řetězce z Azure Portal:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Klíč** a kliknutím na tlačítko **Kopírovat** zkopírujte klíč účtu.
5. Alternativně můžete zkopírovat celý připojovací řetězec. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec.

    ![Snímek obrazovky znázorňující zobrazení přístupových klíčů v Azure Portal](media/storage-view-keys-include/portal-connection-string.png)

Pro přístup k Azure Storage můžete použít libovolný klíč, ale obecně je dobrým zvykem použít první klíč a při střídání klíčů rezervovat použití druhého klíče.

Chcete-li zobrazit nebo číst přístupové klíče účtu, musí být uživatel buď správcem služby, nebo musí být přiřazena role RBAC, která zahrnuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Mezi předdefinované role RBAC, které zahrnují tuto akci, patří role **vlastníka**, **přispěvatele**a **role služby operátora klíče účtu úložiště** . Další informace o roli správce služby najdete v tématu [role správců pro klasický odběr, role Azure RBAC a role Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Podrobné informace o předdefinovaných rolích pro Azure Storage najdete v části **úložiště** v [předdefinovaných rolích Azure pro službu Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage).
