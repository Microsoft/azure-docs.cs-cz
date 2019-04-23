---
title: StorSimple místně připojené svazky – nejčastější dotazy | Dokumentace Microsoftu
description: Poskytuje odpovědi na nejčastější dotazy týkající se StorSimple místně připojené svazky.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319543"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple místně připojené svazky: Nejčastější dotazy (FAQ)
## <a name="overview"></a>Přehled
Níže jsou dotazy a odpovědi, které jste během vytvořit StorSimple místně připojené svazky, převod vrstveného svazku na místně vázaný svazek (a naopak), nebo zálohování a obnovení místně vázaný svazek.

Otázky a odpovědi jsou uspořádány do následujících kategorií

* Vytvořit místně připojený svazek
* Zálohování místně připnuté
* Převod na místně vázaný svazek vrstvený svazek
* Obnovení místně vázaný svazek
* Přebírání služeb při selhání místně vázaný svazek

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Dotazy týkající se vytváření místně vázaných svazků
**Otázka:** Jaká je maximální velikost místně vázaný svazek, který můžete vytvořit na zařízení řady 8000?

**A** zařízení používají StorSimple 8000 Series Update 3.0, umožňuje zřizovat místně vázané svazky o velikosti až 8.5 TB a vrstvené svazky až 200 TB dat v zařízení 8100. Větší zařízení 8600 umožňuje zřizovat místně vázané svazky o velikosti až 22.5 TB a vrstvené svazky o velikosti až 500 TB.

**Otázka:** Můžu v nedávné době inovován zařízení 8100 Update 3.0 a při pokusu vytvořit místně připojený svazek, maximální velikost k dispozici je pouze 6 nebo není 8.5 TB. Proč nelze vytvořit svazek velikosti 8.5 TB?

**A** Pokud zařízení používá aktualizaci 3.0, můžete zřizovat místně vázané svazky až 8.5 TB nebo vrstvené svazky až 200 TB dat v zařízení 8100. Pokud vaše zařízení už zřízeny vrstvené svazky, volné místo dostupné pro vytváření místně vázaný svazek bude úměrně nižší než toto maximální limit. Například pokud zhruba 106 TB vrstvených svazků se zřizují již v zařízení 8100 (což je polovinu vrstvenou kapacitou), pak maximální velikost místní svazek, který vytvoříte v zařízení 8100 bude odpovídajícím způsobem snížit na 4 TB (přibližně poloviční maximální místně připnuté kapacita svazku).

Některé místní prostor zařízení, protože se používají k hostování pracovní sady vrstvených svazků volného místa pro vytváření místně vázaný svazek je snížen, pokud zařízení zřízeny vrstvené svazky. Naopak vytváření místně vázaných svazků proporcionálně snižuje volného místa pro vrstvené svazky. Následující tabulka shrnuje dostupná kapacita vrstveného v zařízení 8100 a 8600 při vytváření místně vázaných svazků.

#### <a name="update-30"></a>Aktualizace 3.0 

| Zřízená kapacita místně připojené svazky | Dostupná kapacita zřídily pro vrstvené svazky – 8100 | Dostupná kapacita, které se mají zřídit pro vrstvené svazky – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |Není k dispozici |277.8 TB |
| 15 TB |Není k dispozici |166.7 TB |
| 22.5 TB |Není k dispozici |0 TB |

**Otázka:** Proč vytvořit místně připojený svazek je dlouho běžící operace?

**Odpověď:** Místně připojené svazky se zřídilo hodně. Pokud chcete vytvořit prostor v místních vrstvách zařízení, nějaká data z vrstvené svazky může doručit bez vyžádání ke cloudu během procesu zřizování. A protože to závisí na velikosti svazku zřizuje, stávající data na vašem zařízení a dostupnou šířku pásma cloudu, čas potřebný k vytvoření místního svazku může být několik hodin.

**Otázka:** Jak dlouho trvá vytvořit místně připojený svazek?

**Odpověď:** Protože místně připojené svazky se zřídilo hodně, některé existující data z vrstvené svazky může doručit bez vyžádání ke cloudu během procesu zřizování. Čas potřebný k vytvořit místně připojený svazek, proto závisí na několika faktory, včetně velikosti svazku, data na vašem zařízení a dostupnou šířku pásma. Čas při vytváření místně připojeného svazku instalovaném zařízení, která nemá žádné svazky, je přibližně 10 minut po terabajtech dat. Vytváření místních svazků však může trvat několik hodin, které na základě faktorů, které bylo vysvětleno výše v zařízení, která se používá.

**Otázka:** Chci vytvořit místně připojený svazek. Jsou nějaké jakékoli osvědčené postupy, které je potřeba mít na paměti?

**Odpověď:** Místně připojené svazky jsou vhodné pro úlohy, které vyžadují místní záruky dat po celou dobu a jsou citlivá na latenci v cloudu. Při zvažování použití místních svazků pro některé své pracovní zatížení, mějte prosím na z následujících akcí:

* Místně připojené svazky se zřídilo hodně a vytváření místních svazků ovlivňuje volné místo k dispozici pro vrstvené svazky. Proto doporučujeme začít s menší velikosti svazků a vertikálně navýšit kapacitu zvýšením požadavků vašeho úložiště.
* Dlouhotrvající operace, která může zahrnovat doručením (push) existující data z vrstvených svazků do cloudu je zřízení svazků. V důsledku toho můžete zaznamenat snížení výkonu na těchto svazcích.
* Zřizování svazků je časově náročná operace. Skutečný čas potřebný závisí na několika faktorech: velikost svazku se zřizuje, data na zařízení a dostupnou šířku pásma. Pokud ještě nezálohovali existující svazky do cloudu, je vytvořit svazek pomalejší. Doporučujeme že provést cloudové snímky existující svazky než zřídíte místní svazek.
* Můžete převést stávající vrstvené svazky na místně připojených svazků, a tento převod zahrnuje zřizování místo v zařízení pro výsledný místně vázaný svazek (kromě Probíhá ukončování Vrstvená data, pokud některý z cloudu). Důvodem jsou opět dlouhotrvající operace, která závisí na faktorech, které jsme jsme probírali výše. Doporučujeme zálohovat existující svazky před převodu podle procesu ještě sníží se pokud existující svazky nejsou zálohovány. Vaše zařízení může také docházet snížený výkon během tohoto procesu.

Další informace o tom, jak [vytvořit místně připojený svazek](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Otázka:** Můžete vytvořit více místně připojené svazky ve stejnou dobu?

**Odpověď:** Ano, ale všechny úlohy vytváření a rozšíření místně připojeného svazku se provádějí postupně.

Místně připojené svazky se zřídilo hodně a k tomu je potřeba vytvořit místní prostor zařízení (což může vést k existující data z doručí ke cloudu během procesu zřizování vrstvených svazků). Proto pokud probíhá úloha zřizování, jiné místní svazek vytvoření úlohy se zařadí do dokončení této úlohy.

Podobně pokud existující místní svazek je rozbalována nebo vytvoříte vrstvený svazek je převáděn na místně vázaný svazek, pak vytvoření nové místně vázaný svazek je zařadí do fronty až do dokončení předchozí úlohy. Rozšíření místně připojeného svazku zahrnuje rozšíření existující místní prostor pro daný svazek. Převod z vrstveného na místně připnutý svazek také zahrnuje vytvoření volné místo pro výsledný místně připnutý svazek. V obou těchto operací, vytvoření nebo rozšíření místní prostor dlouho běží úlohy.

V těchto úloh můžete zobrazit **úlohy** okně služby Správce zařízení StorSimple. Úloha, která se aktivně zpracovává se průběžně aktualizuje tak, aby odrážely průběh zřizování místa. Ostatní úlohy místně vázaný svazek jsou označeny jako spuštěná, ale je zastaven a proces jejich průběh a vyberou v pořadí, v jakém že byly zařazeny do fronty.

**Otázka:** Mohu odstranit místně vázaný svazek. Proč nevidím uvolňovaného místo projeví na dostupné ploše při pokusu o vytvoření nového svazku?

**Odpověď:** Pokud odstraníte místně vázaný svazek, volné místo dostupné pro nové svazky nemusí aktualizovat okamžitě. Služba Správce zařízení StorSimple aktualizuje volné místo dostupné přibližně každou hodinu. Doporučujeme, abyste pro že počkejte hodinu, než se pokusíte vytvořit nového svazku.

**Otázka:** Podporují se místně připojené svazky na cloudovém zařízení?

**Odpověď:** Místně připojené svazky nejsou podporovány na cloudovém zařízení (8010 a 8020 zařízení dříve označované jako virtuální zařízení StorSimple).

**Otázka:** Můžete použít rutiny prostředí Azure PowerShell můžete vytvořit a spravovat místně připojené svazky?

**Odpověď:** Ne, nelze vytvářet místně připojené svazky prostřednictvím rutin prostředí Azure PowerShell (vrstvené svazky, které vytvoříte pomocí prostředí Azure PowerShell). Doporučujeme také je velmi riskantní používat rutiny Azure Powershellu k úpravě jakékoli vlastnosti místně vázaný svazek, jak bude mít nežádoucí vliv na změnu typu svazku na vrstvený.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Otázky týkající se zálohování místně vázaný svazek
**Otázka:** Jsou místní snímky místně připojené svazky, které jsou podporovány?

**Odpověď:** Ano, můžete provést místní snímky místně připojené svazky. Nicméně důrazně doporučujeme, abyste pravidelně zálohovali místně připojené svazky cloudové snímky pro zajištění ochrany vašich dat v případě havárie.

Všimněte si, že místní snímky místně připojené svazky umožňují také vrstvení cloudu a nemusí zůstat u místní vrstvy zařízení.

**Otázka:** Existují nějaké obecné zásady pro správu místních snímků pro místně připojené svazky?

**Odpověď:** Časté pořizování s vysokou mírou změny dat v místně vázaný svazek místních snímků může způsobit místní prostor na zařízení bude rychle spotřebovávat a následek dat od vrstvených svazků se vloží do cloudu. Doporučujeme proto, že minimalizovat počet místních snímků.

**Otázka:** Zobrazila se mi výstrahu s oznámením, že může ukončit platnost mé místní snímky místně připojené svazky. Kdy může k tomu dochází?

**Odpověď:** Časté pořizování s vysokou mírou změny dat v místně vázaný svazek místních snímků může způsobit, že místní prostor na zařízení bude rychle spotřebovávat. Pokud se hojně využívají místních vrstvách zařízení, zařízení zaplní, může dojít k výpadku ená Cloudová a příchozí zápis na svazek může způsobit neplatnost snímky (protože neexistuje žádný prostor k aktualizaci snímků pro odkazování na starší bloky data, která mají přepsaný). V takovém případě bude pokračovat zápis na svazek ke zpracování, ale místní snímky může být neplatný. Neexistuje žádný vliv na stávající cloudové snímky.

Upozornění upozornění je pro vás informovat, že taková situace může nastat a ujistěte se, že stejné adres včas revize plánům místní snímky se méně častá místní snímky nebo odstranit starší místní snímky, které se už nevyžadují.

Pokud se platnost místních snímků, zobrazí se informace upozornění oznamující, že se zrušila platnost místních snímků pro konkrétní zásady zálohování vedle seznamu časová razítka místní snímky, které byly zneplatněny. Tyto snímky budou automaticky odstraněn a už nebude moct zobrazit v **zálohování katalogy** okna na webu Azure Portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Dotazy týkající se převod na místně vázaný svazek vrstvený svazek
**Otázka:** Při převodu do místně vázaný svazek vrstvený svazek můžu jsem sledování některé pomalost v zařízení. Proč k tomu dochází?

**Odpověď:** Proces převodu zahrnuje dva kroky:

1. Zřizování místa v zařízení brzy na--převést místně připnutý svazek.
2. Stahování všechna Vrstvená data z cloudu a zajistit místní záruky.

Oba tyto kroky jsou dlouho běžící operace, které jsou závislé na velikost svazku převádí data na zařízení a dostupnou šířku pásma. Protože některá data z vrstvené svazky může být uložená v cloudu jako součást procesu zřizování, zařízení se mohou vyskytnout snížený výkon během této doby. Kromě toho může být pomalejší proces převodu pokud:

* Existující svazky nezálohovaly do cloudu Proto doporučujeme zálohovat svazky ještě před zahájením převodu.
* Zásady omezení šířky pásma se použily, což by mohlo omezit dostupnou šířku pásma cloudu; Doporučujeme proto, že máte vyhrazený 40 MB/s nebo další připojení ke cloudu.
* Proces převodu může trvat několik hodin z důvodu několika faktory, které bylo vysvětleno výše; Proto doporučujeme provést tuto operaci v obdobích mimo špičky nebo o víkendech, abyste se vyhnuli vlivu na koncové uživatele.

Další informace o tom, jak [převod vrstveného svazku na místně vázaný svazek](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Otázka:** Můžete zrušit operaci převodu svazku?

**Odpověď:** Ne, nemůžete Storno operaci převodu, jakmile zahájíte. Jak je popsáno v předchozí otázce, uvědomte potenciální problémy s výkonem, že může dojít během procesu a použijte osvědčené postupy uvedené výše, při plánování vaší převodu.

**Otázka:** Co se stane Moje svazku, pokud se převod nezdaří?

**Odpověď:** Objem převodu může selhat kvůli problémům s připojením cloudu. Zařízení může nakonec zastavte sledovací proces převodu po určitém počtu neúspěšných pokusů o snížilo Vrstvená data z cloudu. V takové situaci typ svazku budou nadále typ zdrojového svazku před převod, a:

* Kritická výstraha, bude vyvolána upozornění na selhání převodu svazku. Další informace o [výstrahy související s místně připojené svazky](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Pokud převádíte vrstveného na místně vázaný svazek, svazek bude mít vlastnosti vrstveného svazku podle dat může být stále nacházejí v cloudu. Doporučujeme vyřešení potíží s připojením a zkuste operaci převodu zopakovat.
* Podobně když převod z místně vázaný na vrstvený svazek se nezdaří, přestože se svazek se označí jako místně vázaný svazek, ji bude fungovat jako vrstveného svazku (protože data může přesahovat do cloudu). Bude však nadále zabírají prostor v místních vrstvách zařízení. Tento prostor nebude k dispozici pro jiné místně připojené svazky. Doporučujeme se opakovat operaci zajistíte, že dokončení převodu svazku a místní prostor na zařízení se nedá uvolnit.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Otázky týkající se obnovení místně vázaný svazek
**Otázka:** Se místně připojené svazky okamžitě obnovit?

**Odpověď:** Ano, místně připojené svazky se okamžitě obnoví. Poté, co informace metadat pro svazek se načítají z cloudu jako součást operace obnovení, svazek se znovu přepne online a přístupná pomocí hostitele. Však pro svazek místní záruky, že data budou k dispozici, dokud všechna data byla stažena z cloudu a může dojít k poklesu výkonu na těchto svazcích dobu obnovení.

**Otázka:** Jak dlouho trvá k obnovení místně vázaný svazek?

**Odpověď:** Místně připojené svazky se okamžitě obnoví a přepne do online režimu jako informace o svazku metadat je načten z cloudu, i když dat svazku zůstává stáhne na pozadí. Tato druhá část operace obnovení – návrat místní záruky pro objemu dat – je dlouho běžící operace a může trvat několik hodin pro všechna data, která má být provedeno místní znovu. Čas potřebný k dokončení stejné závisí na několika faktorech, jako je například velikost svazku, který se má obnovit a dostupnou šířku pásma. Pokud byl odstraněn původního svazku, který se obnovuje, čas navíc budete přesměrováni na vytvořit místní prostor na zařízení jako součást operace obnovení.

**Otázka:** Potřebuji obnovit mé existující místně vázaný svazek starší snímku (provedeny, když byl vrstvený svazek). Svazek se obnoví jako vrstvené v tomto případě?

**Odpověď:** Ne, svazek se obnoví jako místně vázaný svazek. I když data snímku na čas, kdy byl vrstveného svazku, při obnovení existující svazky se StorSimple vždy používá typ svazku na disk jako aktuálně existuje.

**Otázka:** Nedávno jsem rozšířen Moje místně vázaný svazek, ale teď je potřeba obnovit data na čas, kdy byla menší velikost svazku. Změní velikost obnovení aktuálně svazku a bude potřeba rozšířit velikost svazku po dokončení obnovení?

**Odpověď:** Ano, obnovení bude velikost svazku a budete muset rozšířit velikost svazku po dokončení obnovení.

**Otázka:** Můžete změnit typ svazku během obnovení?

**Odpověď:** Ne, během obnovení nelze změnit typ svazku.

* Svazky, které se odstranily se obnoví jako typ uložený do snímku.
* Existující svazky jsou obnoveny do jejich aktuálního typu, bez ohledu na typ uložený do snímku (viz předchozí dvě otázky).

**Otázka:** Je potřeba obnovit Moje místně vázaný svazek, ale importovali nesprávné bodu v čase snímku. Můžete zrušit aktuální operaci obnovení?

**Odpověď:** Ano, můžete zrušit probíhající operace obnovení. Stav svazku, bude vrácena zpět do stavu na začátku obnovení. Jakékoli zápisy, které byly provedeny během obnovení svazku se však budou ztraceny.

**Otázka:** Můžu pracovat se operace obnovení na jednom z mé místně připojené svazky a nyní zobrazuje snímek v mé nevyřízených položek katalogu, který jsem nemusíte znovu shromážděte vytváření. Co to slouží?

**Odpověď:** Toto je dočasný snímek, který je vytvořen před operaci obnovení a používá se pro vrácení zpět v případě obnovení je zrušena nebo se nezdaří. Neodstraňujte tento snímek; To se automaticky odstraní po dokončení obnovení. Toto chování může dojít, pokud vaše úloha obnovení má jenom místně připojené svazky nebo kombinaci místně připnuté a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde.

**Otázka:** Můžete naklonovat místně vázaný svazek?

**Odpověď:** Ano, je to možné. Místně vázaný svazek bude klonovat jako vrstvený svazek, však ve výchozím nastavení. Další informace o tom, jak [klonovat místně vázaný svazek](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Dotazy týkající se přebírání služeb při selhání místně vázaný svazek
**Otázka:** Potřebuji k převzetí služeb při selhání zařízení do jiného fyzického zařízení. Moje místně připojené svazky se navrátí než místně připnuté a vrstvené?

**Odpověď:** Místně vázaných svazků při selhání místně připnuté pokud cílové zarízení je spuštena StorSimple 8000 series update 3 nebo vyšší.

Další informace o [převzetí služeb při selhání a zotavení po Havárii z místně připojené svazky napříč verzemi](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Otázka:** Se místně připojené svazky okamžitě obnoví během zotavení po havárii (DR)?

**Odpověď:** Ano, místně připojené svazky jsou obnoveny okamžitě během převzetí služeb při selhání. Poté, co informace metadat pro svazek se načítají z cloudu jako součást operace převzetí služeb při selhání, svazek na cílovém zařízení do režimu online a přístupná pomocí hostitele. Mezitím data na svazku budou pokračovat ve stahování na pozadí a může dojít ke snížení výkonu na těchto svazcích po dobu trvání převzetí služeb při selhání.

**Otázka:** Zobrazuje dokončena úloha převzetí služeb při selhání, jak můžete sledovat průběh místně připojený svazek, který je obnovena na cílovém zařízení?

**Odpověď:** Při operaci převzetí služeb při selhání je úloha převzetí služeb při selhání označil jako dokončení jednou všechny svazky sady převzetí služeb při selhání se okamžitě obnoví a uvést do režimu online na cílovém zařízení. Jedná se o všechny místně připojené svazky, které může mít selhání; ale místní záruky dat budou k dispozici pouze pokud stáhl všechna data svazku. Tento postup pro každý místně vázaný svazek, u kterého došlo od monitorování odpovídající úlohy obnovení, které jsou vytvořeny jako součást převzetí služeb při selhání můžete sledovat. Tyto samostatné obnovení úlohy se vytvoří jenom pro místně připojené svazky.

**Otázka:** Můžete změnit typ svazku během převzetí služeb při selhání?

**Odpověď:** Ne, nelze změnit typ svazku při selhání. Pokud se nedaří přes do jiného fyzického zařízení, na kterém běží StorSimple 8000 series update 3, svazky se převzetí služeb při selhání na základě typu svazku uložený do snímku.

**Otázka:** Můžete převzít kontejner svazků se místně připojené svazky ke cloudovému zařízení?

**Odpověď:** Ano, je to možné. Místně připojené svazky převezme jako vrstvené svazky. Další informace o [převzetí služeb při selhání a zotavení po Havárii z místně připojené svazky napříč verzemi](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

