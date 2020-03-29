---
title: Doporučení kontejnerů v Azure Security Center | Dokumenty společnosti Microsoft
description: Tento dokument vysvětluje doporučení Azure Security Center, jak chránit vaše kontejnery.
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
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912356"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Porozumění doporučení týkajících se kontejnerů Azure Security Center

Při migraci monolitových aplikací za účelem spuštění kritických kontejnerizovaných cloudových aplikací v produkčním prostředí můžete využít výhod funkcí kontejnerů, včetně snadného a rychlého nasazení a aktualizace. Vzhledem k tomu, že počet nasazených kontejnerů se stále zvyšuje, musí být zavedena bezpečnostní řešení, která vám poskytnou přehled o stavu zabezpečení kontejnerů a pomohou je chránit před hrozbami.

Azure Security Center poskytuje následující funkce, které vám pomohou zabezpečit kontejnery:

- **Přehled o kontejnerech hostovaných na počítačích IaaS Linux**<br>V Azure Security Center karta Kontejnery zobrazuje všechny virtuální počítače nasazené s Dockerem. Při zkoumání problémů se zabezpečením na virtuálním počítači, Security Center poskytuje další informace týkající se kontejnerů v počítači, jako je například verze Dockeru a počet bitových kopií spuštěných na hostiteli.

    ![karta kontejner](./media/security-center-container-recommendations/docker-recommendation.png)


- **Doporučení zabezpečení založená na srovnávacím testu CIS pro Docker**<br>Security Center kontroluje konfigurace Dockeru a poskytuje vám vhled do nesprávných konfigurací tím, že poskytuje seznam všech neúspěšných pravidel, která byla posouzena. Centrum zabezpečení obsahuje pokyny, které vám pomohou tyto problémy rychle vyřešit a ušetřit čas. Security Center nepřetržitě posuzuje konfigurace Dockeru a poskytuje vám jejich nejnovější stav.

    ![karta kontejner](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Ochrana před hrozbami kontejnerů v reálném čase**<br> Security Center poskytuje ochranu před hrozbami v reálném čase pro vaše kontejnery na počítačích s Linuxem s komponentou AuditD. Výstrahy identifikují několik podezřelých aktivit Dockeru, jako je například vytvoření privilegovaného kontejneru na hostiteli, označení serveru Secure Shell (SSH) spuštěného uvnitř kontejneru Dockeru nebo použití horníků kryptografických. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

    ![karta kontejner](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Doporučení
Použijte níže uvedené tabulky jako odkaz, který vám pomůže pochopit dostupné kontejnery hostované na počítačích IaaS Linux a posouzení zabezpečení jejich konfigurací Dockeru.

| Doporučení | Popis | Odstranění rizika |
| --- | --- | --- |
|Náprava slabých míst v konfiguracích zabezpečení kontejnerů |Na základě doporučených postupů konfigurace můžete napravit chyby zabezpečení v konfiguraci zabezpečení kontejneru.| Chcete-li napravit chyby zabezpečení v konfiguracích zabezpečení kontejneru:<br>1. Projděte si seznam neúspěšných pravidel.<br>2. Upevněte každé pravidlo podle uvedených pokynů.|


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která platí pro jiné typy prostředků Azure, najdete v následujících tématech:

* [Monitorování identity a přístupu v Azure Security Center](security-center-identity-access.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL v Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.