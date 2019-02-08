---
title: Přehled Azure Resource Health | Dokumentace Microsoftu
description: Přehled Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 11/16/2018
ms.openlocfilehash: d2a77e831290aa1ee0fcb6d4addf8f6e90786d52
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858905"
---
# <a name="azure-resource-health-overview"></a>Přehled Azure Resource Health
 
Azure Resource Health pomáhá při diagnostice a získání podpory v případě, že kvůli problému služba Azure ovlivňuje vaše prostředky. Informuje vás o aktuálním a minulém stavu vašich prostředků. A poskytuje technickou podporu, abychom vám zmírnit problémy.

Vzhledem k tomu [stav Azure](https://status.azure.com) vás informuje o potíže se službou, které mají vliv široké škále zákazníků Azure, Resource Health poskytuje individuální řídicí panel stav svých prostředků. Služba Resource Health ukazuje všechny časy, které prostředky byly v minulosti k dispozici z důvodu problémů služby Azure. Pak je jednoduchý rozumět, pokud byl došlo k porušení smlouvy SLA. 

## <a name="resource-definition-and-health-assessment"></a>Posouzení definice a stavu prostředků
Prostředek je konkrétní instance služby Azure: například virtuální počítač, webové aplikace nebo databáze SQL.

Služba Resource Health spoléhá na signály, protože ho vygeneroval jiný služby Azure k vyhodnocení, zda prostředek je v pořádku, či nikoli. Pokud prostředek není v pořádku, Resource Health analyzuje dodatečné informace k určení příčiny problému. Také identifikuje akce, které Microsoft trvá na tento problém vyřešit nebo akce, které můžete provést při řešení příčiny problému. 

Pro další podrobnosti o tom, jak se bude hodnotit stavu, zkontrolujte úplný seznam typů prostředků a kontrolách stavu ve službě [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stav
Stav prostředku se zobrazí jako jedna z následujících stavů.

### <a name="available"></a>K dispozici.
Stav **dostupné** znamená, že služba nezjistila žádné události, které ovlivňují stav prostředku. V případech, ve kterém prostředek se zotavuje ze neplánovaný výpadek během posledních 24 hodin se zobrazí **nedávno Vyřešeno** oznámení.

![Stav "Dostupný" pro virtuální počítač s oznámením "Nedávno vyřešené"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Není dostupný
Stav **není k dispozici** znamená, že služba zjistila k aktuálnímu platformy nebo jiných platforem událost, která má vliv na stav prostředku.

#### <a name="platform-events"></a>Události platformy
Události platformy jsou aktivovány více komponent infrastruktury Azure. Patří mezi ně naplánované akce (například plánovaná údržba) i neočekávané incidentů (například restartování neplánované hostitele).

Služba Resource Health poskytuje další podrobnosti o události a proces obnovení. Také umožňuje i v případě, že nemáte k dispozici aktivní smlouvu podpory společnosti Microsoft, kontaktujte podporu.

![Stav služby "Není k dispozici" pro virtuální počítač z důvodu události platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Události jiné platformy
Události mimo platformy jsou aktivovány akce uživatelů. Příklady jsou zastavení virtuálního počítače nebo dosažení maximálního počtu připojení k Azure Cache pro Redis.

![Stav služby "Není k dispozici" pro virtuální počítač kvůli jiné platformy](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Neznámé
Stav **neznámý** označuje, že Resource Health neobdržel informace o tomto zdroji pro více než 10 minut. Přestože tento stav není úplným a rozhodujícím údaj o stavu prostředku, je důležitý datový bod v procesu odstraňování potíží.

Pokud prostředek běží podle očekávání, stav prostředku se změní na **dostupné** za pár minut.

Pokud máte problémy s prostředkem, **neznámý** stav může naznačovat, že událost platformy ovlivňuje prostředku.

![Stav "Neznámá" pro virtuální počítač](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Sníženo
Stav **snížený** znamená, že váš prostředek byl nalezen ke ztrátě výkonu, i když je stále k dispozici pro použití.
Různé prostředky mají své vlastní kritéria pro kdy určí, že má snížený výkon prostředku.

![Stav "Snížený" pro virtuální počítač](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Nesprávný stav vytváření sestav
Pokud se domníváte, že aktuální stav je nesprávná, můžete nás informovat tak, že vyberete **sestavy nesprávný stav**. V případech, kdy problém s Azure je ovlivňuje doporučujeme vám obraťte se na podporu – od Resource Health. 

![Pole pro odeslání informací o nesprávný stav](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Historické informace
Dostanete až do 14 dnů v historii stavu **historie stavu** část Resource Health. 

![Seznam událostí stavu prostředků za poslední dva týdny](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Začínáme
Chcete-li spustit Resource Health pro jeden prostředek:
1.  Přihlaste se k portálu Azure.
2.  Vyhledejte prostředek.
3.  V nabídce prostředků v levém podokně vyberte **Resource health**.

![Otevírání Resource Health ze zobrazení zdrojů](./media/resource-health-overview/from-resource-blade.png)

Služba Resource Health můžete také přejít výběrem **všechny služby** a zadáte **služba resource health** do filtru textového pole. V **Nápověda a podpora** vyberte [Resource health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otevírání z "Všechny služby" služba Resource Health](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Další postup

Projděte si další informace o službě Resource Health tyto prostředky:
-  [Typy prostředků a kontroly stavu ve službě Azure Resource Health](resource-health-checks-resource-types.md)
-  [Nejčastější dotazy k Azure Resource Health](resource-health-faq.md)




