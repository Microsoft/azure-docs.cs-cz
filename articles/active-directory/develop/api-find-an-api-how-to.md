---
title: Vyhledání rozhraní API pro vlastní vyvinutou aplikaci | Azure
description: Jak nakonfigurovat oprávnění, která potřebujete pro přístup k určitému rozhraní API ve vlastní vyvinuté aplikaci Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: cd3b21050c6a442284647212fdf7c5707943ffc1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885612"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Jak najít konkrétní rozhraní API potřebné pro vlastní vyvinutou aplikaci

Přístup k rozhraní API vyžadují konfiguraci oborů přístupu a rolí. Pokud chcete zpřístupnit webová rozhraní API aplikace prostředků klientským aplikacím, je třeba nakonfigurovat obory přístupu a role pro rozhraní API. Pokud chcete, aby klientská aplikace pro přístup k webovému rozhraní API, musíte nakonfigurovat oprávnění pro přístup k rozhraní API v registraci aplikace.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k zveřejnění webových rozhraní API

Když zpřístupníte webové rozhraní API, rozhraní API se zobrazí v seznamu **Vybrat rozhraní API** při přidávání oprávnění k registraci aplikace. Chcete-li přidat obory přístupu, postupujte podle kroků popsaných v [části Konfigurace aplikace za účelem vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurace klientské aplikace pro přístup k webovým apim

Když přidáte oprávnění k registraci aplikace, můžete **přidat přístup rozhraní API k** vystaveným webovým rozhraním API. Chcete-li získat přístup k webovým rozhraním API, postupujte podle pokynů uvedených v [části Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Další kroky

- [Principy manifestu aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
