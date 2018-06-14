---
title: Připojování Microsoft Azure Application Gateway do Azure Security Center | Microsoft Docs
description: Zjistěte, jak integrovat aplikační brány a posílení celkového zabezpečení vašich prostředků Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854490"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Připojování Microsoft Azure Application Gateway do Azure Security Center
Tento dokument vám pomůže nakonfigurovat integraci s Centrem zabezpečení a brány firewall Application Gateway webových aplikací (firewall webových aplikací).

## <a name="why-connect-application-gateway"></a>Proč připojovat Application Gateway?
Firewall webových aplikací v aplikační brána chrání webových aplikací z běžných útoky založenými na web jako Injektáž SQL, útoky skriptování mezi weby a hijacks relace. Security Center se integruje s aplikační brány pro zabránění a zjištění ohrožení nechráněné webových aplikací ve vašem prostředí.

## <a name="how-do-i-configure-this-integration"></a>Konfigurování této integrace
Security Center vyhledá dříve nasazené instancí firewall webových aplikací v rámci předplatného. Tato řešení se připojte k Security Center umožňuje integraci výstrahy.

> [!NOTE]
> Aplikaci brány firewall webových aplikací můžete taky zřídit ze služby Security Center **doporučení** jak je popsáno v [přidání brány firewall webových aplikací](security-center-add-web-application-firewall.md).
>
>

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. Na **Microsoft Azure nabídky**, vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

3. V části **přehled**, vyberte **řešení zabezpečení**.

  ![Centrum zabezpečení – přehled](./media/security-center-connect-application-gateway/overview.png)

4. V části **zjištěné řešení** najít Microsoft založené na SaaS brány Firewall webových aplikací a vyberte **CONNECT**.

  ![Zjištěná řešení](./media/security-center-connect-application-gateway/connect.png)

5. **Připojit firewall webových aplikací řešení** otevře.  Vyberte **Connect** integrovat firewall webových aplikací a Security Center.

  ![Připojit řešení WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak integrovat aplikace brány firewall webových aplikací ve službě Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Integrovat řešení zabezpečení ve službě Security Center](security-center-partner-integration.md)
* [Připojování Microsoft Advanced Threat Analytics k Security Center](security-center-ata-integration.md)
* [Připojení ochrany identit Azure Active Directory k Security Center](security-center-aadip-integration.md)
* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md).
* [Monitorování partnerských řešení pomocí služby Security Center](security-center-partner-solutions.md).
* [Azure Security Center – nejčastější dotazy](security-center-faq.md).
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/).
