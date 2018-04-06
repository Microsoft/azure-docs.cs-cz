---
title: Analýza vzorce používání Azure CDN | Microsoft Docs
description: Tento článek popisuje různé typy sestavy analýzy, které jsou k dispozici pro produkty Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 3f475c5cc9b766ea9aa5bd39d4a378e8deed5e35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analýza vzorů využití Azure CDN

Jakmile povolíte CDN pro vaši aplikaci, můžete sledovat využití CDN, zkontrolujte stav vaší doručení a potenciální potíže. Azure CDN poskytuje tyto možnosti následujícími způsoby: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Základní analýza prostřednictvím Azure diagnostických protokolů

Základní analýza je k dispozici pro všechny koncové body CDN patřící do Verizon (Standard a Premium) a profilů CDN Akamai (Standard). Azure diagnostics protokoly umožňují základní analýza být exportovány do úložiště Azure, služby event hubs nebo analýzy protokolů. Analýzy protokolů nabízí řešení grafů, které jsou uživatelsky konfigurovatelného a přizpůsobit. Další informace najdete v tématu [Azure diagnostické protokoly](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon základní sestavy

Jako uživatel Azure CDN s **Azure CDN Standard od společnosti Verizon** nebo **Azure CDN Premium od společnosti Verizon** profil, můžete zobrazit sestavy základní Verizon Verizon doplňkovém portálu. Verizon základní sestavy je přístupná prostřednictvím **spravovat** možnost z portálu Azure a nabízí celou řadu zobrazení a grafy. Další informace najdete v tématu [základní sestavy od společnosti Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon vlastní sestavy

Jako uživatel Azure CDN s **Azure CDN Standard od společnosti Verizon** nebo **Azure CDN Premium od společnosti Verizon** profilu, můžete zobrazit na doplňkovém portálu Verizon Verizon vlastní sestavy. Verizon vlastní sestavy je přístupná prostřednictvím **spravovat** možnost z portálu Azure. Verizon vlastní sestavy stránky zobrazuje počet přístupů nebo data přenesená pro každý okraj CName, který patří do profilu Azure CDN. Data lze seskupovat podle kódu nebo mezipaměti stav odpovědi HTTP přes období. Další informace najdete v tématu [vlastní sestavy od společnosti Verizon](cdn-verizon-custom-reports.md).

## <a name="verizon-premium-reports"></a>Verizon premium sestavy

S **Azure CDN Premium od společnosti Verizon**, lze rovněž použít následující sestavy:
   * [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
   * [Statistiky v reálném čase](cdn-real-time-stats.md)
   * [Výkon hraničních uzlů](cdn-edge-performance.md)

