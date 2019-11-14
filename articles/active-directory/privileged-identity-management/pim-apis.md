---
title: Rozhraní API pro Microsoft Graph pro PIM (Preview) – Azure AD | Microsoft Docs
description: Poskytuje informace o použití rozhraní API Microsoft Graph pro Azure AD Privileged Identity Management (PIM) (Preview).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: aea9fbb14d3892419af104c44c8bfd5528eef421
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021917"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Rozhraní API pro Microsoft Graph pro Privileged Identity Management (Preview)

Všechny úlohy Privileged Identity Management můžete provádět pomocí [Microsoft Graph rozhraní API](https://developer.microsoft.com/graph/docs/concepts/overview) pro Azure Active Directory. Tento článek popisuje důležité koncepty pro používání rozhraní Microsoft Graph API pro Privileged Identity Management.

Podrobnosti o rozhraních API Microsoft Graph najdete v referenčních informacích k [rozhraní api Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Rozhraní API pod verzí/beta v Microsoft Graph jsou ve verzi Preview a mohou se měnit. Použití těchto rozhraní API v produkčních aplikacích není podporováno.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li volat rozhraní API Microsoft Graph pro Privileged Identity Management, je nutné mít **minimálně jedno** z následujících oprávnění:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Nastavení oprávnění

Aby mohly aplikace volat rozhraní API Microsoft Graph pro Privileged Identity Management, musí mít požadovaná oprávnění. Nejjednodušším způsobem, jak zadat požadovaná oprávnění, je použít [rozhraní pro vyjádření souhlasu Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Nastavení oprávnění v Graph Exploreru

Pokud k otestování volání používáte Průzkumníka grafů, můžete zadat oprávnění v nástroji.

1. Přihlaste se k [aplikaci Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) jako globální správce.

1. Klikněte na **Upravit oprávnění**.

    ![Průzkumník grafů – Změna oprávnění](./media/pim-apis/graph-explorer.png)

1. Zaškrtněte políčka vedle oprávnění, která chcete zahrnout. `PrivilegedAccess.ReadWrite.AzureAD` ještě není v Graph Exploreru k dispozici.

    ![Průzkumník grafů – Změna oprávnění](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klikněte na **Upravit oprávnění** , aby se změny oprávnění projevily.

## <a name="next-steps"></a>Další kroky

- [Reference k rozhraní API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
