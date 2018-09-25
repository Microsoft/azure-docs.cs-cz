---
title: Zásady zabezpečení služby Azure Security Center můžete nastavit, samostatně nebo jako součást zásady Azure | Dokumentace Microsoftu
description: Tento dokument vám umožní nastavit zásady ve službě Azure Security Center nebo ve službě Azure Policy.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: 3c198ea44953c0b2e72a544cd0e83b6592d9a81f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032068"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Nastavení zásad zabezpečení ve službě Security Center nebo ve službě Azure Policy

Tento článek vám pomůže nakonfigurovat zásady zabezpečení služby Azure Security Center. Zásady Centrum zabezpečení Azure integrovat zásady Azure, abyste mohli nastavit je buď ve službě Security Center na konkrétní předplatné nebo v [Azure Policy](../azure-policy/azure-policy-introduction.md), což vám umožní nastavit zásady napříč skupinami pro správu a mezi různými předplatná...

## <a name="what-are-security-policies"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Azure Security Center můžete definovat zásady pro vaše předplatná Azure a je typu úlohy nebo citlivosti dat. přizpůsobit. Aplikace, které používají regulovaná data, jako jsou identifikovatelné osobní údaje, například může vyžadovat vyšší úroveň zabezpečení než jiné úlohy. Nastavit zásady napříč předplatnými nebo skupin pro správu, je nastavit [Azure Policy](../azure-policy/azure-policy-introduction.md).

> [!NOTE]
> Pokud jste dříve nakonfigurovali zásady zabezpečení na předplatné, které je součástí skupiny pro správu, nebo má více tento počet přiřazení zásad, tyto zásady se zobrazí šedě ve službě Security Center, můžete spravovat tuto zásadu na úrovni skupiny pro správu Azure Stránka zásad. 

## <a name="how-security-policies-work"></a>Způsob fungování zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Zásady můžete upravit ve službě Security Center nebo můžete Azure Policy využít k následujícím úlohám:
- Vytvoření nových definic zásad
- Přiřazení zásad napříč skupinami pro správu a předplatnými, které můžou reprezentovat celou organizaci nebo obchodní jednotku v rámci organizace
- Monitorování dodržování zásad

Další informace o službě Azure Policy najdete v tématu [Vytvoření a správa zásad pro vynucování dodržování předpisů](../azure-policy/create-manage-policy.md).

Zásada Azure se skládá z následujících součástí:

- **Zásada** je pravidlo.
- **Iniciativa** je kolekce zásad.
- **Přiřazení** je použití iniciativy nebo zásady na konkrétní obor (skupina pro správu, předplatné nebo skupina prostředků).

U prostředku se vyhodnotí zásady, které jsou k němu přiřazené, a ten pak získá koeficient dodržování předpisů podle počtu zásad, jejichž předpisy dodržuje.

## <a name="who-can-edit-security-policies"></a>Kdo může upravit zásady zabezpečení?
Security Center používá Role-Based řízení přístupu (RBAC), který poskytuje předdefinované role, které je možné přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace týkající se prostředky, ke kterým mají přístup. Což znamená, že uživatelé mají přiřazenou roli vlastník, Přispěvatel nebo Čtenář na předplatné nebo skupinu prostředků, které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- Čtenář zabezpečení: mají zobrazit práva ke službě Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale že nemůže provádět změny.
- Správce zabezpečení: mají práva zobrazení stejné jako čtenář zabezpečení, a můžete také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.

## <a name="edit-security-policies"></a>Upravení zásad zabezpečení
Ve službě Security Center můžete upravit výchozí zásady zabezpečení pro každé z vašich skupin pro správu a předplatných Azure. Pokud chcete upravit zásady zabezpečení, musíte být vlastníkem, přispěvatelem nebo správcem zabezpečení daného předplatného nebo skupiny pro správu, která ho obsahuje. Zobrazení zásad zabezpečení ve službě Security Center:

> [!NOTE]
> Žádné zásady nastavené v rámci předplatného, který je součástí skupiny pro správu, nebo má více přiřazení zásad, se zobrazí šedě ve službě Security Center. Můžete upravit tyto zásady v [Azure Policy](../azure-policy/azure-policy-introduction.md). 

1. Na řídicím panelu **Security Center** v části **ZÁSADY A DODRŽOVÁÍ PŘEDPISŮ** vyberte **Zásady zabezpečení**. Otevře se okno **Správa zásad**.

    ![Okno Správa zásad](./media/security-center-azure-policy/security-center-policies-fig10.png)

  V okně Správa zásad se zobrazí počet skupin pro správu, předplatných a pracovních prostorů a také struktura skupin pro správu.

  > [!NOTE]
  > Na řídicím panelu Security Center se v části **Pokrytí předplatného** může zobrazit vyšší počet předplatných než v části **Správa zásad**. V části Pokrytí předplatného se zobrazuje počet předplatných úrovně Standard a Free a „nepokrytých“ předplatných. „Nepokrytá“ předplatná nemají povolenou službu Security Center a nezobrazují se v části **Správa zásad**.
  >
  >

  Ve sloupcích tabulky se zobrazuje:

 - Přiřazení iniciativy zásad – Předdefinované zásady a iniciativy ve službě Security Center, které jsou přiřazené k předplatnému nebo skupině pro správu.
 - Dodržování předpisů – Celkové skóre dodržování předpisů pro skupinu pro správu, předplatné nebo pracovní prostor. Skóre je váženým průměrem přiřazení. Tento vážený průměr ovlivňuje počet zásad v jednom přiřazení a počet prostředků, na které se přiřazení vztahuje.

 Například pokud předplatné obsahuje dva virtuální počítače a má přiřazenou iniciativu s pěti zásadami, pak máte v předplatném 10 interních hodnocení. Pokud jeden z virtuálních počítačů nedodržuje předpisy dvou zásad, pak je celkové skóre dodržování předpisů přiřazení vašeho předplatného 80 %.

 - Pokrytí – Identifikuje cenovou úroveň Free nebo Standard, kterou využívá skupina pro správu, předplatné nebo pracovní prostor.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
 - Nastavení – Předplatná mají odkaz **Upravit nastavení**. Po výběru **Upravit nastavení** můžete aktualizovat nastavení vašeho předplatného, jako je shromažďování dat, cenová úroveň a emailová oznámení.

2. Vyberte předplatné nebo skupinu pro správu, pro které chcete povolit zásady zabezpečení. Otevře se okno **Zásady zabezpečení**.

3.  V části **Zásady zabezpečení** vyberte ovládací prvky, které má Security Center monitorovat, a zapněte doporučení tím, že vyberete **Zapnuto**.  Pokud nechcete, aby služba Security Center dané nastavení monitorovala, vyberte **Vypnuto**.

    ![Součásti zásad](./media/security-center-azure-policy/security-policy.png)

4. Vyberte **Uložit**.

## <a name="available-security-policy-definitions"></a>Dostupné definice zásad zabezpečení

Následující tabulku můžete použít jako referenci pro pochopení definic zásad, které jsou dostupné ve výchozích zásadách zabezpečení:

| Zásada | Co tato povolená zásada dělá |
| --- | --- |
| Aktualizace systému |Načte denní seznam dostupných aktualizací zabezpečení a důležitých aktualizací z webu Windows Update nebo ze služby Windows Server Update Services. Načtený seznam závisí na službě nakonfigurované pro vaše virtuální počítače a doporučuje instalaci chybějících aktualizací. V systémech Linux zásada pro určení balíčků, pro které jsou dostupné aktualizace, využívá systém správy balíčků, který je součástí dané distribuce. Také kontroluje aktualizace zabezpečení a důležité aktualizace z virtuálních počítačů služby [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Konfigurace zabezpečení |Denně analyzuje konfigurace operačního systému k určení problémů, které by mohly způsobit zranitelnost virtuálních počítačů vůči útoku. Zásada také doporučuje změny v konfiguraci pro odstranění těchto ohrožení zabezpečení. Další informace o tom, jaké konkrétní konfigurace se monitorují, najdete v [seznamu doporučených standardních hodnot](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (V tomto okamžiku není Windows Server 2016 plně podporovaný.) |
| Ochrana koncových bodů |Doporučuje nastavení ochrany koncových bodů pro všechny virtuální počítače s Windows, aby bylo možné identifikovat a odstraňovat viry, spyware a další škodlivý software. |
| Šifrování disku |Doporučuje povolit šifrování disku ve všech virtuálních počítačích pro zvýšení ochrany dat při nečinnosti. |
| Skupiny zabezpečení sítě |Doporučuje konfiguraci [skupin zabezpečení sítě](../virtual-network/security-overview.md), které řídí příchozí a odchozí přenosy dat do virtuálních počítačů s veřejnými koncovými body. Pokud neurčíte jinak, skupiny zabezpečení sítě nakonfigurované pro určitou podsíť se dědí do všech síťových rozhraní virtuálních počítačů. Kromě kontroly toho, jestli je skupina zabezpečení sítě nakonfigurovaná, tato zásada také vyhodnocuje pravidla zabezpečení příchozích dat a zjišťuje pravidla, která povolují příchozí přenosy dat. |
| Brána firewall webových aplikací |Doporučuje nastavení brány firewall webových aplikací na virtuálních počítačích, pokud je splněná jedna z následujících podmínek: <ul><li>Používá se [veřejná IP adresa na úrovni instance](../virtual-network/virtual-networks-instance-level-public-ip.md) a pravidla zabezpečení příchozích dat pro přidruženou skupinu zabezpečení sítě jsou nakonfigurovaná tak, aby povolovala přístup k portu 80 a 443.</li><li>Používá se IP adresa s vyrovnáváním zatížení a přidružené vyrovnávání zatížení i pravidla překladu adres příchozích dat jsou nakonfigurované tak, aby povolovaly přístup k portu 80 a 443. Další informace najdete v tématu [Podpora služby Load Balancer v Azure Resource Manageru](../load-balancer/load-balancer-arm.md).</li> |
| Brána firewall příští generace |Rozšiřuje ochranu sítě nad rámec skupin zabezpečení sítě, které jsou integrované v Azure. Security Center zjišťuje nasazení, pro která se doporučuje brána firewall nové generace, a potom můžete nastavit virtuální zařízení. |
| Detekce hrozeb a auditování SQL |Doporučuje, abyste povolili auditování přístupu ke službě Azure Database za účelem šetření dodržování předpisů a rozšířeného zjišťování hrozeb. |
| Šifrování SQL |Doporučuje povolit šifrování v klidovém stavu pro služby Azure SQL Database, přidružené zálohy a soubory protokolů transakcí. I v případě, že dojde k porušení zabezpečení vašich dat, nebudou čitelná. |
| Posouzení ohrožení zabezpečení |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| Šifrování úložiště |Tato funkce je aktuálně dostupná pro Soubory Azure a Azure Blob storage. Pokud povolíte šifrování služby Storage, budou se šifrovat jenom nová data a veškeré stávající soubory v účtu úložiště zůstanou nezašifrované. |
| Síťový přístup JIT |Pokud je síťový přístup JIT (právě včas) povolený, Security Center uzamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla skupiny zabezpečení sítě. Na virtuálním počítači vyberete porty, na které má být příchozí provoz uzamčen. Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |

## <a name="management-groups"></a>Skupiny pro správu
Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure poskytují úroveň rozsahu nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. Každý adresář obdrží jednu skupinu pro správu nejvyšší úrovně označovanou jako kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato kořenová skupina pro správu umožňuje používání globálních zásad a přiřazení RBAC na úrovni adresáře. Pokud chcete nastavit skupiny pro správu pro použití se službou Azure Security Center, postupujte podle pokynů v článku [Získání viditelnosti v rámci celého tenanta pro Azure Security Center](security-center-management-groups.md). 

> [!NOTE]
> Je důležité, abyste rozuměli hierarchii skupin pro správu a předplatných. Další informace o skupinách pro správu, kořenových skupinách pro správu a přístupu ke skupinám pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/index.md#root-management-group-for-each-directory).
>
>


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Získání viditelnosti v rámci celého tenanta pro Azure Security Center](security-center-management-groups.md) – Zjistěte, jak nastavit skupiny pro správu pro Azure Security Center. 
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

Další informace o Azure Policy najdete v tématu [Co je Azure Policy](../azure-policy/azure-policy-introduction.md).
