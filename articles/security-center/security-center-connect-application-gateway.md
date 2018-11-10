---
title: Připojení Microsoft Azure Application Gateway ke službě Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak integrovat Application Gateway a Azure Security Center řešeními zvýšit celkové zabezpečení vašich prostředků.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: acb010b0169fd876bf540f7a4115dbabfeda923a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227191"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Připojení Microsoft Azure Application Gateway ke službě Azure Security Center
Tento dokument vám umožní nakonfigurovat integraci s Application Gateway firewall webových aplikací (WAF) a Security Center.

## <a name="why-connect-application-gateway"></a>Proč připojení Application Gateway?
WAF ve službě Application Gateway chrání webové aplikace z common webovými útoky, jako jsou Injektáž SQL, útoky skriptování napříč weby a únos relací. Security Center umožňuje integrací s bránou Application Gateway, aby se předešlo a detekce hrozeb pro nechráněné webové aplikace ve vašem prostředí.

## <a name="how-do-i-configure-this-integration"></a>Jak nakonfigurovat tuto integraci?
Security Center vyhledává dříve nasazené instance WAF v rámci předplatného. Tato řešení můžete připojte ke službě Security Center pro integraci výstrah.

> [!NOTE]
> Waf služby Application Gateway se dá zřídit také ze služby Security Center **doporučení** jak je popsáno v [přidat firewall webových aplikací](security-center-add-web-application-firewall.md).
>
>

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. V **nabídce Microsoft Azure** vyberte **Security Center**.

3. V části **kontrolu zabezpečení prostředků**vyberte **řešení zabezpečení**.

  ![Přehled služby Security Center](./media/security-center-connect-application-gateway/overview.png)

4. V části **zjištěná řešení** vyhledejte Microsoft firewallu webových aplikací SaaS a vyberte **připojit**.

  ![Zjištěná řešení](./media/security-center-connect-application-gateway/connect.png)

5. **Připojit řešení WAF** otevře.  Vyberte **připojit** integrovat WAF a Security Center.

  ![Připojit řešení WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak integrovat waf služby Application Gateway ve službě Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Integrace řešení zabezpečení v Security Center](security-center-partner-integration.md)
* [Propojení Microsoft Advanced Threat Analytics ke službě Security Center](security-center-ata-integration.md)
* [Připojení služby Azure Active Directory Identity Protection ke službě Security Center](security-center-aadip-integration.md)
* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md).
* [Monitorování partnerských řešení pomocí služby Security Center](security-center-partner-solutions.md).
* [Azure Security Center – nejčastější dotazy](security-center-faq.md).
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/).
