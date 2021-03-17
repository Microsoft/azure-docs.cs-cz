---
title: Nejčastější dotazy k místně připnutém svazkům StorSimple | Microsoft Docs
description: Obsahuje odpovědi na nejčastější dotazy týkající se StorSimple místně připnuté svazků.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002710"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple místně připnuté svazky: nejčastější dotazy
## <a name="overview"></a>Přehled
Tady jsou otázky a odpovědi, které byste mohli mít při vytváření StorSimple místně připnutého svazku, převedení vrstveného svazku na místně připojený svazek (a naopak) nebo zálohování a obnovení místně připojeného svazku.

Otázky a odpovědi jsou uspořádané do následujících kategorií:

* Vytvoření místně připojeného svazku
* Zálohování místně připnutého
* Převod vrstveného svazku na místně připojený svazek
* Obnovení místně připojeného svazku
* Převzetí služeb při selhání prostřednictvím místně připojeného svazku

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Otázky týkající se vytvoření místně připnutého svazku
**Č.** Jaká je maximální velikost místně připojeného svazku, kterou můžu na zařízeních řady 8000 vytvořit?

**Na zařízeních** s StorSimple 8000 Series Update 3,0 můžete zřídit místně připojené svazky až do 8,5 TB nebo vrstvené svazky až do 200 TB na zařízení 8100. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB.

**Č.** Nedávno jsme zařízení 8100 upgradovali na aktualizaci 3,0 a při pokusu o vytvoření místně připnutého svazku je maximální dostupná velikost jenom 6 TB a ne 8,5 TB. Proč nemůžu vytvořit svazek 8,5 TB?

**Pokud je v zařízení** spuštěná aktualizace 3,0, můžete na zařízení s 8100 zřídit místně připojené svazky až do 8,5 TB nebo vrstvené svazky až do 200 TB. Pokud už vaše zařízení má vrstvené svazky, pak je dostupné místo pro vytvoření místně připnutého svazku, které je poměrně nižší než tento maximální limit. Pokud se třeba na zařízení 8100 zřídilo přibližně 106 TB vrstvených svazků (což je polovina vrstvené kapacity), pak se maximální velikost místního svazku, kterou můžete vytvořit v zařízení 8100, bude odpovídajícím způsobem snižovat na 4 TB (přibližně polovina maximální místně připnuté kapacity svazku).

Vzhledem k tomu, že se k hostování pracovní sady vrstvených svazků používá jiné místo na zařízení, je dostupné místo pro vytvoření místně připnutého svazku, pokud má zařízení vrstvené svazky. Naopak při vytváření místně připnutého svazku se zmenší dostupný prostor pro vrstvené svazky. Následující tabulka shrnuje dostupnou vrstvenou kapacitu na zařízeních 8100 a 8600 při vytváření místně připnutého svazku.

#### <a name="update-30"></a>Aktualizace 3,0 

| Místně připojené svazky zřízené na kapacitu | Dostupná kapacita, která se zřídí pro vrstvené svazky – 8100 | Dostupná kapacita, která se zřídí pro vrstvené svazky – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |Není k dispozici |277,8 TB |
| 15 TB |Není k dispozici |166,7 TB |
| 22,5 TB |Není k dispozici |0 TB |

**Č.** Proč je místně připnuté vytvoření svazku dlouhodobě běžící operace?

**Určitého.** Místně připnuté svazky jsou silným zajišťovány. Pokud chcete vytvořit místo na místních úrovních zařízení, můžou být během procesu zřizování do cloudu vložená nějaká data z existujících vrstvených svazků. A vzhledem k tomu, že to závisí na velikosti zřízeného svazku, o stávajících datech na vašem zařízení a dostupné šířce pásma v cloudu, čas potřebný k vytvoření místního svazku může trvat několik hodin.

**Č.** Jak dlouho trvá vytvoření místně připojeného svazku?

**Určitého.** Vzhledem k tomu, že místně připnuté svazky jsou silným zřizováním, můžou být během procesu zřizování do cloudu vložená nějaká existující data z vrstvených svazků. Čas potřebný k vytvoření místně připnutého svazku závisí na několika faktorech, včetně velikosti svazku, dat v zařízení a dostupné šířky pásma. Na zařízení, které nemá žádné svazky, je čas na vytvoření místně připnutého svazku asi 10 minut na terabajt dat. Vytváření místních svazků ale může trvat několik hodin, a to na základě výše popsaných faktorů na zařízení, které se používá.

**Č.** Chci vytvořit místně připojený svazek. Existují nějaké osvědčené postupy, o kterých je potřeba vědět?

**Určitého.** Místně připnuté svazky jsou vhodné pro úlohy, které vyžadují místní záruky dat a jsou citlivé na latence v cloudu. Při zvažování využití místních svazků pro jakékoli vaše úlohy mějte na paměti následující:

* Místně připnuté svazky se silným zřizováním a vytváření místních svazků ovlivňuje dostupný prostor pro vrstvené svazky. Proto doporučujeme začít s menším objemem svazků a navýšení kapacity v případě zvýšení požadavků na úložiště.
* Zřizování místních svazků je dlouhodobá operace, která může zahrnovat vložení stávajících dat z vrstvených svazků do cloudu. V důsledku toho může dojít ke snížení výkonu na těchto svazcích.
* Zřizování místních svazků je časově náročná operace. Skutečný čas, který je součástí, závisí na několika faktorech: na zřízeném svazku, na datech v zařízení a na dostupné šířce pásma. Pokud jste svoje stávající svazky nezálohovali do cloudu, je vytvoření svazku pomalejší. Před zřízením místního svazku doporučujeme, abyste provedli cloudové snímky stávajících svazků.
* Existující vrstvené svazky můžete převést na místně připnuté svazky a tento převod zahrnuje zřizování místa na zařízení pro výsledný místně připojený svazek (navíc k tomu, že se z cloudu odnášejí vrstvená data). Znovu se jedná o dlouhodobou provozní operaci, která závisí na faktorech, které jsme probrali výše. Doporučujeme, abyste před převodem zálohovali existující svazky, protože proces bude ještě pomalejší, pokud se existující svazky nezálohují. Během tohoto procesu může dojít i ke snížení výkonu vašeho zařízení.

Další informace o tom, jak [vytvořit místně připojený svazek](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Č.** Můžu současně vytvořit několik místně připojených svazků?

**Určitého.** Ano, ale všechny místně připojené úlohy vytváření a expanze jsou zpracovávány postupně.

Místně připnuté svazky jsou silné a to vyžaduje vytvoření místního prostoru na zařízení (což může způsobit, že se do cloudu během procesu zřizování odešlou stávající data z vrstveného svazku). Proto pokud probíhá úloha zřizování, ostatní úlohy vytváření místních svazků budou zařazeny do fronty až do dokončení této úlohy.

Podobně platí, že pokud se rozbalí existující místní svazek nebo se vrstvený svazek převede na místně připojený svazek, vytvoří se nový místně připojený svazek do fronty, dokud se nedokončí předchozí úloha. Rozšiřování velikosti místně připnutého svazku zahrnuje rozšíření stávajícího místního prostoru pro daný svazek. Převod z vrstveného na místně připnutý svazek zahrnuje také vytvoření místního prostoru pro výsledný místně připojený svazek. V obou těchto operacích je vytvoření nebo rozšíření místního prostoru dlouhodobě spuštěná úloha.

Tyto úlohy můžete zobrazit v okně **úlohy** služby StorSimple Device Manager. Úloha, která se aktivně zpracovává, se průběžně aktualizuje, aby odrážela průběh zřizování prostorů. Zbývající místně připojené svazky jsou označeny jako spuštěné, ale jejich průběh je zastavený a vybírají se v pořadí, ve kterém byly zařazeny do fronty.

**Č.** Odstranil (a) jsem místně připojený svazek. Proč se mi při pokusu o vytvoření nového svazku nezobrazuje volné místo, které se odráží v dostupném prostoru?

**Určitého.** Odstraníte-li místně připojený svazek, nebude možné okamžitě aktualizovat místo dostupné pro nové svazky. Služba StorSimple Device Manager aktualizuje místní prostor, který je k dispozici přibližně každou hodinu. Doporučujeme počkat na hodinu, než se pokusíte vytvořit nový svazek.

**Č.** Jsou místně připojené svazky podporované na cloudovém zařízení?

**Určitého.** Místně připnuté svazky nejsou u cloudového zařízení podporované (8010 a 8020 zařízení dřív označovaná jako virtuální zařízení StorSimple).

**Č.** Můžu k vytváření a správě místně připnuté svazků použít rutiny Azure PowerShell?

**Určitého.** Ne, místně připojené svazky nemůžete vytvářet pomocí rutin Azure PowerShell (každý svazek, který vytvoříte přes Azure PowerShell, je vrstvený). Doporučujeme také, abyste rutiny Azure PowerShell nepoužívali k úpravě jakýchkoli vlastností místně připnutého svazku, protože bude mít neočekávaný účinek na změnu typu svazku na vrstvený.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Dotazy týkající se zálohování místně připojeného svazku
**Č.** Podporují se místní snímky místně připnutého svazku?

**Určitého.** Ano, můžete převzít místní snímky místně připnuté svazků. Důrazně ale doporučujeme, abyste pravidelně zálohovali místně připojené svazky se síťovými snímky a zajistili, že jsou vaše data chráněná v případné havárii.

Mějte na paměti, že místní snímky místně vázaných svazků můžou být také vrstveny na Cloud a nejsou zaručené zůstat v místní úrovni zařízení.

**Č.** Existují nějaké pokyny pro správu místních snímků pro místně připnuté svazky?

**Určitého.** Časté místní snímky spolu s vysokou mírou četnosti změn dat na místně připojeném svazku můžou způsobit, že se místní prostor na zařízení rychle spotřebovává a vytvoří se data z vrstvených svazků, které se odešlou do cloudu. Proto navrhneme minimalizaci počtu místních snímků.

**Č.** Zobrazila se mi výstraha oznamující, že je možné zrušit platnost místních snímků místně připojených svazků. Kdy k tomu může dojít?

**Určitého.** Časté místní snímky spolu s vysokou mírou četnosti změn dat na místně připojeném svazku můžou způsobit, že se místní prostor na zařízení bude spotřebovávat rychle. Pokud jsou místní vrstvy zařízení intenzivně využívány, může dojít k úplnému výpadku cloudu, protože se zařízení stane plným a příchozí zápisy do tohoto svazku mohou způsobit neplatnost snímků (vzhledem k tomu, že žádné místo neexistují k aktualizaci snímků, aby odkazovaly na starší bloky dat, které byly přepsány). V takové situaci se budou i nadále obsluhovat zápisy do svazku, ale místní snímky můžou být neplatné. Váš existující cloudový snímek nemá žádný vliv.

Upozornění výstrahy vás upozorní na to, že tato situace může nastat, a ujistěte se, že si provedete kontrolu vašich místních snímků, abyste měli méně časté místní snímky nebo odstranili starší místní snímky, které už nepotřebujete.

Pokud jsou místní snímky neověřené, obdržíte upozornění s informacemi o tom, že místní snímky pro konkrétní zásady zálohování byly neověřené vedle seznamu časových razítek místních snímků, jejichž platnost byla zrušena. Tyto snímky budou automaticky odstraněny a již je nebudete moci zobrazit v okně **katalogy zálohování** v Azure Portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Dotazy týkající se převedení vrstveného svazku na místně připojený svazek
**Č.** V zařízení se zobrazuje zpomalení při převodu vrstveného svazku na místně připojený svazek. Proč se to děje?

**Určitého.** Proces převodu zahrnuje dva kroky:

1. Zřizování místa na zařízení pro místně připojený svazek, který je k disměněný
2. Stažení všech vrstvených dat z cloudu za účelem zajištění místních záruk.

Oba tyto kroky jsou dlouhodobě běžící operace, které jsou závislé na velikosti převáděného svazku, datech v zařízení a dostupné šířce pásma. Vzhledem k tomu, že některá data z existujících vrstvených svazků můžou být v rámci procesu zřizování přecházet do cloudu, může během této doby dojít v zařízení k nižšímu výkonu. Kromě toho může být proces převodu pomalejší, pokud:

* Existující svazky se nezálohují do cloudu. Proto doporučujeme zálohovat svazky před zahájením převodu.
* Zásady omezování šířky pásma se používaly, což může omezit dostupnou šířku pásma na Cloud; Proto doporučujeme, abyste měli ke cloudu vyhrazené 40 MB/s nebo více připojení.
* Proces převodu může trvat několik hodin, než je více faktorů vysvětleno výše. Proto doporučujeme, abyste tuto operaci provedli během nešpičky nebo po dobu víkendu, abyste se vyhnuli dopadu na koncové příjemce.

Další informace o tom, jak [převést vrstvený svazek na místně připojený svazek](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Č.** Můžu zrušit operaci převodu svazků?

**Určitého.** Ne, operaci převodu po zahájení nelze zrušit. Jak je popsáno v předchozí otázce, mějte na paměti potenciální problémy s výkonem, ke kterým může dojít během procesu, a při plánování konverze postupujte podle výše uvedených osvědčených postupů.

**Č.** Co se stane se svazkem v případě, že operace převodu selhává?

**Určitého.** Převod svazku může selhat kvůli problémům s cloudovým připojením. Zařízení může nakonec zastavit proces převodu po řadě neúspěšných pokusů o uvedení vrstvených dat z cloudu. V takovém scénáři bude typ svazku i nadále před převodem zdrojový typ svazku a:

* Bude vyvolána kritická výstraha upozorňující na selhání převodu svazku. Další informace o [výstrahách souvisejících s místně připnuté svazky](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Pokud převedete vrstvení na místně připojený svazek, svazek bude i nadále vykazovat vlastnosti vrstveného svazku, protože data se stále můžou nacházet v cloudu. Doporučujeme, abyste vyřešili problémy s připojením a pak znovu provedli operaci převodu.
* Podobně platí, že když se převod z místně připnuté na vrstvený svazek nezdaří, i když bude tento svazek označený jako místně připojený svazek, bude fungovat jako vrstvený svazek (protože data by mohla být v cloudu předaná). Bude se ale dál zabírat místo na místních vrstvách zařízení. Tento prostor nebude k dispozici pro jiné místně připojené svazky. Doporučujeme, abyste tuto operaci provedli znovu, abyste zajistili, že je převod svazku dokončený, a místní prostor v zařízení může být uvolněný.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Dotazy týkající se obnovení místně připnutého svazku
**Č.** Jsou místně připojené svazky obnoveny hned?

**Určitého.** Ano, místně připojené svazky se okamžitě obnoví. Jakmile se v rámci operace obnovení z cloudu získávají informace o metadatech, svazek se přepne do online režimu a k němu může získat pøístup hostitel. Místní záruky pro data svazků ale nebudou k dispozici, dokud se všechna data nestáhnou z cloudu, a po dobu jejich obnovení se můžete setkat s nižším výkonem na těchto svazcích.

**Č.** Jak dlouho trvá obnovení místně připojeného svazku?

**Určitého.** Místně připnuté svazky se okamžitě obnovují a přidají se do online režimu, jakmile se z cloudu načtou informace o metadatech svazku, zatímco data svazku se pořád stahují na pozadí. Toto je poslední část operace obnovení – probíhá vracení místních záruk pro data svazků – je dlouhodobá operace, která může trvat několik hodin, než se všechna data znovu připravují. Doba potřebná k dokončení stejného závisí na několika faktorech, například na velikosti obnoveného svazku a dostupné šířce pásma. Pokud je původní svazek, který se má obnovit, odstraněný, v rámci operace obnovení se v zařízení vytvoří další čas.

**Č.** Potřebuji obnovit stávající místně připojený svazek do staršího snímku (pořízený při vrstvení svazku). Bude svazek obnovený v tomto případě v tomto případě?

**Určitého.** Ne, svazek se obnoví jako místně připojený svazek. I když jsou data snímku v době, kdy byla vrstva vrstvená, během obnovování stávajících svazků vždycky používá StorSimple typ svazku na disku, protože v tuto chvíli existuje.

**Č.** V poslední době jsem přidaný místně připojený svazek, ale teď je potřeba obnovit data v době, kdy byla velikost svazku menší. Obnoví velikost aktuálního svazku a bude potřeba po dokončení obnovení zvětšit velikost svazku?

**Určitého.** Ano, obnovení změní velikost svazku a po dokončení obnovení bude nutné velikost svazku zvětšit.

**Č.** Můžu během obnovování změnit typ svazku?

**Určitého.** Ne, typ svazku se během obnovování nedá změnit.

* Svazky, které byly odstraněny, se obnoví jako typ uložený ve snímku.
* Existující svazky jsou obnoveny na základě jejich aktuálního typu bez ohledu na typ uložený ve snímku (viz předchozí dvě otázky).

**Č.** Potřebuji obnovit místně připojený svazek, ale ve snímku času jsem vybral nesprávný bod. Můžu zrušit aktuální operaci obnovení?

**Určitého.** Ano, můžete zrušit probíhající operaci obnovení. Stav svazku se vrátí zpět do stavu na začátku obnovy. Všechny zápisy provedené na svazku během obnovování se ale ztratí.

**Č.** Zahájil (a) jsem operaci obnovení na jednom z místně připojených svazků a teď se zobrazuje snímek v katalogu nevyřízených položek, který se nevytváří znovu. K čemu slouží?

**Určitého.** Jedná se o dočasný snímek, který se vytvoří před operací obnovení a používá se pro vrácení zpět v případě, že se obnovení zruší nebo dojde k chybě. Tento snímek neodstraňujte; až se obnovení dokončí, automaticky se odstraní. K tomuto chování může dojít v případě, že úloha obnovení má jenom místně připnuté svazky nebo kombinaci místně připnutého a vrstveného svazku. Pokud úloha obnovení zahrnuje jenom vrstvené svazky, pak k tomuto chování nedojde.

**Č.** Můžu naklonovat místně připojený svazek?

**Určitého.** Ano, můžete. Místně připojený svazek se ale ve výchozím nastavení naklonuje jako vrstvený svazek. Další informace o [naklonování místně připnutého svazku](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Dotazy týkající se převzetí služeb při selhání prostřednictvím místně připojeného svazku
**Č.** Potřebuji převzít služby zařízení při selhání na jiné fyzické zařízení. Budou místní připojené svazky při převzetí služeb při selhání lokálně připnuté nebo vrstvené?

**Určitého.** Místně připnuté svazky se při převzetí služeb při selhání připnuté, pokud na cílovém zařízení běží StorSimple 8000 Series Update 3 nebo vyšší.

Další informace o [převzetí služeb při selhání a zotavení po havárii na místně připojených svazcích v různých verzích](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Č.** Jsou místně připojené svazky hned obnovené při zotavení po havárii (DR)?

**Určitého.** Ano, místně připnuté svazky se okamžitě obnovují během převzetí služeb při selhání. Jakmile se v rámci operace převzetí služeb při selhání načte z cloudu informace o metadatech, svazek se do cílového zařízení přepne do online režimu a k němu může získat pøístup hostitel. Mezitím se data svazků budou dál stahovat na pozadí a u těchto svazků po dobu jejich převzetí služeb při selhání může dojít ke snížení výkonu.

**Č.** Zobrazuje se dokončená úloha převzetí služeb při selhání, jak můžu sledovat průběh místně připojeného svazku, který se na cílovém zařízení obnovuje?

**Určitého.** Během operace převzetí služeb při selhání je úloha převzetí služeb při selhání označena jako dokončená, jakmile se všechny svazky v sadě převzetí služeb při selhání okamžitě obnoví a přepne do stavu online na cílovém zařízení. To zahrnuje všechny místně připojené svazky, u kterých mohlo dojít k převzetí služeb při selhání. místní záruky dat ale budou k dispozici až po stažení všech dat pro daný svazek. Tento průběh můžete sledovat u každého místně připojeného svazku, u kterého došlo k převzetí služeb při selhání monitorováním odpovídajících úloh obnovení, které jsou vytvořené jako součást převzetí služeb při selhání. Tyto jednotlivé úlohy obnovení budou vytvořeny pouze pro místně připojené svazky.

**Č.** Můžu změnit typ svazku během převzetí služeb při selhání?

**Určitého.** Ne, typ svazku nelze změnit během převzetí služeb při selhání. Pokud převezmete služby při selhání jiné fyzické zařízení se systémem StorSimple 8000 Series Update 3, převezmou se svazky na základě typu svazku uloženého ve snímku.

**Č.** Můžu převzít služby kontejneru svazků s místně připnuté svazky na cloudové zařízení?

**Určitého.** Ano, můžete. Místně připnuté svazky se převezmou jako vrstvené svazky. Další informace o [převzetí služeb při selhání a zotavení po havárii na místně připojených svazcích v různých verzích](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

