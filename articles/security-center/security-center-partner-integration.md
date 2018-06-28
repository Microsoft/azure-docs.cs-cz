---
title: Integrace řešení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s partnerskými řešeními zvýšit celkové zabezpečení vašich prostředků Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 49558f5a4777818478ee11b96636d87b70e1e156
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335362"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení:** Security Center nabízí zjednodušené zřízení integrovaných partnerských řešení. Pro řešení, jako jsou antimalware a hodnocení ohrožení zabezpečení, může Security Center zřídit potřebného agenta na virtuálních počítačích, a pro zařízení brány firewall může Security Center zařídit většinu požadované konfigurace sítě.
- **Integrované detekce:** Události zabezpečení z partnerských řešení se automaticky shromažďují, agregují a zobrazují v rámci výstrah a incidentů služby Security Center. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocená správa a monitorování stavu:** Zákazníci můžou využít integrované události týkající se stavu k monitorování všech partnerských řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

Mezi integrovaná řešení zabezpečení v současné době patří:

- Ochrana koncových bodů ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection) a [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection))
- Firewall webových aplikací ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) a [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall nové generace ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) a [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Posouzení ohrožení zabezpečení ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Prostředí integrace ochrany koncových bodů se může lišit v závislosti na řešení. Následující tabulka obsahuje další podrobnosti o prostředí jednotlivých řešení:

| Endpoint Protection (Ochrana koncových bodů)               | Platformy                             | Instalace Security Center | Zjišťování Security Center |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (antimalware Microsoftu)                  | Windows Server 2016                   | Ne, součást operačního systému           | Ano                       |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 | Prostřednictvím rozšíření                | Ano                       |
| Trend Micro – všechny verze         | Řada Windows Serverů                 | Ne                           | Ano                       |
| Symantec v12.1.1100+              | Řada Windows Serverů                 | Ne                           | Ano                       |
| McAfee v10+                       | Řada Windows Serverů                 | Ne                           | Ano                       |
| Kaspersky                         | Řada Windows Serverů                 | Ne                           | Ne                        |
| Sophos                            | Řada Windows Serverů                 | Ne                           | Ne                        |



## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete také připojit další zdroje dat zabezpečení, včetně:

- Azure AD Identity Protection
- Počítače spuštěné místně nebo v jiných cloudech
- Řešení zabezpečení s podporou formátu CEF (Common Event Format)
- Microsoft Advanced Threat Analytics

![Integrace partnerských řešení](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Přihlaste se k [portálu Azure](https://azure.microsoft.com/features/azure-portal/).

2. V **nabídce Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

  ![Přehled služby Security Center](./media/security-center-partner-integration/overview.png)

3. V části **Přehled** vyberte **Řešení zabezpečení**.

V části **Řešení zabezpečení** můžete zobrazit informace o stavu integrovaných řešení zabezpečení Azure a provádět základní úlohy správy. Můžete také připojit další typy zdrojů dat zabezpečení, jako jsou upozornění služby Azure Active Directory Identity Protection a protokoly brány firewall ve formátu CEF (Common Event Format).

### <a name="connected-solutions"></a>Připojená řešení

Část **Připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojená ke službě Security Center, a informace o stavu jednotlivých řešení.  

![Připojená řešení](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Další informace najdete v tématu [Správa připojených partnerských řešení](security-center-partner-solutions.md).

### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky vyhledává řešení zabezpečení spuštěná v Azure, která však nejsou připojená ke službě Security Center, a zobrazuje je v části **Zjištěná řešení**. To zahrnuje řešení Azure, jako je [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), i partnerská řešení.

> [!NOTE]
> Funkce Zjištěná řešení vyžaduje úroveň Standard služby Security Center na úrovni předplatného. Další informace o cenových úrovních služby Security najdete na stránce s [cenami](security-center-pricing.md).
>
>

Pod řešením vyberte **PŘIPOJIT**, aby se řešení integrovalo se službou Security Center a vy jste dostávali upozornění na výstrahy zabezpečení.

![Zjištěná řešení](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center také vyhledává řešení nasazená v předplatném, která dokážou přesměrovat protokoly CEF (Common Event Format). Zjistěte, jak ke službě Security Center [připojit řešení zabezpečení](quick-security-solutions.md) využívající protokoly CEF.

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Propojení Microsoft Advanced Threat Analytics a Azure Security Center](security-center-ata-integration.md)
* [Propojení Azure Active Directory Identity Protection a Azure Security Center](security-center-aadip-integration.md)
* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Monitorování partnerských řešení pomocí služby Security Center](security-center-partner-solutions.md). Zjistěte, jak monitorovat stav partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Získejte odpovědi na nejčastější dotazy k používání služby Security Center.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
