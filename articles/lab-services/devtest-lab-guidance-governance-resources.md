---
title: Zásady správného řízení infrastruktury Azure DevTest Labs
description: Tento článek obsahuje pokyny pro zásady správného řízení infrastruktury Azure DevTest Labs.
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
ms.openlocfilehash: 2d17f4471383be623cb40276ec1e51257bf3fbce
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740370"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Zásady správného řízení infrastruktury Azure DevTest Labs – prostředky
Tento článek se zabývá zarovnání a správu prostředků pro DevTest Labs ve vaší organizaci. 

## <a name="align-within-an-azure-subscription"></a>Zarovnat v rámci předplatného Azure 

### <a name="question"></a>Otázka
Zarovnání DevTest Labs prostředky v rámci předplatného Azure

### <a name="answer"></a>Odpověď
Před zahájením organizace používat Azure pro vývoj aplikací Obecné, IT plánovači musí nejprve přečtěte si, jak zavádět funkce jako součást svého celkového portfolio služeb. Oblasti ke kontrole by se měla zabývat následující otázky:

- Postupy: měření náklady spojené s životní cyklus vývoje aplikací?
- Jak align organizace navržená služba se zásady podnikového zabezpečení? 
- Se segmentací muset oddělit vývojovém a produkčním prostředí? 
- Jaké ovládací prvky jsou zavedené pro dlouhodobé snadné správy, stability a růstu?

**Nejprve doporučená praxe** se zaměříme na Azure taxonomie organizací ve kterém jsou uvedeny rozdělení mezi produkční a vývoje odběry. V následujícím diagramu navrhované taxonomie umožňuje logické rozdělení vývoj a testování a produkční prostředí. S tímto přístupem můžete zavést organizace fakturační kódy ke sledování náklady spojené s každou prostředí samostatně. Další informace najdete v tématu [zásad správného řízení předplatná](/architecture/cloud-adoption/appendix/azure-scaffold). Kromě toho můžete použít [Azure značky](../azure-resource-manager/resource-group-using-tags.md) k uspořádání prostředků pro účely sledování a účtování.

**Druhý doporučeným postupem** je umožnit DevTest předplatné na portálu Azure Enterprise. To umožňuje organizacím spuštění klientské operační systémy, které nejsou obvykle dostupné v rámci předplatného Azure enterprise. Potom použijte podnikového softwaru, kdy platíte jenom za výpočetní prostředky a není Nestarejte se o licencování. Zajišťuje, že fakturace za určené služby, včetně Image z Galerie v IaaS, jako je například Microsoft SQL Server, je na základě spotřeby pouze. Najdete podrobnosti o předplatném Azure DevTest [tady](https://azure.microsoft.com/offers/ms-azr-0148p/) pro zákazníky, kteří Enterprise Agreement (EA) a [tady](https://azure.microsoft.com/offers/ms-azr-0023p/) pro zákazníky s průběžnými platbami.

![Zarovnání prostředků s předplatnými](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Tento model poskytuje organizace flexibilitu při nasazování Azure DevTest Labs ve velkém měřítku. Organizace může podporovat stovky testovacích prostředí pro různé organizační jednotky se 100 až 1 000 virtuálních počítačů spuštěných současně. Konzistentnějšího pojem řešení centralizované enterprise testovacího prostředí, které můžou sdílet stejné zásady správy konfigurace a kontrolních mechanismů pro zabezpečení.

Tento model také zajišťuje, že organizace není vyčerpání jejich omezení prostředků, které jsou spojené s předplatným Azure. Podrobnosti o předplatném a limity služby najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md). DevTest Labs procesu zřizování může spotřebovat velký počet skupin prostředků. Můžete požádat o omezení navýšit prostřednictvím žádosti o podporu v rámci předplatného Azure DevTest. Prostředky v rámci předplatného produkční neovlivní roste vývoj předplatného používá. Další informace o škálování DevTest Labs, naleznete v tématu [kvóty a omezení škálování ve službě DevTest Labs](devtest-lab-scale-lab.md).

Běžné úrovně limit předplatného, který není potřeba pro je přidělování přiřazení rozsah IP sítě pro podporu vývoje odběry i produkčním prostředí. Tato přiřazení by měl účet pro růst v čase (za předpokladu, že místní připojení nebo jiné síťové topologie, která vyžaduje podnikům spravovat jejich síťového zásobníku místo nastavení na výchozí implementace Azure). Doporučeným postupem je do několika virtuálními sítěmi, které mají velké předponu adresy IP, přiřadit a rozdělit s mnoha velkých podsítě, a nikoli k více virtuálními sítěmi s malé podsítě. Například s 10 předplatným, můžete definovat 10 virtuálních sítí (jeden pro každé předplatné). Všechny testovací prostředí, které nevyžadují izolace můžete sdílet stejnou podsíť ve virtuální síti předplatného.

## <a name="maintain-naming-conventions"></a>Spravovat zásady vytváření názvů

### <a name="question"></a>Otázka
Jak spravovat zásady vytváření názvů Moje prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Můžete rozšířit stávající konvence pojmenování enterprise na provoz Azure a zajištění konzistentní napříč prostředí DevTest Labs.

Při nasazování DevTest Labs, doporučujeme, abyste měli určité výchozí zásady. Tyto zásady centrální skriptů a šablon JSON vynucovat konzistenci nasazujete. Pojmenování zásady je možné implementovat pomocí Azure zásady na úrovni předplatného. Ukázky JSON pro Azure Policy najdete v tématu [ukázek Azure Policy](../azure-policy/json-samples.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Počet uživatelů podle testovacího prostředí a cvičení na organizaci

### <a name="question"></a>Otázka 
Jak zjistím, poměr uživatelé testovacího prostředí a celkového počtu testovacích prostředí potřebné celé organizaci?

### <a name="answer"></a>Odpověď
Doporučujeme vám, že jsou přidruženy stejném testovacího prostředí obchodním jednotkám a vývojářské skupiny, které jsou přidruženy stejném projektu vývoje. Umožňuje stejné typy zásad, obrázky a vypnutí zásad u obou skupin. 

Můžete také zvažte geografické hranice. Například ve východních oblastech severní Spojené státy (USA) mohou vývojáři použít testovací prostředí zřízené ve východní USA 2. A mohou vývojáři v Dallasu, Texas a Liberci přesměrovány na prostředek v USA střed – jih. Při spolupráci s externí třetích stran může být přiřazen do testovacího prostředí, která není používána umožňuje interním vývojářům. 

Můžete také použít testovací prostředí pro konkrétní projekt v rámci projektů Azure DevOps. Pak můžete použít zabezpečení prostřednictvím zadané skupiny Azure Active Directory, který umožňuje přístup k oběma sadu prostředků. Virtuální síť přiřazeny k testovacím prostředí může být jiné hranice pro konsolidaci uživatelů.

## <a name="deletion-of-resources"></a>Odstranění prostředků

### <a name="question"></a>Otázka
Jak jsme zabránili odstranění prostředků v rámci testovacího prostředí?

### <a name="answer"></a>Odpověď
Doporučujeme nastavit příslušná oprávnění na úrovni prostředí tak, aby jenom Autorizovaní uživatelé můžete odstranit prostředky nebo změnit zásady testovacího prostředí. Vývojáři by měl být umístěn v rámci **DevTest Labs uživatelé** skupiny. Vedoucí vývojář nebo zájemce infrastruktury by měla být **DevTest Labs vlastníka**. Doporučujeme vám, že máte jenom dva vlastníků testovacího prostředí. Tato zásada rozšiřuje na úložiště kódu, aby se zabránilo poškození. Testovací prostředí používá mít oprávnění k používání prostředků, ale nelze aktualizovat zásady testovacího prostředí. Přečtěte si článek, který obsahuje seznam rolí a oprávnění, která má každý předdefinované skupiny v rámci testovacího prostředí: [přidat vlastníky a uživatele ve službě Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Přesunout do jiné skupiny prostředků testovacího prostředí 

### <a name="question"></a>Otázka
Je podporováno přesunout do jiné skupiny prostředků testovacího prostředí?

### <a name="answer"></a>Odpověď
Ano. Přejděte na stránku skupiny prostředků na domovské stránce testovacího prostředí. Vyberte **přesunout** na panelu nástrojů a vyberte testovací prostředí, které chcete přesunout do jiné skupiny prostředků. Při vytváření testovacího prostředí se pro vás automaticky vytvoří skupinu prostředků. Však můžete přesunout do jiné skupiny prostředků, které dodržuje zásady vytváření názvů enterprise testovacího prostředí. 

## <a name="next-steps"></a>Další postup
Zobrazit [spravovat náklady a vlastnictví](devtest-lab-guidance-governance-cost-ownership.md).
