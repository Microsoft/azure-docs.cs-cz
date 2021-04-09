---
title: Jak nakonfigurovat aplikaci proxy aplikace | Microsoft Docs
description: Naučte se, jak vytvořit a nakonfigurovat aplikaci proxy aplikací v několika jednoduchých krocích.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255199"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Postup konfigurace aplikace proxy aplikací

Tento článek vám pomůže pochopit, jak nakonfigurovat aplikaci proxy aplikace v rámci služby Azure AD a zpřístupnit tak místní aplikace v cloudu.

## <a name="recommended-documents"></a>Doporučené dokumenty

Pokud se chcete dozvědět o počátečních konfiguracích a vytváření aplikace proxy aplikací prostřednictvím portálu pro správu, postupujte podle pokynů pro [publikování aplikací s využitím Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md).

Podrobnosti o konfiguraci konektorů najdete v tématu [Povolení proxy aplikací v Azure Portal](application-proxy-add-on-premises-application.md).

Informace o nahrávání certifikátů a používání vlastních domén najdete v tématu [práce s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Vytvoření aplikace nebo nastavení adres URL

Pokud budete postupovat podle kroků v dokumentaci k [publikování aplikací pomocí služby Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md) a při vytváření aplikace dojde k chybě, Projděte si podrobnosti o chybě, kde najdete informace a návrhy, jak aplikaci opravit. Většina chybových zpráv obsahuje navrhovanou opravu. Chcete-li se vyhnout běžným chybám, ověřte:

- Jste správce s oprávněním k vytvoření aplikace proxy aplikace.
- Interní adresa URL je jedinečná.
- Externí adresa URL je jedinečná.
- Adresy URL začínají na http nebo https a končí znakem "/".
- Adresa URL by měla být název domény, ne IP adresa.

Chybová zpráva by se měla zobrazit v pravém horním rohu při vytváření aplikace. Můžete také vybrat ikonu oznámení a zobrazit chybové zprávy.

![Zobrazuje, kde se má najít výzva k zadání oznámení v Azure Portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurace konektorů/skupin konektorů

Pokud máte potíže s konfigurací aplikace z důvodu upozornění na konektory a skupiny konektorů, přečtěte si pokyny týkající se povolení proxy aplikace, kde najdete podrobné informace o tom, jak stahovat konektory. Pokud chcete získat další informace o konektorech, přečtěte si [dokumentaci ke konektorům](application-proxy-connectors.md).

Pokud jsou vaše konektory neaktivní, znamená to, že se k této službě nemůžou spojit. To je často způsobeno tím, že všechny požadované porty nejsou otevřeny. Seznam požadovaných portů najdete v části požadavky v dokumentaci povolení proxy aplikací.

## <a name="upload-certificates-for-custom-domains"></a>Nahrajte certifikáty pro vlastní domény.

Vlastní domény umožňují zadat doménu vašich externích adres URL. Chcete-li použít vlastní domény, je nutné odeslat certifikát pro tuto doménu. Informace o používání vlastních domén a certifikátů najdete v tématu [práce s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).

Pokud se setkáte s problémy při nahrávání certifikátu, vyhledejte chybové zprávy na portálu, kde najdete další informace o problému s certifikátem. Mezi běžné problémy s certifikátem patří:

- Certifikát s vypršenou platností
- Certifikát je podepsaný svým držitelem.
- Certifikát nemá privátní klíč.

Chybová zpráva se zobrazí v pravém horním rohu při pokusu o nahrání certifikátu. Můžete také vybrat ikonu oznámení a zobrazit chybové zprávy.

## <a name="next-steps"></a>Další kroky

[Publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-add-on-premises-application.md)
