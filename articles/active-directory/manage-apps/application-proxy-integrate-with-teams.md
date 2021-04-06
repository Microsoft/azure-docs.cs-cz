---
title: Přístup k aplikacím Aplikace Azure AD proxy v týmech | Microsoft Docs
description: Využijte Azure Proxy aplikací služby AD k přístupu k místní aplikaci prostřednictvím Microsoft Teams.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256605"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Přístup k vašim místním aplikacím prostřednictvím Microsoft Teams

Proxy aplikací služby Azure Active Directory poskytuje jednotné přihlašování k místním aplikacím bez ohledu na to, kde jste. Týmy Microsoftu zjednodušují spolupráci na jednom místě. Integrace obou dvou prostředků znamená, že vaši uživatelé mohou být produktivní s jejich ostatními týmu v jakékoli situaci.

Vaši uživatelé můžou do kanálů týmů přidávat cloudové aplikace [pomocí karet](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ale také informace o sharepointových webech nebo nástrojích pro plánování, které jsou hostované místně? Proxy aplikací je řešení. Můžou přidávat aplikace publikované prostřednictvím proxy aplikací ke svým kanálům pomocí stejných externích adres URL, které vždycky používají pro vzdálený přístup k aplikacím. A vzhledem k tomu, že se proxy aplikace ověřuje prostřednictvím Azure Active Directory, uživatelé získají prostředí s jednotným přihlašováním.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalace konektoru proxy aplikací a publikování aplikace

Pokud jste to ještě neudělali, [nakonfigurujte pro svého tenanta proxy aplikací a nainstalujte konektor](application-proxy-add-on-premises-application.md). Pak [publikujte místní aplikaci](application-proxy-add-on-premises-application.md) pro vzdálený přístup. Když publikujete aplikaci, poznamenejte si externí adresu URL, protože se používá k přidání aplikace do týmů.

Pokud už máte své aplikace publikované, ale nepamatujete si své externí adresy URL, vyhledejte je v [Azure Portal](https://portal.azure.com). Přihlaste se a pak přejděte na **Azure Active Directory**  >  **podnikové aplikace**  >  **všechny aplikace** > vyberte vaši aplikaci > **proxy aplikace**.

## <a name="add-your-app-to-teams"></a>Přidání aplikace do týmů

Po publikování aplikace prostřednictvím proxy aplikací Dejte uživatelům informace o tom, že je můžou přidat jako kartu přímo v kanálech týmů, a pak je aplikace dostupná pro všechny členy týmu, aby je používali. Postupujte podle těchto tří kroků:

1. Přejděte na kanál týmy, kam chcete přidat tuto aplikaci, a vyberte možnost **+** Přidat kartu.

   ![Vyberte + a přidejte do týmů kartu.](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Z možností karty vyberte **Web** .

   ![Výběr webu na obrazovce Přidat kartu](./media/application-proxy-integrate-with-teams/website.png)

1. Zadejte název karty a nastavte adresu URL na externí adresu URL proxy aplikace.

   ![Pojmenujte kartu a přidejte externí adresu URL.](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Jakmile jeden člen týmu přidá kartu, zobrazí se pro všechny v kanálu. Všichni uživatelé, kteří mají přístup k aplikaci, získají přístup s jednotným přihlašováním pomocí přihlašovacích údajů, které používají pro Microsoft Teams. Všichni uživatelé, kteří nemají přístup k aplikaci, mohou zobrazit kartu v týmech, ale budou blokovány, dokud jim neudělíte oprávnění k místní aplikaci a Azure Portal publikované verzi aplikace.

## <a name="next-steps"></a>Další kroky

- Naučte se [publikovat místní weby služby SharePoint](application-proxy-integrate-with-sharepoint-server.md) pomocí proxy aplikací.
- Nakonfigurujte své aplikace tak, aby pro svou externí adresu URL používaly [vlastní domény](application-proxy-configure-custom-domain.md) .
