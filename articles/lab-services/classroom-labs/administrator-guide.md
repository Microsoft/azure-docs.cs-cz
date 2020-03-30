---
title: Azure Lab Services – průvodce správcem | Dokumenty společnosti Microsoft
description: Tato příručka pomáhá správcům, kteří vytvářejí a spravují účty testovacího prostředí pomocí služby Azure Lab Services.
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
ms.openlocfilehash: 8608aaab7bb8b6d10e67f27678c17f20a6c243da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370848"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – průvodce správcem
Správci informačních technologií (IT), kteří spravují cloudové prostředky univerzity, jsou obvykle zodpovědní za nastavení účtu testovacího prostředí pro svou školu. Po nastavení účtu testovacího prostředí správci nebo pedagogové vytvoří testovací prostředí ve třídě, která jsou obsažena v účtu testovacího prostředí. Tento článek poskytuje podrobný přehled o azure prostředků zapojených a pokyny pro jejich vytvoření.

![Zobrazení prostředků Azure na vysoké úrovni v účtu testovacího prostředí](../media/administrator-guide/high-level-view.png)

- Testovací prostředí učebny se hostují v rámci předplatného Azure vlastněného službou Azure Lab Services.
- Účty testovacího prostředí, galerie sdílených obrázků a verze obrázků jsou hostovány v rámci vašeho předplatného.
- Svůj účet testovacího prostředí a sdílenou galerii obrázků můžete mít ve stejné skupině prostředků. V tomto diagramu jsou v různých skupinách prostředků. 

## <a name="subscription"></a>Předplatné
Vaše univerzita má jedno nebo více předplatných Azure. Předplatné se používá ke správě fakturace a zabezpečení pro všechny prostředky Azure\služby, které se v něm používají, včetně účtů testovacího prostředí.

Vztah mezi účtem testovacího prostředí a jeho předplatným je důležitý, protože:

- Fakturace se hlásí prostřednictvím předplatného, které obsahuje účet testovacího prostředí.
- Uživatelům v tenantovi Azure Active Directory (AD) můžete uživatelům v tenantovi Azure Active Directory (AD) poskytnout přístup ke službám Azure Lab Services. Můžete přidat uživatele jako vlastníka účtu testovacího prostředí\přispěvatele, tvůrce testovacího prostředí učebny nebo vlastníka testovacího prostředí učebny.

Učebna labs a jejich virtuální počítače (VM) jsou spravované a hostované pro vás v rámci předplatného vlastněného Služby Azure Lab Services.

## <a name="resource-group"></a>Skupina prostředků
Předplatné obsahuje jednu nebo více skupin prostředků. Skupiny prostředků se používají k vytvoření logické seskupení prostředků Azure, které se používají společně v rámci stejného řešení.  

Při vytváření účtu testovacího prostředí je nutné nakonfigurovat skupinu prostředků, která obsahuje účet testovacího prostředí. 

Skupina prostředků je vyžadována také při vytváření [sdílené galerie obrázků](#shared-image-gallery). Můžete se rozhodnout umístit svůj účet testovacího prostředí a sdílenou galerii obrázků do dvou samostatných skupin prostředků, což je typické, pokud plánujete sdílet galerii obrázků mezi různými řešeními. Nebo můžete zvolit jejich zařazení do stejné skupiny prostředků.

Když vytvoříte účet testovacího prostředí, můžete automaticky vytvořit a připojit sdílenou galerii obrázků současně.  Výsledkem této možnosti je vytvoření účtu testovacího prostředí a galerie sdílených obrázků v samostatných skupinách prostředků. Toto chování se zobrazí při použití kroků popsaných v tomto kurzu: [Konfigurace galerie sdílených obrázků v době vytvoření účtu testovacího prostředí](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Obrázek v horní části tohoto článku také používá tuto konfiguraci. 

Doporučujeme předem investovat čas na plánování struktury skupin prostředků, protože není *možné* změnit skupinu prostředků účtu testovacího prostředí nebo sdílené galerie obrázků, jakmile je vytvořena. Pokud potřebujete změnit skupinu prostředků pro tyto prostředky, budete muset odstranit a znovu vytvořit účet testovacího prostředí a\nebo sdílenou galerii obrázků.

## <a name="lab-account"></a>Laboratorní účet
Laboratorní účet slouží jako kontejner pro jednu nebo více laboratoří ve třídě. Když začínáme se službami Azure Lab Services, je běžné mít jenom jeden účet testovacího prostředí. Jako vaše testovací využití se škáluje, můžete později vytvořit další účty testovacího prostředí.

Následující seznam upozorňuje na scénáře, kde může být přínosné více než jeden účet testovacího prostředí:

- **Správa různých požadavků na zásady napříč učebnami** 
    
    Při nastavovat účet testovacího prostředí nastavíte zásady, které se vztahují na *všechna* testovací prostředí v rámci testovacího účtu, například:
    - Virtuální síť Azure se sdílenými prostředky, ke kterým má testovací prostředí učebny přístup. Můžete mít například sadu testovacích prostředí ve třídě, které potřebují přístup ke sdílené sadě dat v rámci virtuální sítě.
    - Image virtuálního počítače (VM), které můžete použít v testovacím prostředí učebny k vytváření virtuálních počítačů. Můžete mít například sadu testovacích prostředí ve třídě, které potřebují přístup k virtuálnímu [počítači datové vědy pro](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) image Linux Marketplace. 
    
    Pokud máte učebny laboratoře, které mají jedinečné požadavky na zásady od sebe, může být prospěšné vytvořit samostatné laboratorní účty pro správu těchto učebny laboratoře samostatně.

- **Samostatný rozpočet podle účtu testovacího prostředí**
  
    Místo vykazování všech nákladů na testovací prostředí ve třídě prostřednictvím jednoho účtu testovacího prostředí můžete potřebovat jasněji oddělený rozpočet. Můžete například vytvořit laboratorní účty pro katedru matematiky univerzity, oddělení informatiky a tak dále, abyste rozdělili rozpočet mezi odděleními.  Náklady na jednotlivé testovací prostředí pak můžete zobrazit pomocí [azure cost managementu](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).
    
- **Izolovat pilotní laboratoře z aktivních\produkčních laboratoří**
  
    Můžete mít případy, kdy chcete pilotní změny zásad pro účet testovacího prostředí bez potenciálního dopadu na aktivní\produkční laboratoře. V tomto typu scénáře vytvoření samostatného účtu testovacího prostředí pro účely pilotního řízení umožňuje izolovat změny. 

## <a name="classroom-lab"></a>Učebna laboratoř
Testovací prostředí učebny obsahuje virtuální počítače ,, které jsou přiřazeny jednomu studentovi. Obecně lze očekávat:

- Mít jednu učebnu laboratoř pro každou třídu.
- Vytvořte novou sadu učebních laboratoří každý semestr (nebo pro každý časový rámec, který je nabízen vaší třídě). Obvykle pro třídy, které mají stejné potřeby obrázku, měli byste použít [sdílenou galerii obrázků](#shared-image-gallery) k opakovanému použití obrázků v testovacích prostředích a semestrech.

Při určování struktury učebních laboratoří zvažte následující body:

- **Všechny virtuální virtuální mích v učebně lab jsou nasazené se stejným obrázkem, který je publikovaný**

    V důsledku toho pokud máte třídu, která vyžaduje, aby byly současně publikovány různé bitové kopie testovacího prostředí, musí být pro každou z nich vytvořena samostatná testovací prostředí třídy.
  
- **Kvóta využití je nastavena na úrovni testovacího prostředí a vztahuje se na všechny uživatele v rámci testovacího prostředí.**
    
    Chcete-li nastavit různé kvóty pro uživatele, musíte vytvořit samostatná testovací prostředí učebny. Po nastavení kvóty je však možné přidat další hodiny konkrétnímu uživateli.
  
- **Plán spuštění nebo vypnutí je nastavený na úrovni testovacího prostředí a platí pro všechny virtuální počítače v testovacím prostředí.**

    Podobně jako předchozí bod, pokud potřebujete nastavit různé plány pro uživatele, musíte vytvořit samostatné učebny. 

## <a name="shared-image-gallery"></a>Sdílená galerie obrázků
Sdílená galerie obrázků je připojena k účtu testovacího prostředí a slouží jako centrální úložiště pro ukládání obrázků. Obrázek se uloží do galerie, když se pedagog rozhodne exportovat z virtuálního počítače šablony učebny v laboratoři. Pokaždé, když pedagog provede změny virtuálního počítače šablony a exportuje, nové verze image se uloží při zachování předchozích verzí.

Instruktoři mohou publikovat verzi obrázku ze sdílené galerie obrázků při vytváření nové laboratoře pro učebny. Přestože galerie může uložit více verzí obrázku, pedagogové mohou vybrat pouze nejnovější verzi během vytváření testovacího prostředí.

Sdílená galerie obrázků je volitelný prostředek, který nemusíte potřebovat okamžitě, když začínáte pouze s několika učebnami. Použití sdílené galerie obrázků však přináší mnoho výhod, které jsou užitečné při škálování na další laboratoře ve třídě:

- **Umožňuje ukládat a spravovat verze bitové kopie virtuálního počítače šablony.**

    Je užitečné vytvořit vlastní obrázek nebo provést změny (software, konfigurace a tak dále) na obrázek z veřejné galerie Marketplace.  Je například běžné, že pedagogové vyžadují instalaci jiného softwaru a nástrojů. Místo toho, aby studenti museli ručně nainstalovat tyto předpoklady sami, lze do sdílené galerie obrázků exportovat různé verze image virtuálního počítače šablony. Tyto verze obrázků pak lze použít při vytváření nových učebních laboratoří.
- **Umožňuje sdílení\opakované použití bitových kopií virtuálních počítačů šablony v laboratořích učebny.**

    Bitovou kopii můžete uložit a znovu použít, abyste ji nemuseli konfigurovat od začátku při každém vytvoření nové ho testovacího prostředí ve třídě. Pokud je například nabízeno více tříd, které potřebují stejný obrázek, je třeba tento obrázek vytvořit pouze jednou a exportovat do sdílené galerie obrázků, aby mohl být sdílen mezi laboratořemi učebny.
- **Zajišťuje dostupnost bitové kopie prostřednictvím replikace.**

    Když uložíte do sdílené galerie obrázků z učebny, obrázek se automaticky replikuje do jiných [oblastí v rámci stejné zeměpisné oblasti](https://azure.microsoft.com/global-infrastructure/regions/). V případě výpadku oblasti není publikování obrázku do testovacího prostředí učebny ovlivněno, protože lze použít repliku obrázku z jiné oblasti.  Publikování virtuálních počítačů z více replik může také pomoci s výkonem.

Chcete-li logicky seskupit sdílené obrázky, máte několik možností:

- Vytvořte více sdílených galerií obrázků. Každý účet testovacího prostředí se může připojit pouze k jedné galerii sdílených obrázků, takže tato možnost bude také vyžadovat vytvoření více účtů testovacího prostředí.
- Nebo můžete použít jednu sdílenou galerii obrázků, která je sdílená více účty testovacího prostředí. V takovém případě může každý účet testovacího prostředí povolit pouze ty obrázky, které se vztahují k laboratořím učebny, které obsahuje.

## <a name="naming"></a>Pojmenování
Když začnete pracovat se službami Azure Lab Services, doporučujeme vytvořit konvence pojmenování pro skupiny prostředků, účty testovacího prostředí, testovací prostředí v učebnách a sdílenou galerii bitové kopie. Zatímco konvence pojmenování, které vytvoříte, budou jedinečné pro potřeby vaší organizace, v následující tabulce jsou uvedeny obecné pokyny.

| Typ prostředku | Role | Navrhovaný vzor | Příklady |
| ------------- | ---- | ----------------- | -------- | 
| Skupina prostředků | Obsahuje jeden nebo více laboratorních účtů a jednu nebo více sdílených galerií obrázků. | \<prostředí s\>-\<\>krátkým názvem organizace -rg<ul><li>**Zkrácený název organizace** označuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro zdroj, například Pilot nebo Production</li><li>**Rg** je zkratka pro typ prostředku: skupina prostředků.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Laboratorní účet | Obsahuje jednu nebo více laboratoří. | \<organizace krátký\>-\<\>název prostředí -la<ul><li>**Zkrácený název organizace** označuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro zdroj, například Pilot nebo Production</li><li>**La** je zkratka pro typ prostředku: laboratorní účet.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Učebna laboratoř | Obsahuje jeden nebo více virtuálních mích. |\<identifikátor\>-\<pedagoga\>-\<časového rámce názvu třídy\><ul><li>**Název třídy** identifikuje název třídy, kterou testovací prostředí podporuje.</li><li>**Časový rámec** určuje časový rámec, ve kterém je třída nabízena.</li>**Identifikátor vzdělání** identifikuje pedagoga, který laboratoř vlastní.</li></ul> | CS1234-podzim2019-johndoe<br/>CS1234-jaro2019-johndoe | 
| Sdílená galerie obrázků | Obsahuje jednu nebo více verzí bitové kopie virtuálního aplikace. | \<organizace krátký\>název galerie | contosouniversitylabsgallery |

Další informace o pojmenování jiných prostředků Azure najdete [v tématu Konvence pojmenování pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Oblasti\Umístění

Při nastavování prostředků Azure Lab Services, budete muset poskytnout oblast (nebo umístění) datového centra, které bude hostitelem prostředku. Tady jsou další podrobnosti o tom, jak oblast ovlivňuje jednotlivé zdroje, které se podílejí na nastavení testovacího prostředí.

### <a name="resource-group"></a>Skupina prostředků

Oblast určuje datové centrum, ve kterém jsou uloženy informace o skupině prostředků. Prostředky Azure obsažené ve skupině prostředků může být v různých oblastech od jejich nadřazené.

### <a name="lab-account"></a>Laboratorní účet

Umístění účtu testovacího prostředí označuje oblast, ve které tento prostředek existuje.  

### <a name="classroom-lab"></a>Učebna laboratoř
    
Umístění, ve které existuje učebna, se liší v závislosti na následujících faktorech:

  - **Účet testovacího prostředí je partnerský vztah k virtuální síti (VNet)**
  
    Účet testovacího prostředí může být [partnerský vztah s virtuální sítí,](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) pokud jsou ve stejné oblasti.  Když je účet testovacího prostředí peered s virtuální sítí, učebny laboratoře se automaticky vytvoří ve stejné oblasti jako účet testovacího prostředí a virtuální sítě.

    > [!NOTE]
    > Když je účet testovacího prostředí peered s virtuální sítí, nastavení **Povolit tvůrce testovacího prostředí vybrat umístění testovacího prostředí** je zakázáno. Další informace o tomto nastavení naleznete v článku: [Povolit autorovi testovacího prostředí vybrat umístění pro testovací prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Žádná virtuální síť není peered ***a*** tvůrci testovacího prostředí není povoleno vybrat umístění testovacího prostředí**
  
    Pokud neexistuje **žádný** partnerský vztah virtuální sítě s účtem testovacího prostředí *a* [tvůrci testovacího prostředí **nemají** povoleno vybrat umístění testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), testovací prostředí učebny se automaticky vytvoří v oblasti, která má dostupnou kapacitu virtuálních účtů.  Konkrétně Azure Lab Services hledá dostupnost v [oblastech, které jsou ve stejné zeměpisné oblasti jako účet testovacího prostředí](https://azure.microsoft.com/global-infrastructure/regions).

  - **Žádná virtuální síť není peered ***a*** tvůrci testovacího prostředí mohou vybrat umístění testovacího prostředí**
       
    Pokud neexistuje **žádný** partnerský vztah virtuální sítě a [tvůrci testovacího prostředí mohou vybrat umístění testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), umístění, která může být vybrána tvůrcem testovacího prostředí, jsou založená na dostupné kapacitě.

Obecným pravidlem je nastavit oblast prostředku na oblast, která je nejblíže uživatelům. Pro učebny laboratoře, to znamená vytvoření učebny laboratoř nejblíže k vašim studentům. Pro online kurzy, kde se studenti nacházejí po celém světě, musíte použít svůj nejlepší úsudek k vytvoření učební laboratoře, která je centrálně umístěná. Nebo rozdělte třídu do několika učebních laboratoří na základě oblasti vašich studentů.

### <a name="shared-image-gallery"></a>Sdílená galerie obrázků

Oblast označuje zdrojovou oblast, kde je uložena první verze bitové kopie před její automatickou replikací do cílových oblastí.

## <a name="vm-sizing"></a>Změna velikosti virtuálního mísa
Když správci nebo tvůrci testovacího prostředí vytvoří testovací prostředí učebny, můžou si vybrat z následujících velikostí virtuálních her na základě potřeb své učebny. Nezapomeňte, že výpočetní velikosti, které jsou k dispozici, závisí na oblasti, ve které se nachází váš účet testovacího prostředí:

| Velikost | Specifikace | Řada | Doporučené použití |
| ---- | ----- | ------ | ------------- |
| Krátkodobé používání| <ul><li>2 jádra</li><li>3,5 GB paměti RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost je nejvhodnější pro příkazový řádek, otevření webového prohlížeče, webové servery s nízkým provozem, malé až střední databáze. |
| Střednědobé používání | <ul><li>4 Jádra</li><li>7 GB PAMĚTI RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy. |
| Střední (vnořená virtualizace) | <ul><li>4 Jádra</li><li>16 GB paměti RAM</li></ul> | [Standard_DC4s_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy.  Tato velikost také podporuje vnořenou virtualizaci. |
| Dlouhodobé používání | <ul><li>8 jader</li><li>32 GB paměti RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Tato velikost je nejvhodnější pro aplikace, které potřebují rychlejší procesory, lepší výkon místního disku, velké databáze, velké mezipaměti.  Tato velikost také podporuje vnořenou virtualizaci. |
| Malý GPU (vizualizace) | <ul><li>6 Jader</li><li>56 GB paměti RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Tato velikost je nejvhodnější pro vzdálenou vizualizaci, streamování, hraní her, kódování pomocí architektur, jako jsou OpenGL a DirectX. |
| Malý GPU (výpočetní) | <ul><li>6 Jader</li><li>56 GB paměti RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Tato velikost je nejvhodnější pro počítačem náročné aplikace, jako je umělá inteligence a hloubkové učení. |
| Střední GPU (vizualizace) | <ul><li>12 Jader</li><li>112 GB paměti RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Tato velikost je nejvhodnější pro vzdálenou vizualizaci, streamování, hraní her, kódování pomocí architektur, jako jsou OpenGL a DirectX. |

## <a name="manage-identity"></a>Správa identity
Pomocí [řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)lze přiřadit následující role, které poskytují přístup k testovacím účtům a testovacím prostředím učebny:

- **Vlastník účtu testovacího prostředí**

    Správce, který vytvoří účet testovacího prostředí, se automaticky přidá do role **vlastníka** účtu testovacího prostředí.  Správce, kterému je přiřazena role **Vlastník,** může:
     - Změňte nastavení účtu testovacího prostředí.
     - Poskytněte ostatním správcům přístup k účtu testovacího prostředí jako vlastníkům nebo přispěvatelům. 
     - Poskytněte pedagogům přístup k laboratořím ve třídě jako tvůrci, vlastníci nebo přispěvatelé.
     - Vytvořte a spravujte všechna testovací prostředí ve třídě v rámci účtu testovacího prostředí.

- **Přispěvatel účtu testovacího prostředí**

    Správce, kterému je přiřazena role **Přispěvatel,** může:
    - Změňte nastavení účtu testovacího prostředí.
    - Vytvořte a spravujte všechna testovací prostředí ve třídě v rámci účtu testovacího prostředí.
    
    *Nemohou* však ostatním uživatelům poskytnout přístup k testovacím účtům nebo laboratořím učebny.

- **Tvůrce učebny v laboratoři**

    Chcete-li vytvořit testovací prostředí ve třídě v rámci účtu testovacího prostředí, musí být pedagog členem role **Tvůrce testovacího prostředí.**  Když pedagog vytvoří učebnu, automaticky se přidá jako vlastník laboratoře.  Podívejte se na návod, jak [přidat uživatele do role Tvůrce **lab** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Vlastník učebny v laboratoři\přispěvatel**
  
    Pedagog může zobrazit a změnit nastavení učebny, pokud je členem role **vlastníka** testovacího prostředí nebo **přispěvatele.** musí být také členem role **čtenáře** účtu testovacího prostředí.

    Klíčovým rozdílem mezi rolemi **vlastníka** testovacího prostředí a **přispěvatele** je, že přispěvatel *nemůže* poskytnout ostatním uživatelům přístup ke správě testovacího prostředí – pouze vlastníci mohou ostatním uživatelům poskytnout přístup ke správě testovacího prostředí.
    
    Kromě toho pedagog *nemůže* vytvářet nové učebny, pokud nejsou také členem role **Lab Creator.**

- **Sdílená galerie obrázků**
    
    Když k účtu testovacího prostředí připojíte sdílenou galerii obrázků, budou vlastníci účtu testovacího prostředí\přispěvatelé a tvůrci testovacího prostředí\vlastníci\přispěvatelé automaticky mít přístup k zobrazení a uložení obrázků v galerii. 

Tady je několik tipů, které vám pomohou s přiřazením rolí:
   - Obvykle pouze správci by měli být členy role **vlastníka** účtu testovacího prostředí nebo **přispěvatele;** můžete mít více než jednoho vlastníka\přispěvatele.

   - Dát pedagogovi možnost vytvářet nová učební laboratoře a spravovat laboratoře, které vytvářejí; stačí přiřadit přístup k roli **Tvůrce testovacího prostředí.**
   
   - Chcete-li dát pedagog možnost spravovat konkrétní učebny laboratoře, ale *ne* schopnost vytvářet nové laboratoře; Měli byste přiřadit přístup k roli **Vlastník** nebo **Přispěvatel** pro každou z učebních laboratoří, které budou spravovat.  Můžete například povolit profesorovi i asistentovi učitele, aby spoluvlastnili učebnu.  Informace o tom, jak [přidat uživatele jako vlastníka do učebny,](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)naleznete v příručce .

## <a name="pricing"></a>Ceny

### <a name="azure-lab-services"></a>Azure Lab Services
Ceny za Služby Azure Lab services jsou popsané v následujícím článku: [Ceny azure lab služby](https://azure.microsoft.com/pricing/details/lab-services/).

Je také třeba zvážit ceny pro sdílenou galerii obrázků, pokud ji plánujete použít pro ukládání a správu verzí obrázků. 

### <a name="shared-image-gallery"></a>Sdílená galerie obrázků
Vytvoření sdílené galerie obrázků a její připojení k účtu testovacího prostředí je zdarma. Náklady nevznikají, dokud neuložíte verzi obrázku do galerie. Ceny za použití sdílené galerie obrázků jsou obvykle poměrně zanedbatelné, ale je důležité pochopit, jak se počítá, protože není zahrnuta v cenách za služby Azure Lab Services.  

#### <a name="storage-charges"></a>Poplatky za úložiště
Pro ukládání verzí bitových kopií používá sdílená galerie obrázků standardní disky spravované pevným diskem. Velikost disku spravovaného pevným diskem, který se používá, závisí na velikosti uložené verze bitové kopie. Ceny: [Ceny spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks/)naleznete v následujícím článku.


#### <a name="replication-and-network-egress-charges"></a>Poplatky za replikaci a odchozí přenos sítě
Když uložíte verzi image pomocí virtuálního počítače šablony učebny lab (VM), Azure Lab Services nejprve uloží do zdrojové oblasti a pak automaticky replikuje verzi zdrojové bitové kopie do jedné nebo více cílových oblastí. Je důležité si uvědomit, že Azure Lab Services automaticky replikuje verzi zdrojové image do všech [cílových oblastí v rámci geografické oblasti,](https://azure.microsoft.com/global-infrastructure/regions/) kde se nachází laboratoře učebny. Pokud je například vaše učební laboratoř v geografii USA, je verze obrázku replikována do každé z osmi oblastí, které existují v USA.

Poplatek za odchozí přenos sítě nastane, když je verze bitové kopie replikována ze zdrojové oblasti do dalších cílových oblastí. Účtovaná částka je založena na velikosti verze bitové kopie při počátečním přenosu dat bitové kopie odchozí chod ze zdrojové oblasti.  Podrobnosti o cenách naleznete v následujícím článku: [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

[Vzdělávací řešení](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) zákazníci mohou být upuštěno od placení výstupních poplatků. Další informace vám poskytne správce účtu.  Další informace naleznete v části **Nejčastější dotazy** v propojeném dokumentu, konkrétně na otázku "Jaké programy přenosu dat existují pro akademické zákazníky a jak se kvalifikuji?".

#### <a name="pricing-example"></a>Příklad ceny
Chcete-li rekapitulovat výše popsané ceny, podívejme se na příklad uložení obrázku virtuálního počítače šablony do sdílené galerie obrázků. Předpokládejme následující scénáře:

- Máte jednu vlastní image virtuálního virtuálního virtuálního sea.
- Ukládáte dvě verze obrázku.
- Vaše laboratoř je v USA, která má celkem osm oblastí.
- Každá verze obrázku má velikost 32 GB; v důsledku toho je cena disku spravovaná hdd je $ 1.54 za měsíc.

Celkové náklady se odhadují na:

Počet bitových kopií × počet verzí × počet replik × cena spravovaného disku

V tomto příkladu jsou náklady:

1 vlastní obrázek (32 GB) x 2 verze x 8 regionů USA x $ 1.54 = $ 24.64 za měsíc

#### <a name="cost-management"></a>Správa nákladů
Pro správce účtu testovacího prostředí je důležité spravovat náklady rutinním mazáním nepotřebných verzí bitových obrázků z galerie. 

Replikaci do určitých oblastí byste neměli odstraňovat jako způsob, jak snížit náklady (tato možnost existuje ve sdílené galerii obrázků). Změny replikace může mít nepříznivý vliv na schopnost služby Azure Lab Service publikovat virtuální počítače z bitových kopií uložených v galerii sdílených bitových kopií.

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytvoření testovacího účtu a testovacího prostředí: [Průvodce nastavením](tutorial-setup-lab-account.md) najdete v kurzu.
