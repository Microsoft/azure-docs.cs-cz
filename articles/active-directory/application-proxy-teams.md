---
title: Přístup k aplikacím Proxy aplikace Azure AD v týmy | Microsoft Docs
description: Přístup k místní aplikace přes Microsoft Teams pomocí proxy aplikace služby Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: f31b5be80ae56ffe4d0f570cb8d25da8cc098009
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Přístup k místním aplikacím přes Teams společnosti Microsoft

Azure Proxy aplikace Active Directory umožňuje jednotné přihlašování k místním aplikacím bez ohledu na to, kde se. Microsoft Teams zjednodušuje vaše spolupráce úsilí na jednom místě. Integraci dvou společně znamená, že vaši uživatelé mohli být produktivní s jejich členy týmu v jakékoliv jiné situaci. 

Uživatele můžete přidat cloudových aplikací k jejich týmy kanály [pomocí karty](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale co o webů SharePoint nebo nástroj plánování, které jsou hostované na místě? Proxy aplikací je řešení. Můžou přidat aplikacím publikovaným pomocí Proxy aplikací k jejich kanály pomocí stejné externí adresy URL vždy používají vzdálený přístup ke své aplikace. A protože Proxy aplikace ověřuje prostřednictvím Azure Active Directory, můžou uživatelé získat jeden možnosti přihlašování.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalace konektoru Proxy aplikace a publikování aplikace

Pokud jste to ještě neudělali, [konfiguraci Proxy aplikace vašeho klienta a instalaci konektoru](manage-apps/application-proxy-enable.md). Potom [publikovat místní aplikace](manage-apps/application-proxy-publish-azure-portal.md) pro vzdálený přístup. Při publikování aplikace, poznamenejte si externí adresu URL vzhledem k tomu, že se používá k přidání aplikace do týmů.

Pokud již máte aplikace publikována, ale nepamatujete jejich externí adresy URL, vyhledejte je [portál Azure](https://portal.azure.com). Přihlaste se a potom přejděte na **Azure Active Directory** > **podnikové aplikace, které** > **všechny aplikace** > vyberte svou aplikaci > **proxy aplikace**.

## <a name="add-your-app-to-teams"></a>Přidání aplikace do týmů

Po publikování aplikace prostřednictvím Proxy aplikace dát uživatelům vědět, že si můžete přidat jako karty přímo v jejich týmy kanály, a pak je k dispozici pro všechny uživatele v týmu používat aplikaci. Kliknul proveďte tyto kroky:

1. Přejděte do týmů kanál, ve které chcete přidat tuto aplikaci a vyberte **+** přidat na kartě.

   ![Vyberte Přidat na kartě](./media/application-proxy-teams/add-tab.png)

2. Vyberte **webu** z kartě Možnosti.

   ![Přidání webu](./media/application-proxy-teams/website.png)

3. Pojmenujte kartě a nastavte adresu URL na externí adresu URL Proxy aplikace. 

   ![Konfigurovat adresu URL a název karty](./media/application-proxy-teams/tab-name-url.png)

Jakmile jeden člen týmu přidá kartu, se zobrazí u všech uživatelů v kanálu. Uživatelé, kteří mají přístup k aplikaci získat přístup jednoho přihlášení pomocí přihlašovacích údajů, které používají pro Teams společnosti Microsoft. Uživatelé, kteří nemají přístup k aplikaci můžete zobrazit na kartě v týmy, ale jsou zablokované bez poskytnutí oprávnění pro místní aplikace a portálu Azure publikovanou verzi aplikace. 

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [publikování webů služby SharePoint místní](application-proxy-enable-remote-access-sharepoint.md) pomocí Proxy aplikace.
- Nakonfigurovat aplikace pro použití [vlastní domény](manage-apps/application-proxy-configure-custom-domain.md) pro jejich externí adresu URL. 
