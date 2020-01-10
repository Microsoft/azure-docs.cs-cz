---
title: Azure Lab Services – příručka pro správce | Microsoft Docs
description: Tato příručka pomáhá správcům, kteří vytvářejí a spravují účty testovacího prostředí pomocí Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771730"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – příručka pro správce
Správcům informačních technologií (IT), kteří spravují cloudové prostředky organizace, se obvykle zodpovídá za nastavení účtu testovacího prostředí pro svou organizaci. Správci nebo pedagogy vytvářejí učebn Labs v účtu testovacího prostředí. Tento článek poskytuje podrobný přehled o prostředcích Azure a pokyny pro jejich vytváření.

![Zobrazení prostředků Azure na vysoké úrovni v účtu testovacího prostředí](../media/administrator-guide/high-level-view.png)

- Laboratoře učebny se hostují v rámci předplatného Azure, které vlastní Azure Lab Services
- Účty testovacího prostředí, Galerie sdílených imagí a verze imagí se hostují v rámci vašeho předplatného.
- Můžete mít účet testovacího prostředí a galerii imagí horizontálních oddílů ve stejné skupině prostředků. V tomto diagramu jsou v různých skupinách prostředků. 

## <a name="subscription"></a>Předplatné
Vaše organizace má jedno nebo několik předplatných Azure. Předplatné se používá ke správě fakturace a zabezpečení pro všechny služby Azure resources\services, které se v ní používají, včetně účtů testovacího prostředí.

Vztah mezi účtem testovacího prostředí a jeho předplatným je důležitý z těchto důvodů:

- Fakturace se oznamuje prostřednictvím předplatného, které obsahuje účet testovacího prostředí.
- Uživatelům v tenantovi Azure Active Directory (AD), který je přidružený k předplatnému, můžete dát přístup k Azure Lab Services. Uživatele můžete přidat buď jako účet testovacího prostředí owner\contributor, nebo jako tvůrce laboratoře učebny.

Učebna Labs a jejich virtuální počítače jsou spravovány výhradně za vás. Aby byly specifické, jsou hostovány v rámci vyhrazeného předplatného, které vlastní Azure Lab Services.

## <a name="resource-group"></a>Skupina prostředků
Předplatné obsahuje jednu nebo více skupin prostředků. Skupiny prostředků slouží k vytváření logických seskupení prostředků Azure, které se používají společně v rámci stejného řešení.  

Když vytváříte účet testovacího prostředí, musíte nakonfigurovat skupinu prostředků, která obsahuje účet testovacího prostředí. 

Skupina prostředků se také vyžaduje při vytváření [Galerie sdílených imagí](#shared-image-gallery). Můžete si vybrat účet testovacího prostředí a galerii sdílených imagí do dvou samostatných skupin prostředků, které jsou typické v případě, že plánujete sdílet galerii imagí napříč různými řešeními. Nebo se můžete rozhodnout, že je vložíte do stejné skupiny prostředků.

Když vytváříte účet testovacího prostředí a automaticky vytváříte a připojujete galerii sdílených imagí, účet testovacího prostředí a galerie sdílených imagí se ve výchozím nastavení vytvoří v samostatných skupinách prostředků. Toto chování se zobrazí při použití kroků popsaných v tomto kurzu: [Konfigurace Galerie sdílených imagí v době vytváření účtu testovacího prostředí](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Obrázek v horní části tohoto článku také používá tuto konfiguraci. 

Doporučujeme vám ještě dlouho předem naplánovat strukturu svých skupin prostředků, protože po jejím vytvoření není možné změnit skupinu prostředků v galerii sdílených imagí účtu testovacího prostředí. Pokud potřebujete změnit skupinu prostředků pro tyto prostředky, budete muset odstranit a znovu vytvořit účet testovacího prostředí and\or sdílenou image galerie.

## <a name="lab-account"></a>Účet testovacího prostředí
Účet testovacího prostředí slouží jako kontejner pro jednu nebo více cvičení v učebně. Když začnete s Azure Lab Services, je běžné mít jenom jeden účet testovacího prostředí. Když se vaše testovací využití škáluje, můžete se později rozhodnout vytvořit další účty testovacího prostředí.

Následující seznam popisuje scénáře, ve kterých může být výhodné více než jeden účet testovacího prostředí:

- **Správa různých požadavků na zásady napříč učebnami Labs** 

    Při nastavování účtu testovacího prostředí můžete nastavit zásady, které se vztahují na všechny učebny v rámci účtu testovacího prostředí, například:
    - Virtuální síť Azure se sdílenými prostředky, ke kterým má laboratoř učebny přístup. Můžete mít například sadu učebn Labs, která potřebuje přístup ke sdílené datové sadě v rámci virtuální sítě.
    - Image virtuálního počítače (VM), které mohou laboratoře v rámci učebny použít k vytvoření virtuálních počítačů. Můžete mít například sadu učebn Labs, která potřebuje přístup k imagi [Data Science VM pro systém Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace.  

    Pokud máte učebnové laboratoře, které mají od sebe jedinečné požadavky na zásady, může být výhodné vytvořit samostatné účty testovacího prostředí pro správu těchto učebných cvičení samostatně.
- **Omezení přístupu k konkrétním cvičením testovacích prostředí pro konkrétní učebny**  

    Když se uživatel přidá jako tvůrce testovacího prostředí, přiřadí se všem učebným cvičením v rámci účtu testovacího prostředí, včetně laboratorních laboratoří, které vytvořili jiní tvůrci testovacích prostředí. Pokud chcete, aby tvůrci testovacích prostředí mohli spravovat konkrétní cvičení, můžete vytvořit samostatné účty testovacího prostředí pro omezení rozsahu jejich přístupu. Můžete například vytvořit samostatný účet testovacího prostředí pro každé oddělení v rámci univerzity. Například: jeden testovací účet pro vědecké oddělení a druhý pro oddělení matematiky atd.   
- **Oddělit rozpočet podle účtu testovacího prostředí**

    Místo toho, aby se všechny náklady na prostředí učebny nahlásily pro jeden účet testovacího prostředí, možná budete muset mít jasně oddělený rozpočet. V příkladu v předchozí odrážce byste mohli vytvořit účet testovacího prostředí pro každé středisko University, aby se rozpočet rozdělují odpovídajícím způsobem. Pomocí Azure Cost Management můžete zobrazit náklady na jednotlivé účty testovacího prostředí.
- **Izolace pilotních cvičení z aktivní laboratoře**

    Můžete mít případy, kdy chcete pilotní zásady změnit na účet testovacího prostředí, aniž by to mělo dopad na aktivní cvičení. V tomto typu scénáře vytvoření samostatného testovacího účtu pro účely pilotního nasazení umožňuje izolovat změny. 

## <a name="classroom-lab"></a>Testovací prostředí pro učebnu
Prostředí učebny obsahuje jeden nebo více virtuálních počítačů, které jsou přiřazeny konkrétnímu studentovi. Obecně můžete očekávat:

- Pro každou třídu máte jednu laboratoř učebny.
- Vytvořte novou sadu učebn Labs každé pololetí (nebo pro každý časový rámec, který vaše třída nabízí). Typicky pro třídy, které mají stejný image, byste měli použít [galerii sdílených imagí](#shared-image-gallery) ke sdílení imagí napříč laboratořemi a pololetí.

Při určování způsobu strukturování cvičení v učebně Vezměte v úvahu následující body:

- **Všechny virtuální počítače v rámci prostředí učebny se nasazují pomocí stejné image, která je publikovaná**. V důsledku toho, pokud máte třídu, která vyžaduje, aby byly současně publikovány jiné image testovacího prostředí, je nutné pro každou z nich vytvořit samostatné laboratoře učeben.
- **Kvóta využití je nastavená na úrovni testovacího prostředí a platí pro všechny uživatele v rámci testovacího prostředí**. Můžete mít například sadu pedagogů, kteří potřebují přístup k virtuálním počítačům třídy pro přípravu na výuku, ale učitelé vyžadují jenom 10 hodin, zatímco studenti zaregistrované ve třídě vyžadují kvótu 40. Chcete-li pro uživatele nastavit různé kvóty, je nutné vytvořit samostatnou laboratoři učeben. Po nastavení kvóty je však možné přidat další hodiny ke konkrétnímu uživateli.
- **Plán spuštění nebo vypnutí se nastaví na úrovni testovacího prostředí a vztahuje se na všechny virtuální počítače v testovacím prostředí**. Podobně jako u předchozího bodu, pokud potřebujete nastavit různé plány pro uživatele, je nutné vytvořit samostatnou laboratoř pro učebnu. 

## <a name="shared-image-gallery"></a>Galerie sdílených imagí
Galerie sdílených imagí je připojená k účtu testovacího prostředí a slouží jako centrální úložiště pro ukládání imagí. Obrázek je uložený v galerii, když se Educator rozhodne uložit z virtuálního počítače šablony laboratoře pro učebnu (VM). Pokaždé, když Educator provede změny na virtuálním počítači šablony a uloží, budou se při zachování předchozích verzí ukládat nové verze image.

Instruktoři můžou publikovat verzi image z Galerie sdílených imagí při vytváření nového testovacího prostředí pro učebnu. I když galerie může ukládat více verzí obrazu, můžou učitelé při vytváření testovacího prostředí vybrat jenom nejnovější verzi.

Galerie sdílených imagí je volitelný prostředek, který možná nebudete potřebovat hned při spuštění jenom v několika laboratořích pro učebnu. Díky galerii sdílených imagí ale máte spoustu výhod, které jsou užitečné při škálování pro více učebných cvičení:

- **Umožňuje ukládat a spravovat verze image virtuálního počítače šablony**.

    To je užitečné při vytváření vlastní image nebo provádění změn (softwaru, konfigurace atd.) k imagi z veřejné galerie Marketplace.  Například je běžné, že pedagogy vyžadují, aby se nainstalovaly různé software\tooling. Místo toho, aby si studenti museli tyto požadavky ručně nainstalovat na vlastní, různé verze image virtuálního počítače se dají uložit do galerie sdílených imagí. Tyto verze image se pak dají použít při vytváření nových prostředí pro učebnu Labs.
- **Povoluje sharing\reuse imagí šablon virtuálních počítačů napříč učebn Labs**.

    Nemusíte přitom při každém vytvoření nového testovacího prostředí pro učebnu konfigurovat Image od nuly. Pokud je například nabízeno více tříd, které potřebují stejný obrázek, je nutné tento obrázek vytvořit pouze jednou a Uložit do galerie sdílených imagí, aby bylo možné je sdílet v rámci Učebn Labs.
- **Zajišťuje dostupnost obrazu prostřednictvím replikace**.

    Při ukládání do galerie sdílených imagí z prostředí učebny se vaše image automaticky replikuje do jiných oblastí ve stejné zeměpisné oblasti. Pokud dojde k výpadku oblasti, publikování virtuálního počítače šablony v laboratoři učebnosti není ovlivněno pomocí repliky imagí v jiných oblastech. Kromě toho pomáhá s výkonem ve scénářích publikování s více virtuálními počítači tím, že rozšíří na používání různých replik.

Pro logickou skupinu sdílených imagí máte několik možností:

- Vytvořte několik galerií sdílených imagí. Každý účet testovacího prostředí se může připojit jenom k jedné galerii sdílených imagí, takže tato možnost taky vyžaduje vytvoření více účtů testovacího prostředí.
- Nebo můžete použít jednu galerii sdílených imagí, která je sdílená více účty testovacího prostředí. V takovém případě každý účet testovacího prostředí může povolit pouze image, které se vztahují na učebny, které obsahuje.

## <a name="naming"></a>Pojmenování
Po zahájení práce s Azure Lab Services doporučujeme zřídit zásady vytváření názvů pro skupiny prostředků, účty testovacího prostředí, laboratoře v učebně a galerii sdílených imagí. Zásady pojmenování, které vytvoříte, budou jedinečné pro potřeby vaší organizace. v následující tabulce jsou uvedeny obecné pokyny.

| Typ prostředku | Role | Navrhovaný vzor | Příklady |
| ------------- | ---- | ----------------- | -------- | 
| Skupina prostředků | Obsahuje jeden nebo víc účtů testovacího prostředí a jednu nebo víc galerií sdílených imagí. | \<zkrácený název organizace\>-prostředí \<\>– RG<ul><li>**Zkrácený název organizace** Určuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro daný prostředek, jako je například test nebo produkce.</li><li>**RG** představuje typ prostředku: Skupina prostředků.</li></ul> | contosouniversitylabs – RG<br/>contosouniversitylabs-test-RG<br/>contosouniversitylabs-prod-RG |
| Účet testovacího prostředí | Obsahuje jednu nebo více cvičení | \<zkrácený název organizace\>-prostředí \<\>-La<ul><li>**Zkrácený název organizace** Určuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro daný prostředek, jako je například test nebo produkce.</li><li>**La** představuje typ prostředku: účet testovacího prostředí.</li></ul> | contosouniversitylabs – La<br/>mathdeptlabs – La<br/>sciencedeptlabs-test-La<br/>sciencedeptlabs-prod-La |
| Testovací prostředí pro učebnu | Obsahuje jeden nebo víc virtuálních počítačů. |název třídy \<\>-\<časový rámec\>-\<identifikátor Educator\><ul><li>**Název třídy** identifikuje název třídy, kterou testovací prostředí podporuje.</li><li>**Časový rámec** identifikuje časový rámec, ve kterém je třída nabízena.</li>**Identifikátor vzdělávání** identifikuje Educator, který vlastní testovací prostředí.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Galerie sdílených imagí | Obsahuje jednu nebo více verzí imagí virtuálních počítačů. | \>Galerie \<krátký název organizace | contosouniversitylabsgallery |

Další informace o pojmenování dalších prostředků Azure najdete v tématu zásady [vytváření názvů pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Oblasti nebo umístění
Při nastavování prostředků Azure Lab Services je nutné zadat oblast nebo umístění datového centra, které bude hostovat daný prostředek. Tady jsou další podrobnosti o tom, jak region\location ovlivňuje každý z následujících prostředků, které se používají v nasazení testovacích služeb:

- **Skupina prostředků**

    Oblast Určuje datové centrum, ve kterém se ukládají informace o skupině prostředků. Prostředky Azure obsažené v rámci skupiny prostředků mohou být v různých oblastech od jejich nadřazeného objektu.
- **Účet testovacího prostředí nebo prostředí učebny**

    Umístění účtu testovacího prostředí indikuje oblast pro tento prostředek. Učebna Labs vytvořená v účtu testovacího prostředí se dá nasadit do jakékoli oblasti v rámci stejné geografické oblasti. Konkrétní oblast, do které jsou nasazené virtuální počítače testovacího prostředí, se automaticky vybere v závislosti na kapacitě dostupné v oblasti v daném čase.  
    Pokud správce umožňuje tvůrcům testovacího prostředí zvolit umístění testovacího prostředí, budou umístění, která jsou k dispozici pro výběr, založena na dostupné místní kapacitě při vytváření testovacího prostředí.

    Umístění testovacího prostředí učebny také určuje, které výpočetní velikosti virtuálních počítačů jsou k dispozici pro výběr. Určité velikosti výpočetních prostředků jsou k dispozici pouze v rámci určitých umístění.
- **Galerie sdílených imagí**

    Oblast označuje zdrojovou oblast, kde je uložena první verze obrázku před tím, než se automaticky replikuje do cílových oblastí.
    
Obecným pravidlem je nastavit region\location prostředku na jeden, který je nejblíže svým uživatelům. V rámci učebny Labs to znamená, že je vytvoření prostředí učebny co nejblíže vašim studentům. V případě online kurzů, ve kterých se studenty nacházejí po celém světě, je potřeba k vytvoření prostředí učebny, které je centrálně umístěné, použít své nejlepší rozhodnutí. Nebo rozdělte třídu na více laboratoří učebny na základě oblasti vašeho studenta.

## <a name="vm-sizing"></a>Změna velikosti virtuálního počítače
Když správci nebo tvůrci testovacího prostředí vytvoří testovací prostředí, můžou si vybrat z následujících velikostí virtuálních počítačů na základě potřeb své učebny. Mějte na paměti, že dostupné velikosti výpočtů závisí na oblasti, ve které se nachází váš účet testovacího prostředí:

| Velikost | Specifikace | Navrhované použití |
| ---- | ----- | ------------- |
| Malé| <ul><li>2 jádra</li><li>3,5 GB RAM</li></ul> | Tato velikost je nejvhodnější pro příkazový řádek, otevírá webový prohlížeč, webové servery s nízkým provozem, malé až střední databáze. |
| Střední | <ul><li>4 jádra</li><li>7 GB RAM</li></ul> | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. |
| Střední (vnořená virtualizace) | <ul><li>4 jádra</li><li>16 GB PAMĚTI RAM</li></ul> | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy.  Tato velikost také podporuje vnořenou virtualizaci. |
| Velké | <ul><li>8 jader</li><li>32 GB RAM</li></ul> | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti.  Tato velikost také podporuje vnořenou virtualizaci. |
| Malý grafický procesor (vizualizace) | <ul><li>6 jader</li><li>56 GB RAM</li> | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. |
| Malý grafický procesor (COMPUTE) | <ul><li>6 jader</li><li>56 GB RAM</li></ul> |Tato velikost nejlépe vyhovuje aplikacím náročným na počítač, jako je umělá a obsáhlá výuka. |
| Střední GPU (vizualizace) | <ul><li>12 jader</li><li>112 GB RAM</li></ul> | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. |

## <a name="manage-identity"></a>Správa identity
Existují dva typy rolí, které může mít správce účtu testovacího prostředí:

- **Vlastník**

    Správce, kterému je přiřazena role **vlastníka** , má úplný přístup k účtu testovacího prostředí, včetně práva udělovat ostatním uživatelům přístup k účtu testovacího prostředí a přidávat autory testovacího prostředí. Správce, který ve výchozím nastavení vytvoří účet testovacího prostředí, se přidá jako vlastník.
- **Přispěvatel**

    Správce, který má přiřazenou roli přispěvatele, může změnit nastavení účtu testovacího prostředí, ale nemůže udělit přístup jiným uživatelům. ani můžou přidat tvůrci testovacích prostředí.

Když připojíte galerii sdílených imagí k účtu testovacího prostředí, bude se automaticky předávat správcům i tvůrcům testovacího prostředí, aby mohli zobrazit a uložit obrázky v galerii. 

## <a name="pricing"></a>Ceny

### <a name="azure-lab-services"></a>Azure Lab Services
Ceny za Azure Lab Services jsou popsané v následujícím článku: [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

Pokud plánujete použít pro ukládání a správu verzí imagí, je potřeba zvážit také ceny pro galerii sdílených imagí. 

### <a name="shared-image-gallery"></a>Galerie sdílených imagí
Vytvoření sdílené Galerie imagí a její připojení k účtu testovacího prostředí je bezplatné. Náklady se neúčtují, dokud neuložíte verzi obrazu do galerie. Ceny za použití sdílené Galerie imagí jsou obvykle poměrně zanedbatelné, ale je důležité pochopit, jak se počítají, protože nejsou zahrnuté do cen pro Azure Lab Services.  

### <a name="storage-charges"></a>Poplatky za úložiště
K ukládání verzí imagí používá Galerie sdílených imagí standardní disky spravované HDD. Velikost disku spravovaného PEVNÝm diskem, který se používá, závisí na velikosti uložené verze image. V následujícím článku se zobrazí ceny: ceny za [spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Poplatky za replikaci a přenos sítě
Když ukládáte verzi Image pomocí virtuálního počítače šablony testovacího prostředí (VM), služba testovacího prostředí je nejdřív uloží do zdrojové oblasti a pak automaticky replikuje verzi zdrojového obrazu do jedné nebo více cílových oblastí. Je důležité si uvědomit, že Azure Lab Services automaticky replikuje verzi zdrojového obrázku do všech cílových oblastí v rámci geografického umístění, kde se nachází vývojové prostředí učebny. Pokud je například vaše prostředí vaší učebny v zeměpisné poloze USA, je verze image replikována do všech osmi oblastí, které existují v USA.

Poplatek za výstupní síť se stane při replikaci verze image ze zdrojové oblasti do dalších cílových oblastí. Účtovaná částka vychází z velikosti verze bitové kopie, když je data na obrázku zpočátku převedena ze zdrojové oblasti.  Podrobnosti o cenách najdete v následujícím článku: [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Zákazníkům s [vzdělávacími řešeními](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) se můžou vzdát poplatky za výstupní náklady. Promluvte si svého správce účtu a získejte další informace.  Další informace najdete v části **Nejčastější dotazy** v odkazovaném dokumentu, konkrétně na otázku "jaké programy pro přenos dat existují pro akademické zákazníky a jak se dá kvalifikovat?").

### <a name="pricing-example"></a>Příklad cen
Pokud si chcete rekapitulace ceny popsané výše, Podívejme se na příklad uložení naší šablony virtuálního počítače do galerie sdílených imagí. Předpokládejme následující scénáře:

- Máte jednu vlastní image virtuálního počítače.
- Ukládáte dvě verze image.
- Vaše testovací prostředí je v USA, což má celkem osm oblastí.
- Každá verze image má velikost 32 GB; v důsledku toho cena za disk spravovaný PEVNÝm diskem je $1,54 za měsíc.

Celkové náklady jsou odhadované jako:

Počet imagí × počet verzí × počet replik × spravovaná cena za jednotku

V tomto příkladu jsou náklady následující:

1 vlastní image (32 GB) × 2 verze x 8 americké oblasti × $1,54 = $24,64 za měsíc

### <a name="cost-management"></a>Správa nákladů
Pro správce účtu testovacího prostředí je důležité spravovat náklady pomocí rutiny, které z Galerie odstraňují nepotřebné verze imagí. 

Replikaci byste neměli odstraňovat do konkrétních oblastí jako způsob snížení nákladů (Tato možnost je k dispozici v galerii sdílených imagí). Změny replikace můžou mít negativní vliv na schopnost Azure Lab Service publikovat virtuální počítače z imagí uložených v galerii sdílených imagí.

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytvoření účtu testovacího prostředí a testovacího prostředí najdete v tomto kurzu: vytvoření [účtu testovacího prostředí](tutorial-setup-lab-account.md)
