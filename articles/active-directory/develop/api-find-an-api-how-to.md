---
title: Vyhledání konkrétního rozhraní API pro vlastní vyvinuté aplikaci potřeba | Dokumentace Microsoftu
description: Jak nakonfigurovat oprávnění budete potřebovat přístup k dané rozhraní API ve vaší vlastní vyvinuté aplikaci Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 306d16dd306fc569181e0334e6674a9c935aac1f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182217"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Vyhledání konkrétního rozhraní API potřebné pro vlastní vyvinuté aplikaci

Přístup k rozhraním API vyžadují konfiguraci oborů přístupu a rolí. Pokud chcete vystavit prostředků aplikace webového rozhraní API pro klientské aplikace, budete muset nakonfigurovat obory přístupu a role pro rozhraní API. Pokud chcete, aby klientská aplikace pro přístup k webovému rozhraní API, budete muset nakonfigurovat oprávnění pro přístup k rozhraní API v registraci aplikace.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k zveřejnění webových rozhraní API

Pokud zveřejňujete webového rozhraní API, rozhraní API zobrazovaných v **vyberte rozhraní API** při přidávání oprávnění registraci aplikace. Chcete-li přidat oborů přístupu, postupujte podle kroků uvedených v [přidání oborů přístup do vaší aplikace prostředků](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurace klientské aplikace pro přístup k webovým rozhraním API

Když přidáte oprávnění k registraci vaší aplikace, můžete **přidat přístup přes rozhraní API** vystavené webového rozhraní API. Chcete-li získat přístup k webovým rozhraním API, postupujte podle kroků uvedených v [přidat přihlašovací údaje nebo oprávnění pro přístup k webovým rozhraním API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="next-steps"></a>Další postup

-   [Integrace aplikací se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Vysvětlení manifestu aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


