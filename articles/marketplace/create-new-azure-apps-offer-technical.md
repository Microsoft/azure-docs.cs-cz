---
title: Jak přidat technické podrobnosti pro nabídku aplikací Azure
description: Naučte se, jak přidat technické podrobnosti o nabídce aplikací Azure v partnerském centru.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370080"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Jak přidat technické podrobnosti pro nabídku aplikací Azure

Tento článek popisuje, jak zadat technické údaje, které vám pomůžou připojit se k vašemu řešení Microsoftu pro komerční tržišti. Toto připojení nám umožní zřídit vaši nabídku pro zákazníka, pokud se rozhodne získat a spravovat.

Tuto část dokončete jenom v případě, že vaše nabídka zahrnuje spravovanou aplikaci, která bude vysílat události měření pomocí [rozhraní API pro fakturaci účtované podle objemu dat](partner-center-portal/marketplace-metering-service-apis.md) a bude mít službu, která se ověří pomocí tokenu zabezpečení Azure AD. Další informace najdete v tématu věnovaném [strategiím ověřování služby na webu Marketplace](partner-center-portal/marketplace-metering-service-authentication.md) na různých možnostech ověřování.

## <a name="technical-configuration-offer-level"></a>Technická konfigurace (úroveň nabídky)

Karta **Technická konfigurace** se týká jenom v případě, že vytvoříte spravovanou aplikaci, která generuje události měření pomocí [rozhraní API pro účtované měření na základě webu Marketplace](partner-center-portal/marketplace-metering-service-apis.md). Pokud ano, proveďte následující kroky. V opačném případě přejít na [Další kroky](#next-steps). 

Další informace o těchto polích najdete v tématu [plánování nabídky aplikací Azure pro komerční tržiště](plan-azure-application-offer.md#technical-configuration).

1. Na kartě **Technická konfigurace** zadejte **Azure Active Directory id TENANTA** a **Azure Active Directory ID aplikace** , které se používá k ověření připojení mezi našimi dvěma službami za ověřenou komunikací.

1. Než budete pokračovat na další kartu, vyberte **Uložit koncept** . Přehled plánu:

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit plány pro nabídku aplikací Azure](create-new-azure-apps-offer-plans.md)
