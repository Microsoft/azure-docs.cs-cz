---
title: Doporučení kontejneru ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje doporučení Azure Security Center pro jak pomoct ochránit vaše kontejnery.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60705250"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Vysvětlení doporučení kontejneru služby Azure Security Center

Při migraci vašich aplikací monolitu ke spuštění klíčových, kontejnerizovaných aplikací nativních pro cloud v produkčním prostředí, můžete využít funkce kontejnerů, včetně snadné a rychlé nasazení a aktualizace. V průběhu počet kontejnerů ke zvýšení zabezpečení řešení musí být ve místo, kde můžete poskytnout přehled o stavu zabezpečení vašich kontejnerů a pomáhá chránit před hrozbami.

Azure Security Center poskytuje následující možnosti a pomáhá vám zabezpečit vaše kontejnery:

- **Přehled kontejnerů, které jsou hostované na počítačích IaaS s Linuxem**<br>Na kartě kontejnery ve službě Azure Security Center, zobrazují všechny virtuální počítače nasazené pomocí Docker. Při zkoumání problémů se zabezpečením na virtuálním počítači, Security Center poskytuje další informace související s kontejnery v počítači, jako je například verze Dockeru a počtu imagí, spuštěných na hostiteli.

    ![Karta kontejneru](./media/security-center-container-recommendations/docker-recommendation.png)


- **Doporučení zabezpečení podle srovnávacích testů CIS pro Docker**<br>Security Center kontroluje konfigurace Dockeru a poskytuje vám vhled do nesprávných konfigurací tím, že poskytuje seznam všech neúspěšných pravidel, která byla posouzena. Security Center obsahuje pokyny, které vám pomůžou tyto problémy rychle vyřešit a ušetřit čas. Security Center nepřetržitě posuzuje konfigurace Dockeru a poskytuje vám jejich nejnovější stav.

    ![Karta kontejneru](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Zjišťování hrozeb v reálném čase kontejneru**<br> Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnery na počítačích s Linuxem pomocí AuditD komponenty. Výstrahy Identifikujte několik podezřelých aktivit Dockeru, jako je například vytvoření privilegovaných kontejneru na hostiteli, údaj o běžící uvnitř kontejneru Dockeru nebo s využitím kryptografických modulů pro dolování server Secure Shell (SSH). Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

    ![Karta kontejneru](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Doporučení
V následujících tabulkách použijte jako vám pomůžou pochopit dostupné kontejnery, které jsou hostované na počítače s Linuxem IaaS a vyhodnocení zabezpečení jejich konfigurace Dockeru.

| Doporučení | Popis | Náprava |
| --- | --- | --- |
|Náprava ohrožení zabezpečení v kontejneru konfigurace zabezpečení |Náprava ohrožení zabezpečení v kontejneru konfigurace zabezpečení na základě osvědčených postupů konfigurace.| Napravit ohrožení zabezpečení v konfiguracích zabezpečení kontejnerů:<br>1. Projděte si seznam pravidel, která selhala.<br>2. Opravte každé pravidlo podle zadaného pokynů.|


## <a name="next-steps"></a>Další postup
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

