---
title: Vylepšení provozního excellencyu pomocí Poradce
description: Využijte Azure Advisor k optimalizaci a vyspělosti vaší provozní kvality vašich předplatných Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 1cac5a3f93df8422a3896b1251857bf552731fb4
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125391"
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

Azure Advisor detekuje pravidla výstrah, která mají v oddílu podmínky zadány neplatné dotazy. Můžete vytvořit pravidla upozornění protokolu v Azure Monitor a použít je ke spouštění analytických dotazů v zadaných intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se můžou v průběhu času stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce doporučuje, abyste v pravidle výstrahy opravili dotaz, abyste zabránili jeho automatickému zakázání a zajistili monitorování pokrytí vašich prostředků v Azure. [Přečtěte si další informace o řešení potíží s pravidly výstrah.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Použití Azure Policy doporučení

Azure Policy je služba v Azure, kterou můžete použít k vytváření, přiřazování a správě zásad. Tyto zásady vynutily pravidla a účinky na vaše prostředky. Následující Azure Policy doporučení vám pomůžou dosáhnout provozní excellency: 

**Správa značek.** Tyto zásady při vytvoření nebo aktualizaci jakéhokoli prostředku přidají nebo nahradí zadanou značku a hodnotu. Stávající prostředky můžete opravit tak, že aktivujete úlohu nápravy. Tato zásada neupravuje značky ve skupinách prostředků.

**Vyvynuťte požadavky na geografické dodržování předpisů.** Tyto zásady umožňují omezit umístění, která může vaše organizace zadat při nasazování prostředků. 

**Zadejte povolené SKU virtuálních počítačů pro nasazení.** Tyto zásady umožňují zadat sadu skladových položek virtuálních počítačů, které může vaše organizace nasazovat.

**Vynutili *audit virtuálních počítačů, které nepoužívají spravované disky*.**

**Povolení *zdědí značku ze skupin prostředků*.** Tyto zásady při vytvoření nebo aktualizaci jakéhokoli prostředku přidají nebo nahradí zadanou značku a hodnotu z nadřazené skupiny prostředků. Stávající prostředky můžete opravit tak, že aktivujete úlohu nápravy.

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [REST API Advisoru](https://docs.microsoft.com/rest/api/advisor/)
