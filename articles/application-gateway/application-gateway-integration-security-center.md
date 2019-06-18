---
title: Integrace brány aplikací pomocí Azure Security Center | Dokumentace Microsoftu
description: Tato stránka obsahuje informace o tom, jak Application Gateway je integrovaná do služby Azure Security Center.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122291"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Přehled integrace mezi Application Gateway a Azure Security Center

Zjistěte, jak služba Application Gateway a Security Center vám pomoct chránit prostředky webové aplikace. Firewall webových aplikací brány aplikací (WAF) se integruje s [Security Center](../security-center/security-center-intro.md) zajistit bezproblémové zobrazení, aby se zabránilo detekci a reakce na hrozby do nechráněné webové aplikace ve vašem prostředí.

## <a name="overview"></a>Přehled

Waf služby Application Gateway chrání webové aplikace před zneužitím a ohrožením zabezpečení je doporučení ve službě Security Center. Povolené webové prostředky, které WAF nechrání zobrazit ve službě security center jako doporučení s vysokou závažností. Doporučení pro brány firewall webových aplikací se zobrazují na **přehled** stránce v části **aplikací**.

![integrace pomocí služby security center][1]

Kliknutím na jakékoli doporučení týkající se brány firewall webových aplikací se otevře novou stránku s podrobnostmi o doporučení.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Přidat firewall webových aplikací pro existující prostředek

Přejděte do **všechny služby** > **zabezpečení + Identita** > **Security Center** a na **Security Center – přehled**, klikněte na tlačítko **aplikací**. Na **Security Center – aplikace**, tabulka obsahuje seznam aplikací, které Security Center zjistila ve vašem předplatném.

![Webové aplikace][3]

Po kliknutí na webovou aplikaci s kritický problém, můžete získat **stav zabezpečení aplikace** stránky. Na obrázku níže, webové aplikace, které nejsou chráněny firewallu webových aplikací. 

![webové prostředky, které nejsou chráněny][2]

Klikněte na tlačítko **přidat firewall webových aplikací** pod **doporučení** otevřít **přidat Firewall webových aplikací** stránky.

Pokud není máte existující aplikační bráně nebo chcete vytvořit nový, klikněte na tlačítko **vytvořit nový** a na **vytvořit nový Web Application Firewall**a klikněte na tlačítko **společnosti Microsoft – služba Application Gateway** . To vás provede kroky k vytvoření služby application gateway. Webová aplikace je v tuto chvíli přidat, protože chráněný prostředek, Security Center teď sleduje tento prostředek je chráněn firewallu webových aplikací. To nepřidá jej jako člena fondu back-endu.

Pokud máte existující aplikační bráně, můžete ho pod **použít existující řešení**

![stránka Přidat firewall webových aplikací][4]

Přidání webové aplikace do služby application gateway pomocí Security Center nepřidá zdroj jako člena fondu back-endu. To je nutné provést na prostředek služby application gateway přímo.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Přidejte prostředek do existující firewallu webových aplikací

Přejděte do **všechny služby** > **zabezpečení + Identita** > **Security Center** a na **Security Center – přehled**, klikněte na tlačítko **partnerská řešení**. Zobrazit existující brány aplikace pracující s Security Center v **partnerských řešení** stránky.

![Partnerská řešení][7]

Klikněte na tlačítko **propojit aplikaci** otevřete **propojit aplikace**, zde jsou uvedeny možnosti vybrat stávající aplikace. Vyberte aplikace, které chcete chránit a klikněte na tlačítko **OK**. To nepřidá webové aplikace k back-endový fond služby application gateway. Tím se nastaví prostředky jako na chráněný prostředek, Security Center můžete sledovat. Bude příslušný materiál přidán jako členem fondu back-endu, musí provést ve službě application gateway, můžete kliknout na aktuální stránce **konzola řešení** přejdete na prostředek služby application gateway ve kterém můžete přidat webové aplikace back-endový fond.

![partnerských řešení aplikací][6]

## <a name="finalize-configuration"></a>Dokončení konfigurace

Security Center zjišťuje aplikace přidat do služby application gateway jako na chráněný prostředek.  Monitoruje stav tohoto prostředku a zajistí, že je chráněný pomocí služby application gateway. Dalším krokem je přidání privátní IP adresa, veřejnou IP adresu nebo síťové karty virtuálního počítače do fondu back-endu služby application gateway. Dokud to se provádí další doporučení **finalizace ochrany aplikací** se nezobrazí, dokud prostředek je přidat.

![stránka Přidat firewall webových aplikací][5]

## <a name="security-alerts"></a>Výstrahy zabezpečení

Přechod na v Security Center **detekce** > **výstrahy zabezpečení**.  Tady najdete výstrahy waf služby application Gateway. Výstrahy jsou rozdělené podle pravidel WAF.

![Výstrahy zabezpečení][8]

Kliknutím na pravidlo poskytne seznam výstrah pro příslušné pravidlo WAF. Každé upozornění zobrazí další podrobnosti o zjištění. Podrobnosti obsahují odkaz ke službě application gateway.
 
![Podrobnosti výstrahy][9]

## <a name="next-steps"></a>Další postup

Informace o povolení firewallu webových aplikací v existující aplikační bráně, najdete v tématu [vytvoření nebo aktualizace služby Azure Application Gateway s firewallem webových aplikací](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png