---
title: Přidání brány firewall webových aplikací v Azure Security Center | Microsoft Docs
description: Tento dokument ukazuje, jak implementovat doporučení Azure Security Center **přidání brány firewall webových aplikací** a **dokončit ochranu aplikace**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2018
ms.author: terrylan
ms.openlocfilehash: e28a1f6b865dae3abe2cb9dfac2921c6a2034491
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203518"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Přidání brány firewall webových aplikací v Azure Security Center
Azure Security Center může doporučujeme, abyste přidali brány firewall webových aplikací (firewall webových aplikací) z partnera společnosti Microsoft k zabezpečení webových aplikací. Tento dokument vás příklad toho, jak použít toto doporučení provede.

Doporučení firewall webových aplikací je zobrazený pro všechny veřejné přístupných IP adresy (IP úrovni Instance nebo IP skupinu s vyrovnáváním zatížení), skupinu zabezpečení sítě spojenou s otevřete příchozí webovými porty (80,443).

Security Center doporučuje zřízení firewall webových aplikací, které pomáhají bránit proti útokům na cílení na vaše webové aplikace na virtuálních počítačích a na externí aplikace služby prostředí (App Service Environment) nasazené v části [izolovaná](https://azure.microsoft.com/pricing/details/app-service/windows/) plán služby. Plán Isolated hostuje aplikace v privátním, vyhrazeném prostředí Azure a je ideální pro aplikace, které vyžadují zabezpečená připojení k místní síti nebo dodatečný výkon a škálování. Kromě aplikace se v izolovaném prostředí vaše aplikace musí mít externí IP adresu nástroj pro vyrovnávání zatížení. Další informace o App Service Environment, najdete v článku [dokumentace k aplikaci služby prostředí](../app-service/environment/intro.md).

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V části **doporučení**, vyberte **zabezpečení webové aplikace pomocí brány firewall webových aplikací**.
   ![Zabezpečení webové aplikace][1]
2. V části **zabezpečení webových aplikací pomocí brány firewall webových aplikací**, vyberte webovou aplikaci. **Přidání brány Firewall webových aplikací** otevře.
   ![Přidání brány firewall webových aplikací][2]
3. Můžete použít existující brány firewall webových aplikací, pokud je k dispozici nebo můžete vytvořit nový. V tomto příkladu nejsou žádné existující WAFs k dispozici, vytvoříme firewall webových aplikací.
4. Pokud chcete vytvořit firewall webových aplikací, vyberte ze seznamu partnerů integrované řešení. V tomto příkladu jsme vyberte **brány Firewall webových aplikací Barracuda**.
5. **Brány Firewall webových aplikací barracuda** otevře, takže získáte informace o partnerských řešení. Vyberte **Vytvořit**.

   ![Okno informace o brány firewall][3]

6. **Nové brány Firewall webových aplikací** otevře, kde můžete provádět **konfigurace virtuálního počítače** kroky a zadejte **firewall webových aplikací informace**. Vyberte **konfigurace virtuálního počítače**.
7. V části **konfigurace virtuálního počítače**, zadejte požadované informace o číselníku virtuálního počítače, který běží firewall webových aplikací.
   ![Konfigurace virtuálního počítače][4]
8. Vraťte se do **nové brány Firewall webových aplikací** a vyberte **firewall webových aplikací informace**. V části **firewall webových aplikací informace**, nakonfigurujete firewall webových aplikací, sám sebe. Krok 7 umožňuje nakonfigurovat virtuální počítač, na kterém běží firewall webových aplikací a krok 8 umožňuje zřídit firewall webových aplikací, sám sebe.

## <a name="finalize-application-protection"></a>Finalizace ochrany aplikací
1. Vraťte se do **doporučení**. Nový záznam vygenerovalo po vytvoření firewall webových aplikací, nazývá **dokončit ochranu aplikace**. Tato položka způsobem zjistíte, že je potřeba dokončit proces ve skutečnosti připojení firewall webových aplikací v rámci virtuální sítě Azure tak, aby ho může chránit aplikace.

   ![Finalizace ochrany aplikací][5]

2. Vyberte **dokončit ochranu aplikace**. Otevře se nové okno. Uvidíte, že se webové aplikace, které musí mít jeho provoz přesměruje.
3. Vyberte webovou aplikaci. Otevře se okno umožňující kroky pro dokončení instalace brány firewall webových aplikací. Proveďte kroky a pak vyberte **omezení přenosu**. Security Center pak provede telefonického kabeláž.

   ![Omezit přenosy][6]

> [!NOTE]
> Přidáním těchto aplikací na vaše stávající nasazení firewall webových aplikací můžete chránit několika webových aplikací ve službě Security Center.
>
>

Nyní jsou plně integrované protokoly z této firewall webových aplikací. Security Center můžete spustit automaticky shromažďování a analyzování protokolů tak, aby vám ho můžete surface výstrahy důležité zabezpečení.

## <a name="next-steps"></a>Další postup
Tento dokument vám ukázal, jak provést doporučení Security Center "Přidat webovou aplikaci." Další informace o konfiguraci brány firewall webových aplikací, naleznete v následujících tématech:

* [Konfigurace brány Firewall webových aplikací (firewall webových aplikací) pro služby App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
