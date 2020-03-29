---
title: Zásady správného řízení infrastruktury Azure DevTest Labs – prostředek
description: Tento článek se zabývá zarovnáním a správou prostředků pro Azure DevTest Labs ve vaší organizaci.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8bb00c770c61a0a5462a01ae552bd7e40a7cdb36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470645"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Řízení infrastruktury Azure DevTest Labs – prostředky
Tento článek se zabývá zarovnáním a správou prostředků pro devTest Labs v rámci vaší organizace. 

## <a name="align-within-an-azure-subscription"></a>Zarovnání v rámci předplatného Azure 

### <a name="question"></a>Otázka
Jak zarovnám prostředky DevTest Labs v rámci předplatného Azure?

### <a name="answer"></a>Odpověď
Než organizace začne používat Azure pro obecný vývoj aplikací, plánovači IT by měli nejprve zkontrolovat, jak zavést možnosti jako součást jejich celkového portfolia služeb. Oblasti, které je třeba přezkoumat, by se měly zabývat následujícími obavami:

- Jak měřit náklady spojené s životním cyklem vývoje aplikací?
- Jak organizace slaďuje navrhovanou nabídku služeb se zásadami podnikového zabezpečení? 
- Je segmentace nutná k oddělení vývojového a produkčního prostředí? 
- Jaké kontroly jsou zavedeny pro dlouhodobé snadné řízení, stabilitu a růst?

**První doporučenýpostup** je zkontrolovat taxonomie Azure organizací, kde jsou popsány rozdíly mezi produkčními a vývojovými předplatnými. V následujícím diagramu umožňuje navrhovaná taxonomie logické oddělení vývojového/testovacího a produkčního prostředí. S tímto přístupem může organizace zavést fakturační kódy ke sledování nákladů spojených s každým prostředím zvlášť. Další informace naleznete [v tématu Zásadsprávné hodování předškolního předplatného](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Kromě toho můžete použít [značky Azure](../azure-resource-manager/management/tag-resources.md) k uspořádání prostředků pro účely sledování a fakturace.

**Druhým doporučeným postupem** je povolit předplatné DevTest v rámci portálu Azure Enterprise. Umožňuje organizaci spouštět klientské operační systémy, které obvykle nejsou dostupné v předplatném Azure enterprise. Pak použijte podnikový software, kde platíte pouze za výpočetní prostředky a nemusíte se starat o licencování. Zajišťuje, že fakturace pro určené služby, včetně obrázků galerie v IaaS, jako je microsoft SQL Server, je založena pouze na spotřebě. Podrobnosti o předplatném Azure DevTest najdete [tady](https://azure.microsoft.com/offers/ms-azr-0148p/) pro zákazníky smlouvy Enterprise (EA) a [tady](https://azure.microsoft.com/offers/ms-azr-0023p/) pro zákazníky s průběžným platbou.

![Zarovnání prostředků s předplatnými](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Tento model poskytuje organizaci flexibilitu při nasazování azure devtest labs ve velkém měřítku. Organizace může podporovat stovky testovacích prostředí pro různé organizační jednotky se 100 až 1000 virtuálními počítači spuštěnými paralelně. Podporuje pojem centralizované řešení podnikové laboratoře, které může sdílet stejné principy správy konfigurace a řízení zabezpečení.

Tento model také zajišťuje, že organizace nevyčerpá vyčerpá vyčerpává své limity prostředků přidružené k jejich předplatné Azure. Podrobnosti o omezení předplatného a služeb najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md). Proces zřizování devTest Labs může spotřebovat velký počet skupin prostředků. Můžete požádat o zvýšení limitů prostřednictvím žádosti o podporu v předplatném Azure DevTest. Prostředky v rámci produkčního předplatného nejsou ovlivněny jako využití předplatného vývoje. Další informace o škálování DevTest Labs, najdete [v tématu škálování kvót a omezení devTest Labs](devtest-lab-scale-lab.md).

Společný limit úrovně předplatného, který je třeba započítávat, je způsob přidělení přiřazení rozsahu IP adres sítě pro podporu produkčního i vývojového předplatného. Tato přiřazení by měla představovat růst v průběhu času (za předpokladu, že místní připojení nebo jiné síťové topologie, která vyžaduje, aby podnik spravovat své síťové zásobníku namísto výchozí implementace Azure). Doporučeným postupem je mít několik virtuálních sítí, které mají předponu velké IP adresy přiřazené a rozdělené s mnoha velkými podsítěmi, spíše než mít více virtuálních sítí s malými podsítěmi. Například s 10 předplatnými můžete definovat 10 virtuálních sítí (jeden pro každé předplatné). Všechna testovací prostředí, která nevyžadují izolaci, mohou sdílet stejnou podsíť ve virtuální síti předplatného.

## <a name="maintain-naming-conventions"></a>Udržovat konvence pojmenování

### <a name="question"></a>Otázka
Jak lze udržovat konvence pojmenování v prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Můžete chtít rozšířit aktuální konvence pojmenování rozlehlé sítě na operace Azure a učinit je konzistentní v prostředí DevTest Labs.

Při nasazování DevTest Labs doporučujeme, abyste měli konkrétní zásady spuštění. Tyto zásady nasadíte pomocí centrálního skriptu a šablon JSON, abyste vynutili konzistenci. Zásady pojmenování lze implementovat prostřednictvím zásad Azure používaných na úrovni předplatného. Ukázky JSON pro zásady Azure najdete v [tématu ukázky zásad Azure](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Počet uživatelů na testovací prostředí a testovací prostředí na organizaci

### <a name="question"></a>Otázka 
Jak zjistím poměr uživatelů na testovací prostředí a celkový počet testovacích prostředí potřebných v rámci organizace?

### <a name="answer"></a>Odpověď
Doporučujeme, aby organizační jednotky a vývojové skupiny, které jsou přidruženy ke stejnému vývojovému projektu, byly přidruženy ke stejnému testovacímu prostředí. Umožňuje stejné typy zásad, bitových kopií a zásad vypnutí, které mají být použity pro obě skupiny. 

Možná budete také muset zvážit zeměpisné hranice. Například vývojáři na severovýchodě Spojených států (USA) mohou používat testovací prostředí zřízené v usa – východ. A vývojáři v Dallasu, Texasu a Denveru v Coloradu mohou být nasměrováni k použití zdroje v americkém south centralu. Pokud existuje společné úsilí s externí třetí stranou, mohou být přiřazeny k testovacímu prostředí, které interní vývojáři nepoužívají. 

Můžete také použít testovací prostředí pro konkrétní projekt v rámci projektů Azure DevOps. Potom použijete zabezpečení prostřednictvím zadané skupiny Služby Azure Active Directory, která umožňuje přístup k oběma množině prostředků. Virtuální síť přiřazená testovacímu prostředí může být další hranicí pro konsolidaci uživatelů.

## <a name="deletion-of-resources"></a>Odstranění prostředků

### <a name="question"></a>Otázka
Jak můžeme zabránit odstranění prostředků v rámci testovacího prostředí?

### <a name="answer"></a>Odpověď
Doporučujeme nastavit správná oprávnění na úrovni testovacího prostředí tak, aby pouze oprávnění uživatelé mohli odstranit prostředky nebo změnit zásady testovacího prostředí. Vývojáři by měly být umístěny v rámci **skupiny DevTest Labs Users.** Vedoucím vývojářem nebo vedoucím infrastruktury by měl být **vlastník devTest Labs**. Doporučujeme, abyste měli pouze dva vlastníky laboratoře. Tato zásada sahá směrem k úložišti kódu, aby se zabránilo poškození. Použití testovacího prostředí mají práva k použití prostředků, ale nelze aktualizovat zásady testovacího prostředí. Podívejte se na následující článek, který uvádí role a práva, které má každá předdefinovaná skupina v rámci testovacího prostředí: [Přidání vlastníků a uživatelů v laboratořích Azure DevTest .](devtest-lab-add-devtest-user.md)

## <a name="move-lab-to-another-resource-group"></a>Přesunutí testovacího prostředí do jiné skupiny prostředků 

### <a name="question"></a>Otázka
Je podporováno přesunutí testovacího prostředí do jiné skupiny prostředků?

### <a name="answer"></a>Odpověď
Ano. Přejděte na stránku Skupina prostředků z domovské stránky testovacího prostředí. Potom vyberte **Přesunout** na panelu nástrojů a vyberte testovací prostředí, které chcete přesunout do jiné skupiny prostředků. Při vytváření testovacího prostředí se automaticky vytvoří skupina prostředků. Můžete však chtít přesunout testovací prostředí do jiné skupiny prostředků, která se řídí konvencemi pojmenování rozlehlé sítě. 

## <a name="next-steps"></a>Další kroky
Viz [Správa nákladů a vlastnictví](devtest-lab-guidance-governance-cost-ownership.md).
