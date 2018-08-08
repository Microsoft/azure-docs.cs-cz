---
title: Doporučení Azure Advisoru vysoké dostupnosti | Dokumentace Microsoftu
description: Použití Azure Advisoru ke zlepšení vysoké dostupnosti všech vašich nasazení Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 297a213fe4219b834187f977e3281eb939352f60
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594429"
---
# <a name="advisor-high-availability-recommendations"></a>Poradce doporučení k vysoké dostupnosti

Azure Advisor pomáhá zajistit a zlepšování kontinuity důležité podnikové aplikace. Doporučení k vysoké dostupnosti v Advisoru z tak můžete získat **vysoké dostupnosti** karty řídicí panel služby Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zajištění odolnosti proti chybám virtuálních počítačů

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor identifikuje virtuální počítače, které nejsou součástí skupiny dostupnosti a doporučuje jejich přesunutí do skupiny dostupnosti. Tato konfigurace zajistí, že během buď plánované i neplánované údržby, alespoň jeden virtuální počítač je k dispozici a splňuje SLA pro Azure virtual Machines. Můžete vytvořit skupinu dostupnosti pro virtuální počítač nebo virtuální počítač přidat do stávající sady dostupnosti.

> [!NOTE]
> Pokud budete chtít vytvořit skupinu dostupnosti, je nutné přidat alespoň jeden další virtuální počítač do něj. Doporučujeme, abyste během výpadku je k dispozici, které sami seskupíte dvě nebo více virtuálních počítačů ve skupině dostupnosti nastavena zajistit, že aspoň jeden počítač.

## <a name="ensure-availability-set-fault-tolerance"></a>Ujistěte se, že skupina dostupnosti, odolnosti proti chybám 

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor určí skupiny dostupnosti, které obsahují jeden virtuální počítač a doporučuje přidání jednoho nebo více virtuálních počítačů k němu. Tato konfigurace zajistí, že během buď plánované i neplánované údržby, alespoň jeden virtuální počítač je k dispozici a splňuje SLA pro Azure virtual Machines. Můžete k vytvoření virtuálního počítače nebo přidat existující virtuální počítač do skupiny dostupnosti.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Zajištění odolnosti proti chybám application gateway
Zajištění kontinuity obchodních procesů klíčových aplikací, které využívají služby application Gateway, identifikuje Advisor bránu instance aplikace, které nejsou konfigurovány pro odolnost proti chybám a navrhne nápravné akce, které můžete provést. Advisor určuje střední a velké aplikace s jedinou instancí brány a doporučí přidávání nejméně jedna další instance. Také určuje instance jednoho nebo více malých aplikačních bran a doporučuje migrace na střední nebo velké skladové položky. Advisor doporučuje tyto akce, které vaše aplikace instance brány jsou nakonfigurujte tak, aby splňovat požadavky na aktuální smlouvy SLA pro tyto prostředky.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Zvýšit výkon a spolehlivost disků virtuálního počítače.

Advisor identifikuje virtuální počítače s disky standard a doporučuje upgrade na disky premium.
 
Azure Premium Storage poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače, na kterých běží I intenzivních vstupně-výstupních operací. Disky virtuálních počítačů, které používají účty služby premium storage ukládat data na jednotky SSD (Solid-State Drive). Pro zajištění nejlepšího výkonu pro vaši aplikaci doporučujeme migrovat všechny disky virtuálního počítače, které vyžadují vysokou vstupně-výstupních operací na premium storage. 

Pokud vaše disky nevyžadují vysoké IOPS, můžete omezit náklady na údržbu ve standardním úložišti. Storage úrovně Standard ukládá data na disku virtuálního počítače na jednotky pevných disků (HDD) namísto jednotky SSD. Můžete k migraci disků vašich virtuálních počítačů na disky premium. Disky Premium podporují ve většině skladových položek virtuálních počítačů. Ale v některých případech, pokud chcete použít disky úrovně premium, potřebujete k upgradu vašeho skladových položek virtuálních počítačů i.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrana před náhodným odstraněním dat virtuálního počítače

Nastavení zálohování virtuálních počítačů zajistí dostupnost důležitých podnikových dat a poskytuje ochranu proti náhodnému odstranění nebo poškození.  Advisor identifikuje virtuální počítače, kde není povolené zálohování, a doporučí povolení zálohování. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zajistěte, že abyste měli přístup k odborníkům cloudu Azure, kdykoli ji potřebujete

Při spuštění nejzásadnější úlohy, je důležité mít přístup k technické podpoře v případě potřeby. Advisor identifikuje potenciální nejzásadnější odběry, které nemají v jejich plán podpory součástí je technická podpora a doporučuje upgrade na možnost, která obsahuje technickou podporu.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytvoření služby Azure Service Health výstrahy, která vás upozorní, kdy vás ovlivňují problémy Azure

Doporučujeme nastavit upozornění služby Azure Service Health, která vás upozorní, kdy vás ovlivňují problémy se službami Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu, když jsou ovlivněny problém se službou Azure. Advisor určí odběry, které nemají v nakonfigurovaná upozornění a doporučuje vytvoření nového.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurace koncových bodů Traffic Manageru pro odolnost proti chybám

Profily Traffic Manageru s více než jeden koncový bod prostředí vyšší dostupnost, pokud se nezdaří libovolný daný koncový bod. Umístění koncových bodů v různých oblastech dále zvyšuje spolehlivost služby. Advisor identifikuje profily Traffic Manageru níž se nachází pouze jeden koncový bod a doporučuje přidávání aspoň jeden další koncový bod v jiné oblasti.

Pokud všechny koncové body v profilu služby Traffic Manager, který je nakonfigurovaný pro směrování blízkých výrazů jsou ve stejné oblasti, uživatelé z jiných oblastí zaznamenat zpoždění připojení. Přidání nebo přesunutí koncový bod do jiné oblasti bude celkový výkon a zajištění lepší dostupnosti, pokud selžou i všechny koncové body v jedné oblasti. Advisor určí profily Traffic Manageru, které jsou nakonfigurované pro bezkontaktní směrování, kde jsou všechny koncové body ve stejné oblasti a doporučuje přidání nebo přesunutí koncový bod do jiné oblasti Azure.

Pokud se profil služby Traffic Manager je nakonfigurovaný pro geografické směrování, provoz se směruje do koncových bodů podle definovaných oblastí. Pokud se nezdaří oblasti, neexistuje žádné předdefinované převzetí služeb při selhání. Máte koncový bod, ve které oblastní seskupení je konfigurována pro "Všechny (celý svět)" vyhnout provoz probíhá vyřazování a vylepšit dostupnost služeb. Advisor identifikuje profily Traffic Manageru, které jsou nakonfigurované pro geografické směrování, pokud neexistuje žádný koncový bod nakonfigurovaný tak, aby mít oblastní seskupení jako "Všechny (celý svět)" a doporučuje provedením této změny konfigurace.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak získat přístup k doporučení k vysoké dostupnosti v Advisoru

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na tlačítko **vysoké dostupnosti** kartu.

## <a name="next-steps"></a>Další postup

Další informace o doporučení Advisoru naleznete v tématu:
* [Úvod do Azure Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Náklady na doporučení advisoru](advisor-performance-recommendations.md)
* [Poradce doporučení k výkonu](advisor-performance-recommendations.md)
* [Bezpečnostní doporučení advisoru](advisor-security-recommendations.md)

