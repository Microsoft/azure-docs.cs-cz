---
title: Horizontální navýšení kapacity Azure DevTest Labs infrastruktury
description: Tento článek poskytuje pokyny pro škálování infrastruktury Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478786"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Horizontální navýšení kapacity Azure DevTest Labs infrastruktury
Před implementací DevTest Labs v podnikovém měřítku je k dispozici několik klíčových rozhodovacích bodů. Porozumění těmto rozhodovacím bodům na vysoké úrovni pomáhá organizaci s rozhodováním o návrhu v budoucnu. Nicméně tyto body by neměly mít v organizaci zpětnou kontrolu konceptu. V horních třech oblastech pro počáteční plánování horizontálního navýšení kapacity jsou:

- Sítě a zabezpečení
- Topologie předplatného
- Role a odpovědnosti

## <a name="networking-and-security"></a>Sítě a zabezpečení
Sítě a zabezpečení jsou základem pro všechny organizace. I když nasazení na podnikové úrovni vyžaduje mnohem hlubší analýzu, existuje omezený počet požadavků na úspěšné provedení zkušebního konceptu. Mezi několik klíčových oblastí fokusu patří:

- **Předplatné Azure** – nasazení DevTest Labs vyžaduje přístup k předplatnému Azure s odpovídajícími právy k vytváření prostředků. Existuje několik způsobů, jak získat přístup k předplatným Azure, včetně smlouva Enterprise a průběžné platby. Další informace o získání přístupu k předplatnému Azure najdete v tématu [licencování Azure pro podnik](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Přístup k místním prostředkům** – některé organizace vyžadují, aby jejich prostředky v DevTest Labs měly přístup k místním prostředkům. Je potřeba zabezpečené připojení z místního prostředí k Azure. Proto je důležité nastavit/nakonfigurovat připojení VPN nebo Express Route předtím, než začnete. Další informace najdete v tématu [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
- **Další požadavky na zabezpečení** – další požadavky na zabezpečení, jako jsou zásady počítače, přístup k veřejným IP adresám, připojení k Internetu, jsou scénáře, které je třeba před implementací testování konceptu zkontrolovat. 

## <a name="subscription-topology"></a>Topologie předplatného
Topologie předplatného je důležitým aspektem návrhu při nasazení DevTest Labs do podniku. Není však nutné přesvědčit všechna rozhodnutí až do doby, kdy byl proveden důkaz konceptu. Při vyhodnocování počtu předplatných potřebných pro podnikovou implementaci existují dvě extrémní podmínky: 

- Jedno předplatné pro celou organizaci
- Předplatné na uživatele

V dalším kroku zvýrazníme profesionály každého přístupu.

### <a name="one-subscription"></a>Jedno předplatné
Přístup k jednomu předplatnému se často nedá spravovat ve velkém podniku. Omezení počtu předplatných ale přináší následující výhody:

- **Odhad** nákladů na podnik.  Rozpočtování je v rámci jednoho předplatného mnohem jednodušší, protože všechny prostředky jsou v jednom fondu. Tento přístup umožňuje jednodušší rozhodování o tom, kdy v jednom okamžiku vydávat míry řízení nákladů v rámci fakturačního cyklu.
- **Spravovatelnost** virtuálních počítačů, artefaktů, vzorců, konfigurace sítě, oprávnění, zásad atd. je jednodušší, protože všechny aktualizace jsou vyžadovány pouze v jednom předplatném, nikoli při provádění aktualizací v mnoha předplatných.
- **Síťové** úsilí se značně zjednodušuje v jednom předplatném pro podniky, u kterých je místní připojení potřeba. Propojení virtuálních sítí mezi předplatnými (model hvězdicové sítě) se vyžaduje u dalších odběrů, které vyžadují další konfiguraci, správu, adresní prostory IP adres atd.
- **Týmová spolupráce** je jednodušší, když všichni pracují ve stejném předplatném, například je snazší změnit přiřazení virtuálního počítače spolupracovnímu procesu, sdílet týmové prostředky atd.

### <a name="subscription-per-user"></a>Předplatné na uživatele
Samostatné předplatné na uživatele poskytuje stejné možnosti jako alternativní spektrum. Mezi výhody, které mají mnoho předplatných, patří:

- **Kvóty Azure pro škálování** nebrání v přijetí. Například v případě tohoto zápisu Azure povoluje 200 účtů úložiště na předplatné. Pro většinu služeb v Azure existují provozní kvóty (mnoho se dá přizpůsobit, některé ale nemůžou). V tomto modelu předplatného na uživatele je vysoce pravděpodobné, že většina kvót dosáhne. Další informace o aktuálních kvótách škálování Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- **Vratek** do skupin nebo jednotlivých vývojářů se stane mnohem jednodušším, aby organizacím zohlednily náklady pomocí jejich současného modelu.
- **Vlastnictví & oprávnění** prostředí DevTest Labs je jednoduché. Vývojářům poskytujete přístup na úrovni předplatného a 100% zodpovídá za všechno, co zahrnuje konfiguraci sítě, zásady testovacího prostředí a správu virtuálních počítačů.

V podniku může být k dispozici dostatek omezení pro extrémní spektrum. Proto možná budete muset nastavit odběry způsobem, který spadá do středu těchto extrémních hodnot. V rámci osvědčeného postupu by měl být cílem organizace použití minimálního počtu předplatných, pokud je to možné, mějte na paměti vynucené funkce, které zvyšují celkový počet předplatných. Pro opakování iterace je topologie předplatného zásadní pro podnikové nasazení DevTest Labs, ale nemělo by se zpozdit o koncept. V článku týkajícím se [zásad správného řízení](devtest-lab-guidance-governance-policy-compliance.md) najdete další podrobnosti o tom, jak se rozhodnout na členitosti a předplatném v organizaci.

## <a name="roles-and-responsibilities"></a>Role a odpovědnosti
Koncepce konceptu DevTest Labs má tři primární role s definovanými zodpovědností – vlastníkem předplatného, vlastníkem DevTest Labs a uživatelem DevTest Labs a volitelně také přispěvatelem.

- **Vlastník předplatného** – vlastník předplatného má práva ke správě předplatného Azure, včetně přiřazování uživatelů, správy zásad, vytváření & správy síťové topologie, vyžádání zvýšení kvóty atd. Další informace najdete v [tomto článku](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Vlastník DevTest Labs** – vlastník DevTest Labs má úplný přístup správce k testovacímu prostředí. Tato osoba zodpovídá za přidání a odebrání uživatelů, správu nastavení nákladů, Obecné nastavení testovacího prostředí a další úlohy založené na virtuálních počítačích/artefaktech. Vlastník testovacího prostředí má také všechna práva uživatele DevTest Labs.
- **Uživatel DevTest Labs** – uživatel DevTest Labs může vytvářet a využívat virtuální počítače v testovacím prostředí. Tito jednotlivci mají některé minimální administrativní funkce na virtuálních počítačích, které vytvoří (spustí/zastaví/odstraní nebo nakonfigurují své virtuální počítače). Uživatelé nemůžou spravovat virtuální počítače jiných uživatelů.

## <a name="next-steps"></a>Další kroky
Podívejte se na další článek v této sérii: [orchestrujte implementaci Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)