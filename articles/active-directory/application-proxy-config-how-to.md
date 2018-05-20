---
title: Postup konfigurace aplikace Proxy aplikace | Microsoft Docs
description: Zjistěte, jak vytvořit aplikaci Proxy aplikace konfigurace v několika jednoduchých krocích
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 299d3df1c9ae82bb9f184e9ffb1dd922013e5e65
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-configure-an-application-proxy-application"></a>Postup konfigurace aplikace Proxy aplikace

Tento článek vám pomůže lépe porozumět konfiguraci aplikaci Proxy aplikace v rámci Azure AD vystavit vaše místní aplikace do cloudu.

## <a name="recommended-documents"></a>Doporučené dokumenty 

Další informace o vytvoření aplikace Proxy aplikace prostřednictvím portálu pro správu a počáteční konfigurace, postupujte podle [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md).

Podrobnosti o konfiguraci konektorů najdete v tématu [povolení Proxy aplikace v portálu Azure](manage-apps/application-proxy-enable.md).

Informace o nahrávání certifikátů a používání vlastní domény najdete v tématu [práce s vlastní domény v Azure AD Application Proxy](manage-apps/application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Vytvoření aplikace nebo nastavení adresy URL.

Pokud postupujete podle kroků v [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md) dokumentace a jsou získávání k chybě při vytváření aplikace, najdete v části Podrobnosti o chybě informace a návrhy, jak opravit aplikaci. Většina chybové zprávy, obsahuje navrhované opravu. Aby se zabránilo běžné chyby, ověřte:

-   Správci s oprávněním k vytvoření aplikace Proxy aplikace

-   Interní adresa URL je jedinečný

-   Externí adresa URL je jedinečný

-   Adresy URL začínat protokolu http nebo https a končit "/"

-   Adresa URL by měla být název domény, nikoli IP adresu

Chybová zpráva by měla zobrazit v pravém horním rohu při vytváření aplikace. Můžete také vybrat na ikonu oznámení naleznete v chybových zprávách.

   ![Oznámení řádku](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Nakonfigurujte konektory nebo konektor skupiny

Pokud máte potíže s konfigurací aplikace z důvodu upozornění o konektorech a konektor skupiny, najdete pokyny k povolení Proxy aplikace podrobnosti o tom, jak stáhnout konektory. Pokud chcete získat další informace o konektory, přečtěte si téma [konektory dokumentaci](manage-apps/application-proxy-connectors.md).

Pokud vaše konektory jsou neaktivní, znamená to, aby byly možné vás zastihnout na službu. To je často protože nejsou otevřené požadované porty. Pokud chcete zobrazit seznam požadované porty, najdete v části předpoklady povolení Proxy aplikace dokumentace.

## <a name="upload-certificates-for-custom-domains"></a>Nahrát certifikáty pro vlastní domény

Vlastní domény umožňují zadejte doménu vaší externí adresy URL. Pokud chcete používat vlastní domény, budete muset nahrát na server certifikát pro tuto doménu. Informace o používání vlastní domény a certifikáty, najdete v části [práce s vlastní domény v Azure AD Application Proxy](manage-apps/application-proxy-configure-custom-domain.md). 

Pokud narazíte na problémy odeslání vašeho certifikátu, vyhledejte chybové zprávy na portálu pro další informace o problému s certifikátem. Běžné problémy s certifikátem patří:

-   Vypršela platnost certifikátu

-   Certifikát je podepsaný svým držitelem

-   Certifikát chybí privátní klíč

Chybová zpráva zobrazit v pravém horním rohu jako pokusíte odešlete certifikát. Můžete také vybrat na ikonu oznámení naleznete v chybových zprávách.

   ![Oznámení řádku](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Další postup
[Publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md)
