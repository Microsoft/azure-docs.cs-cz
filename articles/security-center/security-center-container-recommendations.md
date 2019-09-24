---
title: Doporučení kontejneru v Azure Security Center | Microsoft Docs
description: Tento dokument vysvětluje Azure Security Center doporučení, jak lépe chránit vaše kontejnery.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202393"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Vysvětlení Azure Security Centerch doporučení ke kontejneru

Když migrujete aplikace monolitu tak, aby v produkčním prostředí běžely klíčové, kontejnerové nativní aplikace pro Cloud, můžete využít výhod funkcí kontejnerů, včetně snadného a rychlého nasazení a aktualizace. Vzhledem k tomu, že počet nasazených kontejnerů se stále zvyšuje, je potřeba zajistit, aby byla k dispozici řešení zabezpečení, která vám umožní získat přehled o stavu zabezpečení kontejnerů a chránit je před hrozbami.

Azure Security Center poskytuje následující možnosti, které vám pomůžou zabezpečit vaše kontejnery:

- **Viditelnost kontejnerů hostovaných na počítačích s IaaS Linux**<br>V Azure Security Center karta kontejnery zobrazuje všechny virtuální počítače nasazené pomocí Docker. Při zkoumání problémů zabezpečení na virtuálním počítači Security Center poskytuje další informace týkající se kontejnerů v počítači, jako je například verze Docker a počet imagí spuštěných na hostiteli.

    ![karta kontejner](./media/security-center-container-recommendations/docker-recommendation.png)


- **Doporučení týkající se zabezpečení na základě srovnávacího testu CIS pro Docker**<br>Security Center kontroluje konfigurace Dockeru a poskytuje vám vhled do nesprávných konfigurací tím, že poskytuje seznam všech neúspěšných pravidel, která byla posouzena. Security Center poskytuje pokyny, které vám pomůžou tyto problémy rychle vyřešit a ušetřit čas. Security Center nepřetržitě posuzuje konfigurace Dockeru a poskytuje vám jejich nejnovější stav.

    ![karta kontejner](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Detekce hrozeb kontejneru v reálném čase**<br> Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnery na počítačích se systémem Linux pomocí auditované komponenty. Výstrahy identifikují několik podezřelých aktivit Docker, například vytvoření privilegovaného kontejneru na hostiteli, indikaci serveru Secure Shell (SSH) běžícího v kontejneru Docker nebo použití šifrovacího dolování hlásíu. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

    ![karta kontejner](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Doporučení
Následující tabulky vám pomůžou pochopit dostupné kontejnery hostované v počítačích s IaaS Linux a posouzení zabezpečení jejich konfigurací Docker.

| Doporučení | Popis | Náprava |
| --- | --- | --- |
|Náprava ohrožení zabezpečení v konfiguraci zabezpečení kontejnerů |Opravte chyby zabezpečení v kontejnerech Konfigurace zabezpečení na základě osvědčených postupů konfigurace.| Náprava chyb zabezpečení v kontejnerech Konfigurace zabezpečení:<br>1. Projděte si seznam neúspěšných pravidel.<br>2. Opravte všechna pravidla podle zadaných pokynů.|


## <a name="next-steps"></a>Další kroky
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.

