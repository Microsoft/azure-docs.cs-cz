---
title: Zvyšte provozní excellency pro vaše předplatná Azure pomocí Azure Advisor
description: Využijte poradce k optimalizaci a vyspělosti provozní kvality vašich předplatných Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 861b12c2267ffa89985ff11357de92da5a4ac870
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658454"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Zajištění provozní kvality pomocí Azure Advisor

Azure Advisor doporučení provozní kvality, která zákazníkům pomůžou s efektivitou procesů a pracovních postupů, možností správy prostředků a nasazení osvědčenými postupy. Tato doporučení můžete získat z Advisoru na kartě **provozní výkon** řídicího panelu Advisoru.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytváření upozornění na Azure Service Health, která se mají upozornit, když se na vás bude týkat problémy Azure

Doporučujeme, abyste nastavili výstrahy Azure Service Health, aby byly oznamovány, když se vás týkají problémy se službou Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu v případě, že máte vliv na problém se službou Azure. Advisor identifikuje odběry, u kterých nejsou nakonfigurované výstrahy, a doporučuje je vytvořit.


## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Návrh účtů úložiště, aby nedošlo k překročení maximálního limitu předplatného

Oblast Azure může podporovat maximálně 250 účtů úložiště na jedno předplatné. Po dosažení tohoto limitu nebudete moct vytvořit žádné další účty úložiště v této kombinaci oblasti nebo předplatného. Advisor zkontroluje vaše předplatná a doporučení pro plochu, abyste mohli navrhovat méně účtů úložiště pro všechny, které jsou blízko dosažení maximálního limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k odborníkům na cloud Azure, když ho potřebujete.

Při používání důležitých podnikových úloh je důležité mít v případě potřeby přístup k technické podpoře. Advisor identifikuje potenciální podniková předplatná, která neobsahují technickou podporu zahrnutou do plánu podpory a doporučuje upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="delete-and-recreate-your-pool-to-remove-a-deprecated-internal-component"></a>Odebrání zastaralé interní komponenty odstraněním a opětovným vytvořením fondu

Váš fond využívá zastaralou interní komponentu. Pokud chcete zvýšit stabilitu a výkon, odeberte a znovu vytvořte fond.

## <a name="repair-invalid-log-alert-rules"></a>Opravit neplatná pravidla upozornění protokolu

Azure Advisor zjistí pravidla upozornění, která mají v oddílu podmínky zadány neplatné dotazy. Pravidla upozornění protokolu se vytváří ve službě Azure Monitor a slouží ke spouštění analytických dotazů v určených intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se v průběhu času můžou stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce doporučujeme, abyste v pravidle výstrahy opravili dotaz, abyste zabránili jeho automatickému zakázání a zajistili sledování pokrytí vašich prostředků v Azure. [Další informace o řešení potíží s pravidly upozornění](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Dodržujte doporučené postupy pomocí Azure Policy

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynucují různá pravidla a efekty pro vaše prostředky. Níže jsou uvedená doporučení Azure Policy, která vám pomůžou dosáhnout provozní excellency: 
1. Správa značek pomocí Azure Policy: tyto zásady při vytváření nebo aktualizaci libovolného prostředku přidávají nebo nahradí zadanou značku a hodnotu. Stávající prostředky je možné napravit aktivací úlohy nápravy. Tato změna také nemění značky ve skupinách prostředků.
2. Vysaďte požadavky na geografické dodržování předpisů pomocí Azure Policy: zásady vám umožní omezit umístění, která může organizace zadat při nasazování prostředků. 
3. Zadejte povolené SKU virtuálních počítačů pro nasazení: Tato zásada umožňuje zadat sadu SKU virtuálních počítačů, které může vaše organizace nasazovat.
4. Vynucování zásad Auditovat virtuální počítače, které nepoužívajíc spravované disky, s využitím služby Azure Policy
5. Použijte možnost zdědit značku ze skupin prostředků pomocí Azure Policy: zásada přidá nebo nahradí určenou značku a hodnotu z nadřazené skupiny prostředků při vytvoření nebo aktualizaci některého prostředku. Stávající prostředky je možné napravit aktivací úlohy nápravy.

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
