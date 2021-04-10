---
title: Začínáme s ověřováním Azure AD
description: Přečtěte si, jak získat přístup k ověřování Azure Active Directory (Azure AD) pro využívání rozhraní Azure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/31/2021
ms.author: inhenkel
ms.openlocfilehash: e45a0eefff3a6a7f7dd37bde3ee3bffaedd93dcd
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166611"
---
# <a name="get-credentials-to-access-media-services-api"></a>Získání přihlašovacích údajů pro přístup k rozhraní Media Services API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Když použijete ověřování Azure AD pro přístup k rozhraní Azure Media Services API, máte dvě možnosti ověřování:

- **Ověřování instančního objektu** (doporučeno)

    Ověří službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy: webové aplikace, aplikace Function App, Logic Apps, rozhraní API nebo mikroslužba.
- **Ověřování uživatelů**

    Ověřte osobu, která aplikaci používá k interakci s Media Servicesmi prostředky. Interaktivní aplikace by měla nejdřív vyzvat uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu, kterou autorizovaní uživatelé používají k monitorování úloh kódování nebo živého streamování. 

Tento článek popisuje kroky pro získání přihlašovacích údajů pro přístup k rozhraní Media Services API. Vyberte z následujících karet.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud účet nemáte, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/).
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services pomocí Azure Portal](account-create-how-to.md).

## <a name="portal"></a>[Azure Portal](#tab/portal/)

### <a name="api-access"></a>API access

Stránka **přístup přes rozhraní API** umožňuje vybrat metodu ověřování, kterou chcete použít pro připojení k rozhraní API. Stránka také poskytuje hodnoty, které potřebujete pro připojení k rozhraní API.

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet Media Services.
2. V levém navigačním panelu vyberte okno **přístup přes rozhraní API** .
3. V části **připojit k Media Services rozhraní API** vyberte verzi rozhraní API Media Services, ke které se chcete připojit (verze v3 je nejnovější verze služby).

### <a name="service-principal-authentication--recommended"></a>Ověřování instančního objektu (doporučeno)

Ověřuje službu pomocí aplikace Azure Active Directory (Azure AD) a tajného klíče. Doporučuje se pro jakékoli služby střední vrstvy, které volají rozhraní Media Services API. Příklady jsou Web Apps, funkce, Logic Apps, rozhraní API a mikroslužby. Toto je doporučená metoda ověřování.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Správa aplikace a tajného kódu v Azure AD

V části **Správa aplikace AAD a tajného klíče** můžete vybrat nebo vytvořit novou aplikaci Azure AD a vygenerovat tajný klíč. Pro účely zabezpečení nelze tajný klíč zobrazit po zavření okna. Aplikace používá ID aplikace a tajný klíč k ověření k získání platného tokenu pro Media Services.

Ujistěte se, že máte dostatečná oprávnění k registraci aplikace v tenantovi Azure AD a přiřazení aplikace k roli v předplatném Azure. Další informace najdete v tématu [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

#### <a name="connect-to-media-services-api"></a>Připojení k rozhraní Media Services API

**Rozhraní API připojit k Media Services** poskytuje hodnoty, které slouží k připojení vaší aplikace instančního objektu. Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

### <a name="user-authentication"></a>Ověřování uživatelů

Tato možnost slouží k ověření zaměstnance nebo člena Azure Active Directory, který používá aplikaci k interakci s prostředky Media Services. Interaktivní aplikace by nejdřív měla uživatele vyzvat k zadání přihlašovacích údajů uživatele. Tato metoda ověřování by se měla používat jenom pro aplikace pro správu.

#### <a name="connect-to-media-services-api"></a>Připojení k rozhraní Media Services API

Zkopírujte přihlašovací údaje pro připojení uživatelské aplikace z oddílu **připojení k Media Services rozhraní API** . Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání, kódování a streamování videí pomocí Media Services V3](stream-files-tutorial-with-api.md).
