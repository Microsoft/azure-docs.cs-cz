---
title: Přehled stavu prostředků Azure
description: Přehled stavu prostředků Azure
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159247"
---
# <a name="resource-health-overview"></a>Přehled stavu zdrojů
 
Azure Resource Health vám pomůže diagnostikovat a získat podporu pro problémy se službami, které ovlivňují vaše prostředky Azure. Informuje o aktuálním a minulém stavu vašich zdrojů.

[Zprávy](https://status.azure.com) o stavu Azure o problémech se službami, které ovlivňují širokou škálu zákazníků Azure. Resource Health poskytuje přizpůsobený řídicí panel stavu vašich prostředků. Stav prostředků zobrazuje všechny časy, které vaše prostředky byly nedostupné z důvodu problémů se službou Azure. Tato data usnadňují zjištění, zda byla porušena žádost SLA.

## <a name="resource-definition-and-health-assessment"></a>Definice zdrojů a hodnocení stavu

*Prostředek* je konkrétní instance služby Azure, jako je například virtuální počítač, webová aplikace nebo databáze SQL. Resource Health spoléhá na signály z různých služeb Azure k posouzení, zda je prostředek v pořádku. Pokud prostředek není v pořádku, resource health analyzuje další informace k určení zdroje problému. Také hlásí o akcích, které společnost Microsoft podniká k vyřešení problému a identifikuje věci, které můžete udělat pro jeho řešení.

Další informace o tom, jak se hodnotí stav, najdete v seznamu typů prostředků a kontroly stavu na [webu Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stav

Stav prostředku je zobrazen jako jeden z následujících stavů.

### <a name="available"></a>K dispozici.

*K dispozici* znamená, že nejsou zjištěny žádné události, které ovlivňují stav prostředku. V případech, kdy se zdroj zotavil z neplánovaných prostojů během posledních 24 hodin, se zobrazí oznámení "Nedávno vyřešeno".

![Stav *K dispozici* pro virtuální počítač, který má oznámení "Nedávno vyřešeno"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Neaktivní

*Není k dispozici* znamená, že služba zjistila probíhající platformu nebo událost mimo platformu, která ovlivňuje stav prostředku.

#### <a name="platform-events"></a>Události platformy

Události platformy se aktivují více komponent infrastruktury Azure. Zahrnují jak naplánované akce (například plánovanou údržbu), tak neočekávané incidenty (například neplánované restartování hostitele nebo poškozený hardware hostitele, který se předpokládá, že se po zadaném časovém okně nezdaří).

Resource Health poskytuje další podrobnosti o události a procesu obnovení. Umožňuje také kontaktovat podporu společnosti Microsoft i v případě, že nemáte aktivní smlouvu o podpoře.

![Stav *Nedostupné* pro virtuální počítač z důvodu události platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Události mimo platformu

Události mimo platformu jsou spouštěny akcemi uživatelů. Mezi příklady patří zastavení virtuálního počítače nebo dosažení maximálního počtu připojení k Azure Cache pro Redis.

![Stav "Není k dispozici" pro virtuální počítač z důvodu události bez platformy](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Není známo

*Neznámý* znamená, že stav prostředků neobdržel informace o prostředku více než 10 minut. Přestože tento stav není definitivní údaj o stavu prostředku, je to důležitý datový bod pro řešení potíží.

Pokud je prostředek spuštěn podle očekávání, stav prostředku se po několika minutách změní na *K dispozici.*

Pokud dochází k problémům s prostředkem, *neznámý* stav může znamenat, že událost v platformě ovlivňuje prostředek.

![Stav *Neznámý* pro virtuální počítač](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Snížený výkon

*Snížená* znamená, že váš prostředek zjistil ztrátu výkonu, i když je stále k dispozici pro použití.

Různé zdroje mají svá vlastní kritéria, když hlásí, že jsou degradovány.

![Stav *Degradované* pro virtuální počítač](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Nahlášení nesprávného stavu

Pokud se domníváte, že aktuální stav je nesprávný, můžete nám to sdělit výběrem **možnosti Nahlásit nesprávný stav**. V případech, kdy se vás týká problém s Azure, doporučujeme kontaktovat podporu ze služby Resource Health.

![Formulář pro odeslání informací o nesprávném stavu](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informace o historii

V části **Historie stavu** v části Zdraví prostředků můžete získat přístup až k 30denní historii.

![Seznam událostí stavu zdrojů za poslední dva týdny](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Začínáme

Otevření stavu prostředků pro jeden zdroj:

1. Přihlaste se k portálu Azure.
2. Vyhledejte prostředek.
3. V nabídce zdroje v levém podokně vyberte **možnost Stav prostředků**.

![Otevření stavu zdrojů ze zobrazení zdrojů](./media/resource-health-overview/from-resource-blade.png)

Ke stavu zdrojů můžete také získat přístup výběrem **možnosti Všechny služby** a zadáním **stavu prostředků** do textového pole filtru. V podokně **Nápověda + podpora** vyberte [položku Stav prostředků](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otevření stavu prostředků z "Všechny služby"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Další kroky

Další informace o stavu zdrojů najdete v těchto odkazech:
-  [Typy prostředků a kontroly stavu ve stavu prostředků Azure](resource-health-checks-resource-types.md)
-  [Nejčastější dotazy týkající se stavu prostředků Azure](resource-health-faq.md)
