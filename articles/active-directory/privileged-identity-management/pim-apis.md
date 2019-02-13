---
title: Rozhraní Microsoft Graph API pro Azure AD Privileged Identity Management (PIM) (Preview) | Dokumentace Microsoftu
description: Poskytuje informace o používání rozhraní Microsoft Graph API pro Azure Active Directory Privileged Identity Management (PIM) (Preview).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97b548d199dd98a0f8c788c8c50ba618f721f4ab
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183934"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>Rozhraní Microsoft Graph API PIM (Preview)

Pro většinu úloh můžete provádět v Azure AD Privileged Identity Management (PIM) pomocí webu Azure portal, můžete provést také pomocí [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview). Tento článek popisuje některé důležité koncepty při použití rozhraní API Microsoft Graphu PIM. Podrobnosti o rozhraní API Microsoft Graphu, podívejte se [odkaz na Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Rozhraní API v rámci verze /beta v Microsoft Graphu jsou ve verzi preview a můžou se změnit. Použití těchto rozhraní API v aplikacích v produkčním prostředí nepodporují.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li volat rozhraní API Microsoft Graphu PIM, musíte mít **jeden nebo více** následující oprávnění:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Nastavit oprávnění

Pro aplikace pro volání rozhraní API Microsoft Graphu PIM musí mít požadovaná oprávnění. Nejjednodušší způsob, jak zadat potřebná oprávnění, je použít [rozhraní Azure AD pro udělování souhlasu](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Nastavit oprávnění v Graph Exploreru

Pokud používáte Graph Exploreru k otestování volání, můžete zadat oprávnění v nástroji.

1. Přihlaste se k [Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer) jako globální správce.

1. Klikněte na tlačítko **změnit oprávnění**.

    ![Rozhraní Graph Explorer – upravit oprávnění](./media/pim-apis/graph-explorer.png)

1. Přidání značek zaškrtnutí vedle oprávnění, která chcete zahrnout. `PrivilegedAccess.ReadWrite.AzureAD` ještě není k dispozici v Graph Exploreru.

    ![Rozhraní Graph Explorer – upravit oprávnění](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klikněte na tlačítko **upravit oprávnění** změny se oprávnění.

## <a name="next-steps"></a>Další postup

- [Referenční informace k Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
