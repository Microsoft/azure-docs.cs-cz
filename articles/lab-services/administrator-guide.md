---
title: Azure Lab Services – příručka pro správce | Microsoft Docs
description: Tato příručka pomáhá správcům, kteří vytvářejí a spravují účty testovacího prostředí pomocí Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: a39ee2cc57c8fc1497c3798759bd40d1ed2976e3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425299"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – příručka pro správce
Správcům informačních technologií (IT), kteří spravují cloudové prostředky vysoké školy, se obvykle zodpovídá za nastavení účtu testovacího prostředí pro svou školu. Po nastavení účtu testovacího prostředí můžou správci nebo pedagogy vytvářet učebny, které jsou obsaženy v rámci účtu testovacího prostředí. Tento článek poskytuje podrobný přehled o prostředcích Azure a pokyny pro jejich vytváření.

![Zobrazení prostředků Azure na vysoké úrovni v účtu testovacího prostředí](./media/administrator-guide/high-level-view.png)

- Laboratoře učebny se hostují v rámci předplatného Azure, které vlastní Azure Lab Services.
- Účty testovacího prostředí, Galerie sdílených imagí a verze imagí se hostují v rámci vašeho předplatného.
- Můžete mít účet testovacího prostředí a galerii sdílených imagí ve stejné skupině prostředků. V tomto diagramu jsou v různých skupinách prostředků. 

## <a name="subscription"></a>Předplatné
Vaše univerzita má jedno nebo několik předplatných Azure. Předplatné se používá ke správě fakturace a zabezpečení pro všechny služby Azure resources\services, které se v ní používají, včetně účtů testovacího prostředí.

Vztah mezi účtem testovacího prostředí a jeho předplatným je důležitý z těchto důvodů:

- Fakturace se oznamuje prostřednictvím předplatného, které obsahuje účet testovacího prostředí.
- Uživatelům v Azure Active Directory (AD) daného předplatného můžete udělit přístup k Azure Lab Services. Můžete přidat uživatele jako účet testovacího prostředí owner\contributor, učebnu pro tvorbu testovacího prostředí nebo vlastníka prostředí učebny.

Učebna Labs a jejich virtuální počítače jsou spravované a hostované v rámci předplatného, které vlastní Azure Lab Services.

## <a name="resource-group"></a>Skupina prostředků
Předplatné obsahuje jednu nebo více skupin prostředků. Skupiny prostředků slouží k vytváření logických seskupení prostředků Azure, které se používají společně v rámci stejného řešení.  

Když vytváříte účet testovacího prostředí, musíte nakonfigurovat skupinu prostředků, která obsahuje účet testovacího prostředí. 

Skupina prostředků se také vyžaduje při vytváření [Galerie sdílených imagí](#shared-image-gallery). Můžete si vybrat účet testovacího prostředí a galerii sdílených imagí do dvou samostatných skupin prostředků, které jsou typické v případě, že plánujete sdílet galerii imagí napříč různými řešeními. Nebo se můžete rozhodnout, že je vložíte do stejné skupiny prostředků.

Když vytváříte účet testovacího prostředí, můžete automaticky vytvořit a připojit galerii sdílených imagí současně.  Tato možnost způsobí, že se účet testovacího prostředí a galerie sdílených imagí vytvoří v samostatných skupinách prostředků. Toto chování se zobrazí při použití kroků popsaných v tomto kurzu: [Konfigurace Galerie sdílených imagí v době vytváření účtu testovacího prostředí](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Obrázek v horní části tohoto článku také používá tuto konfiguraci. 

Doporučujeme, abyste včas investovali do plánu struktury svých skupin prostředků, protože po jejím vytvoření *není* možné změnit skupinu prostředků Galerie sdílených imagí účtu testovacího prostředí. Pokud potřebujete změnit skupinu prostředků pro tyto prostředky, budete muset odstranit a znovu vytvořit účet testovacího prostředí and\or sdílenou image galerie.

## <a name="lab-account"></a>Účet testovacího prostředí

Účet testovacího prostředí slouží jako kontejner pro jednu nebo více cvičení v učebně. Když začnete s Azure Lab Services, je běžné mít jenom jeden účet testovacího prostředí. Když se vaše testovací využití škáluje, můžete se později rozhodnout vytvořit další účty testovacího prostředí.

Následující seznam popisuje scénáře, ve kterých může být výhodné více než jeden účet testovacího prostředí:

- **Správa různých požadavků na zásady napříč učebnami Labs**

    Při nastavování účtu testovacího prostředí můžete nastavit zásady, které se vztahují na *všechny* učebny v rámci účtu testovacího prostředí, například:
    - Virtuální síť Azure se sdílenými prostředky, ke kterým má laboratoř učebny přístup. Můžete mít například sadu učebn Labs, která potřebuje přístup ke sdílené datové sadě v rámci virtuální sítě.
    - Image virtuálního počítače (VM), které mohou laboratoře v rámci učebny použít k vytvoření virtuálních počítačů. Můžete mít například sadu učebn Labs, která potřebuje přístup k imagi [Data Science VM pro systém Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace.

    Pokud máte učebnové laboratoře, které mají od sebe jedinečné požadavky na zásady, může být výhodné vytvořit samostatné účty testovacího prostředí pro správu těchto učebných cvičení samostatně.

- **Oddělit rozpočet podle účtu testovacího prostředí**
  
    Místo vytváření sestav pro všechny náklady na učebnu učebny prostřednictvím jednoho účtu testovacího prostředí může být potřeba více jasně odděleného rozpočtu. Můžete například vytvořit účty testovacího prostředí pro oddělení IT, počítačové vědy a tak dále, abyste mohli rozdělit rozpočet mezi jednotlivá oddělení.  Náklady na jednotlivé účty testovacího prostředí pak můžete zobrazit pomocí [Azure cost management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Izolace pilotních cvičení z active\production Labs**
  
    Můžete mít případy, kdy chcete pilotní pravidla pro účet testovacího prostředí, aniž by to mělo vliv na active\production Labs. V tomto typu scénáře vytvoření samostatného testovacího účtu pro účely pilotního nasazení umožňuje izolovat změny. 

## <a name="classroom-lab"></a>Testovací prostředí pro učebnu

Prostředí učebny obsahuje virtuální počítače (VM), které jsou přiřazeny jednomu studentovi.  Obecně můžete očekávat:

- Pro každou třídu máte jednu laboratoř učebny.
- Vytvořte novou sadu učebn Labs každé pololetí (nebo pro každý časový rámec, který vaše třída nabízí). Typicky pro třídy, které mají stejný obraz, byste měli použít [galerii sdílených imagí](#shared-image-gallery) k opakovanému použití imagí v laboratořích a pololetí.

Při určování způsobu strukturování cvičení v učebně Vezměte v úvahu následující body:

- **Všechny virtuální počítače v rámci prostředí učebny se nasazují pomocí stejné image, která je publikovaná.**

    V důsledku toho, pokud máte třídu, která vyžaduje, aby byly současně publikovány jiné image testovacího prostředí, je nutné pro každou z nich vytvořit samostatné laboratoře učeben.
  
- **Kvóta využití je nastavená na úrovni testovacího prostředí a platí pro všechny uživatele v rámci testovacího prostředí.**

    Chcete-li pro uživatele nastavit různé kvóty, je nutné vytvořit samostatnou laboratoři učeben. Po nastavení kvóty je však možné přidat další hodiny ke konkrétnímu uživateli.
  
- **Plán spuštění nebo vypnutí se nastaví na úrovni testovacího prostředí a vztahuje se na všechny virtuální počítače v testovacím prostředí.**

    Podobně jako u předchozího bodu, pokud potřebujete nastavit různé plány pro uživatele, je nutné vytvořit samostatnou laboratoř pro učebnu.

Ve výchozím nastavení bude mít každé prostředí pro každou učebnu svou vlastní virtuální síť.  Pokud máte povolen partnerský vztah virtuálních sítí, bude mít každé prostředí učebny svou vlastní podsíť v partnerském vztahu k zadané virtuální síti.

## <a name="shared-image-gallery"></a>Galerie sdílených imagí

Galerie sdílených imagí je připojená k účtu testovacího prostředí a slouží jako centrální úložiště pro ukládání imagí. Obrázek se uloží do galerie, když se Educator rozhodne exportovat z virtuálního počítače šablony laboratoře pro učebnu. Pokaždé, když Educator provede změny pro virtuální počítač šablony a export, ukládají se nové verze image při zachování předchozích verzí.

Instruktoři můžou publikovat verzi image z Galerie sdílených imagí při vytváření nového testovacího prostředí pro učebnu. I když Galerie ukládá více verzí obrazu, můžou učitelé při vytváření testovacího prostředí vybrat jenom nejnovější verzi.

Galerie sdílených imagí je volitelný prostředek, který možná nebudete potřebovat hned při spuštění jenom v několika laboratořích pro učebnu. Díky galerii sdílených imagí ale máte spoustu výhod, které jsou užitečné při škálování pro více učebných cvičení:

- **Umožňuje ukládat a spravovat verze image virtuálního počítače šablony.**

    Je vhodné vytvořit vlastní image nebo provést změny (software, konfigurace atd.) z Galerie veřejné služby Marketplace.  Například je běžné, že pedagogy vyžadují, aby se nainstalovaly různé software\tooling. Místo toho, aby se studenti nemuseli ručně nainstalovat tyto požadavky na vlastní, je možné exportovat různé verze image virtuálních počítačů do galerie sdílených imagí. Tyto verze image se pak dají použít při vytváření nových prostředí pro učebnu Labs.
- **Povoluje sharing\reuse imagí šablon virtuálních počítačů napříč učebnami Labs.**

    Image můžete uložit a znovu použít, abyste nemuseli konfigurovat Image od začátku pokaždé, když vytvoříte novou laboratoř pro učebnu. Pokud je například nabízeno více tříd, které potřebují stejný obrázek, je nutné tento obrázek vytvořit pouze jednou a exportovat do galerie sdílených imagí, aby jej bylo možné sdílet v rámci učebn Labs.
- **Zajišťuje dostupnost obrazu prostřednictvím replikace**

    Když ukládáte do galerie sdílených imagí z prostředí učebny, vaše image se automaticky replikuje do jiných [oblastí ve stejné zeměpisné oblasti](https://azure.microsoft.com/global-infrastructure/regions/). V případě, že dojde k výpadku oblasti, publikování image do vašeho testovacího prostředí není ovlivněno, protože je možné použít repliku obrázků z jiné oblasti.  Publikování virtuálních počítačů z více replik může také pomáhat s výkonem.

Pro logickou skupinu sdílených imagí máte několik možností:

- Vytvořte několik galerií sdílených imagí. Každý účet testovacího prostředí se může připojit jenom k jedné galerii sdílených imagí, takže tato možnost taky vyžaduje vytvoření více účtů testovacího prostředí.
- Nebo můžete použít jednu galerii sdílených imagí, která je sdílená více účty testovacího prostředí. V takovém případě každý účet testovacího prostředí může povolit pouze image, které se vztahují na učebny, které obsahuje.

## <a name="naming"></a>Pojmenování

Po zahájení práce s Azure Lab Services doporučujeme zřídit zásady vytváření názvů pro skupiny prostředků, účty testovacího prostředí, laboratoře v učebně a galerii sdílených imagí. Zásady pojmenování, které vytvoříte, budou jedinečné pro potřeby vaší organizace. v následující tabulce jsou uvedeny obecné pokyny.

| Typ prostředku | Role | Navrhovaný vzor | Příklady |
| ------------- | ---- | ----------------- | -------- | 
| Skupina prostředků | Obsahuje jeden nebo víc účtů testovacího prostředí a jednu nebo víc galerií sdílených imagí. | \<organization short name\>-\<environment\>– RG<ul><li>**Zkrácený název organizace** Určuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro daný prostředek, jako je pilotní nebo produkční.</li><li>**RG** představuje typ prostředku: Skupina prostředků.</li></ul> | contosouniversitylabs – RG<br/>contosouniversitylabs-pilot – RG<br/>contosouniversitylabs-prod-RG |
| Účet testovacího prostředí | Obsahuje jednu nebo více cvičení | \<organization short name\>-\<environment\>– La<ul><li>**Zkrácený název organizace** Určuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro daný prostředek, jako je pilotní nebo produkční.</li><li>**La** představuje typ prostředku: účet testovacího prostředí.</li></ul> | contosouniversitylabs – La<br/>mathdeptlabs – La<br/>sciencedeptlabs-pilot – La<br/>sciencedeptlabs-prod-La |
| Testovací prostředí pro učebnu | Obsahuje jeden nebo víc virtuálních počítačů. |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Název třídy** identifikuje název třídy, kterou testovací prostředí podporuje.</li><li>**Časový rámec** identifikuje časový rámec, ve kterém je třída nabízena.</li>**Identifikátor vzdělávání** identifikuje Educator, který vlastní testovací prostředí.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Galerie sdílených imagí | Obsahuje jednu nebo více verzí imagí virtuálních počítačů. | \<organization short name\>seznamů | contosouniversitylabsgallery |

Další informace o pojmenování dalších prostředků Azure najdete v tématu zásady [vytváření názvů pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Při nastavování prostředků Azure Lab Services budete muset zadat oblast (nebo umístění) datového centra, které bude hostovat prostředek. Tady jsou další podrobnosti o tom, jak region ovlivňuje jednotlivé prostředky, které jsou součástí nastavení testovacího prostředí.

### <a name="resource-group"></a>Skupina prostředků

Oblast Určuje datové centrum, ve kterém se ukládají informace o skupině prostředků. Prostředky Azure obsažené v rámci skupiny prostředků mohou být v různých oblastech od jejich nadřazeného objektu.

### <a name="lab-account"></a>Účet testovacího prostředí

Umístění účtu testovacího prostředí indikuje oblast, ve které tento prostředek existuje.  

### <a name="classroom-lab"></a>Testovací prostředí pro učebnu

Umístění, v němž prostředí učebny existuje, se liší v závislosti na následujících faktorech:

  - **Účet testovacího prostředí je partnerský vztah k virtuální síti (VNet)**
  
    Účet testovacího prostředí může mít [partnerský vztah s virtuální](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) sítí, pokud jsou ve stejné oblasti.  V případě, že účet testovacího prostředí má partnerský vztah s virtuální sítí, učební laboratoře se automaticky vytvoří ve stejné oblasti jako účet testovacího prostředí i virtuální síť.

    > [!NOTE]
    > Když je účet testovacího prostředí v partnerském vztahu s virtuální sítí, nastavení, které **Povolí autorovi testovacího prostředí vybrat umístění testovacího prostředí** , je zakázané. Další informace o tomto nastavení najdete v článku: [Povolení programu Lab Creator pro výběr umístění pro testovací prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - * * Žádná virtuální síť nemá partnerské vztahy **_a_*_ tvůrci testovacího prostředí nemůžou vybírat testovací prostředí location_ *.
  
    Pokud není k dispozici **žádná** virtuální síť s partnerským vztahem s účtem testovacího prostředí *a* [tvůrci testovacího prostředí nemůžou vybrat umístění testovacího prostředí **not** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), laboratoře učebny se automaticky vytvoří v oblasti, která má dostupnou kapacitu virtuálního počítače.  Konkrétně Azure Lab Services vyhledá dostupnost v [oblastech, které se nacházejí ve stejné zeměpisné oblasti jako účet testovacího prostředí](https://azure.microsoft.com/global-infrastructure/regions).

  - * * Žádná virtuální síť není partnerským vztahem **_a_*_ nemohou vybrat testovacího prostředí, location_ *
       
    Když není k dispozici **žádný** partnerský vztah virtuálních sítí a [tvůrci testovacího prostředí můžou vybrat umístění testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), budou umístění, která se dají vybrat v programu Lab Creator, vycházet z dostupné kapacity.

> [!NOTE]
> Aby bylo zajištěno, že je pro oblast dostatečná kapacita virtuálních počítačů, je důležité, abyste si nejdřív vyžádali kapacitu prostřednictvím účtu testovacího prostředí nebo při vytváření testovacího prostředí.

Obecným pravidlem je nastavit oblast prostředku na jednu, která je nejblíže svým uživatelům. V rámci učebny Labs to znamená vytvořit prostředí učebny, které je nejblíže vašim studentům. V případě online kurzů, ve kterých se studenty nacházejí po celém světě, je potřeba k vytvoření prostředí učebny, které je centrálně umístěné, použít své nejlepší rozhodnutí. Nebo rozdělte třídu na více laboratoří učebny na základě oblasti vašeho studenta.

### <a name="shared-image-gallery"></a>Galerie sdílených imagí

Oblast označuje zdrojovou oblast, kde je uložena první verze obrázku před tím, než se automaticky replikuje do cílových oblastí.

## <a name="vm-sizing"></a>Změna velikosti virtuálního počítače

Když správci nebo tvůrci testovacího prostředí vytvoří testovací prostředí, můžou si vybrat z následujících velikostí virtuálních počítačů na základě potřeb své učebny. Mějte na paměti, že dostupné velikosti výpočtů závisí na oblasti, ve které se nachází váš účet testovacího prostředí:

| Velikost | Specifikace | Řada | Navrhované použití |
| ---- | ----- | ------ | ------------- |
| Malý| <ul><li>2 jádra</li><li>3,5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost je nejvhodnější pro příkazový řádek, otevírá webový prohlížeč, webové servery s nízkým provozem, malé až střední databáze. |
| Střední | <ul><li>4 jádra</li><li>7 GB PAMĚTI RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. |
| Střední (vnořená virtualizace) | <ul><li>4 jádra</li><li>16 GB PAMĚTI RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy.
| Velký | <ul><li>8 jader</li><li>16 GB PAMĚTI RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti.  Tato velikost také podporuje vnořenou virtualizaci. |
| Velký (vnořená virtualizace) | <ul><li>8 jader</li><li>32 GB paměti RAM</li></ul>  | [Standard_D8s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti. |
| Malý grafický procesor (vizualizace) | <ul><li>6 jader</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. |
| Malý grafický procesor (COMPUTE) | <ul><li>6 jader</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Tato velikost nejlépe vyhovuje aplikacím náročným na počítač, jako je umělá a obsáhlá výuka. |
| Střední GPU (vizualizace) | <ul><li>12 jader</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. |

## <a name="manage-identity"></a>Správa identity

Pomocí [řízení přístupu na základě role v Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)se dají přiřadit následující role, které umožní přístup k účtům testovacího prostředí a Labs v učebně:

- **Vlastník účtu testovacího prostředí**

    Správce, který vytvoří účet testovacího prostředí, se automaticky přidá do role **vlastníka** účtu testovacího prostředí.  Správce, kterému je přiřazena role **vlastníka** , může:
     - Změňte nastavení účtu testovacího prostředí.
     - Udělte ostatním správcům přístup k účtu testovacího prostředí jako vlastníci nebo přispěvatele.
     - Poskytněte učitelům přístup k učebným cvičením jako tvůrci, vlastníci nebo přispěvatele.
     - Vytvářejte a spravujte všechna učebná cvičení v rámci účtu testovacího prostředí.

- **Přispěvatel účtu testovacího prostředí**

    Správce, který má přiřazenou roli **přispěvatele** , může:
    - Změňte nastavení účtu testovacího prostředí.
    - Vytvářejte a spravujte všechna učebná cvičení v rámci účtu testovacího prostředí.

    Nemůžou ale *cannot* jiným uživatelům poskytnout přístup k účtům testovacího prostředí ani učebným cvičením.

- **Tvůrce testovacího prostředí učebny**

    Aby bylo možné vytvořit učebnu Labs v rámci účtu testovacího prostředí, musí být Educator členem role **Tvůrce testovacího prostředí** .  Když Educator vytvoří testovací prostředí pro učebnu, automaticky se přidá jako vlastník testovacího prostředí.  Informace o tom, jak [Přidat uživatele do role **testovacího prostředí** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role), najdete v kurzu. 

- **Owner\contributor testovacího prostředí pro učebnu**
  
    Educator může zobrazit a změnit nastavení testovacího prostředí učebny, když jsou členy role testovacího **prostředí nebo role** **přispěvatele** . musí být také členy role **čtenáře** účtu testovacího prostředí.

    Hlavním rozdílem mezi rolemi **vlastníka** a **přispěvatele** testovacího prostředí je, že Přispěvatel *nemůže* udělit přístup ke správě testovacího prostředí ostatním uživatelům.

    Kromě toho Educator *nemůže* vytvořit novou učebnu Labs, pokud nejsou zároveň členem role **Tvůrce testovacího prostředí** .

- **Galerie sdílených imagí**

    Když připojíte galerii sdílených imagí k účtu testovacího prostředí, účet testovacího prostředí owners\contributors a testovací creators\owners\contributors automaticky udělí přístup k zobrazení a uložení obrázků v galerii.

Tady je několik tipů, které vám pomůžou s přiřazováním rolí:
   - Obvykle by měly být členy role **vlastníka** nebo **přispěvatele** účtu testovacího prostředí. můžete mít více než jeden owner\contributor..
   - Aby Educator mohli vytvořit novou výukovou laboratoř a spravovat laboratoře, které vytvořili; musíte přiřadit přístup pouze k roli **Tvůrce testovacího prostředí** .
   - Aby Educator schopnost spravovat konkrétní učebny, ale *ne* možnost vytvářet nové laboratoře; měli byste přidělit přístup k roli **vlastníka** nebo **přispěvatele** pro každou z učebnních cvičení, kterou budou spravovat.  Můžete například chtít, aby profesor i Pomocník pro vzdělávání spoluvlastníící prostředí učebny.  Informace o tom, jak [Přidat uživatele jako vlastníka k prostředí učebny](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner), najdete v příručce.

## <a name="pricing"></a>Ceny

### <a name="azure-lab-services"></a>Azure Lab Services

Ceny za Azure Lab Services jsou popsané v následujícím článku: [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

Pokud plánujete použít pro ukládání a správu verzí imagí, je potřeba zvážit také ceny pro galerii sdílených imagí. 

### <a name="shared-image-gallery"></a>Galerie sdílených imagí

Vytvoření sdílené Galerie imagí a její připojení k účtu testovacího prostředí je bezplatné. Náklady se neúčtují, dokud neuložíte verzi obrazu do galerie. Ceny za použití sdílené Galerie imagí jsou obvykle poměrně zanedbatelné, ale je důležité pochopit, jak se počítají, protože nejsou zahrnuté do cen pro Azure Lab Services.  

#### <a name="storage-charges"></a>Poplatky za úložiště

K ukládání verzí imagí používá Galerie sdílených imagí standardní disky spravované HDD. Velikost disku spravovaného PEVNÝm diskem, který se používá, závisí na velikosti uložené verze image. V následujícím článku se zobrazí ceny: ceny za [spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Poplatky za replikaci a přenos sítě

Když ukládáte verzi Image pomocí virtuálního počítače šablony testovacího prostředí (VM), Azure Lab Services nejdřív uloží do zdrojové oblasti a pak automaticky replikuje verzi zdrojového obrazu do jedné nebo víc cílových oblastí. Je důležité si uvědomit, že Azure Lab Services automaticky replikuje verzi zdrojového obrázku do všech cílových [oblastí v rámci geografické](https://azure.microsoft.com/global-infrastructure/regions/) oblasti, kde se nachází vývojové prostředí učebny. Pokud je například vaše prostředí vaší učebny v oblasti USA, je verze bitové kopie replikována do každé z osmi oblastí, které existují v rámci USA.

Poplatek za výstupní síť se stane při replikaci verze image ze zdrojové oblasti do dalších cílových oblastí. Účtovaná částka vychází z velikosti verze bitové kopie, když je data na obrázku zpočátku převedena ze zdrojové oblasti.  Podrobnosti o cenách najdete v následujícím článku: [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Zákazníkům s [vzdělávacími řešeními](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) se můžou vzdát poplatky za výstupní náklady. Promluvte si svého správce účtu a získejte další informace.  Další informace najdete v části **Nejčastější dotazy** v odkazovaném dokumentu, konkrétně na otázku "jaké programy pro přenos dat existují pro akademické zákazníky a jak se dá kvalifikovat?".

#### <a name="pricing-example"></a>Příklad ceny

Pokud si chcete rekapitulace ceny popsané výše, Podívejme se na příklad uložení naší šablony virtuálního počítače do galerie sdílených imagí. Předpokládejme následující scénáře:

- Máte jednu vlastní image virtuálního počítače.
- Ukládáte dvě verze image.
- Vaše testovací prostředí je v USA, což má celkem osm oblastí.
- Každá verze image má velikost 32 GB; v důsledku toho cena za disk spravovaný PEVNÝm diskem je $1,54 za měsíc.

Celkové náklady jsou odhadované jako:

Počet imagí × počet verzí × počet replik × spravovaná cena za jednotku

V tomto příkladu jsou náklady následující:

1 vlastní image (32 GB) × 2 verze x 8 americké oblasti × $1,54 = $24,64 za měsíc

#### <a name="cost-management"></a>Správa nákladů

Pro správce účtu testovacího prostředí je důležité spravovat náklady pomocí rutiny, které z Galerie odstraňují nepotřebné verze imagí. 

Replikaci byste neměli odstraňovat do konkrétních oblastí jako způsob snížení nákladů (Tato možnost je k dispozici v galerii sdílených imagí). Změny replikace můžou mít negativní vliv na schopnost Azure Lab Service publikovat virtuální počítače z imagí uložených v galerii sdílených imagí.

## <a name="next-steps"></a>Další kroky

V tomto kurzu najdete podrobné pokyny k vytvoření účtu testovacího prostředí a testovacího prostředí: [nastavení Průvodce](tutorial-setup-lab-account.md)
