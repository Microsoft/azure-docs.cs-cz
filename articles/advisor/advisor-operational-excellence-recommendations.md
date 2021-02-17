---
title: Vylepšení provozního excellencyu pomocí Poradce
description: Využijte Azure Advisor k optimalizaci a vyspělosti vaší provozní kvality vašich předplatných Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 0b938a0c7a42182bb8d2a50b48d65a0844d952a6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579963"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Dosažení provozní úrovně pomocí Azure Advisor

Doporučení provozní kvality v Azure Advisor vám může pomáhat: 
- Efektivita procesů a pracovních postupů.
- Spravovatelnost prostředků.
- Osvědčené postupy nasazení. 

Tato doporučení můžete získat na kartě **provozní výkon** řídicího panelu poradce.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Vytváření upozornění na Azure Service Health, která se mají upozornit, když vás budou problémy s Azure týkat

Doporučujeme, abyste nastavili výstrahy Azure Service Health, abyste se dostali k problémům se službou Azure, abyste byli informováni. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu v případě, že potíže se službou Azure ovlivňují. Advisor identifikuje odběry, u kterých nejsou nakonfigurované výstrahy, a doporučuje je nakonfigurovat.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Navrhněte svoje účty úložiště, abyste zabránili dosažení maximálního limitu předplatného.

Oblast Azure může podporovat maximálně 250 účtů úložiště na jedno předplatné. Po dosažení tohoto limitu nebudete moci vytvořit účty úložiště v této kombinaci oblasti nebo předplatného. Advisor kontroluje vaše předplatná a poskytuje doporučení, která vám pomůžou navrhnout méně účtů úložiště pro všechny oblasti a předplatné, které se blíží limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k odborníkům na cloud Azure, když ho potřebujete.

Při používání důležitých podnikových úloh je důležité mít přístup k technické podpoře, když ji potřebujete. Advisor identifikuje potenciální obchodní předplatná, která nemají technickou podporu zahrnutou do plánu podpory. Doporučuje se upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Odstraní a znovu vytvoří fond pro odebrání zastaralé interní součásti.

Pokud váš fond používá zastaralou interní komponentu, odstraňte a znovu vytvořte fond pro lepší stabilitu a výkon.

## <a name="repair-invalid-log-alert-rules"></a>Opravit neplatná pravidla upozornění protokolu

Azure Advisor detekuje pravidla výstrah, která mají v oddílu podmínky zadány neplatné dotazy. Můžete vytvořit pravidla upozornění protokolu v Azure Monitor a použít je ke spouštění analytických dotazů v zadaných intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se můžou v průběhu času stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce doporučuje, abyste v pravidle výstrahy opravili dotaz, abyste zabránili jeho automatickému zakázání a zajistili monitorování pokrytí vašich prostředků v Azure. [Přečtěte si další informace o řešení potíží s pravidly výstrah.](../azure-monitor/alerts/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Použití Azure Policy doporučení

Azure Policy je služba v Azure, kterou můžete použít k vytváření, přiřazování a správě zásad. Tyto zásady vynutily pravidla a účinky na vaše prostředky. Následující Azure Policy doporučení vám pomůžou dosáhnout provozní excellency: 

**Správa značek.** Tyto zásady při vytvoření nebo aktualizaci jakéhokoli prostředku přidají nebo nahradí zadanou značku a hodnotu. Stávající prostředky můžete opravit tak, že aktivujete úlohu nápravy. Tato zásada neupravuje značky ve skupinách prostředků.

**Vyvynuťte požadavky na geografické dodržování předpisů.** Tyto zásady umožňují omezit umístění, která může vaše organizace zadat při nasazování prostředků. 

**Zadejte povolené SKU virtuálních počítačů pro nasazení.** Tyto zásady umožňují zadat sadu skladových položek virtuálních počítačů, které může vaše organizace nasazovat.

**Vynutili *audit virtuálních počítačů, které nepoužívají spravované disky*.**

**Povolení *zdědí značku ze skupin prostředků*.** Tyto zásady při vytvoření nebo aktualizaci jakéhokoli prostředku přidají nebo nahradí zadanou značku a hodnotu z nadřazené skupiny prostředků. Stávající prostředky můžete opravit tak, že aktivujete úlohu nápravy.

Poradce doporučuje několik individuálních zásad Azure, které zákazníkům pomohou dosáhnout provozní kvality, a to přijetím osvědčených postupů. Pokud se zákazník rozhodne přiřadit Doporučené zásady, potlačíme doporučení. Pokud se zákazník rozhodne zásadu odebrat později, bude poradce i nadále potlačit doporučení, protože interpretuje jeho odebrání jako silný signál k následujícím akcím:

1.  Zákazník zásadu odebral, protože navzdory doporučení Poradce pro něj neplatí pro svůj konkrétní případ použití. 
2.  Zákazník si tyto zásady před přiřazením a odebráním ví a v případě, že ji přiřadí a odebere, a může je znovu přiřadit nebo odebrat podle potřeby bez ohledu na to, jestli se později bude relevantní pro případ použití. Pokud ji zákazník najde v nejlepším zájmu, aby znovu přiřadil stejné zásady, může tak učinit v Azure Policy, aniž by vyžadoval doporučení v Advisoru. Všimněte si, že tato logika se vztahuje konkrétně na doporučení zásad v kategorii provozní kvality. Tato pravidla neplatí pro doporučení zabezpečení.  


## <a name="no-validation-environment-enabled"></a>Není povolené žádné prostředí pro ověřování
Azure Advisor určuje, že v aktuálním předplatném není povolené prostředí ověřování. Při vytváření fondů hostitelů jste na \" kartě Vlastnosti vybrali možnost Ne \" pro \" prostředí ověřování \" . Aspoň jeden fond hostitelů s povoleným ověřovacím prostředím zajišťuje kontinuitu podnikových služeb prostřednictvím nasazení služby Virtual Desktop systému Windows s dřívější detekcí potenciálních problémů. [Další informace](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Zajištění produkčního prostředí (ne prostředí pro ověřování) za účelem získání výhod stabilních funkcí
Azure Advisor zjistí, že je v prostředí ověřování zapnuté příliš mnoho fondů hostitelů. Aby mohla prostředí ověřování nejlépe zajišťovat jejich účel, měli byste mít alespoň jednu, ale nikdy více než polovinu fondů hostitelů v ověřovacím prostředí. Díky povolenému zůstatku mezi fondy hostitelů s povoleným ověřovacím prostředím a s tím, jak je zakázané, budete mít možnost využívat výhody nasazení s více fázemi, které nabízí virtuální plocha Windows s určitými aktualizacemi. Chcete-li tento problém vyřešit, otevřete vlastnosti fondu hostitelů a vyberte možnost \" ne \" vedle \" nastavení prostředí ověřování \" .

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Povolení Analýzy provozu a zobrazení přehledů o vzorcích provozu napříč prostředky Azure
Analýza provozu je cloudové řešení, které umožňuje získat přehled o aktivitách uživatelů a aplikací v Azure. Analýza provozu poskytuje přehledy o toku provozu na základě analýzy protokolů toků skupiny zabezpečení sítě (NSG) služby Network Watcher. Analýza provozu umožňuje zobrazit hlavní mluvčí napříč nasazeními Azure i jinými nasazeními, zkoumat otevřené porty, protokoly a škodlivé toky ve vašem prostředí a optimalizovat nasazení sítě z hlediska výkonu. Protokoly toků můžete zpracovávat v 10minutových a 60minutových intervalech, které zajišťují rychlejší analýzy provozu. Je dobrým zvykem povolit Analýza provozu pro prostředky Azure. 


## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [REST API Advisoru](/rest/api/advisor/)
