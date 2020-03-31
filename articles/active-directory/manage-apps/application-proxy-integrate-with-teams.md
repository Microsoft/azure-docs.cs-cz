---
title: Přístup k aplikacím proxy aplikací Azure AD v Teams | Dokumenty společnosti Microsoft
description: Pomocí proxy aplikací Azure AD přístup k místní aplikaci prostřednictvím Microsoft Teams.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807764"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Přístup k místním aplikacím prostřednictvím Microsoft Teams

Proxy aplikace Azure Active Directory poskytuje jednotné přihlašování k místním aplikacím bez ohledu na to, kde se vejdete. Microsoft Teams zjednodušuje vaše společné úsilí na jednom místě. Integrace obou dohromady znamená, že vaši uživatelé mohou být produktivní se svými spoluhráči v každé situaci.

Vaši uživatelé můžou do svých kanálů Teams přidávat cloudové aplikace [pomocí karet](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale co sharepointové weby nebo plánovací nástroj hostovaný místně? Aplikační proxy je řešení. Do svých kanálů mohou přidávat aplikace publikované prostřednictvím proxy aplikace pomocí stejných externích adres URL, které vždy používají pro vzdálený přístup ke svým aplikacím. A protože proxy aplikace ověřuje prostřednictvím služby Azure Active Directory, vaši uživatelé získat jednotné přihlašování.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalace konektoru Proxy aplikace a publikování aplikace

Pokud jste tak ještě neučinili, [nakonfigurujte proxy aplikace pro svého klienta a nainstalujte konektor](application-proxy-add-on-premises-application.md). Potom [publikujte místní aplikaci](application-proxy-add-on-premises-application.md) pro vzdálený přístup. Když publikujete aplikaci, poznamenejte si externí adresu URL, protože se používá k přidání aplikace do Teams.

Pokud už máte svoje aplikace publikované, ale nepamatujete si jejich externí adresy URL, vyhledejte je na [webu Azure Portal](https://portal.azure.com). Přihlaste se a přejděte do**aplikací** >  **Azure Active Directory** > Enterprise**Všechny aplikace** > vybrat aplikaci > proxy **aplikace**.

## <a name="add-your-app-to-teams"></a>Přidání aplikace do Teams

Po publikování aplikace prostřednictvím proxy aplikace dejte uživatelům vědět, že ji můžou přidat jako kartu přímo do svých kanálů Teams, a pak je aplikace dostupná všem členům týmu. Nechte je postupovat podle těchto tří kroků:

1. Přejděte na kanál Teams, kam chcete **+** přidat tuto aplikaci, a vyberte, že chcete přidat kartu.

   ![Výběr + pro přidání karty v Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Z možností karty vyberte **Web.**

   ![Výběr webu na obrazovce Přidat kartu](./media/application-proxy-integrate-with-teams/website.png)

1. Pojmenujte kartu a nastavte adresu URL na externí adresu URL proxy aplikace.

   ![Pojmenování karty a přidání externí adresy URL](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Jakmile jeden člen týmu přidá kartu, zobrazí se pro všechny v kanálu. Všichni uživatelé, kteří mají přístup k aplikaci, získají přístup k jednotnému přihlášení s přihlašovacími údaji, které používají pro Microsoft Teams. Všichni uživatelé, kteří nemají přístup k aplikaci, můžou kartu zobrazit v Teams, ale jsou zablokovaní, dokud jim neudáte oprávnění k místní aplikaci a publikované verzi aplikace na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [publikovat místní sharepointové weby](application-proxy-integrate-with-sharepoint-server.md) pomocí proxy aplikace.
- Nakonfigurujte aplikace tak, aby pro externí adresu URL [používaly vlastní domény.](application-proxy-configure-custom-domain.md)
