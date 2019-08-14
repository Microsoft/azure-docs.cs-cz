---
title: Přidání brány firewall webových aplikací v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení **přidejte bránu firewall webových aplikací** a dokončete **ochranu aplikací**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706172"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Přidání brány firewall webových aplikací v Azure Security Center
Azure Security Center může doporučit, abyste přidali Firewall webových aplikací (WAF) od partnera Microsoftu k zabezpečení webových aplikací. Tento dokument vás provede příkladem použití tohoto doporučení.

WAF doporučení se zobrazuje pro všechny veřejné IP adresy (buď IP adresa na úrovni instance nebo IP s vyrovnáváním zatížení), které mají přidruženou skupinu zabezpečení sítě s otevřenými vstupními webovými porty (80 443).

Security Center doporučuje zřídit WAF, které vám pomůžou chránit před útoky, které cílí na vaše webové aplikace na virtuálních počítačích, a v externích App Servicech prostředích (přihlašovacích mechanismů) nasazených v rámci plánu [izolované](https://azure.microsoft.com/pricing/details/app-service/windows/) služby. Plán Isolated hostuje aplikace v privátním, vyhrazeném prostředí Azure a je ideální pro aplikace, které vyžadují zabezpečená připojení k místní síti nebo dodatečný výkon a škálování. Kromě vaší aplikace, která se nachází v izolovaném prostředí, musí mít aplikace vyrovnávání zatížení externí IP adresy. Další informace o pomocném mechanismu řízení najdete v [dokumentaci k App Service Environment](../app-service/environment/intro.md).

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není pokrokový průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V části **doporučení**vyberte **zabezpečená webová aplikace pomocí brány firewall webových aplikací**.
   ![Zabezpečená webová aplikace][1]
2. V části **zabezpečení webových aplikací pomocí brány firewall webových aplikací**vyberte webovou aplikaci. Otevře se okno **Přidat Firewall webových aplikací** .
   ![Přidání brány firewall webových aplikací][2]
3. Můžete použít existující bránu firewall webových aplikací, pokud je k dispozici, nebo můžete vytvořit novou. V tomto příkladu nejsou k dispozici žádná stávající WAF, takže vytvoříme WAF.
4. Pokud chcete vytvořit WAF, vyberte řešení ze seznamu integrovaných partnerů. V tomto příkladu vybereme **Barracuda Firewall webových aplikací**.
5. Otevře se **Brána Firewall webových aplikací Barracuda** , která vám poskytne informace o partnerském řešení. Vyberte **Vytvořit**.

   ![Okno s informacemi o bráně firewall][3]

6. Otevře se **Nová brána firewall webových aplikací** , kde můžete provádět kroky **Konfigurace virtuálních počítačů** a poskytovat **WAF informace**. Vyberte **Konfigurace virtuálního počítače**.
7. V části **Konfigurace virtuálního počítače**zadejte informace požadované k aktivaci virtuálního počítače, na kterém běží WAF.

   ![Konfigurace virtuálního počítače][4]
   
8. Vraťte se k **novému firewallu webových aplikací** a vyberte **WAF informace**. V části **WAF informace**nakonfigurujete vlastní WAF. Krok 7 vám umožní nakonfigurovat virtuální počítač, na kterém běží WAF, a krok 8 vám umožní zřídit WAF sám sebe.

## <a name="finalize-application-protection"></a>Ukončení ochrany aplikací
1. Vraťte se k **doporučení**. Po vytvoření WAF se vygenerovala nová položka, která se nazývá **finalizace aplikace Protection**. Tato položka vám poskytne informace o tom, že je potřeba dokončit proces vlastního zapojení WAF v rámci Azure Virtual Network tak, aby mohla aplikace chránit.

   ![Ukončení ochrany aplikací][5]

2. Vyberte **finalizace ochrany aplikací**. Otevře se nové okno. Uvidíte, že je k dispozici webová aplikace, která potřebuje přesměrovat provoz.
3. Vyberte webovou aplikaci. Otevře se okno, které vám nabídne kroky pro dokončení instalace firewallu webových aplikací. Proveďte kroky a pak vyberte možnost **omezit přenos**. Security Center pak bude pro vás propojování.

   ![Omezit přenosy][6]

> [!NOTE]
> Můžete chránit více webových aplikací v Security Center přidáním těchto aplikací do stávajících nasazení WAF.
>
>

Protokoly z tohoto WAF jsou teď plně integrované. Security Center může začít automaticky shromažďovat a analyzovat protokoly, aby mohli na vás naplochit důležité výstrahy zabezpečení.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste si ukázali, jak implementovat Security Center doporučení "Přidání webové aplikace". Další informace o konfiguraci firewallu webových aplikací najdete v následujících tématech:

* [Konfigurace firewallu webových aplikací pro službu App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si blogové příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
