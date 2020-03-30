---
title: Integrace aplikační brány s Azure Security Center | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje informace o tom, jak je aplikační brána integrovaná do Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277191"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Přehled integrace mezi aplikační bránou a Centrem zabezpečení Azure

Zjistěte, jak služba Application Gateway a Security Center pomáhají chránit prostředky webových aplikací. Brána firewall webových aplikací (APPLICATION gateway firewall) se integruje s [Security Center](../security-center/security-center-intro.md) poskytovat bezproblémové zobrazení pro prevenci, detekci a reakci na hrozby nechráněných webových aplikací ve vašem prostředí.

## <a name="overview"></a>Přehled

Aplikace Gateway WAF je doporučení v Centru zabezpečení pro ochranu webových aplikací před zneužitím a zranitelností. Webové prostředky, které nejsou chráněny waf zobrazit v centru zabezpečení jako doporučení s vysokou závažností. Doporučení pro brány firewall webových aplikací jsou zobrazena na stránce **Přehled** v části **Aplikace**.

![integrace s bezpečnostním centrem][1]

Kliknutím na všechna doporučení týkající se brány firewall webových aplikací se otevře nová stránka s podrobnostmi o doporučení.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Přidání brány firewall webové aplikace do existujícího prostředku

Přejděte na**Centrum zabezpečení** **zabezpečení + identity** >  **a** > na **centrum zabezpečení – přehled**, klepněte na **položku Aplikace**. V **Centru zabezpečení – aplikace**obsahuje tabulka seznam aplikací, které centrum zabezpečení zjistilo ve vašem předplatném.

![webové aplikace][3]

Kliknutím na webovou aplikaci s kritickým problémem získáte stránku **Stav zabezpečení aplikace.** Na obrázku níže, webová aplikace, která není chráněna bránou firewall webové aplikace. 

![webové prostředky nejsou chráněny][2]

Kliknutím na **Přidat bránu firewall webové aplikace** v části **Doporučení** otevřete **stránku Přidat bránu firewall webové aplikace.**

Pokud nemáte existující aplikační bránu nebo chcete vytvořit novou, klepněte na **tlačítko Vytvořit nový** a v **příkazu Vytvořit novou bránu firewall webových aplikací**a klepněte na položku Microsoft – **brána aplikací**. Tím provedete kroky k vytvoření aplikační brány. V tomto okamžiku je vaše webová aplikace přidána jako chráněný prostředek, Security Center nyní sleduje, že tento prostředek je chráněn bránou firewall webové aplikace. To nepřidá jako člen fondu back-endu.

Pokud máte existující aplikační bránu, můžete ji zvolit v části **Použít existující řešení**

![Stránka pro přidání brány firewall webové aplikace][4]

Přidání webové aplikace do brány aplikace prostřednictvím Centra zabezpečení nepřidá prostředek jako člena back-endového fondu. To je nutné provést přímo u prostředku brány aplikace.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Přidání prostředku do existující brány firewall webové aplikace

Přejděte na **Centrum** > **zabezpečení zabezpečení + identity** > **Security Center** a na centru zabezpečení **– přehled**, klikněte na **partnerská řešení**. Existující brány aplikací centra zabezpečení se zobrazují na stránce **Partnerská řešení.**

![partnerská řešení][7]

Kliknutím na **odkaz aplikace** otevřete **Link Aplikace**, zde máte možnost vybrat existující aplikace. Vyberte aplikace, které chcete chránit, a klepněte na tlačítko **OK**. To nepřidá webovou aplikaci do back-endového fondu aplikační brány. Tím nastavíte prostředky jako chráněný prostředek, aby jej centrum zabezpečení mohlo sledovat. Chcete-li přidat prostředek jako člen back-endového fondu, je nutné to provést na bráně aplikace, z aktuální stránky můžete kliknout na **konzolu řešení,** která má být převedena do prostředku brány aplikace, kde můžete přidat webovou aplikaci do fondu back-endu.

![partnerské řešení aplikace][6]

## <a name="finalize-configuration"></a>Dokončení konfigurace

Security Center sleduje aplikace přidané do aplikační brány jako chráněný prostředek.  Monitoruje stav tohoto prostředku a zajišťuje, že je chráněn aplikační bránou. Dalším krokem je přidání privátní IP, veřejné IP nebo NIC virtuálního počítače do back-endového fondu aplikační brány. Dokud se tak neprovádí další doporučení **Finalize ochrany aplikace** se zobrazí, dokud prostředek je přidán.

![Stránka pro přidání brány firewall webové aplikace][5]

## <a name="security-alerts"></a>Výstrahy zabezpečení

V centru zabezpečení přejděte na**upozornění zabezpečení** **DETECTION** > .  Zde najdete waf upozornění pro vaše aplikační brány. Výstrahy jsou rozděleny podle pravidla WAF.

![výstrahy zabezpečení][8]

Výběrpravidla poskytne seznam výstrah pro dané pravidlo WAF. Každá výstraha zobrazuje další podrobnosti o nálezu. Podrobnosti poskytují odkaz na bránu aplikace.
 
![podrobnosti výstrahy][9]

## <a name="next-steps"></a>Další kroky

Informace o povolení brány firewall webových aplikací na existující bráně aplikace najdete v části [Vytvoření nebo aktualizace brány aplikace Azure pomocí brány firewall webové aplikace](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png