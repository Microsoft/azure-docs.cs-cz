---
title: StorSimple místně vázaných svazků NEJČASTĚJŠÍ Dotazy| Dokumenty společnosti Microsoft
description: Poskytuje odpovědi na často kladené otázky týkající se místně vázaných svazků StorSimple.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: aa69d8b07d31b5cf0386e34c113475cbf4191891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319543"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple místně vázaných svazků: nejčastější dotazy (FAQ)
## <a name="overview"></a>Přehled
Následují otázky a odpovědi, které můžete mít při vytváření místně vázaný svazek StorSimple, převést vrstvený svazek na místně vázaný svazek (a naopak) nebo zálohovat a obnovit místně vázaný svazek.

Otázky a odpovědi jsou uspořádány do následujících kategorií

* Vytvoření místně vázanýho svazku
* Zálohování místně připnutého
* Převod vrstveného svazku na místně vázaný svazek
* Obnovení místně vázanýho svazku
* Převzetí selhání nad místně vázaným svazkem

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Otázky týkající se vytvoření místně připojeného svazku
**Otázka:** Jaká je maximální velikost místně vázaných svazků, které lze vytvořit na zařízeních řady 8000?

**A** Na zařízeních se systémem StorSimple 8000 Series Update 3.0 můžete zřídit místně vázaných svazků až 8,5 TB nebo vrstvené svazky až 200 TB na zařízení 8100. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB.

**Otázka:** Nedávno jsem upgradoval své zařízení 8100 na aktualizaci 3.0 a když se snažím vytvořit místně vázaný svazek, maximální dostupná velikost je pouze 6 TB a ne 8.5 TB. Proč nelze vytvořit svazek 8,5 TB?

**A** Pokud je v zařízení spuštěna aktualizace 3.0, můžete na zařízení 8100 zřídit místně vázaných svazků až 8,5 TB nebo vrstvené svazky až 200 TB. Pokud vaše zařízení již má vrstvené svazky, bude místo, které je k dispozici pro vytvoření místně vázanýsvazek proporcionálně nižší než tento maximální limit. Pokud například na vašem zařízení 8100 (což je polovina vrstvené kapacity) již bylo zřízeno přibližně 106 TB vrstvených svazků, bude maximální velikost místního svazku, který můžete vytvořit na zařízení 8100, odpovídajícím způsobem snížena na 4 TB (zhruba maximální místně připnuté kapacity objemu).

Vzhledem k tomu, že některé místní místo v zařízení se používá k hostování pracovní sady vrstvených svazků, dostupné místo pro vytvoření místně vázaný svazek se sníží, pokud má zařízení vrstvené svazky. Naopak vytvoření místně vázaný svazek úměrně snižuje dostupné místo pro vrstvené svazky. Následující tabulky shrnují dostupnou vrstvenou kapacitu na zařízeních 8100 a 8600 při vytvoření místně vázaných svazků.

#### <a name="update-30"></a>Aktualizace 3.0 

| Místně vázaných svazků zřízená kapacita | Dostupná kapacita pro vrstvené svazky – 8100 | Dostupná kapacita pro vrstvené objemy – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |Není k dispozici |277,8 TB |
| 15 TB |Není k dispozici |166,7 TB |
| 22,5 TB |Není k dispozici |0 TB |

**Otázka:** Proč je vytvoření místně vázaných svazků dlouho běžící operací?

**A.** Místně vázaných svazků jsou hustě zřízené. Chcete-li vytvořit místo na místní vrstvy zařízení, některá data z existujícívrstvované svazky může být posunuta do cloudu během procesu zřizování. A protože to závisí na velikosti zřizovaného svazku, existujících datech v zařízení a dostupné šířce pásma do cloudu, může být doba potřebná k vytvoření místního svazku několik hodin.

**Otázka:** Jak dlouho trvá vytvoření místně vázaný svazek?

**A.** Vzhledem k tomu, že místně vázaných svazků jsou hustě zřízené, některá existující data z vrstvené svazky může být posunuta do cloudu během procesu zřizování. Doba potřebná k vytvoření místně vázanýsvazek proto závisí na několika faktorech, včetně velikosti svazku, dat v zařízení a dostupné šířky pásma. Na čerstvě nainstalovaném zařízení, které nemá žádné svazky, je čas vytvoření místně vázaný svazek přibližně 10 minut na terabajt dat. Vytvoření místních svazků však může trvat několik hodin na základě výše uvedených faktorů na zařízení, které je používáno.

**Otázka:** Chcete vytvořit místně vázaný svazek. Existují nějaké osvědčené postupy, které musím znát?

**A.** Místně vázaných svazků jsou vhodné pro úlohy, které vyžadují místní záruky dat za všech okolností a jsou citlivé na latence cloudu. Při zvažování využití místních svazků pro některou z vašich úloh, mějte na paměti následující:

* Místně vázaných svazků jsou hustě zřízené a vytváření místních svazků má vliv na dostupné místo pro vrstvené svazky. Proto doporučujeme začít s menšími svazky a vertikálně navýšit kapacitu, jak se zvýší váš požadavek na úložiště.
* Zřizování místních svazků je dlouhotrvající operace, která může zahrnovat odesílání existujících dat z vrstvených svazků do cloudu. V důsledku toho může dojít ke snížení výkonu na těchto svazcích.
* Zřizování místních svazků je časově náročná operace. Skutečný čas závisí na několika faktorech: velikosti zřizovaného svazku, datech v zařízení a dostupné šířce pásma. Pokud jste nezálohovali stávající svazky do cloudu, je vytváření svazku pomalejší. Doporučujeme, abyste před zřízením místního svazku pořizovali cloudové snímky stávajících svazků.
* Existující vrstvené svazky můžete převést na místně vázaných svazků a tento převod zahrnuje zřizování místa na zařízení pro výsledný místně vázaný svazek (kromě snížení vrstvených dat, pokud existují, z cloudu). Opět se jedná o dlouho běžící operaci, která závisí na faktorech, které jsme diskutovali výše. Doporučujeme zálohovat stávající svazky před konverzí, protože proces bude ještě pomalejší, pokud stávající svazky nejsou zálohovány. Během tohoto procesu může dojít ke snížení výkonu zařízení.

Další informace o [vytvoření místně vázanýsvazek](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Otázka:** Mohu vytvořit více místně vázaných svazků současně?

**A.** Ano, ale všechny místně vázaný svazek vytváření a rozšíření úlohy jsou zpracovávány postupně.

Místně vázaných svazků jsou hustě zřízené a to vyžaduje vytvoření místního prostoru na zařízení (což může mít za následek existující data z vrstvené svazky, které mají být nabízeny do cloudu během procesu zřizování). Proto pokud probíhá úloha zřizování, ostatní úlohy vytváření místního svazku budou zařazeny do fronty, dokud nebude tato úloha dokončena.

Podobně pokud se rozbaluje existující místní svazek nebo vrstvený svazek je převáděn na místně vázaný svazek, bude vytvoření nového místně vázanýho svazku zařazeno do fronty, dokud nebude dokončena předchozí úloha. Rozšíření velikosti místně vázaný svazek zahrnuje rozšíření existujícího místního prostoru pro tento svazek. Převod z vrstveného na místně vázaný svazek také zahrnuje vytvoření místního prostoru pro výsledný místně vázaný svazek. V obou těchto operacích je vytvoření nebo rozšíření místního prostoru dlouho běžící úlohou.

Tyto úlohy můžete zobrazit v okně **Úlohy** služby StorSimple Device Manager. Úloha, která je aktivně zpracovávána, je průběžně aktualizována tak, aby odrážela průběh zřizování prostoru. Zbývající místně vázaný svazek úlohy jsou označeny jako spuštěné, ale jejich průběh je zastaven a jsou vyskladněny v pořadí, ve kterých byly zařazeny do fronty.

**Otázka:** Odstranil jsem místně vázaný svazek Proč se při pokusu o vytvoření nového svazku nezobrazí rekultivovaný prostor, který se odráží v dostupném prostoru?

**A.** Pokud odstraníte místně vázaný svazek, místo, které je k dispozici pro nové svazky, nemusí být okamžitě aktualizováno. Služba StorSimple Device Manager aktualizuje místní prostor, který je k dispozici přibližně každou hodinu. Doporučujeme počkat hodinu, než se pokusíte vytvořit nový svazek.

**Otázka:** Jsou místně vázaných svazků podporovány na cloudovém zařízení?

**A.** Místně vázaných svazků nejsou podporovány na cloudovém zařízení (8010 a 8020 zařízení dříve označované jako virtuální zařízení StorSimple).

**Otázka:** Můžu pomocí rutin Azure PowerShellu vytvářet a spravovat místně připnuté svazky?

**A.** Ne, nemůžete vytvářet místně připnuté svazky prostřednictvím rutin Azure PowerShellu (jakýkoli svazek, který vytvoříte prostřednictvím Azure PowerShellu, je vrstvený). Doporučujeme také nepoužívat rutiny Prostředí Azure PowerShell k úpravám vlastností místně vázaný svazek, protože bude mít nežádoucí účinek úpravy typu svazku na vrstvený.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Otázky týkající se zálohování místně připojeného svazku
**Otázka:** Jsou podporovány místní snímky místně vázaných svazků?

**A.** Ano, můžete pořizovat místní snímky místně vázaných svazků. Důrazně však doporučujeme, abyste pravidelně zálohovali místně připojené svazky pomocí cloudových snímků, abyste zajistili, že vaše data budou v případě havárie chráněna.

Všimněte si, že místní snímky místně vázaných svazků můžete také vrstvy do cloudu a není zaručeno, že zůstane v místní vrstvě zařízení.

**Otázka:** Existují nějaké pokyny pro správu místních snímků pro místně vázaných svazků?

**A.** Časté místní snímky spolu s vysokou rychlostí změn dat v místně vázaným svazku může způsobit rychlé spotřebování místního místa v zařízení a způsobit, že data z vrstvených svazků budou nabízena do cloudu. Proto doporučujeme minimalizovat počet místních snímků.

**Otázka:** Zobrazila se upozornění, že místní snímky místně vázaných svazků mohou být zrušeny. Kdy se to může stát?

**A.** Časté místní snímky spolu s vysokou rychlostí změn dat v místně vázaný svazek může způsobit, že místní místo v zařízení rychle spotřebovávat. Pokud jsou místní vrstvy zařízení silně používány, může dojít k výpadku rozšířeného cloudu, že se zařízení zaplní a příchozí zápisy do svazku mohou mít za následek zneplatnění snímků (protože neexistuje žádné místo pro aktualizaci snímků tak, aby odkazovaly na starší bloky přepsána). V takové situaci zápisy do svazku bude nadále obsluhována, ale místní snímky může být neplatný. Neexistuje žádný vliv na vaše stávající snímky cloudu.

Upozornění je upozornit, že taková situace může nastat a ujistěte se, že řešit stejné včas kontrolou místní snímky plány pořizovat méně časté místní snímky nebo odstranění starší místní snímky, které již nejsou vyžadovány.

Pokud jsou místní snímky zneplatněny, obdržíte informační výstrahu s upozorněním, že místní snímky pro konkrétní zásady zálohování byly zrušeny spolu se seznamem časových razítek místních snímků, které byly zrušeny. Tyto snímky se automaticky odstraní a už je nebudete moct zobrazit v okně **Katalogy zálohování** na webu Azure Portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Otázky týkající se převodu vrstveného svazku na místně vázaný svazek
**Otázka:** Pozoruji určitou pomalost na zařízení při převodu vrstveného svazku na místně vázaný svazek. Proč se to děje?

**A.** Proces převodu zahrnuje dva kroky:

1. Zřízení místa na zařízení pro brzy převedený místně vázaný svazek.
2. Stahování všech vrstvených dat z cloudu, abyste zajistili místní záruky.

Oba tyto kroky jsou dlouho běžící operace, které jsou závislé na velikosti převáděného svazku, data v zařízení a dostupné šířky pásma. Vzhledem k tomu, že některá data z existujících vrstvených svazků se mohou v rámci procesu zřizování vylít do cloudu, může během této doby dojít ke snížení výkonu zařízení. Kromě toho může být proces převodu pomalejší, pokud:

* Existující svazky nebyly zálohovány do cloudu; proto doporučujeme zálohovat svazky před zahájením konverze.
* Byly použity zásady omezení šířky pásma, které mohou omezit dostupnou šířku pásma pro cloud; Proto doporučujeme mít vyhrazené připojení 40 Mb/s nebo více ke cloudu.
* Proces převodu může trvat několik hodin kvůli více faktorům vysvětleným výše; Proto doporučujeme provést tuto operaci v době bez špičky nebo o víkendu, abyste se vyhnuli dopadu na koncové spotřebitele.

Další informace o [převodu vrstveného svazku na místně vázaný svazek](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Otázka:** Mohu operaci převodu svazku zrušit?

**A.** Ne, operaci převodu nelze zrušit po zahájení. Jak je popsáno v předchozí otázce, mějte prosím na paměti potenciální problémy s výkonem, se kterými se můžete během procesu setkat, a při plánování konverze postupujte podle výše uvedených doporučených postupů.

**Otázka:** Co se stane s mým svazkem, pokud se operace převodu nezdaří?

**A.** Převod svazku může selhat z důvodu problémů s připojením ke cloudu. Zařízení může nakonec zastavit proces převodu po sérii neúspěšných pokusů o smělé množství dat z cloudu. V takovém případě bude typ svazku i nadále typ zdrojového svazku před převodem a:

* Bude vyvolána kritická výstraha, která vás upozorní na selhání převodu svazku. Další informace o [výstrahách týkajících se místně vázaných svazků](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Pokud převádíte vrstvené vrstvené na místně vázaný svazek, svazek bude nadále vykazovat vlastnosti vrstveného svazku, protože data mohou být stále umístěna v cloudu. Doporučujeme vyřešit problémy s připojením a potom opakovat operaci převodu.
* Podobně při převodu z místně vázaný na vrstvený svazek selže, i když svazek bude označen jako místně vázaný svazek, bude fungovat jako vrstvený svazek (protože data mohla být rozlita do cloudu). Bude však i nadále zabírat místo na místních úrovních zařízení. Toto místo nebude k dispozici pro jiné místně vázaných svazků. Doporučujeme, abyste tuto operaci zopakovat, abyste zajistili, že převod svazku je dokončen a místní prostor v zařízení lze uvolnit.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Otázky týkající se obnovení místně připojeného svazku
**Otázka:** Jsou místně připnuté svazky obnoveny okamžitě?

**A.** Ano, místně připnuté svazky jsou obnoveny okamžitě. Jakmile jsou informace o metadatech svazku v rámci operace obnovení vyžádány z cloudu, svazek je přepnut do režimu online a může k němu získat přístup hostitel. Místní záruky pro data svazku však nebudou k dispozici, dokud nebudou všechna data stažena z cloudu, a může dojít ke snížení výkonu těchto svazků po dobu trvání obnovení.

**Otázka:** Jak dlouho trvá obnovení místně vázaný svazek?

**A.** Místně vázaných svazků jsou obnoveny okamžitě a přepnuty do režimu online, jakmile jsou informace o metadatech svazku načteny z cloudu, zatímco data svazku se nadále stahují na pozadí. Tato druhá část operace obnovení – získání zpět místní záruky pro data svazku - je dlouhotrvající operace a může trvat několik hodin pro všechna data, která mají být znovu místní. Doba potřebná k dokončení tohoto nástroje závisí na několika faktorech, jako je například velikost obnovovaného svazku a dostupná šířka pásma. Pokud byl původní svazek, který je obnovován, odstraněn, bude v rámci operace obnovení odebrán další čas na vytvoření místního místa v zařízení.

**Otázka:** Potřebuji obnovit stávající místně vázaný svazek na starší snímek (pořízený při vrstvení svazku). Bude svazek v tomto případě obnoven jako vrstvený?

**A.** Ne, svazek bude obnoven jako místně vázaný svazek. Přestože snímek pochází z doby, kdy byl svazek vrstvený, při obnovení existujících svazků, StorSimple vždy používá typ svazku na disku tak, jak aktuálně existuje.

**Otázka:** Nedávno jsem rozšířil místně vázaný svazek, ale nyní potřebuji obnovit data do doby, kdy byl svazek menší. Bude obnovena změna velikosti aktuálního svazku a bude nutné po dokončení obnovení rozšířit velikost svazku?

**A.** Ano, obnovení změní velikost svazku a po dokončení obnovení bude nutné rozšířit velikost svazku.

**Otázka:** Mohu během obnovení změnit typ svazku?

**A.** Ne, typ svazku nelze během obnovení změnit.

* Svazky, které byly odstraněny, jsou obnoveny jako typ uložený ve snímku.
* Existující svazky jsou obnoveny na základě jejich aktuální typ, bez ohledu na typ uložený ve snímku (viz předchozí dvě otázky).

**Otázka:** Potřebuji obnovit místně vázaný svazek, ale vybral jsem nesprávný bod v časovém snímku. Mohu zrušit aktuální operaci obnovení?

**A.** Ano, můžete zrušit probíhající operaci obnovení. Stav svazku bude vrácena zpět do stavu na začátku obnovení. Všechny zápisy, které byly provedeny na svazku, zatímco obnovení probíhá budou ztraceny.

**Otázka:** Spustil jsem operaci obnovení na jednom z místních vázaných svazků a teď se v katalogu nevyřízených položek zobrazí snímek, který si nepamatuji vytváření. K čemu se to používá?

**A.** Toto je dočasný snímek, který je vytvořen před operací obnovení a používá se pro vrácení zpět v případě, že obnovení je zrušeno nebo se nezdaří. Neodstraňujte tento snímek. po dokončení obnovení bude automaticky odstraněn. K tomuto chování může dojít, pokud úloha obnovení má pouze místně vázaných svazků nebo kombinaci místně vázaných a vrstvených svazků. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde.

**Otázka:** Mohu klonovat místně vázaný svazek?

**A.** Ano, můžete. Místně vázaný svazek však bude ve výchozím nastavení klonován jako vrstvený svazek. Další informace o [klonování místně vázanýsvazek](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Otázky týkající se selhání nad místně vázaným svazkem
**Otázka:** Potřebuji přepojit zařízení na jiné fyzické zařízení. Budou místně připnuté svazky přepojit jako místně připnuté nebo vrstvené?

**A.** Místně vázaných svazků se nezdaří jako místně připnuté, pokud cílové zařízení je spuštěna StorSimple 8000 série aktualizace 3 nebo vyšší.

Další informace o [převzetí služeb při selhání a zotavení po havárii místně vázaných svazků napříč verzemi](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Otázka:** Jsou místně připnuté svazky okamžitě obnoveny během zotavení po havárii?

**A.** Ano, místně připnuté svazky se během převzetí služeb při selhání okamžitě obnoví. Jakmile jsou informace o metadatech svazku vrámci operace převzetí služeb při selhání vyžádány z cloudu, svazek se přepne do režimu online na cílovém zařízení a může k němu získat přístup hostitel. Mezitím data svazku bude i nadále stahovat na pozadí a může dojít ke snížení výkonu na těchto svazcích po dobu trvání převzetí služeb při selhání.

**Otázka:** Vidím dokončenou úlohu převzetí služeb při selhání, jak lze sledovat průběh místně připnutého svazku, který se obnovuje na cílovém zařízení?

**A.** Během operace převzetí služeb při selhání je úloha převzetí služeb při selhání označena jako dokončená, jakmile budou všechny svazky v sadě převzetí služeb při selhání okamžitě obnoveny a převedeny do režimu online na cílovém zařízení. To zahrnuje všechny místně vázaných svazků, které mohly být převzetí mísení. Místní záruky dat však budou k dispozici pouze po stažení všech dat pro svazek. Tento průběh můžete sledovat pro každý místně vázaný svazek, který byl převzetí služeb při selhání, sledováním odpovídajících úloh obnovení, které jsou vytvořeny jako součást převzetí služeb při selhání. Tyto jednotlivé úlohy obnovení budou vytvořeny pouze pro místně vázaných svazků.

**Otázka:** Je možné změnit typ svazku během převzetí služeb při selhání?

**A.** Ne, typ svazku nelze během převzetí služeb při selhání změnit. Pokud přejdete k převzetí softwaru do jiného fyzického zařízení, se kterým je spuštěna aktualizace řady StorSimple 8000 3, svazky budou převzetí mísa převzetí mů e-li na základě typu svazku uloženého ve snímku.

**Otázka:** Můžu přepojit kontejner svazku s místně vázanými svazky do cloudového zařízení?

**A.** Ano, můžete. Místně vázaných svazků bude převzetí s úspěšnou převzetí jako vrstvené svazky. Další informace o [převzetí služeb při selhání a zotavení po havárii místně vázaných svazků napříč verzemi](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

