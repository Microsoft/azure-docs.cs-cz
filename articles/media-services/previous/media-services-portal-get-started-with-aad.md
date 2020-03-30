---
title: Začínáme s ověřováním Azure AD pomocí webu Azure Portal| Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí portálu Azure používat přístup k ověřování Azure Active Directory (Azure AD) ke spotřebovávat rozhraní API Azure Media Services.
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
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330664"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Začínáme s ověřováním Azure AD s využitím webu Azure Portal

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Přečtěte si, jak používat portál Azure pro přístup k ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API Azure Media Services.

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud nemáte účet, začněte s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete [v tématu Vytvoření účtu Mediální chod Azure pomocí portálu Azure](media-services-portal-create-account.md).

Při použití ověřování Azure AD s Azure Media Services, máte dvě možnosti ověřování:

- **Ověřování instančního objektu**. Ověřte službu. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které spouštějí služby daemon, služby střední vrstvy nebo naplánované úlohy: webové aplikace, aplikace funkcí, aplikace logiky, api nebo mikroslužbu.
- **Ověření uživatele**. Ověřte osobu, která používá aplikaci k interakci s prostředky Mediálních služeb. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření. Příkladem je aplikace konzoly pro správu používaná oprávněnými uživateli ke sledování úloh kódování nebo živého streamování. 

## <a name="access-the-media-services-api"></a>Přístup k rozhraní Media Services API

Tato stránka umožňuje vybrat metodu ověřování, kterou chcete použít pro připojení k rozhraní API. Stránka také obsahuje hodnoty, které potřebujete pro připojení k rozhraní API.

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte účet Mediálních služeb.
2. Vyberte způsob připojení k rozhraní API mediálních služeb.
3. V části **Connect to Media Services API**vyberte verzi rozhraní API mediálních služeb, ke které se chcete připojit.

## <a name="service-principal-authentication--recommended"></a>Ověření instančního objektu (doporučeno)

Ověří službu pomocí aplikace Azure Active Directory (Azure AD) a tajných kódů. To se doporučuje pro všechny služby střední vrstvy, které volají do rozhraní API mediálních služeb. Příklady jsou webové aplikace, funkce, logic apps, api a mikroslužeb. Toto je doporučená metoda ověřování.

### <a name="manage-your-azure-ad-app-and-secret"></a>Správa aplikace Azure AD a tajných klíče

Správa **aplikace AAD a tajných klíče** umožňuje vybrat nebo vytvořit novou aplikaci Azure AD a generovat tajný klíč. Z bezpečnostních důvodů nelze tajný klíč zobrazit po zavření okna. Aplikace používá ID aplikace a tajný klíč pro ověřování k získání platného tokenu pro mediální služby.

Ujistěte se, že máte dostatečná oprávnění k registraci aplikace s tenantem Azure AD a přiřadit aplikaci k roli ve vašem předplatném Azure. Další informace naleznete v tématu [Požadovaná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Připojení k rozhraní API mediálních služeb

Rozhraní **API connect to Media Services** poskytuje hodnoty, které používáte k připojení aplikace instančního objektu. Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="user-authentication"></a>Ověřování uživatelů

Tuto možnost lze použít k ověření zaměstnance nebo člena služby Azure Active Directory, který používá aplikaci k interakci s prostředky Mediální služby. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření uživatele. Tato metoda ověřování by měla být použita pouze pro aplikace pro správu.

### <a name="connect-to-media-services-api"></a>Připojení k rozhraní API mediálních služeb

Zkopírujte pověření a připojte uživatelskou aplikaci z části **Připojit k rozhraní API mediálních služeb.** Můžete získat textové hodnoty nebo zkopírovat bloky JSON nebo XML.

## <a name="next-steps"></a>Další kroky

Začínáme s [nahráváním souborů do svého účtu](media-services-portal-upload-files.md).
