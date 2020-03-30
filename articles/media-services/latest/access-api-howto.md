---
title: Začínáme s ověřováním Azure AD
description: Zjistěte, jak získat přístup k ověřování Azure Active Directory (Azure AD) využívat rozhraní API Azure Media Services.
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
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478250"
---
# <a name="get-credentials-to-access-media-services-api"></a>Získání přihlašovacích údajů pro přístup k rozhraní API mediálních služeb  

Když k přístupu k rozhraní API Azure Media Services používáte ověřování Azure AD, máte dvě možnosti ověřování:

- **Ověření instančního objektu** (doporučeno)

    Ověřte službu. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které spouštějí služby daemon, služby střední vrstvy nebo naplánované úlohy: webové aplikace, aplikace funkcí, aplikace logiky, api nebo mikroslužbu.
- **Ověřování uživatelů**

    Ověřte osobu, která používá aplikaci k interakci s prostředky Mediálních služeb. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření. Příkladem je aplikace konzoly pro správu používaná oprávněnými uživateli ke sledování úloh kódování nebo živého streamování. 

Tento článek popisuje postup pro získání přihlašovacích údajů pro přístup k rozhraní API mediálních služeb. Vyberte si z následujících karet.

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud nemáte účet, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete [v tématu Vytvoření účtu Mediální chod Azure pomocí portálu Azure](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

### <a name="api-access"></a>API access 

Přístupová stránka **rozhraní API** umožňuje vybrat metodu ověřování, kterou chcete použít pro připojení k rozhraní API. Stránka také obsahuje hodnoty, které potřebujete pro připojení k rozhraní API.

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte účet Mediálních služeb.
2. Vyberte způsob připojení k rozhraní API mediálních služeb.
3. V části **Connect to Media Services API**vyberte verzi rozhraní API mediálních služeb, ke které se chcete připojit (V3 je nejnovější verze služby).

### <a name="service-principal-authentication--recommended"></a>Ověření instančního objektu (doporučeno)

Ověří službu pomocí aplikace Azure Active Directory (Azure AD) a tajných kódů. To se doporučuje pro všechny služby střední vrstvy, které volají do rozhraní API mediálních služeb. Příklady jsou webové aplikace, funkce, logic apps, api a mikroslužeb. Toto je doporučená metoda ověřování.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Správa aplikace Azure AD a tajných klíče

Správa **aplikace AAD a tajných klíče** umožňuje vybrat nebo vytvořit novou aplikaci Azure AD a generovat tajný klíč. Z bezpečnostních důvodů nelze tajný klíč zobrazit po zavření okna. Aplikace používá ID aplikace a tajný klíč pro ověřování k získání platného tokenu pro mediální služby.

Ujistěte se, že máte dostatečná oprávnění k registraci aplikace s tenantem Azure AD a přiřadit aplikaci k roli ve vašem předplatném Azure. Další informace naleznete v tématu [Požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Připojení k rozhraní API mediálních služeb

Rozhraní **API connect to Media Services** poskytuje hodnoty, které používáte k připojení aplikace instančního objektu. Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

### <a name="user-authentication"></a>Ověřování uživatelů

Tuto možnost lze použít k ověření zaměstnance nebo člena služby Azure Active Directory, který používá aplikaci k interakci s prostředky Mediální služby. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření uživatele. Tato metoda ověřování by měla být použita pouze pro aplikace pro správu.

#### <a name="connect-to-media-services-api"></a>Připojení k rozhraní API mediálních služeb

Zkopírujte pověření a připojte uživatelskou aplikaci z části **Připojit k rozhraní API mediálních služeb.** Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Další kroky

[Kurz: Nahrávání, kódování a streamování videí pomocí mediálních služeb v3](stream-files-tutorial-with-api.md).
