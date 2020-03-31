---
title: Rozhraní API microsoft graphu pro PIM (preview) – Azure AD | Dokumenty společnosti Microsoft
description: Obsahuje informace o používání rozhraní API Microsoft Graphu pro správu privilegovaných identit (PIM) (Preview) služby Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da6bffbc54bfa6e9c39ddace665eb7cfec58614
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75638659"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Rozhraní API aplikace Microsoft Graph pro správu privilegovaných identit (preview)

Všechny úlohy správy privilegovaných identit můžete provádět pomocí [rozhraní API microsoft graphu](https://developer.microsoft.com/graph/docs/concepts/overview) pro Azure Active Directory. Tento článek popisuje důležité koncepty pro použití rozhraní API aplikace Microsoft Graph pro správu privilegovaných identit.

Podrobnosti o rozhraních API microsoft graphu najdete v [referenční příručce rozhraní API pro privilegované identity Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> Rozhraní API v rámci /beta verze v Microsoft Graphu jsou ve verzi Preview a mohou se změnit. Použití těchto api v produkčních aplikacích není podporováno.
>
> Přestože jsou všechny operace PIM podporovány prostřednictvím našich příkazů rozhraní API graphu, ke schvalování žádostí o aktivaci používáme jiný systém. Graph API pro schválení je v současné době vyvíjen a bude vydána v příštích několika měsících.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li volat rozhraní API aplikace Microsoft Graph pro správu privilegovaných identit, musíte mít **jedno nebo více** z následujících oprávnění:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Nastavení oprávnění

Aby aplikace mohly volat rozhraní API aplikace Microsoft Graph pro správu privilegovaných identit, musí mít požadovaná oprávnění. Nejjednodušší způsob, jak určit požadovaná oprávnění, je použití [rámce souhlasu Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Nastavení oprávnění v Průzkumníkovi grafů

Pokud k testování hovorů používáte Průzkumník a graf, můžete v nástroji zadat oprávnění.

1. Přihlaste se k [Průzkumníku grafů](https://developer.microsoft.com/graph/graph-explorer) jako globální správce.

1. Klepněte na **tlačítko Změnit oprávnění**.

    ![Průzkumník grafů – změna oprávnění](./media/pim-apis/graph-explorer.png)

1. Zaškrtněte políčka vedle oprávnění, která chcete zahrnout. `PrivilegedAccess.ReadWrite.AzureAD`v aplikaci Graph Explorer ještě není k dispozici.

    ![Průzkumník grafů – změna oprávnění](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Chcete-li použít změny oprávnění, klepněte na **tlačítko Změnit oprávnění.**

## <a name="next-steps"></a>Další kroky

- [Odkaz na rozhraní API pro privilegované identity Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
