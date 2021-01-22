---
title: Začínáme s ověřováním Azure AD pomocí Azure Portal | Microsoft Docs
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
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c2ac03f600f57ae97cebb77e3b67bcd466955d0b
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695403"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Začínáme s ověřováním Azure AD s využitím webu Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Naučte se používat Azure Portal k přístupu k ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API Azure Media Services.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud účet nemáte, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).

Pokud používáte ověřování Azure AD s Azure Media Services, máte dvě možnosti ověřování:

- **Ověřování instančního objektu**. Ověří službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy: webové aplikace, aplikace Function App, Logic Apps, rozhraní API nebo mikroslužba.
- **Ověřování uživatelů**. Ověřte osobu, která aplikaci používá k interakci s Media Servicesmi prostředky. Interaktivní aplikace by měla nejdřív vyzvat uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu, kterou autorizovaní uživatelé používají k monitorování úloh kódování nebo živého streamování. 

## <a name="access-the-media-services-api"></a>Přístup k rozhraní Media Services API

Tato stránka umožňuje vybrat metodu ověřování, kterou chcete použít pro připojení k rozhraní API. Stránka také poskytuje hodnoty, které potřebujete pro připojení k rozhraní API.

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet Media Services.
2. Vyberte, jak se připojit k rozhraní Media Services API.
3. V části **připojit k Media Services rozhraní API** vyberte verzi rozhraní API Media Services, ke které se chcete připojit.

## <a name="service-principal-authentication--recommended"></a>Ověřování instančního objektu (doporučeno)

Ověřuje službu pomocí aplikace Azure Active Directory (Azure AD) a tajného klíče. Doporučuje se pro jakékoli služby střední vrstvy, které volají rozhraní Media Services API. Příklady jsou Web Apps, funkce, Logic Apps, rozhraní API a mikroslužby. Toto je doporučená metoda ověřování.

### <a name="manage-your-azure-ad-app-and-secret"></a>Správa aplikace a tajného kódu v Azure AD

V části **Správa aplikace AAD a tajného klíče** můžete vybrat nebo vytvořit novou aplikaci Azure AD a vygenerovat tajný klíč. Pro účely zabezpečení nelze tajný klíč zobrazit po zavření okna. Aplikace používá ID aplikace a tajný klíč k ověření k získání platného tokenu pro Media Services.

Ujistěte se, že máte dostatečná oprávnění k registraci aplikace v tenantovi Azure AD a přiřazení aplikace k roli v předplatném Azure. Další informace najdete v tématu [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

### <a name="connect-to-media-services-api"></a>Připojení k rozhraní Media Services API

**Rozhraní API připojit k Media Services** poskytuje hodnoty, které slouží k připojení vaší aplikace instančního objektu. Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="user-authentication"></a>Ověřování uživatelů

Tato možnost slouží k ověření zaměstnance nebo člena Azure Active Directory, který používá aplikaci k interakci s prostředky Media Services. Interaktivní aplikace by nejdřív měla uživatele vyzvat k zadání přihlašovacích údajů uživatele. Tato metoda ověřování by se měla používat jenom pro aplikace pro správu.

### <a name="connect-to-media-services-api"></a>Připojení k rozhraní Media Services API

Zkopírujte přihlašovací údaje pro připojení uživatelské aplikace z oddílu **připojení k Media Services rozhraní API** . Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="next-steps"></a>Další kroky

Začněte s [nahráváním souborů na svůj účet](media-services-portal-upload-files.md).
