---
title: Postup nalezení konkrétní API potřebné pro aplikaci zákaznických | Microsoft Docs
description: Jak nakonfigurovat oprávnění, potřebujete získat přístup k dané rozhraní API v vaše vlastní vyvinuté aplikaci Azure AD
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 94f3c68e9a1433d2a433e2a3bbe557b487e7bf75
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332054"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Postup nalezení konkrétní API potřebné pro aplikaci zákaznických

Přístup k rozhraním API vyžadují konfiguraci oborů přístupu a rolí. Pokud chcete vystavit prostředků aplikace webového rozhraní API pro klientské aplikace, budete muset nakonfigurovat obory přístupu a rolí pro rozhraní API. Pokud chcete klientskou aplikaci pro přístup k webové rozhraní API, musíte nakonfigurovat oprávnění pro přístup k rozhraní API v registraci aplikace.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace prostředků aplikace ke zveřejnění webových rozhraní API

Při vystavení webového rozhraní API, rozhraní API se zobrazí v **vybrat rozhraní API** při přidání oprávnění k registraci aplikaci. Chcete-li přidat oborů přístupu, postupujte podle kroků uvedených v [přidání oborů přístupu k vaší aplikaci prostředků](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurace klientskou aplikaci pro přístup k webové rozhraní API

Když přidáte oprávnění registrace aplikace, můžete **přidat přístup pomocí rozhraní API** zveřejněné webovému rozhraní API. Pro přístup k webové rozhraní API, postupujte podle kroků uvedených v [přidat přihlašovací údaje nebo oprávnění pro přístup k webové rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další postup

-   [Integrace aplikací se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Principy manifest aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


