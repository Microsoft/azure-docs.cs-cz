---
title: Zvyšte provozní excellency pro vaše předplatná Azure pomocí Azure Advisor | Microsoft Docs
description: Pomocí služby Advisor můžete optimalizovat a dosáhnout provozní kvality předplatných Azure.
services: advisor
documentationcenter: NA
author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 10/24/2019
ms.author: sagupt
ms.openlocfilehash: 7cf3d3b34d0921cd111f8111bc2008ef0eced962
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033287"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Zajištění provozní kvality pomocí Azure Advisor

Azure Advisor doporučení provozní kvality, která zákazníkům pomůžou s efektivitou procesů a pracovních postupů, možností správy prostředků a nasazení osvědčenými postupy. Tato doporučení můžete získat z Advisoru na kartě **provozní výkon** řídicího panelu Advisoru.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytváření upozornění na Azure Service Health, která se mají upozornit, když se na vás bude týkat problémy Azure

Doporučujeme, abyste nastavili výstrahy Azure Service Health, aby byly oznamovány, když se vás týkají problémy se službou Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu v případě, že máte vliv na problém se službou Azure. Advisor identifikuje odběry, u kterých nejsou nakonfigurované výstrahy, a doporučuje je vytvořit.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Návrh účtů úložiště, aby nedošlo k překročení maximálního limitu předplatného

Oblast Azure může podporovat maximálně 250 účtů úložiště na jedno předplatné. Po dosažení tohoto limitu nebudete moct vytvořit žádné další účty úložiště v této kombinaci oblasti nebo předplatného. Advisor zkontroluje vaše předplatná a doporučení pro plochu, abyste mohli navrhovat méně účtů úložiště pro všechny, které jsou blízko dosažení maximálního limitu.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k odborníkům na cloud Azure, když ho potřebujete.

Při používání důležitých podnikových úloh je důležité mít v případě potřeby přístup k technické podpoře. Advisor identifikuje potenciální podniková předplatná, která neobsahují technickou podporu zahrnutou do plánu podpory a doporučuje upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="repair-invalid-log-alert-rules"></a>Opravit neplatná pravidla upozornění protokolu

Azure Advisor zjistí pravidla upozornění, která mají v oddílu podmínky zadány neplatné dotazy. Pravidla upozornění protokolu se vytváří ve službě Azure Monitor a slouží ke spouštění analytických dotazů v určených intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se v průběhu času můžou stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce doporučujeme, abyste v pravidle výstrahy opravili dotaz, abyste zabránili jeho automatickému zakázání a zajistili sledování pokrytí vašich prostředků v Azure. [Další informace o řešení potíží s pravidly upozornění](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Dodržujte doporučené postupy pomocí Azure Policy

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynutily různá pravidla a vliv na vaše prostředky. Níže jsou uvedená doporučení k zásadám Azure, která vám pomůžou dosáhnout provozní excellency: 
1. Správa značek pomocí Azure Policy: tyto zásady při vytváření nebo aktualizaci libovolného prostředku přidávají nebo nahradí zadanou značku a hodnotu. Stávající prostředky lze opravit aktivací úlohy nápravy. Tato změna také nemění značky ve skupinách prostředků.
2. Vysaďte požadavky na geografické dodržování předpisů pomocí Azure Policy: zásady vám umožní omezit umístění, která může organizace zadat při nasazování prostředků. 
3. Zadejte povolené SKU virtuálních počítačů pro nasazení: Tato zásada umožňuje zadat sadu SKU virtuálních počítačů, které může vaše organizace nasazovat.
4. Vynutilit audit virtuálních počítačů, které nepoužívají spravované disky, pomocí zásad Azure
5. Použití příkazu zdědit značku ze skupin prostředků pomocí zásad Azure: když se nějaký prostředek vytvoří nebo aktualizuje, přidá nebo nahradí zadanou značku a hodnotu z nadřazené skupiny prostředků. Stávající prostředky lze opravit aktivací úlohy nápravy.

## <a name="next-steps"></a>Další kroky

Další informace o doporučení Advisoru najdete tady:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro vysokou dostupnost poradce](advisor-high-availability-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
