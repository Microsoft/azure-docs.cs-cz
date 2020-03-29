---
title: Vylepšete provozní excelenci pro vaše předplatná Azure pomocí Azure Advisoru
description: Využijte Poradce k optimalizaci a zralosti v provozní dokonalosti pro vaše předplatná Azure
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443078"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Dosáhněte provozní dokonalosti s Azure Advisorem

Doporučení pro provozní dokonalost Azure Advisor pomáhají zákazníkům s efektivitou procesů a pracovních postupů, možností správy zdrojů a doporučenými postupy nasazení. Tato doporučení můžete získat od poradce na kartě **Provozní dokonalost** řídicího panelu Poradce.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytvořte výstrahy stavu služby Azure, které mají být upozorňovány, když se vás týkají problémy s Azure

Doporučujeme nastavit výstrahy stavu služby Azure, které mají být upozorňovány, když se vás týkají problémy se službou Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje přizpůsobené pokyny a podporu, když se vás na vliv na problém služby Azure. Advisor identifikuje odběry, které nemají výstrahy nakonfigurována a doporučuje vytvořit jeden.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Navrhněte účty úložiště, abyste zabránili dosažení maximálního limitu předplatného

Oblast Azure může podporovat maximálně 250 účtů úložiště na jedno předplatné. Po dosažení limitu nebudete moci vytvořit žádné další účty úložiště v této kombinaci oblasti/předplatného. Poradce zkontroluje vaše předplatná a doporučení povrchu, abyste měli navrhnout méně účtů úložiště pro všechny, které se blíží dosažení maximálního limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k cloudovým odborníkům Azure, když to potřebujete

Při spuštění úlohy kritické pro podnikání je důležité mít v případě potřeby přístup k technické podpoře. Advisor identifikuje potenciální důležitá obchodní předplatná, která nemají technickou podporu zahrnutou v plánu podpory, a doporučuje upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="repair-invalid-log-alert-rules"></a>Oprava neplatných pravidel upozornění protokolu

Azure Advisor zjistí pravidla výstrah, která mají neplatné dotazy zadané v jejich části podmínky. Pravidla upozornění protokolu se vytváří ve službě Azure Monitor a slouží ke spouštění analytických dotazů v určených intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se v průběhu času můžou stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce vám doporučí opravit dotaz v pravidle výstrahy, abyste zabránili jeho automatickému zakázání a zajistili monitorování pokrytí vašich prostředků v Azure. [Další informace o řešení pravidel upozornění](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Podle osvědčených postupů pomocí Zásad Azure

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynucují různá pravidla a efekty pro vaše prostředky. Níže jsou uvedena doporučení zásad Azure, která vám pomohou dosáhnout provozní excelence: 
1. Správa značek pomocí zásad Azure: Tato zásada přidá nebo nahradí zadanou značku a hodnotu při vytvoření nebo aktualizaci jakéhokoli prostředku. Stávající prostředky je možné napravit aktivací úlohy nápravy. To také nemění značky ve skupinách prostředků.
2. Vynuťte požadavky na geografickou kompatibilitu pomocí zásad Azure: Zásady umožňují omezit umístění, která vaše organizace může určit při nasazování prostředků. 
3. Určete povolené su su virtuálního počítače pro nasazení: Tato zásada umožňuje určit sadu jednotek SKU virtuálních strojů, které může vaše organizace nasadit.
4. Vynucení příkazu Auditovat virtuální počítače, které nepoužívají spravované disky, pomocí zásad Azure
5. Použijte "Zdědit značku ze skupin prostředků" pomocí zásad Azure: Zásada přidá nebo nahradí zadanou značku a hodnotu z nadřazené skupiny prostředků při vytvoření nebo aktualizaci jakéhokoli prostředku. Stávající prostředky je možné napravit aktivací úlohy nápravy.

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců najdete v tématu:
* [Úvod do poradce](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení nákladů poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
* [Doporučení poradce pro vysokou dostupnost](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
