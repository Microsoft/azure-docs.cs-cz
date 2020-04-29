---
title: Analýza způsobů využití Azure CDN | Microsoft Docs
description: Tento článek popisuje různé typy sestav analýzy, které jsou k dispozici pro Azure CDN Products.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253607"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analýza vzorů využití Azure CDN

Po povolení CDN pro vaši aplikaci můžete monitorovat využití CDN, kontrolovat stav doručování a řešit potenciální problémy. Azure CDN poskytují tyto možnosti následujícími způsoby: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Základní analýzy prostřednictvím diagnostických protokolů Azure

Základní analýza je dostupná pro koncové body CDN pro všechny cenové úrovně. Protokoly diagnostiky Azure umožňují exportovat základní analýzy do úložiště Azure, do Center událostí nebo do protokolů Azure Monitor. Protokoly Azure Monitor nabízí řešení s grafy, které jsou uživatelsky konfigurovatelné a přizpůsobitelné. Další informace o diagnostických protokolech Azure najdete v tématu [diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Základní sestavy Verizon

Jako uživatel Azure CDN se **standardem Azure CDN od Verizon** nebo **Azure CDN Premium z profilu Verizon** můžete zobrazit sestavy Verizon Core na doplňkovém portálu Verizon. Sestavy Verizon Core jsou přístupné prostřednictvím možnosti **Spravovat** z Azure Portal a nabízí celou řadu grafů a zobrazení. Další informace najdete v tématu [základní sestavy z Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Vlastní sestavy Verizon

Jako uživatel Azure CDN se **standardem Azure CDN od Verizon** nebo **Azure CDN Premium z profilu Verizon** můžete na doplňkovém portálu Verizon zobrazit Verizon vlastní sestavy. Vlastní sestavy Verizon jsou přístupné prostřednictvím možnosti **Spravovat** z Azure Portal. Na stránce vlastní sestavy Verizon se zobrazuje počet přístupů nebo dat přenesených u každého okraje CName, který patří do profilu Azure CDN. Data lze seskupit podle kódu odpovědi HTTP nebo stavu mezipaměti za jakékoli časové období. Další informace najdete v tématu [vlastní sestavy z Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium ze sestav Verizon

S **Azure CDN Premium z Verizon**můžete také získat přístup k následujícím sestavám:
   * [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
   * [Statistiky v reálném čase](cdn-real-time-stats.md)
   * [Výkon hraničních uzlů](cdn-edge-performance.md)

