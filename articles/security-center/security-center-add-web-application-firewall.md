---
title: Přidat firewall webových aplikací ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení Azure Security Center **přidat firewall webových aplikací** a **finalizace ochrany aplikací**.
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
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105039"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Přidat firewall webových aplikací ve službě Azure Security Center
Azure Security Center může doporučit přidat firewall webových aplikací (WAF) od partnera Microsoftu pro zabezpečení webové aplikace. Tento dokument vás příklad toho, jak toto doporučení.

Doporučení WAF se zobrazí pro jakékoli veřejnou IP adresu (IP adresa na úrovni Instance nebo IP adresu s vyrovnáváním zatížení), který má skupinu zabezpečení sítě spojenou s otevřenými vstupními webovými porty (80,443).

Security Center doporučuje zřízení WAF, které pomáhají bránit útokům cílí na vaše webové aplikace na virtuálních počítačích a na externího prostředí App Service (ASE) nasazené v rámci [izolované](https://azure.microsoft.com/pricing/details/app-service/windows/) plán služby. Plán Isolated hostuje aplikace v privátním, vyhrazeném prostředí Azure a je ideální pro aplikace, které vyžadují zabezpečená připojení k místní síti nebo dodatečný výkon a škálování. Kromě vaší aplikace v izolovaném prostředí vaše aplikace musí mít externí IP adresu nástroje pro vyrovnávání zatížení. Další informace o službě ASE, najdete v článku [dokumentace pro App Service Environment](../app-service/environment/intro.md).

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V části **doporučení**vyberte **zabezpečit webovou aplikaci pomocí firewallu webových aplikací**.
   ![Zabezpečené webové aplikace][1]
2. V části **zabezpečení webových aplikací pomocí firewallu webových aplikací**, vyberte webovou aplikaci. **Přidat Firewall webových aplikací** otevře.
   ![Přidání brány firewall webových aplikací][2]
3. Můžete použít stávajícímu firewallu webových aplikací, pokud je k dispozici nebo vytvořit nové. V tomto příkladu k dispozici žádné stávajících řešení Waf, můžeme vytvořit brány WAF.
4. Vytvoření brány WAF, vyberte ze seznamu partnerů integrované řešení. V tomto příkladu vybereme **Barracuda Web Application Firewall**.
5. **Barracuda Web Application Firewall** otevře že poskytuje informace o partnerských řešení. Vyberte **Vytvořit**.

   ![Informace o okno Brána firewall][3]

6. **Nový Firewall webových aplikací** se otevře, kde můžete provádět **konfigurace virtuálního počítače** kroky a poskytují **informace o WAF**. Vyberte **konfigurace virtuálního počítače**.
7. V části **konfigurace virtuálního počítače**, zadejte informace požadované pro zprovoznění virtuálního počítače, na kterém běží WAF.

   ![Konfigurace virtuálního počítače][4]
   
8. Vraťte se na **nový Firewall webových aplikací** a vyberte **informace o WAF**. V části **informace o WAF**, nakonfigurujete WAF, samotného. Krok 7 umožňuje konfigurovat virtuální počítač, na kterém běží WAF a kroku 8 umožňuje zřízení WAF, samotného.

## <a name="finalize-application-protection"></a>Finalizace ochrany aplikací
1. Vraťte se na **doporučení**. Po vytvoření WAF, nazývá se vygeneroval nový záznam **finalizace ochrany aplikací**. Tato položka vám umožňuje vědět, které potřebujete k dokončení procesu ve skutečnosti Vyřešeno WAF v rámci virtuální sítě Azure tak, aby mohl chránit aplikace.

   ![Finalizace ochrany aplikací][5]

2. Vyberte **finalizace ochrany aplikací**. Otevře se nové okno. Uvidíte, že je webová aplikace, musí mít jeho provoz přesměruje.
3. Vyberte webová aplikace. Otevře se okno, který obsahuje kroky k dokončení nastavení firewallu webových aplikací. Proveďte kroky a pak vyberte **omezení provozu**. Security Center pak provede její nahoru za vás.

   ![Omezit přenosy][6]

> [!NOTE]
> Přidáním těchto aplikací na vašich stávajících nasazení WAF může chránit několik webových aplikací ve službě Security Center.
>
>

Nyní jsou plně integrované protokoly z této WAF. Security Center můžete spustit automaticky shromažďuje a analyzuje protokoly tak, aby jeho plochu na důležité výstrahy zabezpečení pro vás.

## <a name="next-steps"></a>Další postup
Tento dokument vám ukázali, jak implementovat doporučení služby Security Center "Přidat webovou aplikaci." Další informace o konfiguraci brány firewall webových aplikací, naleznete v následujících tématech:

* [Konfigurace firewallu webových aplikací pro službu App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
