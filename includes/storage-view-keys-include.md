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
Zobrazení a zkopírování přístupových klíčů účtu úložiště nebo připojovacího řetězce z webu Azure Portal:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Klíč** a kliknutím na tlačítko **Kopírovat** zkopírujte klíč účtu.
5. Alternativně můžete zkopírovat celý připojovací řetězec. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec.

    ![Snímek obrazovky znázorňující zobrazení přístupových klíčů na webu Azure Portal](media/storage-view-keys-include/portal-connection-string.png)

Můžete použít buď klíč pro přístup k Azure Storage, ale obecně je vhodné použít první klíč a rezervovat použití druhého klíče při otáčení klíčů.

Chcete-li zobrazit nebo číst přístupové klíče účtu, musí být uživatel správcem služeb nebo musí být přiřazena role RBAC, která zahrnuje **microsoft.storage/storageaccounts/listkeys/action**. Some built-in RBAC roles that include this action are the **Owner**, **Contributor**, and **Storage Account Key Operator Service Role** roles. Další informace o roli správce služby najdete [v tématu Klasické role správce předplatného, role Azure RBAC a role Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Podrobné informace o předdefinovaných rolích pro Azure Storage najdete v části **Úložiště** [ve integrovaných rolích Azure pro Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage).
