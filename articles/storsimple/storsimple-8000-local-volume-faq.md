---
title: StorSimple místně připnutý svazky – nejčastější dotazy | Microsoft Docs
description: Poskytuje odpovědi na nejčastější dotazy týkající se svazky zařízení StorSimple místně vázaný.
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
ms.openlocfilehash: 857647e89b7ac5d9bc72bc4b2ace0ea7279353f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875589"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple místně připojené svazky: Nejčastější dotazy (FAQ)
## <a name="overview"></a>Přehled
Dále jsou otázky a odpovědi, které by mohly mít při vytvořit svazek StorSimple místně vázaný, převést vrstvený svazek k místně vázaný svazek (a naopak), nebo zálohování a obnovení místně vázaný svazek.

Otázky a odpovědi jsou uspořádány do následujících kategorií

* Vytváření místně vázaný svazek
* Zálohování místně vázaný
* Převádění vrstvený svazek na místně vázaný svazek
* Obnovení místně vázaný svazek
* Při přechodu místně vázaný svazek

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Dotazy týkající se vytváření místně vázaný svazek
**Otázka:** Co je maximální velikost místně vázaný svazek, který můžete vytvořit na 8000 řadu zařízení?

**A** na zařízení se systémem StorSimple 8000 řady aktualizace 3.0, můžete zřizovat místně vázané svazky až na 8.5 TB a vrstvené svazky o velikosti až 200 TB zařízení 8100. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB.

**Otázka:** Zařízení 8100 nedávno upgradu na aktualizaci 3.0 a při pokusu vytvořit místně vázaný svazek, maximální velikost k dispozici je pouze 6 TB a není šířka 8,5 TB. Proč nelze vytvořit svazek šířka 8,5 TB?

**A** Pokud vaše zařízení používá aktualizace 3.0, můžete zřizovat místně vázané svazky na 8.5 TB nebo zřízeny vrstvené svazky o velikosti až 200 TB zařízení 8100. Pokud vaše zařízení už zřízeny vrstvené svazky, bude úměrně nižší než tento maximální limit místa pro vytvoření místně vázaný svazek. Například pokud přibližně 106 TB vrstvené svazky se zřizují již v zařízení 8100 (což je polovinu vrstvené kapacity), pak maximální velikost místního svazku, který můžete vytvořit v zařízení 8100 odpovídajícím způsobem zmenší se 4 TB (přibližně polovinu maximální místně připnuli kapacita svazku).

Protože některé volné místo na zařízení se používá k hostování pracovní sady vrstvených svazků, se snižuje dostupné místo pro vytvoření místně vázaný svazek, pokud zařízení zřízeny vrstvené svazky. Naopak vytváření místně vázaný svazek úměrně snižuje dostupný prostor pro vrstvené svazky. Následující tabulka shrnuje dostupné kapacity vrstvené na zařízení 8100 a 8600 při vytváření místně vázaných svazků.

#### <a name="update-30"></a>Aktualizace 3.0 
| Zřízená kapacita místně vázaných svazků | Dostupná kapacita zřídit pro vrstvené svazky - 8100 | Dostupná kapacita zřídit pro vrstvené svazky - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |Není k dispozici |277.8 TB |
| 15 TB |Není k dispozici |166.7 TB |
| 22,5 TB |Není k dispozici |0 TB |

**Otázka:** Proč vytváření místně vázaných svazků je dlouhotrvající operace?

**Odpověď:** Místně vázaných svazků jsou tlustě zřízený. Pokud chcete vytvořit prostor v místních vrstvách zařízení, může některá data z vrstvené svazky vloží do cloudu během procesu zřizování. A vzhledem k tomu, že to závisí na velikosti svazku se zřídí stávající data v zařízení a dostupnou šířku pásma do cloudu, čas potřebný k vytvoření místního svazku může být několik hodin.

**Otázka:** Jak dlouho trvá vytvořit místně vázaný svazek?

**Odpověď:** Protože místně vázaných svazků jsou tlustě zřízený, může některá existující data z vrstvené svazky vloží do cloudu během procesu zřizování. Čas potřebný k vytvoření místně vázaný svazek proto závisí na několika faktory, včetně velikosti svazku, dat na zařízení a dostupnou šířku pásma. Čas vytvoření místně vázaný svazek na nově instalovaném zařízení, které má žádné svazky, je přibližně 10 minut za terabajt data. Vytváření místních svazků však může trvat několik hodin, které jsou založeny na faktorech vysvětlené dřív na zařízení, která je používána.

**Otázka:** Chcete vytvořit místně vázaný svazek. Existují veškeré nejlepší postupy, které je nutné mít na paměti?

**Odpověď:** Místně vázaných svazků jsou vhodné pro úlohy, které vyžadují místní záruky dat po celou dobu a jsou citlivá na latenci v cloudu. Při určování využití místní svazky v žádném z vašich zatížení, Upozorňujeme následující:

* Místně vázaných svazků jsou tlustě zřízený a vytváření místních svazků ovlivní volné místo pro vrstvené svazky. Proto doporučujeme začít s menším svazky a škálovat jako požadavek zvyšuje vašeho úložiště.
* Zřizování místních svazků je dlouhotrvající operace, které mohou zahrnovat nabízet existující data z vrstvené svazky do cloudu. V důsledku toho může docházet v těchto svazcích snížený výkon.
* Zajištění místní svazků je časově náročná operace. Skutečný čas související se situací, závisí na několika faktorech: velikost svazku se zřídí, data na zařízení a dostupnou šířku pásma. Pokud nebyla zálohována existující svazky do cloudu, je vytvoření svazku pomalejší. Doporučujeme, že provedete cloudových snímků existující svazky než zřídíte místní svazek.
* Místně vázaných svazků můžete převést vrstvené svazky a tento převod zahrnuje zřizování místa na zařízení pro výsledný místně vázaný svazek (kromě ukončování vrstvené data, pokud existuje, z cloudu). Znovu toto je dlouhotrvající operace, které závisí na faktorech, které jsme probrali výše. Doporučujeme zálohovat existující svazky před převod jako proces bude i pomalejší, pokud nejsou existující svazky zálohovat. Zařízení může také dojít k omezení výkonu během tohoto procesu.

Další informace o tom, jak [vytvořit místně vázaný svazek](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Otázka:** Můžete vytvořit více místně vázaných svazků najednou?

**Odpověď:** Ano, ale všechny úlohy vytváření a rozšíření místně vázaný svazek se provádějí postupně.

Tlustě zřízený místně vázaných svazků to vyžaduje vytvoření volné místo v zařízení (což může mít za následek existující data ze vrstvené svazky, které mají být během procesu zřizování nabídnutých do cloudu). Proto pokud právě probíhá zřizování úlohy, jiné místní svazek vytvoření úlohy se zařadí do fronty až do dokončení této úlohy.

Podobně pokud je rozbalována existující místní svazek nebo vrstvený svazek je převáděn na místně vázaný svazek, pak vytvoření nové místně vázaný svazek je zařadit do fronty až do dokončení předchozí úlohy. Rozšiřování velikost místně vázaný svazek zahrnuje rozšíření stávající volné místo pro tento svazek. Převod z vrstvené pro místně vázaný svazek také zahrnuje vytvoření volné místo pro výsledná místně připnutý svazku. V obou těchto operací, vytvoření nebo rozšíření volné místo s dlouhým běží úlohy.

Tyto úlohy v lze zobrazit **úlohy** okno služby StorSimple Manager zařízení. Úlohy, které jsou aktivně zpracovávány průběžně aktualizován, aby odrážel průběh zřizování místa. Zbývající úlohy místně vázaný svazek označen jako spuštěná, ale jejich průběhu je zastaven a proces a jsou zachyceny v pořadí, v jakém že byly zařazeny do fronty.

**Otázka:** Uživatel odstranil místně vázaný svazek. Proč nevidím regenerovaný místo promítnuta dostupné místo při pokusu o vytvoření nového svazku?

**Odpověď:** Pokud odstraníte místně vázaný svazek, místa na disku pro nové svazky nemusí být okamžitě aktualizován. Služby StorSimple Manager zařízení aktualizuje volné místo dostupné přibližně za hodinu. Doporučujeme že čekat na jednu hodinu, než se pokusíte vytvořit nový svazek.

**Otázka:** Jsou podporovány místně vázaných svazků na cloudu zařízení?

**Odpověď:** Místně vázaných svazků nejsou podporovány na zařízení cloudu (8010 8020 zařízení a dříve označované jako virtuální zařízení StorSimple).

**Otázka:** Můžete použít rutiny prostředí Azure PowerShell k vytváření a správě místně vázaných svazků?

**Odpověď:** Ne, nelze vytvořit místně vázaných svazků prostřednictvím rutin prostředí Azure PowerShell (víceúrovňová jakýkoli svazek, který vytvoříte pomocí prostředí Azure PowerShell). Doporučujeme také nepoužívejte rutin prostředí Azure PowerShell k úpravě jakékoli vlastnosti místně vázaný svazek, protože bude mít nežádoucí vliv na změny typu svazku na vrstvené.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Dotazy týkající se zálohování místně vázaný svazek
**Otázka:** Jsou místní snímky místně vázaných svazků, které jsou podporovány?

**Odpověď:** Ano, můžete využít místní snímky místně vázaných svazků. Nicméně důrazně doporučujeme, abyste pravidelně zálohovali místně vázaných svazků s cloudových snímků pro zajištění ochrany dat v případě havárie.

Upozorňujeme, že místní snímky místně vázaných svazků můžete také vrstvy cloudu a se nezaručuje, že setrvá ve vrstvě místní zařízení.

**Otázka:** Existují žádné pokyny pro správu místních snímků místně vázaných svazků?

**Odpověď:** Časté místní snímky spolu s vysokou míru změn dat v místně vázaný svazek může dojít volné místo v zařízení rychle využívat a mít za následek data z vrstvené svazky se instaluje do cloudu. Doporučujeme proto, že můžete minimalizovat počet snímků, místní.

**Otázka:** Zobrazila se výstraha s oznámením, že moje místní snímky místně vázaných svazků může zrušena. Když tomu může dojít?

**Odpověď:** Časté místní snímky spolu s vysokou míru změn dat v místně vázaný svazek může dojít volné místo na zařízení využívat rychle. Pokud používáte výraznou místních vrstvách zařízení, výpadku rozšířené cloudu může způsobit zařízení maximálnímu zaplnění a příchozí zápis na svazek může způsobit zrušení platnosti snímky (jak žádné místo existuje aktualizovat snímky k odkazování na starší bloky dat, která byla přepsání). V takovém případě bude pokračovat zápis na svazek ke zpracování, ale místní snímky může být neplatný. Neexistuje žádný vliv na vaše stávající cloudových snímků.

Upozornění na výstrahy je s upozorněním, můžete tato situace nastat a ujistěte se, že stejný vyřešit včas tak, že kontrola vaše místní snímky plány trvat méně častá místních snímků nebo odstranění starší místní snímků, které se už nevyžadují.

Pokud jsou zneplatněny místní snímky, obdržíte výstrahu informace, upozornění, že místní snímky pro konkrétní zásady zálohování se zrušila platnost vedle seznamu časová razítka místní snímky, které došlo ke zrušení platnosti. Tyto snímky bude automaticky odstraněna a již nebudete moci zobrazit v **zálohování katalogů** okno na portálu Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Otázky týkající se převodu vrstvený svazek k místně vázaný svazek
**Otázka:** Při převodu vrstvený svazek k místně vázaný svazek I mě sledování některé pomalost na zařízení. Proč je této situaci?

**Odpověď:** Proces převodu zahrnuje dva kroky:

1. Zřizování místa v zařízení brzy na--převést místně připnuli svazku.
2. Stahování žádné vrstvené dat z cloudu zajistit místní záruky.

Oba tyto kroky jsou dlouhé spuštění operace, které jsou závislé na velikost svazku se převést data na zařízení a dostupnou šířku pásma. Jako některá data z vrstvené svazky může distribuována do cloudu jako součást procesu zřizování, zařízení může dojít k omezení výkonu během této doby. Kromě toho může být pomalejší proces převodu pokud:

* Existující svazky ještě nebyly zálohovány do cloudu; Proto doporučujeme že zálohování svazků před zahájením převodu z.
* Byly použity zásady omezení šířky pásma, což by mohlo omezit dostupnou šířku pásma do cloudu; Doporučujeme proto, že máte vyhrazený 40 MB/s nebo další připojení ke cloudu.
* Proces převodu může trvat několik hodin z důvodu několik faktorů vysvětlené dřív; Doporučujeme proto, že při provádění této operace v době bez vrcholů nebo na víkendu, abyste se vyhnuli vlivu na koncoví uživatelé.

Další informace o tom, jak [převést vrstvený svazek na místně vázaný svazek](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Otázka:** Můžete zrušit operaci převodu svazku?

**Odpověď:** Ne, nemůžete Storno operaci převodu jednou inicioval. Jak je popsáno v předchozí otázce, je potřeba upozornit možných potíží s výkonem, můžete dojde během procesu a použijte osvědčené postupy při plánování vaší převod uvedené výše.

**Otázka:** Co se stane Moje svazku, pokud se nezdaří operace převodu?

**Odpověď:** Převod svazku může selhat z důvodu problémy s připojením k cloudu. Zařízení může zastavit proces převodu nakonec po určitém počtu neúspěšných pokusů o ukončila činnost vrstvené data z cloudu. V takové situaci typ svazku nadále typ zdroje svazku před převodu a:

* Kritická výstraha, bude vyvolána jako upozornění na selhání převodu svazku. Další informace o [výstrahy související s místně vázaných svazků](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Pokud převádíte vrstvené na místně vázaný svazek, svazek bude vykazovat vlastnosti vrstvený svazek, jako jsou data může stále umístěny v cloudu. Doporučujeme, vyřešte problémy s připojením a opakujte operaci převodu.
* Podobně pokud převod místně vázaný na vrstvený svazek nezdaří, i když svazek budou označeny jako místně vázaný svazek, ho bude fungovat jako vrstvený svazek (protože data může přesahovat do cloudu). Zabírají prostor v místních vrstvách zařízení, ale bude pokračovat. Tento prostor nebudete mít k dispozici pro ostatní místně vázaných svazků. Doporučujeme zajistit, že dokončení převodu svazku a volné místo na zařízení se nedá uvolnit tuto operaci zkuste zopakovat.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Otázky týkající se obnovení místně vázaný svazek
**Otázka:** Jsou místně vázaných svazků okamžitě obnovit?

**Odpověď:** Ano, jsou okamžitě obnovit místně vázaných svazků. Jakmile informace metadat pro svazek pocházejí z cloudu v rámci operace obnovení, svazek je uvést do režimu online a přístupné pro hostitele. Místní záruky pro svazek, že data nebudou přítomen, dokud se všechna data byl stažen z cloudu a může dojít k však snižuje výkon v těchto svazcích dobu obnovení.

**Otázka:** Jak dlouho trvá obnovit místně vázaný svazek?

**Odpověď:** Místně vázaných svazků jsou obnoveny okamžité a také informace o svazku metadata se načítají z cloudu, zatímco data na svazku stále ke stažení na pozadí uvést do režimu online. Této druhé části operace obnovení – získávání zpět místní záruky pro datový svazek – je dlouhotrvající operace a může trvat několik hodin pro všechna data, které musí být místní znovu. Čas potřebný k dokončení stejné závisí na několika faktorech, například velikost svazku, který se má obnovit a dostupnou šířku pásma. Pokud byla odstraněna původní svazek, který je obnovena, další čas budete přesměrováni na vytvoření volné místo v zařízení v rámci operace obnovení.

**Otázka:** Je třeba obnovit mé existující místně vázaný svazek starší snímku (provedeny, když byla vrstvené svazek). Svazek se obnoví jako v tomto případě vrstvené?

**Odpověď:** Ne, budou obnoveny svazek jako místně vázaný svazek. I když data snímku na čas, kdy byl vrstvené svazku, při obnovení existující svazky, StorSimple vždy používá typ svazku na disku, protože aktuálně existuje.

**Otázka:** I my místně vázaný svazek nedávno rozšířeno, ale teď chcete obnovit data na čas, kdy byla menší velikost svazku. Změní velikost obnovení aktuální svazku a bude nutné rozšířit velikost svazku po dokončení obnovení?

**Odpověď:** Ano, obnovení bude změní velikost svazku a je potřeba rozšířit velikost svazku po dokončení obnovení.

**Otázka:** Můžete změnit typ svazku během obnovení?

**A.** Ne, nemůžete změnit typ svazku během obnovení.

* Jako typ uložené ve snímku se obnoví svazky, které byly odstraněny.
* Existující svazky jsou obnoveny podle jejich aktuálního typu, bez ohledu na typ uložené ve snímku (viz předchozí dva dotazy).

**Otázka:** Je nutné obnovit Moje místně vázaný svazek, ale importovali správný bod v čas snímku. Můžete zrušit aktuální operaci obnovení?

**Odpověď:** Ano, můžete zrušit probíhající operace obnovení. Stav svazku, bude vrácena zpět do stavu na začátku obnovení. Všechny zápisů, které byly provedeny během provádění obnovení svazku však budou ztraceny.

**Otázka:** Můžu spustit operaci obnovení na jeden z mých místně vázaných svazků a zobrazují snímku v mé nevyřízených položek katalogu, která I není recollect vytváření. To k čemu slouží?

**Odpověď:** Toto je dočasný snímek, který je vytvořen před operace obnovení a používá se pro vrácení zpět v případě obnovení je zrušená nebo selže. Neodstraňujte tento snímek; jej bude automaticky odstraněna po dokončení obnovení. Toto chování může dojít, pokud vaše úlohy obnovení má pouze místně vázaný svazky nebo kombinaci místně vázaný a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde.

**Otázka:** Může klonovat místně vázaný svazek?

**Odpověď:** Ano, můžete. Místně vázaný svazek bude klonovat jako vrstvený svazek, ale ve výchozím nastavení. Další informace o tom, jak [klonovat místně vázaný svazek](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Dotazy týkající se selhání přes místně vázaný svazek
**Otázka:** Je třeba převzít své zařízení do jiného fyzického zařízení. Bude Moje místně vázaných svazků se přes místně vázaný nebo vrstvené?

**Odpověď:** Místně vázaných svazků jsou převzetí služeb při selhání jako místně vázaný pokud cílové zařízení běží aktualizace řady StorSimple 8000 3 nebo vyšší.

Další informace o [převzetí služeb při selhání a zotavení po Havárii z místně vázaný svazky mezi verzemi](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Otázka:** Místně vázaných svazků okamžitě obnoveny během zotavení po havárii (DR)?

**Odpověď:** Ano, místně vázané svazky jsou obnoveny okamžitě během převzetí služeb při selhání. Jakmile informace metadat pro svazek pocházejí z cloudu v rámci operace převzetí služeb při selhání, svazek na cílovém zařízení do režimu online a přístupné pro hostitele. Současně data svazek bude pokračovat ve stahování na pozadí a může dojít k snížený výkon v těchto svazcích po dobu trvání převzetí služeb při selhání.

**Otázka:** Zobrazit dokončena úloha převzetí služeb při selhání, jak můžete sledovat průběh místně vázaný svazek, který je obnovena na cílovém zařízení?

**Odpověď:** Během operace převzetí služeb při selhání převzetí služeb při selhání úlohy je označena dokončení jednou všechny svazky v sadě převzetí služeb při selhání byla okamžitě obnovit a uvést do režimu online na cílovém zařízení. To zahrnuje všechny místně vázaných svazků, které může mít byla při selhání; ale místní záruky dat bude dostupný pouze pokud stáhl všechna data pro svazek. Můžete sledovat tento postup pro každý místně vázaný svazek, který byl selhal nepřevezme monitorování odpovídající úlohy obnovení, které jsou vytvořené jako součást převzetí služeb při selhání. Tyto úlohy obnovení jednotlivých pouze vytvoří místně vázaných svazků.

**Otázka:** Můžete změnit typ svazku během převzetí služeb při selhání?

**Odpověď:** Ne, nemůže změnit typ svazku během převzetí služeb při selhání. Pokud se nedaří přes jiné fyzické zařízení, které běží StorSimple 8000 řady aktualizace 3, svazky jsou převzetí služeb při selhání na základě typu svazku uložená ve snímku.

**Otázka:** Můžete převzít kontejner svazků s místně vázaných svazků do cloudu zařízení?

**Odpověď:** Ano, můžete. Místně vázaných svazků převezme služby při selhání jako vrstvené svazky. Další informace o [převzetí služeb při selhání a zotavení po Havárii z místně vázaný svazky mezi verzemi](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

