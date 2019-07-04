---
title: Vyhledání konkrétního rozhraní API pro vlastní vyvinuté aplikaci potřeba | Dokumentace Microsoftu
description: Jak nakonfigurovat oprávnění budete potřebovat přístup k dané rozhraní API ve vaší vlastní vyvinuté aplikaci Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2492a9346585698132e7fd9cfcde068ffd60ebc5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476156"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Vyhledání konkrétního rozhraní API potřebné pro vlastní vyvinuté aplikaci

Přístup k rozhraním API vyžadují konfiguraci oborů přístupu a rolí. Pokud chcete vystavit prostředků aplikace webového rozhraní API pro klientské aplikace, budete muset nakonfigurovat obory přístupu a role pro rozhraní API. Pokud chcete, aby klientská aplikace pro přístup k webovému rozhraní API, budete muset nakonfigurovat oprávnění pro přístup k rozhraní API v registraci aplikace.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k zveřejnění webových rozhraní API

Pokud zveřejňujete webového rozhraní API, rozhraní API zobrazovaných v **vyberte rozhraní API** při přidávání oprávnění registraci aplikace. Chcete-li přidat oborů přístupu, postupujte podle kroků uvedených v [konfigurace aplikace k vystavení webové rozhraní API](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurace klientské aplikace pro přístup k webovým rozhraním API

Když přidáte oprávnění k registraci vaší aplikace, můžete **přidat přístup přes rozhraní API** vystavené webového rozhraní API. Chcete-li získat přístup k webovým rozhraním API, postupujte podle kroků uvedených v [konfigurovat klientskou aplikaci pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Další postup

-   [Vysvětlení manifestu aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


