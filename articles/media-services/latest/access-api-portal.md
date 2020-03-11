---
title: Začínáme s ověřováním Azure AD pomocí Azure Portal
description: Naučte se používat Azure Portal k přístupu k ověřování Azure Active Directory (Azure AD) ke využívání rozhraní API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 03643ea3f96448e5ec9693cd8ce9589472e8e3f2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082740"
---
# <a name="use-the-azure-portal-to-get-credentials-to-access-media-services-api"></a>Použití Azure Portal k získání přihlašovacích údajů pro přístup k rozhraní Media Services API  

Když použijete ověřování Azure AD pro přístup k rozhraní Azure Media Services API, máte dvě možnosti ověřování:

- **Ověřování instančního objektu**. Ověří službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy: webové aplikace, aplikace Function App, Logic Apps, rozhraní API nebo mikroslužba.
- **Ověřování uživatelů**. Ověřte osobu, která aplikaci používá k interakci s Media Servicesmi prostředky. Interaktivní aplikace by měla nejdřív vyzvat uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu používaná autorizovanými uživateli k monitorování úloh kódování nebo živého streamování. 

V tomto článku se dozvíte, jak pomocí Azure Portal získat přihlašovací údaje pro přístup k rozhraní Media Services API.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure. Pokud účet nemáte, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services pomocí Azure Portal](create-account-portal.md).

## <a name="api-access"></a>Přístup přes rozhraní API 

Stránka **přístup přes rozhraní API** umožňuje vybrat metodu ověřování, kterou chcete použít pro připojení k rozhraní API. Stránka také poskytuje hodnoty, které potřebujete pro připojení k rozhraní API.

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet Media Services.
2. Vyberte, jak se připojit k rozhraní Media Services API.
3. V části **připojit k Media Services rozhraní API**vyberte verzi rozhraní API Media Services, ke které se chcete připojit (verze v3 je nejnovější verze služby).

## <a name="service-principal-authentication--recommended"></a>Ověřování instančního objektu (doporučeno)

Ověřuje službu pomocí aplikace Azure Active Directory (Azure AD) a tajného klíče. Doporučuje se pro jakékoli služby střední vrstvy, které volají rozhraní Media Services API. Příklady jsou Web Apps, funkce, Logic Apps, rozhraní API a mikroslužby. Toto je doporučená metoda ověřování.

### <a name="manage-your-azure-ad-app-and-secret"></a>Správa aplikace a tajného kódu v Azure AD

V části **Správa aplikace AAD a tajného klíče** můžete vybrat nebo vytvořit novou aplikaci Azure AD a vygenerovat tajný klíč. Pro účely zabezpečení nelze tajný klíč zobrazit po zavření okna. Aplikace používá ID aplikace a tajný klíč k ověření k získání platného tokenu pro Media Services.

Ujistěte se, že máte dostatečná oprávnění k registraci aplikace v tenantovi Azure AD a přiřazení aplikace k roli v předplatném Azure. Další informace najdete v tématu [požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Připojení k rozhraní Media Services API

**Rozhraní API připojit k Media Services** poskytuje hodnoty, které slouží k připojení vaší aplikace instančního objektu. Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="user-authentication"></a>Ověřování uživatelů

Tato možnost slouží k ověření zaměstnance nebo člena Azure Active Directory, který používá aplikaci k interakci s prostředky Media Services. Interaktivní aplikace by nejdřív měla uživatele vyzvat k zadání přihlašovacích údajů uživatele. Tato metoda ověřování by se měla používat jenom pro aplikace pro správu.

### <a name="connect-to-media-services-api"></a>Připojení k rozhraní Media Services API

Zkopírujte přihlašovací údaje pro připojení uživatelské aplikace z oddílu **připojení k Media Services rozhraní API** . Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání, kódování a streamování videí pomocí Media Services V3](stream-files-tutorial-with-api.md).
