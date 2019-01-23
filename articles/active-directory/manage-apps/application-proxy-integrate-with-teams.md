---
title: Přístup k aplikacím Proxy aplikací Azure AD v Teams | Dokumentace Microsoftu
description: Pomocí Proxy aplikací Azure AD pro přístup k místní aplikaci pomocí Microsoft Teams.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 91273ad2a938dce693323f0c53b571eb112d3888
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478412"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Přístup k místním aplikacím přes Microsoft Teams

Azure Active Directory Application Proxy umožňuje jednotné přihlašování k místním aplikacím bez ohledu na to, kde jsou. Microsoft Teams zjednodušuje váš spolupráci úsilí na jednom místě. Integrace dvou společně znamená, že vaši uživatelé mohli být produktivní s ostatními členy týmu v každé situaci. 

Uživatelé mohou přidat cloudové aplikace na svých kanálů Teams [pomocí karet](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale co Sharepointových webech nebo plánovací nástroj, který je hostovaný místně? Proxy aplikací je řešení. Můžou přidat aplikace do svých kanálů pomocí stejné externí adresy URL vždy používají vzdálený přístup k jejich aplikace publikované prostřednictvím Proxy aplikací. A protože Proxy aplikace ověřuje prostřednictvím Azure Active Directory, uživatelé získají jednotné přihlašování.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Nainstalujte konektor Proxy aplikací a publikování aplikace

Pokud jste tak dosud neučinili, [nakonfigurujte Proxy aplikací pro vašeho tenanta a nainstalujte konektor](application-proxy-add-on-premises-application.md). Potom [publikování aplikace v místním](application-proxy-add-on-premises-application.md) pro vzdálený přístup. Když publikujete aplikaci, poznamenejte si externí adresu URL vzhledem k tomu, že se používá k přidání aplikace do Teams.

Pokud už máte aplikace publikována, ale nepamatujete jejich externí adresy URL, vyhledejte je [webu Azure portal](https://portal.azure.com). Přihlaste se a přejděte na adresu **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** > vyberte svou aplikaci >  **Proxy aplikací**.

## <a name="add-your-app-to-teams"></a>Přidání aplikace do Teams

Po publikování aplikace prostřednictvím Proxy aplikací, umožněte svým uživatelům vědět, že ji můžou přidat jako karta přímo v jejich kanálů Teams a pak je k dispozici pro všechny uživatele v týmu používat aplikace. Požádejte představují tyto tři kroky:

1. Přejděte do kanálu Teams, kde chcete přidat tuto aplikaci a vyberte **+** přidejte kartu.

   ![Vyberte možnost přidat na kartě](./media/application-proxy-integrate-with-teams/add-tab.png)

2. Vyberte **webu** z možnosti tabulátoru.

   ![Přidat web](./media/application-proxy-integrate-with-teams/website.png)

3. Pojmenujte kartu a nastavte adresu URL na externí adresu URL Proxy aplikací. 

   ![Konfigurovat název karty a adresu URL](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Jakmile jeden člen týmu přidá na kartu, zobrazí se se pro všechny uživatele v kanálu. Všichni uživatelé, kteří mají přístup k ní získat přístup jednotné přihlašování pomocí přihlašovacích údajů, které používají pro Microsoft Teams. Všichni uživatelé, kteří nemají přístup k aplikaci můžete zobrazit na kartě v týmech, ale jsou blokovány, dokud je poskytnete oprávnění k místní aplikaci a Azure portal publikovanou verzi aplikace. 

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [publikovat místní Sharepointové weby](application-proxy-integrate-with-sharepoint-server.md) pomocí Proxy aplikace.
- Konfigurace vaší aplikace, které používají [vlastních domén](application-proxy-configure-custom-domain.md) pro jejich externí adresu URL. 
