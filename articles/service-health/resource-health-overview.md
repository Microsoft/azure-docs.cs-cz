---
title: Přehled Azure Resource Health | Microsoft Docs
description: Přehled o stavu prostředků Azure.
services: Resource health
documentationcenter: ''
author: shawntabrizi
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: shawn.tabrizi
ms.openlocfilehash: 99e996f182aac774f2e2565d87fd0debaba1b2d1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
ms.locfileid: "30263118"
---
# <a name="azure-resource-health-overview"></a>Přehled Azure Resource Health
 
Azure Resource Health umožňuje diagnostikovat a získat podporu, pokud se problém s služba Azure ovlivní vaše prostředky. Můžete informuje o aktuální a starší stav svých prostředků. A poskytuje technickou podporu můžete zmírnit problémy.

Zatímco [stavu Azure](https://status.azure.com) informující o služby problémy, které ovlivňují širokou škálu Azure zákazníků, stav prostředku vám dává přizpůsobené řídicí panel stavu prostředků. Stav prostředků ukazuje všechny časy, které prostředky byly k dispozici v minulosti z důvodu problémů služby Azure. Pak je jednoduchý porozumět, pokud byl došlo k porušení SLA. 

## <a name="resource-definition-and-health-assessment"></a>Vyhodnocení stavu a definice prostředků
Prostředek je konkrétní instanci služby Azure: například virtuální počítač, webovou aplikaci nebo v databázi SQL.

Stav prostředku spoléhá na signály vysílaných různých služeb Azure k vyhodnocení, zda prostředek je v pořádku, či nikoli. Pokud prostředek není v pořádku, analyzuje stav prostředku dodatečné informace k určení příčiny problému. Také identifikuje akce, které společnost Microsoft provádí na opravě problému nebo akce, které můžete provést k vyřešení příčinu problému. 

Další informace o tom, jak je vyhodnocení stavu, zkontrolujte úplný seznam typů prostředků a změnami stavu [stav prostředku Azure](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stav
Stav prostředku je zobrazen jako jeden z následujících stavů.

### <a name="available"></a>Dostupný
Stav **dostupné** znamená, že služba zjistila všechny události, které ovlivňují stav prostředku. V případech, kdy prostředek se obnovila neplánované výpadky během posledních 24 hodin, uvidíte **nedávno vyřešeny problémy s** oznámení.

![Stav "Dostupný" pro virtuální počítač s "Nedávno vyřešené problémy" oznámení](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Není k dispozici.
Stav **není k dispozici** znamená, že služba zjistila probíhající platformy nebo události jiné platformy, která má vliv na stav prostředku.

#### <a name="platform-events"></a>Události platformy
Platforma události jsou aktivovány více součástí infrastruktury Azure. Obsahují plánované akce (například plánované údržby) a neočekávané incidenty (například restartování neplánované hostitele).

Stav prostředků obsahuje další podrobnosti o události a proces obnovení. Také umožňuje obraťte se na podporu i v případě, že nemáte active podpory smlouvy společnosti Microsoft.

![Stav "Není k dispozici" pro virtuální počítač z důvodu události platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Události jiné platformy
Události jiné platformy se spouštějí akcemi uživatelů. Mezi příklady jsou zastavení virtuálního počítače nebo dosažení maximálního počtu připojení k mezipaměti Redis.

![Stav "Není k dispozici" pro virtuální počítač z důvodu událost jiné platformy](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown
Stav **neznámé** označuje, že stav prostředku nedostal informace o tento prostředek pro více než 10 minut. I tento stav není spolehlivý Indikace stavu prostředku, když je bod důležitých dat ve proces řešení potíží.

Pokud prostředek běží podle očekávání, stav prostředku se změní na **dostupné** po několika minutách.

Pokud dojde k problémům s hledaným prostředkem **neznámé** stav může naznačovat, že událost v platformou ovlivňuje prostředku.

![Stav "Neznámý" pro virtuální počítač](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Snížený výkon
Stav **snížený** označuje, že prostředku zjistila ke ztrátě výkonu, i když bude stále k dispozici pro použití.
Různé prostředky mají své vlastní kritéria pro při určí, že je ke snížení prostředku.

![Stav "Snížený" pro virtuální počítač](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Nesprávný stav vytváření sestav
Pokud se domníváte, že aktuální stav je nesprávná, vám může dejte nám vědět výběrem **sestavy nesprávný stav**. V případech, kde problém s Azure je ovlivňuje doporučujeme kontaktovat podporu od stavu prostředků. 

![Pole pro odeslání informací o nesprávný stav](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Historické informace
Dostanete až 14 dnů v historii stavu **historii stavů** části stav prostředku. 

![Seznam stavů prostředků událostí za poslední dva týdny](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Začínáme
Chcete-li otevřít stav prostředku pro jeden prostředek:
1.  Přihlaste se k portálu Azure.
2.  Vyhledejte prostředek.
3.  V nabídce prostředků v levém podokně vyberte **stav prostředku**.

![Stav prostředku otevírání ze zobrazení prostředků](./media/resource-health-overview/from-resource-blade.png)

Můžete taky přejít stav prostředku výběrem **všechny služby** a zadáním příkazu **stav prostředku** do textového pole filtru. V **Nápověda a podpora** podokně, vyberte [stav prostředku](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otevření prostředku stavu z "Všechny služby"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Další postup

Podívejte se na tyto prostředky Další informace o stavu prostředků:
-  [Typy prostředků a stav kontrol ve stavu prostředků Azure](resource-health-checks-resource-types.md)
-  [Nejčastější dotazy o stavu prostředků Azure](resource-health-faq.md)




