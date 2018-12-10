---
title: Jak nakonfigurovat aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit aplikaci Proxy aplikací konfigurace v několika jednoduchých krocích
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: a7a5c097f0400d89398cef66eeb7bd0ce8d5e97b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132427"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Jak nakonfigurovat aplikaci Proxy aplikací

Tento článek pomůže pochopit, jak nakonfigurovat aplikaci Proxy aplikací v Azure AD k vystavení vašich místních aplikací do cloudu.

## <a name="recommended-documents"></a>Doporučené dokumenty 

Další informace o tvorby aplikace Proxy aplikací prostřednictvím portálu pro správu a počáteční konfiguraci, postupujte [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md).

Podrobnosti o konfiguraci konektorů najdete v tématu [povolení Proxy aplikace na webu Azure Portal](application-proxy-add-on-premises-application.md).

Informace o nahrávání certifikátů a používání vlastních domén najdete v tématu [práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Vytvoření nastavení aplikace nebo adresy URL

Pokud postupujete kroky v [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md) dokumentaci a jsou zobrazuje chyba vytváření aplikace, najdete v podrobnostech o chybě informace a návrhy k vyřešení aplikace. Většina chybové zprávy zahrnují navrhované opravy. Aby se zabránilo běžné chyby, zkontrolujte:

-   Jste správce s oprávněním k vytvoření aplikace Proxy aplikací

-   Interní adresa URL je jedinečný

-   Externí adresa URL je jedinečný

-   Adresy URL začínat http nebo https a končí "/"

-   Adresa URL by měl být název domény, ne IP adresy

Když vytvoříte aplikaci, by měl v pravém horním rohu zobrazí chybová zpráva. Můžete také vybrat ikonu oznámení naleznete v chybových zprávách.

   ![Oznámení řádku](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurace skupiny konektorů a konektorů

Pokud máte potíže s konfigurací aplikace z důvodu upozornění o konektorech a skupiny konektorů, pokyny pro povolení Proxy aplikace podrobnosti o tom, jak stáhnout konektory. Pokud chcete další informace o konektorech najdete v článku [konektory dokumentaci](application-proxy-connectors.md).

Pokud vaše konektory jsou neaktivní, to znamená, že nemůže kontaktovat službu. Často je to kvůli nejsou všechny požadované porty otevřené. Zobrazit seznam požadované porty, najdete v části požadavky na povolení Proxy aplikací, dokumentace ke službě.

## <a name="upload-certificates-for-custom-domains"></a>Nahrát certifikáty pro vlastní domény

Vlastní domény umožňují zadat doménou externí adresy URL. Pokud chcete použít vlastní domény, budete muset nahrát na server certifikát pro tuto doménu. Informace o používání vlastních domén a certifikátů najdete v tématu [práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md). 

Pokud narazíte na problémy odeslání vašeho certifikátu, vyhledejte chybové zprávy na portálu pro další informace o problému s certifikátem. Běžné problémy s certifikátem patří:

-   Certifikát s prošlou platností

-   Certifikát je podepsaný svým držitelem

-   Certifikát neobsahuje privátní klíč

Při pokusu o odeslání certifikátu, zobrazí chybová zpráva v pravém horním rohu. Můžete také vybrat ikonu oznámení naleznete v chybových zprávách.

   ![Oznámení řádku](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Další postup
[Publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md)
