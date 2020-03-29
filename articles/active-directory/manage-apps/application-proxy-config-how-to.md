---
title: Konfigurace aplikace Proxy aplikace | Dokumenty společnosti Microsoft
description: V několika jednoduchých krocích se dozvíte, jak vytvořit a nakonfigurovat aplikaci proxy aplikace
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807858"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Konfigurace aplikace Proxy aplikace

Tento článek vám pomůže pochopit, jak nakonfigurovat aplikaci proxy aplikací v rámci Azure AD vystavit místní aplikace do cloudu.

## <a name="recommended-documents"></a>Doporučené dokumenty

Chcete-li se dozvědět o počátečníkonfigurace a vytvoření aplikace proxy aplikace prostřednictvím portálu pro správu, postupujte podle [publikování aplikací pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md).

Podrobnosti o konfiguraci konektorů najdete [v tématu Povolení proxy aplikace na webu Azure Portal](application-proxy-add-on-premises-application.md).

Informace o nahrávání certifikátů a používání vlastních domén najdete [v tématu Práce s vlastními doménami v proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Vytvoření aplikace/nastavení adres URL

Pokud používáte kroky v publikovat aplikace pomocí dokumentace [proxy aplikace Azure AD](application-proxy-add-on-premises-application.md) a zobrazuje se chyba při vytváření aplikace, podívejte se na podrobnosti o chybě pro informace a návrhy, jak opravit aplikaci. Většina chybových zpráv obsahuje navrhovanou opravu. Chcete-li se vyhnout běžným chybám, ověřte:

- Jste správce s oprávněním k vytvoření aplikace Proxy aplikace
- Interní adresa URL je jedinečná.
- Externí adresa URL je jedinečná
- Adresy URL začínají protokolem http nebo https a končí "/"
- Adresa URL by měla být název domény, nikoli IP adresa

Při vytváření aplikace by se měla v pravém horním rohu zobrazit chybová zpráva. Můžete také vybrat ikonu oznámení a zobrazit chybové zprávy.

![Ukazuje, kde najít výzvu oznámení na webu Azure Portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurace skupin konektorů/konektorů

Pokud máte potíže s konfigurací aplikace z důvodu upozornění na konektory a skupiny konektorů, naleznete v článku pokyny k povolení proxy aplikace podrobnosti o stažení konektorů. Pokud se chcete dozvědět více o konektory, naleznete v [dokumentaci konektory](application-proxy-connectors.md).

Pokud jsou konektory neaktivní, znamená to, že se nemohou dostat ke službě. To je často proto, že všechny požadované porty nejsou otevřeny. Seznam požadovaných portů naleznete v části s požadavky v povolení dokumentace proxy aplikace.

## <a name="upload-certificates-for-custom-domains"></a>Nahrání certifikátů pro vlastní domény

Vlastní domény umožňují určit doménu externích adres URL. Chcete-li používat vlastní domény, musíte nahrát certifikát pro tuto doménu. Informace o používání vlastních domén a certifikátů najdete [v tématu Práce s vlastními doménami v proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).

Pokud narazíte na problémy s nahráváním certifikátu, vyhledejte na portálu další informace o problému s certifikátem. Mezi běžné problémy s certifikáty patří:

- Certifikát, jehož platnost vypršela
- Certifikát je podepsán svým držitelem
- Certifikát umezuje soukromý klíč.

Při pokusu o nahrání certifikátu se v pravém horním rohu zobrazí chybová zpráva. Můžete také vybrat ikonu oznámení a zobrazit chybové zprávy.

## <a name="next-steps"></a>Další kroky

[Publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-add-on-premises-application.md)
