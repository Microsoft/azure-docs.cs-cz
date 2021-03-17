---
title: Řízení Azure DevTest Labs infrastruktury – prostředek
description: Tento článek popisuje zarovnání a správu prostředků pro Azure DevTest Labs v rámci vaší organizace.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480911"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Řízení Azure DevTest Labs infrastruktury – prostředky
Tento článek popisuje zarovnání a správu prostředků pro DevTest Labs v rámci vaší organizace. 

## <a name="align-within-an-azure-subscription"></a>Zarovnat v rámci předplatného Azure 

### <a name="question"></a>Otázka
Návody v rámci předplatného Azure narovnávat prostředky DevTest Labs?

### <a name="answer"></a>Odpověď
Předtím, než organizace začne používat Azure pro obecný vývoj aplikací, je třeba nejprve zkontrolovat, jak tuto funkci zavést v rámci svých celkových portfolií služeb. Oblasti pro kontrolu by měly řešit následující aspekty:

- Jak změřit náklady spojené s životním cyklem vývoje aplikací?
- Jak organizace zarovnává navrhovanou nabídku služeb s podnikovými zásadami zabezpečení? 
- Je segmentace nutná k oddělení vývoje a produkčního prostředí? 
- Jaké ovládací prvky jsou zavedeny k dlouhodobému usnadnění správy, stability a růstu?

**Prvním doporučeným postupem** je kontrola taxonomie Azure pro organizace, kde jsou pojmenovány divize mezi produkčním a vývojovým předplatným. V následujícím diagramu nabízí navržená taxonomie logické oddělení vývoje a testování a produkčních prostředí. V rámci tohoto přístupu může organizace zavést kódy fakturace a sledovat náklady spojené s jednotlivými prostředími samostatně. Další informace najdete v tématu zásady [správného řízení předplatného](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Kromě toho můžete pomocí [značek Azure](../azure-resource-manager/management/tag-resources.md) organizovat prostředky pro účely sledování a fakturace.

**Druhým doporučeným postupem** je povolení předplatného DevTest na portálu Azure Enterprise. Umožňuje, aby organizace spouštěla klientské operační systémy, které nejsou obvykle dostupné v rámci předplatného Azure Enterprise. Pak použijte podnikový software, kde platíte jenom za výpočetní výkon a nedělejte si starosti s licencováním. Zajišťuje, aby fakturace určených služeb, včetně imagí galerie v IaaS, jako je například Microsoft SQL Server, byla založena pouze na spotřebě. Podrobnosti o předplatném Azure DevTest najdete [tady](https://azure.microsoft.com/offers/ms-azr-0148p/) pro zákazníky smlouva Enterprise (EA) a [tady](https://azure.microsoft.com/offers/ms-azr-0023p/) pro zákazníky s průběžnými platbami.

![Zarovnání prostředků s předplatnými](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Tento model poskytuje organizaci flexibilitu při nasazování Azure DevTest Labs se škálováním. Organizace může podporovat stovky laboratoří pro různé obchodní jednotky s 100 až 1000 virtuálními počítači, které běží paralelně. Podporuje pojem centralizovaného řešení podnikového prostředí, které může sdílet stejné zásady správy konfigurace a řízení zabezpečení.

Tento model také zajišťuje, že organizace nevyčerpá své limity prostředků přidružené k předplatnému Azure. Podrobnosti o omezeních předplatného a služeb najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Proces zřizování DevTest Labs může využívat velký počet skupin prostředků. Můžete požadovat, aby se limity zvýšily prostřednictvím žádosti o podporu v předplatném Azure DevTest. Prostředky v rámci produkčního předplatného nejsou ovlivněny, protože vývojové předplatné se rozrůstá v používání. Další informace o škálování DevTest Labs najdete v tématu [kvóty a limity škálování v DevTest Labs](devtest-lab-scale-lab.md).

Běžným omezením na úrovni předplatného, které se musí vystavit, je způsob, jakým se přidělují přiřazení rozsahu síťových IP adres pro podporu produkčních i rozvojových předplatných. Tato přiřazení by měla pohlížet na růst v čase (za předpokladu, že se jedná o místní připojení nebo jiná síťová topologie, která vyžaduje, aby organizace spravovala síťový zásobník místo výchozí implementace v Azure). Doporučeným postupem je mít několik virtuálních sítí, které mají přiřazenou předponu velkých IP adres a jsou rozdělené s mnoha velkými podsítěmi, a ne více virtuálními sítěmi s malými podsítěmi. Například s 10 předplatnými můžete definovat 10 virtuálních sítí (jeden pro každé předplatné). Všechny laboratoře, které nevyžadují izolaci, můžou sdílet stejnou podsíť ve virtuální síti předplatného.

## <a name="maintain-naming-conventions"></a>Zachování konvencí pojmenování

### <a name="question"></a>Otázka
Návody udržovat zásady vytváření názvů napříč prostředím DevTest Labs?

### <a name="answer"></a>Odpověď
Je možné, že budete chtít stávající podnikové zásady vytváření názvů v rámci Azure zvýšit a zajistit jejich konzistenci v prostředí DevTest Labs.

Při nasazování DevTest Labs doporučujeme, abyste měli konkrétní počáteční zásady. Tyto zásady nasadíte pomocí centrálního skriptu a šablon JSON pro vymáhání konzistence. Zásady pojmenování se dají implementovat prostřednictvím zásad Azure použitých na úrovni předplatného. Ukázky JSON pro Azure Policy najdete v tématu [Azure Policy Samples](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Počet uživatelů na testovací prostředí a cvičení na organizaci

### <a name="question"></a>Otázka 
Návody určit poměr uživatelů na testovací prostředí a celkový počet cvičení potřebných v rámci organizace?

### <a name="answer"></a>Odpověď
Doporučujeme, aby obchodní jednotky a skupiny pro vývoj, které jsou přidruženy ke stejnému vývojovému projektu, byly přidruženy ke stejnému testovacímu prostředí. Umožňuje použít pro obě skupiny stejné typy zásad, obrázků a zásad vypnutí. 

Možná budete muset zvážit i geografické hranice. Například vývojáři v oblasti USA – východ USA (US) můžou používat testovací prostředí zřízené ve východní USA 2. A vývojáři v Dallasu, Texas a Denver můžou Coloradu směrovat na používání prostředků v USA (střed) – jih. Pokud se jedná o spolupráci s externí třetí stranou, mohla by být přiřazena testovacímu prostředí, které nepoužívá interní vývojáři. 

Testovací prostředí můžete použít také pro konkrétní projekt v rámci projektů Azure DevOps. Pak můžete zabezpečení použít prostřednictvím zadané skupiny Azure Active Directory, která umožňuje přístup k oběma prostředkům. Pro konsolidaci uživatelů může být virtuální síť přiřazená k testovacímu prostředí jinou hranicí.

## <a name="deletion-of-resources"></a>Odstranění prostředků

### <a name="question"></a>Otázka
Jak můžeme zabránit odstranění prostředků v testovacím prostředí?

### <a name="answer"></a>Odpověď
Doporučujeme, abyste na úrovni testovacího prostředí nastavili správná oprávnění, aby mohli odstraňovat prostředky nebo měnit zásady testovacího prostředí jenom autorizovaní uživatelé. Vývojáři by měli být uvedení do skupiny **uživatelů DevTest Labs** . Vedoucí vývojář nebo vedoucí infrastruktury by měli být **vlastníkem DevTest Labs**. Doporučujeme, abyste měli jenom dva vlastníky testovacího prostředí. Tato zásada rozšiřuje do úložiště kódu, aby nedocházelo k poškození. Testovací prostředí používá práva k používání prostředků, ale nemůžou aktualizovat zásady testovacího prostředí. Přečtěte si následující článek, který obsahuje informace o rolích a oprávněních, které mají jednotlivé předdefinované skupiny v rámci testovacího prostředí: [Přidání vlastníků a uživatelů v Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Přesunout testovací prostředí do jiné skupiny prostředků 

### <a name="question"></a>Otázka
Je podporováno přesun testovacího prostředí do jiné skupiny prostředků?

### <a name="answer"></a>Odpověď
Ano. Přejděte na stránku skupiny prostředků z domovské stránky vašeho testovacího prostředí. Pak na panelu nástrojů vyberte **přesunout** a vyberte testovací prostředí, které chcete přesunout do jiné skupiny prostředků. Při vytváření testovacího prostředí se pro vás automaticky vytvoří skupina prostředků. Testovací prostředí ale můžete chtít přesunout do jiné skupiny prostředků, která následuje po konvencích podnikového vytváření názvů. 

## <a name="next-steps"></a>Další kroky
Viz [Správa nákladů a vlastnictví](devtest-lab-guidance-governance-cost-ownership.md).
