---
title: Přehled Azure Resource Health
description: Přečtěte si, jak Azure Resource Health pomáhá s diagnostikou a získáním podpory pro problémy se službami, které ovlivňují vaše prostředky Azure.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 25425eadfaf54ba56cde3b377a44cd85c2b68959
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967874"
---
# <a name="resource-health-overview"></a>Přehled Resource Health
 
Azure Resource Health vám pomůže s diagnostikou a získáním podpory pro problémy se službami, které ovlivňují vaše prostředky Azure. Oznamuje stav svých prostředků v aktuálním a minulém stavu.

Zprávy o [stavu Azure](https://status.azure.com) týkající se problémů se službami, které mají vliv na širokou škálu zákazníků Azure. Resource Health poskytuje přizpůsobený řídicí panel stavu vašich prostředků. Resource Health zobrazuje všechny časy nedostupnosti vašich prostředků z důvodu problémů se službami Azure. Tato data vám umožní snadno zjistit, jestli došlo k porušení smlouvy SLA.

## <a name="resource-definition-and-health-assessment"></a>Definice prostředků a posouzení stavu

*Prostředek* je konkrétní instance služby Azure, jako je například virtuální počítač, Webová aplikace nebo SQL Database. Resource Health spoléhá na signály z různých služeb Azure a vyhodnotí, jestli je prostředek v pořádku. Pokud prostředek není v pořádku, Resource Health analyzuje další informace a určí zdroj problému. Také obsahuje zprávy o akcích, které společnost Microsoft provádí při řešení problému, a identifikuje věci, které můžete vyřešit.

Další informace o vyhodnocení stavu najdete v seznamu typů prostředků a kontrol stavu na adrese [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stav

Stav prostředku se zobrazuje jako jeden z následujících stavů.

### <a name="available"></a>K dispozici

*K dispozici* znamená, že nebyly zjištěny žádné události, které mají vliv na stav prostředku. V případech, kdy se prostředek zotavil z neplánovaného výpadku za posledních 24 hodin, se zobrazí oznámení "nedávno Vyřešeno".

![Stav * k dispozici * pro virtuální počítač s oznámením "nedávno Vyřešeno"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Neaktivní

*Nedostupné* znamená, že služba zjistila trvalou platformu nebo jinou událost než platformu, která má vliv na stav prostředku.

#### <a name="platform-events"></a>Události platformy

Události platformy se spouštějí více komponentami infrastruktury Azure. Zahrnují jak plánované akce (například plánované údržby), tak neočekávané incidenty (například neplánovaný restart hostitele nebo snížený hostitelský hardware, který je po určeném časovému intervalu neúspěšný).

Resource Health poskytuje další podrobnosti o události a procesu obnovení. Také vám umožňuje kontaktovat podpora Microsoftu i v případě, že nemáte aktivní smlouvu o podpoře.

![Stav * nedostupný * pro virtuální počítač kvůli události platformy](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Události jiné než platformy

Akce uživatele aktivují události jiné než platformy. Mezi příklady patří zastavení virtuálního počítače nebo dosažení maximálního počtu připojení k Azure cache pro Redis.

![Stav "není k dispozici" pro virtuální počítač z důvodu události jiné než platformy](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Neznámý

*Neznámý* znamená, že Resource Health nedostaly informace o prostředku více než 10 minut. I když tento stav není konečným náznakem stavu prostředku, je důležitým datovým bodem pro řešení problémů.

Pokud je prostředek spuštěný podle očekávání, stav prostředku se změní na *k dispozici* po několika minutách.

Pokud dojde k potížím s prostředkem, *Neznámý* stav může znamenat, že událost na platformě má vliv na prostředek.

![Stav * neznámý * pro virtuální počítač](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Snížený výkon

*Degradováno* znamená, že váš prostředek zjistil ztrátu výkonu, i když je stále k dispozici pro použití.

Různé prostředky mají vlastní kritéria, když hlásí, že jsou degradovány.

![Stav * snížený počet * pro virtuální počítač](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Hlášení nesprávného stavu

Pokud se domníváte, že aktuální stav je nesprávný, můžete nás informovat tak, že vyberete možnost **sestava nesprávný stav**stavu. V případech, kdy se vám problém s Azure dotýká, doporučujeme, abyste se obrátili na podporu od Resource Health.

![Formulář pro odeslání informací o nesprávném stavu](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informace o historii

V části **historie stavu** Resource Health můžete získat přístup k historii až 30 dnů.

![Seznam událostí Resource Health za poslední dva týdny](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Začínáme

Pro otevření Resource Health pro jeden prostředek:

1. Přihlaste se k portálu Azure.
2. Vyhledejte prostředek.
3. V nabídce prostředek v levém podokně vyberte **stav prostředku**.

![Otevření Resource Health ze zobrazení prostředků](./media/resource-health-overview/from-resource-blade.png)

K Resource Health můžete také přistupovat výběrem možnosti **všechny služby** a zadáním **stavu prostředků** do textového pole Filtr. V podokně **help + podpora** vyberte [stav prostředku](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Otevírá se Resource Health ze všech služeb.](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Další kroky

Další informace o Resource Health najdete v těchto odkazech:
-  [Typy prostředků a kontroly stavu v Azure Resource Health](resource-health-checks-resource-types.md)
-  [Nejčastější dotazy týkající se Azure Resource Health](resource-health-faq.md)
