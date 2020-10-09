---
title: Application Gateway integrace s Azure Security Center | Microsoft Docs
description: Tato stránka poskytuje informace o tom, jak je Application Gateway Integrovaná do Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76277191"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Přehled integrace mezi Application Gateway a Azure Security Center

Přečtěte si, jak Application Gateway a Security Center lépe chránit prostředky webových aplikací. Firewall webových aplikací služby Application Gateway (WAF) se integruje s [Security Center](../security-center/security-center-intro.md) , aby poskytoval bezproblémové zobrazení, které zabraňuje, detekuje a reaguje na hrozby nechráněných webových aplikací ve vašem prostředí.

## <a name="overview"></a>Přehled

Application Gateway WAF je doporučení Security Center pro ochranu webových aplikací před zneužitím a ohrožení zabezpečení. Webové prostředky s povolenou ochranou pomocí WAF ukazují ve službě Security Center jako doporučení s vysokou závažností. Doporučení pro brány firewall webových aplikací se zobrazují na stránce **Přehled** v části **aplikace**.

![integrace se službou Security Center][1]

Kliknutím na jakékoli doporučení týkající se brány firewall webových aplikací se otevře nová stránka s podrobnostmi o doporučení.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Přidání firewallu webových aplikací do existujícího prostředku

Přejděte na **všechny služby**  >  **zabezpečení a identita**  >  **Security Center** a v **Security Center – přehled**klikněte na **aplikace**. V **Security Centerch aplikacích**tabulka obsahuje seznam aplikací, které Security Center zjištěné ve vašem předplatném.

![webové aplikace][3]

Kliknutím na webovou aplikaci s kritickým problémem získáte stránku **stavu zabezpečení aplikací** . Na následujícím obrázku je webová aplikace, která není chráněna bránou firewall webových aplikací. 

![webové prostředky nejsou chráněny][2]

Kliknutím na **Přidat Firewall webových aplikací** v části **doporučení** otevřete stránku **Přidat Firewall webových aplikací** .

Pokud nemáte existující Application Gateway nebo chcete vytvořit nový, klikněte na **vytvořit nové** a  **vytvořte novou bránu firewall webových aplikací**a klikněte na **Microsoft-Application Gateway**. Provede vás kroky k vytvoření aplikační brány. V tomto okamžiku je webová aplikace přidána jako chráněný prostředek, Security Center nyní sleduje, zda je tento prostředek chráněn bránou firewall webových aplikací. Tím se nepřidá jako člen fondu back-end.

Pokud máte existující Aplikační bránu, můžete ji vybrat v části **použít existující řešení** .

![Stránka pro přidání brány firewall webových aplikací][4]

Přidání webové aplikace do aplikační brány prostřednictvím Security Center nepřidá prostředek jako člena fondu back-end. To je nutné provést přímo na prostředku služby Application Gateway.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Přidání prostředku do existující brány firewall webových aplikací

Přejděte na **všechny služby**  >  **zabezpečení a identita**  >  **Security Center** a v **Security Center – přehled**klikněte na možnost **Partnerská řešení**. Existující brány Application Gateway, které používají Security Center, se zobrazí na stránce **Partnerská řešení** .

![Partnerská řešení][7]

Kliknutím na **propojit aplikaci** otevřete **linku aplikace**. zobrazí se zde možnosti výběru existujících aplikací. Vyberte aplikace, které chcete chránit, a klikněte na **OK**. Tato aplikace nepřidá webovou aplikaci do fondu back-end služby Application Gateway. Tím se prostředky nastaví jako chráněný prostředek, aby ho Security Center mohl sledovat. Chcete-li přidat prostředek jako člena fondu back-end, musí být tato akce provedena v bráně Application Gateway, a to z aktuální stránky, kterou můžete kliknout na **konzolu řešení** , aby se převzala do prostředku služby Application Gateway, kde můžete přidat webovou aplikaci do fondu back-end.

![aplikace partnerských řešení][6]

## <a name="finalize-configuration"></a>Dokončit konfiguraci

Security Center sleduje aplikace přidané do služby Application Gateway jako chráněný prostředek.  Monitoruje stav tohoto prostředku a zajišťuje jeho ochranu pomocí aplikační brány. Dalším krokem je přidání privátní IP adresy, veřejné IP adresy nebo síťové karty virtuálního počítače do back-endového fondu služby Application Gateway. Až to uděláte, zobrazí se další doporučení k **finalizaci ochrany aplikací** , dokud se prostředek nepřidá.

![Stránka pro přidání brány firewall webových aplikací][5]

## <a name="security-alerts"></a>Výstrahy zabezpečení

V rámci Security Center přejděte na **zjišťování**  >  **výstrah zabezpečení**.  Tady najdete WAF výstrahy pro vaše aplikační brány. Výstrahy jsou rozdělené podle pravidla WAF.

![výstrahy zabezpečení][8]

Výběrem pravidla se zobrazí seznam výstrah pro pravidlo konkrétního WAF. Každá výstraha obsahuje další podrobnosti o hledání. Podrobnosti poskytují odkaz na aplikační bránu.
 
![Podrobnosti výstrahy][9]

## <a name="next-steps"></a>Další kroky

Informace o tom, jak povolit bránu firewall webových aplikací v existující aplikační bráně, najdete v tématu [Vytvoření nebo aktualizace Application Gateway Azure pomocí brány firewall webových aplikací](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png