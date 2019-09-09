---
title: Rozhraní API pro Microsoft Graph pro PIM (Preview) – Azure Active Directory | Microsoft Docs
description: Poskytuje informace o použití rozhraní API Microsoft Graph pro Azure AD Privileged Identity Management (PIM) (Preview).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c7f42d536880f2578c62c6c4866b21be1cc9dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804554"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>Rozhraní API pro Microsoft Graph pro PIM (Preview)

Pro většinu úloh, které můžete provádět v Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pomocí Azure Portal, můžete také použít [rozhraní api Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview). Tento článek popisuje některé důležité pojmy při použití rozhraní API Microsoft Graph pro PIM. Podrobnosti o rozhraních API Microsoft Graph najdete v referenčních informacích k [rozhraní api Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Rozhraní API pod verzí/beta v Microsoft Graph jsou ve verzi Preview a mohou se měnit. Použití těchto rozhraní API v produkčních aplikacích není podporováno.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li volat rozhraní API Microsoft Graph pro PIM, musíte mít **alespoň jedno** z následujících oprávnění:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Nastavit oprávnění

Aby mohly aplikace volat rozhraní API Microsoft Graph pro PIM, musí mít požadovaná oprávnění. Nejjednodušším způsobem, jak zadat požadovaná oprávnění, je použít [rozhraní pro vyjádření souhlasu Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Nastavení oprávnění v Graph Exploreru

Pokud k otestování volání používáte Průzkumníka grafů, můžete zadat oprávnění v nástroji.

1. Přihlaste se k [aplikaci Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) jako globální správce.

1. Klikněte na **Upravit oprávnění**.

    ![Průzkumník grafů – Změna oprávnění](./media/pim-apis/graph-explorer.png)

1. Vedle oprávnění, která chcete zahrnout, přidejte zaškrtnutí. `PrivilegedAccess.ReadWrite.AzureAD`není zatím k dispozici v Graph Exploreru.

    ![Průzkumník grafů – Změna oprávnění](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klikněte na **Upravit oprávnění** , aby se změny oprávnění projevily.

## <a name="next-steps"></a>Další postup

- [Reference k rozhraní API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
