---
title: Škálování infrastruktury Azure DevTest Labs
description: Tento článek obsahuje pokyny pro škálování infrastruktury Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 3a48cef2210721bf7116b1c4ad1169779288f47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644830"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Škálování infrastruktury Azure DevTest Labs
Před implementací DevTest Labs v podnikovém měřítku existuje několik klíčových bodů rozhodnutí. Pochopení těchto rozhodovacích bodů na vysoké úrovni pomáhá organizaci s rozhodnutími o návrhu v budoucnu. Tyto body by však neměly zadržet organizaci od spuštění dokladu o konceptu. První tři oblasti pro počáteční plánování rozšiřování jsou:

- Vytváření sítí a zabezpečení
- Topologie předplatného
- Role a odpovědnosti

## <a name="networking-and-security"></a>Vytváření sítí a zabezpečení
Vytváření sítí a zabezpečení jsou základními kameny pro všechny organizace. Zatímco celopodnikové nasazení vyžaduje mnohem hlubší analýzu, existuje omezený počet požadavků na úspěšné provedení důkazu koncepce. Několik klíčových oblastí zájmu patří:

- **Předplatné Azure** – K nasazení DevTest Labs, musíte mít přístup k předplatnému Azure s příslušnými právy k vytváření prostředků. Existuje několik způsobů, jak získat přístup k předplatným Azure, včetně smlouvy Enterprise a průběžných plateb. Další informace o získání přístupu k předplatnému Azure najdete v [tématu Licencování Azure pro podnik](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Přístup k místním prostředkům** – některé organizace vyžadují své prostředky v DevTest Labs mají přístup k místním prostředkům. Je potřeba zabezpečené připojení z místního prostředí do Azure. Proto je důležité nastavit nebo nakonfigurovat připojení VPN nebo Express Route před zahájením. Další informace naleznete v tématu [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
- **Další požadavky na zabezpečení** – Další požadavky na zabezpečení, jako jsou zásady počítače, přístup k veřejným IP adresám, připojení k Internetu, jsou scénáře, které může být nutné zkontrolovat před implementací dokladu koncepce. 

## <a name="subscription-topology"></a>Topologie předplatného
Topologie předplatného je důležitým aspektem návrhu při nasazování devtest labs na enterprise. Není však nutné všechna rozhodnutí zpevňovat, dokud nebude dokončen doklad o koncepci. Při vyhodnocování počtu předplatných požadovaných pro podnikovou implementaci existují dva extrémy: 

- Jedno předplatné pro celou organizaci
- Předplatné na uživatele

Dále zdůrazníme výhody každého přístupu.

### <a name="one-subscription"></a>Jedno předplatné
Přístup jednoho předplatného často nelze spravovat ve velkém podniku. Omezení počtu předplatných však poskytuje následující výhody:

- **Prognózování** nákladů pro podnik.  Rozpočtování se stává mnohem jednodušší v jednom předplatném, protože všechny prostředky jsou v jednom fondu. Tento přístup umožňuje jednodušší rozhodování o tom, kdy v daném okamžiku uplatnit opatření pro kontrolu nákladů v fakturačním cyklu.
- **Správa** virtuálních počítačů, artefaktů, vzorců, konfigurace sítě, oprávnění, zásad atd.
- **Síťové** úsilí je značně zjednodušeno v jednom předplatném pro podniky, kde je vyžadováno místní připojení. Připojení virtuálních sítí napříč předplatnými (model rozbočovače) je vyžadováno s dalšími předplatnými, což vyžaduje další konfiguraci, správu, prostory IP adres atd.
- **Týmová spolupráce** je jednodušší, když všichni pracují ve stejném předplatném – například je jednodušší přiřadit virtuální ho k virtuálnímu zařízení spolupracovníkovi, sdílet prostředky týmu atd.

### <a name="subscription-per-user"></a>Předplatné na uživatele
Samostatné předplatné na uživatele poskytuje rovné příležitosti pro alternativní spektrum. Mezi výhody mnoha předplatných patří:

- **Azure škálování kvóty** nebudou bránit přijetí. Například k tomuto psaní Azure umožňuje 200 účtů úložiště na předplatné. Existují provozní kvóty pro většinu služeb v Azure (mnoho z nich lze přizpůsobit, některé ne. V tomto modelu předplatného na uživatele je vysoce nepravděpodobné, že by bylo dosaženo většiny kvót. Další informace o aktuálních kvótách škálování Azure najdete v [tématu Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Zpětné náklady pro **skupiny** nebo jednotlivé vývojáře se stávají mnohem jednoduššími, což organizacím umožňuje účtovat náklady pomocí aktuálního modelu.
- **Vlastnictví & oprávnění** prostředí DevTest Labs jsou jednoduchá. Vývojářům poskytujete přístup na úrovni předplatného a jsou 100% zodpovědní za vše, včetně konfigurace sítě, zásad testovacího prostředí a správy virtuálních počítačů.

Na Enterprise může být dost omezení na extrémy spektra. Proto může být nutné nastavit odběry způsobem, který spadá do prostřed těchto extrémů. Jako osvědčený postup by mělo být cílem organizace použít minimální počet předplatných, jak je to možné s ohledem na vynucení funkce, které zvyšují celkový počet odběrů. Chcete-li zopakovat, topologie předplatného je důležité pro podnikové nasazení DevTest Labs, ale neměl by zpozdit doklad koncepce. Další podrobnosti v článku [zásad správného řízení](devtest-lab-guidance-governance-policy-compliance.md) o tom, jak rozhodnout o předplatné a rozlišovací schopnost laboratoře v organizaci.

## <a name="roles-and-responsibilities"></a>Role a odpovědnosti
DevTest Labs proof concept má tři primární role s definovanými odpovědnostmi – vlastník předplatného, vlastník DevTest Labs, uživatel DevTest Labs a volitelně přispěvatel.

- **Vlastník předplatného** – Vlastník předplatného má práva ke správě předplatného Azure, včetně přiřazování uživatelů, správy zásad, vytváření & správu síťové topologie, požadování zvýšení kvóty atd. Další informace naleznete v [tomto článku](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs vlastník** – DevTest Labs vlastník má plný přístup pro správu do laboratoře. Tato osoba je zodpovědná za přidání nebo odebrání uživatelů, správu nastavení nákladů, obecné nastavení testovacího prostředí a další úlohy založené na virtuálních počítačůch nebo artefaktech. Vlastník testovacího prostředí má také všechna práva uživatele DevTest Labs.
- **DevTest Labs uživatel** – DevTest Labs uživatel můžete vytvořit a využívat virtuální počítače v testovacím prostředí. Tito jednotlivci mají některé minimální možnosti správy na virtuálních počítačích, které vytvářejí (start/stop/delete/configure their VMs). Uživatelé nemohou spravovat virtuální uživatele jiných uživatelů.

## <a name="next-steps"></a>Další kroky
Podívejte se na další článek v této sérii: [Orchestrate implementace Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)