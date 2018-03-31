---
title: Průvodce - Analytics odstraňováním potíží s Azure Mobile Engagement.
description: Řešení potíží s analýzy, monitorování, segmentaci a řídicí panel v Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b19d018b83ee8b3d5848d29afff190d3dcaf3fde
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Průvodce řešením potíží pro problémy analýzy, monitorování, segmentaci a řídicí panel
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Následují informace o možných problémech se můžete setkat s jak Azure Mobile Engagement shromažďuje informace o aplikacích, zařízení a uživatelů.

## <a name="missingdelayed-information"></a>Chybějící nebo opožděná informace
### <a name="issue"></a>Problém
* V uvedených v analýzy, segmentace nebo řídicí panel je zpožděno informace.
* Chybí informace z monitorování.
* Chybí informace z analýzy, segmentace nebo řídicí panel.
* Nedosáhli limitů segmentace.

### <a name="causes"></a>Způsobí, že
* Můžete použít rozhraní API analýzy, monitorování API a rozhraní API segmenty zobrazíte, pokud žádná data jste chybí uživatelské rozhraní je viditelné prostřednictvím rozhraní API.
* Pokud Azure Mobile Engagement SDK není správně integrovaná do aplikace pak nebudete moci zobrazit informace v analýzy, segmentace, monitorování nebo řídicí panely.
* Segmenty nelze změnit po jejich vytvoření, segmenty může být pouze "klonovaný" (zkopírovat) nebo "zničen" (odstranit). Segmenty může obsahovat jenom 10 kritéria.
* Nejlepší způsob, jak otestovat chybějící informace z monitorování je nastavit testovací zařízení, odinstalujte a znovu nainstalovat aplikaci na testovací zařízení.
* Informace se aktualizují každých 24 hodin pro analýzy, segmentace nebo řídicí panely.
* Informace v nové segmenty, se nemusí zobrazit do 24 hodin po jejich vytvoření i v případě, že předchozí informace podle segmentu.
* Filtrování dat analýzy v uživatelském rozhraní se zobrazí všechny příklady tohoto typu bez ohledu na verzi vaší aplikace (např.) "Dojde k chybě" filtrovat podle názvu se zobrazí z verze 1 a 2 verzi aplikace).
* Časové období pro analýzy je založena na datum z nastavení zařízení uživatelů, takže může uživatele, jehož telefon má datum nesprávné sady zobrazí v nesprávný časové období.
* Žádné na straně serveru, který data se protokolují, když použijete tlačítko pro "test" nabízených oznámení, data se protokolují pouze pro skutečné nabízené kampaně.

## <a name="cant-locate-items-in-ui"></a>Nelze najít položky v uživatelském rozhraní
### <a name="issue"></a>Problém
* Nelze vytvořit segmenty založené na určité součástí nebo informace o aplikaci vlastní značky kritéria.
* Nelze najít určité součástí nebo informace o aplikaci vlastní značky kritéria analýzy, monitorování nebo řídicí panely.
* Nemůže interpretovat data v analýzy, monitorování, segmentace nebo řídicí panely.

### <a name="causes"></a>Způsobí, že
* Některé položky součástí a značky informace o aplikaci jsou dostupné jen jako nabízené kritéria, ale nemusí být přidány do segment nebo viditelné z analýzy, monitorování nebo řídicí panel. 
* Pro integrované položky a značky informace o aplikaci, které nelze přidat do segmentu bude muset instalace seznamu cílových kritérií pro každou kampaň provádí stejnou funkci jako cílení na základě v segmentu.
* Kontextové nabídky v částech analýzy, monitorování, segmentaci a řídicí panely Azure Mobile Engagement uživatelského rozhraní pro další pomoc v tématu a jak informace.

## <a name="crash-troubleshooting"></a>Řešení potíží s havárií
### <a name="issue"></a>Problém
* Aplikace spadne uvedených v analýzy, monitorování nebo řídicí panel.

### <a name="causes"></a>Způsobí, že
* Chcete-li vyřešit aplikace spadne zobrazená v analýzy, monitorování nebo řídicí panel nezapomeňte zaškrtnout známé problémy s předchozími verzemi sady SDK v poznámkách k verzi.
* Chcete-li dále řešit havárie aplikací proveďte událost z testovací zařízení s instalaci aplikace a vyhledávání ID zařízení v části "Monitorování událostí –" rozhraní Azure Mobile Engagement. Pak proveďte událost, která je příčinou aplikace k chybě a vyhledejte další informace v části "Havárií – monitorování" rozhraní Azure Mobile Engagement. 

