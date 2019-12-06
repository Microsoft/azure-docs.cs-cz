---
title: Jak najít konkrétní rozhraní API potřebné pro vlastní aplikaci | Microsoft Docs
description: Jak nakonfigurovat oprávnění, která potřebujete pro přístup k určitému rozhraní API ve vlastní vyvíjené aplikaci Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c223df28f8082125d48f1e3619088de5cf2687
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844596"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Vyhledání konkrétního rozhraní API, které je potřeba pro aplikaci vytvořenou pro vlastní použití

Přístup k rozhraním API vyžaduje konfiguraci oborů přístupu a rolí. Pokud chcete zpřístupnit webová rozhraní API pro klientské aplikace, musíte nakonfigurovat obory přístupu a role pro rozhraní API. Pokud chcete, aby klientská aplikace měla přístup k webovému rozhraní API, musíte nakonfigurovat oprávnění pro přístup k rozhraní API v registraci aplikace.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k zveřejnění webových rozhraní API

Když vystavíte webové rozhraní API, rozhraní API se zobrazí v seznamu **Vyberte rozhraní API** při přidávání oprávnění k registraci aplikace. Pokud chcete přidat obory přístupu, postupujte podle kroků uvedených v části [Konfigurace aplikace k vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurace klientské aplikace pro přístup k webovým rozhraním API

Když přidáváte oprávnění k registraci vaší aplikace, můžete **Přidat přístup rozhraní API** k vystaveným webovým rozhraním API. Pokud chcete získat přístup k webovým rozhraním API, postupujte podle kroků uvedených v části [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Další kroky

- [Princip Azure Active Directory manifestu aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
