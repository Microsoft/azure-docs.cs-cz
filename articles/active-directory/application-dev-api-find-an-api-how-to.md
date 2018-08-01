---
title: Vyhledání konkrétního rozhraní API pro vlastní vyvinuté aplikaci potřeba | Dokumentace Microsoftu
description: Jak nakonfigurovat oprávnění budete potřebovat přístup k dané rozhraní API ve vaší vlastní vyvinuté aplikaci Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: f15e84d6efa30d2e66c7ff1d92551bece94938e7
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363342"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Vyhledání konkrétního rozhraní API potřebné pro vlastní vyvinuté aplikaci

Přístup k rozhraním API vyžadují konfiguraci oborů přístupu a rolí. Pokud chcete vystavit prostředků aplikace webového rozhraní API pro klientské aplikace, budete muset nakonfigurovat obory přístupu a role pro rozhraní API. Pokud chcete, aby klientská aplikace pro přístup k webovému rozhraní API, budete muset nakonfigurovat oprávnění pro přístup k rozhraní API v registraci aplikace.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k vystavení webové rozhraní API

Pokud zveřejňujete webového rozhraní API, rozhraní API zobrazovaných v **vyberte rozhraní API** při přidávání oprávnění registraci aplikace. Chcete-li přidat oborů přístupu, postupujte podle kroků uvedených v [přidání oborů přístup do vaší aplikace prostředků](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurace klientské aplikace pro přístup k webovým rozhraním API

Když přidáte oprávnění k registraci vaší aplikace, můžete **přidat přístup přes rozhraní API** vystavené webového rozhraní API. Chcete-li získat přístup k webovým rozhraním API, postupujte podle kroků uvedených v [přidat přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další postup

-   [Integrace aplikací se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Vysvětlení manifestu aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


