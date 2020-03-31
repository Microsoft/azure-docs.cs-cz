---
title: Pokyny pro efektivní kotevní zážitky
description: Pokyny a důležité informace k vytvoření a vyhledání kotvy efektivně pomocí Azure prostorové kotvy.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270524"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Vytvoření efektivního prostředí ukotvení pomocí prostorových ukotvení Azure

Tento článek obsahuje pokyny a důležité informace, které vám pomohou efektivně vytvářet a lokalizovat kotvy pomocí prostorových kotev.

## <a name="good-anchors"></a>Dobré kotvy

Prostorové kotvy vám pomohou vytvořit dobré kotvy. Je důležité investovat čas do vzdělávání nebo vedení uživatelů v uživatelském prostředí (UX) k vytvoření dobrých kotev. Investováním do vytváření dobrých kotev dopředu pomáháte koncovým uživatelům spolehlivě najít kotvy:

- Na různých zařízeních.
- V různých časech.
- V různých světelných podmínkách.
- Z požadovaných perspektiv v prostoru.

## <a name="static-and-dynamic-locations"></a>Statická a dynamická umístění

Součástí návrhu kotvy zkušenosti je výběr míst. Budou umístění statická a definovaná správcem prostoru? Nebo budou dynamické a definované uživatelem?

Manažer maloobchodu může chtít statické prostředí v obchodě, které uživatele přiláká k návštěvě. Ale vývojář smíšené reality deskové hry by pravděpodobně chtěl nechat uživatele vybrat si, kde hrát.

U statických umístění můžete administrátory naučit trávit čas kurátorováním prostoru s dobrými kotvami.

U dynamických umístění byste měli přemýšlet o tom, jak učíte nebo vedete uživatele v uživatelském prostředí k vytváření dobrých kotev.

## <a name="stable-visual-features"></a>Stabilní vizuální funkce

Vizuální sledovací systémy používané na zařízeních se smíšenou realitou a rozšířenou realitou spoléhají na vizuální vlastnosti prostředí. Chcete-li získat co nejspolehlivější zážitek:

- *Vytvořte* kotvy v umístěních, která mají stabilní vizuální prvky (to znamená funkce, které se často nemění).

- *Nevytvářejte* kotvy na velkých prázdných plochách, které nemají žádné rozlišovací vlastnosti.

- *Nevytvářejte* kotvy na vysoce reflexních materiálech.

- *Nevytvářejte* kotvy na plochách, kde se vzorek opakuje, například koberec nebo tapeta.

![Příklady dobrého prostředí pro kotvy a špatné prostředí pro kotvy](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Různé perspektivy zobrazení

Při vytváření kotvy přemýšlejte o lidech, kteří se později pokusí kotvu najít.

Vezměme si například kotvu uprostřed místnosti, která má dvě dveře. Pravděpodobně budete chtít umožnit uživatelům vstoupit do místnosti z obou dveří. Při vytváření kotvy budete muset skenovat její polohu z obou dveří. Můžete změnit perspektivy pro sběr dat prostředí kolem kotvy tak, aby uživatelé mohli najít kotvu z obou dveří.

Obecně platí, že při vytváření kotvy, skenování z pohledu lidí, kteří se budou snažit najít. Takže pokud umisťujete virtuální obsah na venkovní sochu, má smysl chodit kolem sochy, zatímco ji skenujete, když vytváříte kotvu. Pokud je vaše kotva v rohu místnosti, je tu jen jeden směr, ze které se k ní přiblížíte. Při vytváření této kotvy, můžete skenovat právě z tohoto pohledu.

## <a name="multiple-anchors"></a>Více kotev

Osvětlení může změnit vizuální funkce, které aplikace detekuje. Kotvy vytvořené v silném přirozeném světle může být obtížné najít v umělém světle, a naopak.

Pokud máte tento problém, může pomoci vytvořit dvě kotvy. Na stejném místě vytvořte jednu kotvu za denního světla a druhou v umělém světle. Aplikace pak může dotaz ovat pro obě kotvy. Když je umístěna některá z těchto kotvy, aplikace bude mít pro kotvu pózu.

Podobně v prostředích, kde se vizuální prvky mění, protože se většina objektů pohybuje, může pomoci více kotev. Pokud je příliš obtížné najít kotvu z důvodu významných změn v prostředí, můžete ji nahradit novou. Můžete to provést například v maloobchodě, kde se rozvržení aktualizuje každých několik měsíců.

## <a name="targets-and-rooms"></a>Cíle a místnosti

V mnoha případech je kotva vstupním bodem prostředí vaší aplikace. Budete chtít projít tímto krokem rychle a spolehlivě, aby uživatelé mohli vstoupit do vašeho prostředí. Trávit čas o tom, jak uživatelé najdou vaše kotvy, je důležitým krokem k návrhu. Je užitečné přemýšlet o nalezení kotvy, pokud jde o dva obecné scénáře: *cíle* a *pokoje*.

### <a name="targets"></a>Cíle

V cílovém scénáři umístění kotvy je dobře známo. Například ve fiktivní aplikaci pro malování smíšené reality umístí jeden uživatel virtuální plátno na zeď. Ona pokyn ostatní uživatelé v místnosti, aby místo jejich zařízení na stejném místě na zdi najít kotvu a začít zkušenosti.

Dalším příkladem cílového scénáře může být označení v kavárně s nápisem "Hledat nabídky". Kavárna zde umístila kotvu. Jak uživatelé skenují znamení, najdou kotvu a zadají se do prostředí rozšířené reality, aby našli nabídky na kávu.

V cílovém scénáři mohou fotografie pomoci. Pokud uživatelům na svém zařízení zobrazíte fotografii zamýšleného cíle, mohou rychle zjistit, co mají skenovat v reálném světě. Můžete například pomoci uživatelům dostat se do obecné oblasti zamýšleného cíle pomocí gps. Když uživatel přijde, vaše aplikace zobrazí fotografii cíle. Uživatel se rozhlédne po prostoru, najde cíl a vyhledá kotvu.

![Obrázek kotvy s fotografií cíle na mobilním zařízení uživatele](./media/start-here-edit.png)

### <a name="rooms"></a>Prostory

Ve scénáři místnosti uživatelé vstupují do prostoru jednoduše s vědomím, že je zde někde kotva. Uživatelé skenují prostor pomocí svého zařízení a rychle vyhledávají kotvu.

Toto prostředí obvykle vyžaduje, abyste vytvořili dobře kurátorské kotvy, jak je popsáno v různých perspektivách zobrazení. Pokud jste při vytváření kotvy naskenovali místnost z mnoha perspektiv, uživatelé mohou skenovat téměř kdekoli, když se ji pokusí najít.

![Obrázek toho, jak může uživatel prohledat místnost a najít kotvu](./media/scan-room.png)

V podstatě strávíte více času skenováním místa při vytváření kotvy, aby později uživatelé mohli rychle skenovat a vyhledat kotvu. Při vytváření zkušeností budete muset zvážit tento důležitý kompromis.

Příklad aplikace pro malování smíšené reality, o které jsme diskutovali dříve, nefunguje dobře jako scénář místnosti. Zde uživatel, který umístí kotvu, chce, aby se k prostředí rychle připojili ostatní. Uživatelé nechtějí čekat na spuštění prostředí, dokud nebude místnost dobře naskenována. Vzhledem k tomu, že všichni uživatelé přesně vědí, kam nasměrovat své zařízení k vyhledání kotev, tento příklad funguje lépe jako cílový scénář.

## <a name="anchor-location"></a>Umístění ukotvení

Vizuální sledovací systémy spoléhají na vizuální funkce v prostředí. Čím více vizuálních funkcí, které skenování obsahuje, tím vyšší je pravděpodobnost nalezení kotvy.

Postupujte podle obecných pokynů v této části k vytvoření uživatelského prostředí, které podporuje užitečné prohledává prostředí.

Za prvé, pokud uživatel nenajde kotvu během několika sekund, aplikace by měla povzbudit uživatele, aby přesunuli zařízení, aby zachytili více perspektiv. Aplikace může také povzbudit uživatele, aby se pohybovali po prostředí a hledali kotvu z více perspektiv. Čím více perspektiv funkcí zařízení vidí, tím lépe.

Pro cílové scénáře požádejte uživatele, aby se pohyboval kolem cíle a zobrazil jej z různých úhlů pohledu. Jinými slovy požádejte uživatele, aby zachytil cíl z nových perspektiv, dokud není umístěna kotva.

V případě místnostních scénářů požádejte uživatele, aby místnost pomalu prohledává. Například požádejte uživatele, aby se otočil, aby zachytil 180 stupňů nebo dokonce 360 stupňů místnosti. Nebo požádejte uživatele, aby si prohlédl místnost z nové perspektivy.

Nejsmysluplnější metodou je skenování po celé místnosti. Skenování po celé místnosti zachycuje více vizuálních prvků prostředí než například skenování blízké zdi. Skenování blízké zdi nezachytí tolik užitečných vizuálních prvků prostředí.

Při hledání kotvy není užitečné zařízení opakovaně přesouvat ze strany na stranu. To jednoduše zachycuje stejné body ze stejné perspektivy.

## <a name="experience-tests"></a>Zkušenosti testy

V tomto článku jsme diskutovali o obecných pokynech. S prostorovými kotvami píšete aplikace, které interagují s reálným světem. Z tohoto důvodu byste měli věnovat čas testování scénářů ukotvení aplikace v reálných prostředích. To platí zejména pro prostředí, které představují, kde očekáváte, že uživatelé používat aplikaci.
