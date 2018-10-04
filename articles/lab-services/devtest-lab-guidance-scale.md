---
title: Škálovat infrastrukturu s Azure DevTest Labs
description: Tento článek obsahuje pokyny pro vertikální navyšování kapacity infrastruktury Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: ed6c21762fed331e2f11378ec9b6d91cba9bec11
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251011"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Škálovat infrastrukturu s Azure DevTest Labs
Ještě před jejich implementací DevTest Labs v podnikovém měřítku, se několik bodů klíčová rozhodnutí. Pochopení těchto rozhodovací body na vysoké úrovni pomáhá organizacím s rozhodováním ohledně návrhu v budoucnu. Ale tyto body by neměl Ponecháme organizace spuštění testování konceptu. První tři oblasti pro počáteční plánování vertikálně navýšit kapacitu, jsou:

- Sítě a zabezpečení
- Předplatné topologie
- Role a zodpovědnosti

## <a name="networking-and-security"></a>Sítě a zabezpečení
Sítě a zabezpečení jsou základů u všech organizací. Při nasazení podnikové úrovni vyžaduje mnohem hlubší analýzu, jsou menší počet požadavků úspěšně provést testování konceptu. Pár klíčových oblastí fokus patří:

- **Předplatné Azure** – nasazení DevTest Labs, musíte mít přístup k předplatnému Azure s příslušná práva k vytváření prostředků. Existuje mnoho způsobů, jak získat přístup k předplatným Azure, včetně Enterprise Agreement a průběžné platby. Další informace o získání přístupu k předplatnému Azure, najdete v části [licencování Azure pro podniky](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Přístup k místním prostředkům** – některé organizace vyžadují svoje prostředky ve službě DevTest Labs mají přístup k místním prostředkům. Je potřeba zabezpečené připojení z místního prostředí do Azure. Proto je důležité, že jste nastavili nahoru/konfigurovat síť VPN nebo Express Route připojení než začnete. Další informace najdete v tématu [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
- **Další požadavky na zabezpečení** – další požadavky na zabezpečení, jako je například zásady počítače, přístup k veřejné IP adresy, připojení k Internetu jsou scénáře, které může být nutné zkontrolovat před implementací testování konceptu. 

## <a name="subscription-topology"></a>Předplatné topologie
Topologie předplatného je zásadní aspekt návrhu, při nasazování DevTest Labs k podnikové síti. Ale není potřeba ztuhnout všechna rozhodnutí až po dokončení testování konceptu. Při vyhodnocování počet předplatných, které jsou nezbytné pro podnikové implementace, existují dvěma extrémy: 

- Jedno předplatné pro celou organizaci
- Předplatné uživatele

Dále jsme zvýrazněte výhody obou těchto přístupů.

### <a name="one-subscription"></a>Jedno předplatné
Často není ve velkých podnicích spravovat přístup k jednomu předplatnému. Omezení počtu předplatných, která však nabízí následující výhody:

- **Prognózování** náklady pro organizace.  Plánování rozpočtu stane mnohem jednodušší, v rámci jednoho předplatného, protože veškeré prostředky jsou v jeden fond. Tento přístup umožňuje jednodušší rozhodovací na při vykonávat náklady pod kontrolou míry v daném okamžiku během fakturačního cyklu.
- **Možnosti správy** virtuálních počítačů, artefakty, vzorce, konfigurace sítě, oprávnění, zásady, je jednodušší atd, protože všechny aktualizace jsou nezbytné pouze jedno předplatné na rozdíl od provedení aktualizací napříč mnoha předplatnými.
- **Sítě** úsilí je výrazně zjednodušené v rámci jednoho předplatného pro podniky, kde místní připojení je povinné. Propojení virtuálních sítí mezi předplatnými (model centra s paprsky) je požadován spolu s další předplatná, která vyžaduje další konfiguraci, správu, adresní prostory IP adres, atd.
- **Týmovou spolupráci** je jednodušší, pokud všichni pracuje ve stejném předplatném – například je snazší změnit přiřazení virtuálního počítače na nějaký kolega, sdílení týmových prostředků atd.

### <a name="subscription-per-user"></a>Předplatné uživatele
Samostatné předplatné uživatele poskytuje stejné příležitostí k alternativní spektra. Mezi výhody s mnoha předplatnými patří:

- **Azure škálování kvóty** nebudou bránit přijetí. Například v době psaní tohoto textu, který umožňuje 200 účtů úložiště na jedno předplatné Azure. Existují provozní kvóty pro většinu služeb v Azure (řada je přizpůsobit, některé nelze). V tomto modelu předplatného na uživatele je vysoce nepravděpodobné, že se dosáhne kvóty většina. Další informace o aktuální kvóty škálování Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- **Vratky** do skupin nebo jednotlivé vývojáři budou mnohem jednodušší, umožňují organizacím, aby se zohlednily nákladů s využitím jejich současného modelu.
- **Vlastnictví a oprávnění** DevTest Labs jsou jednoduché prostředí. Vývojáři poskytnout přístup k úrovni předplatného a jsou 100 % za všechno včetně konfigurace sítí, zásady testovacího prostředí a správa virtuálních počítačů.

V podnikové síti může být dost omezení pro extrémní spektra. Proto budete muset nastavit odběry způsobem, který spadá uprostřed těmito hranicemi. Jako osvědčený postup by mělo být cílem organizace pomocí minimálního počtu předplatných, která jako možné dodržujte při tom vynucuje funkce, které zvýšit celkový počet předplatných. Zdůrazňujeme, předplatné topologie je velmi důležité pro podnikové nasazení služby DevTest Labs, ale neměli zpoždění testování konceptu. Existují další podrobnosti [zásad správného řízení](devtest-lab-guidance-governance-policy-compliance.md) článek o tom, jak při rozhodování o předplatném a testovacího prostředí členitosti v organizaci.

## <a name="roles-and-responsibilities"></a>Role a zodpovědnosti
Prostředí testování konceptu služby DevTest Labs má tři primární role definované zodpovědní – vlastník předplatného, vlastník DevTest Labs, uživatel služby DevTest Labs a volitelně přispěvatele.

- **Vlastník předplatného** – vlastník předplatného, má práva ke správě předplatného Azure včetně přiřazování uživatelů, Správa zásad, vytváření a správa topologií sítě, požaduje zvýšení kvót atd. Další informace najdete v [tomto článku](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs vlastníka** – má plný přístup správce ke testovacího prostředí DevTest Labs vlastník. Tato osoba je zodpovědný za přidání/odebrání uživatelů, Správa nákladů nastavení, nastavení obecné testovacího prostředí a další úlohy založená na virtuálních počítačích nebo artefaktů. Vlastník testovacího prostředí má také všechna práva uživatel služby DevTest Labs.
- **Uživatel služby DevTest Labs** – uživatelské DevTest Labs můžete vytvářet a využívat virtuální počítače v testovacím prostředí. Tito pracovníci mají některé minimální funkce pro správu na virtuálních počítačích, které vytvářejí (start/stop/delete/konfigurace svých virtuálních počítačích). Uživatele nelze spravovat virtuální počítače jiných uživatelů.

## <a name="next-steps"></a>Další postup
Přečtěte si další článek v této sérii: [Orchestrace implementace Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)