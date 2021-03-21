---
title: Azure Lab Services – příručka pro správce | Microsoft Docs
description: Tato příručka pomáhá správcům, kteří vytvářejí a spravují účty testovacího prostředí pomocí Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462465"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – příručka pro správce
Správci informačních technologií (IT), kteří spravují cloudové prostředky vysoké školy, jsou obvykle odpovědni za nastavení účtu testovacího prostředí pro svou školu. Po nastavení účtu testovacího prostředí můžou správci nebo pedagogy vytvořit Labs, které jsou obsažené v rámci účtu. Tento článek poskytuje podrobný přehled o prostředcích Azure, které se týkají, a pokyny pro jejich vytváření.

![Diagram vysokého zobrazení prostředků Azure v účtu testovacího prostředí](./media/administrator-guide/high-level-view.png)

- Labs se hostují v rámci předplatného Azure, které vlastní Azure Lab Services.
- Účty testovacího prostředí, Galerie sdílených imagí a verze imagí se hostují v rámci vašeho předplatného.
- Můžete mít účet testovacího prostředí a galerii sdílených imagí ve stejné skupině prostředků. V tomto diagramu jsou v různých skupinách prostředků.

Další informace o architektuře najdete v tématu [Základy architektury Labs](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Předplatné
Vaše univerzita může mít jedno nebo několik předplatných Azure. Předplatná slouží ke správě fakturace a zabezpečení pro všechny prostředky a služby Azure, které se v ní používají, včetně testovacích účtů.

Vztah mezi účtem testovacího prostředí a jeho předplatným je důležitý z těchto důvodů:

- Fakturace se oznamuje prostřednictvím předplatného, které obsahuje účet testovacího prostředí.
- Uživatelům v předplatném Azure Active Directory (Azure AD) můžete udělit přístup k Azure Lab Services. Uživatele můžete přidat jako vlastníka nebo přispěvatele účtu testovacího prostředí nebo jako autora testovacího prostředí nebo vlastníka testovacího prostředí.

Laboratoře a jejich virtuální počítače se spravují a hostují v rámci předplatného, které vlastní Azure Lab Services.

## <a name="resource-group"></a>Skupina prostředků
Předplatné obsahuje jednu nebo více skupin prostředků. Skupiny prostředků slouží k vytváření logických seskupení prostředků Azure, které se používají společně v rámci stejného řešení.  

Když vytváříte účet testovacího prostředí, musíte nakonfigurovat skupinu prostředků, která obsahuje účet testovacího prostředí. 

Skupina prostředků se také vyžaduje při vytváření [Galerie sdílených imagí](#shared-image-gallery). Svůj účet testovacího prostředí a galerii sdílených imagí můžete umístit do stejné skupiny prostředků nebo do dvou samostatných skupin prostředků. Tento druhý postup můžete chtít provést, pokud plánujete sdílet galerii imagí napříč různými řešeními.

Když vytváříte účet testovacího prostředí, můžete automaticky vytvořit a připojit galerii sdílených imagí současně.  Tato možnost způsobí, že se účet testovacího prostředí a galerie sdílených imagí vytvoří v samostatných skupinách prostředků. Toto chování se zobrazí, až budete postupovat podle kroků popsaných v [galerii sdílených imagí v době vytváření účtu testovacího prostředí](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) . Obrázek na začátku tohoto článku používá tuto konfiguraci. 

Doporučujeme, abyste investovali čas předem, abyste naplánovali strukturu svých skupin prostředků, protože po jejím vytvoření *není* možné změnit účet testovacího prostředí nebo skupinu prostředků Galerie sdílených imagí. Pokud potřebujete změnit skupinu prostředků pro tyto prostředky, budete muset odstranit a znovu vytvořit účet testovacího prostředí nebo galerii sdílených imagí.

## <a name="lab-account"></a>Účet testovacího prostředí

Účet testovacího prostředí slouží jako kontejner pro jednu nebo více cvičení. Když začnete s Azure Lab Services, je nejčastější mít jeden účet testovacího prostředí. Vzhledem k vyšší škále využití testovacího prostředí se můžete rozhodnout vytvořit další účty testovacího prostředí později.

Následující seznam popisuje scénáře, ve kterých může být výhodné více než jeden účet testovacího prostředí:

- **Správa různých požadavků na zásady napříč laboratořemi**

    Při nastavování účtu testovacího prostředí nastavujete zásady, které se vztahují na *všechny* laboratoře v rámci účtu testovacího prostředí, například:
    - Virtuální síť Azure se sdílenými prostředky, ke kterým může testovací prostředí přistupovat. Můžete mít například sadu cvičení, které potřebují přístup ke sdílené datové sadě v rámci virtuální sítě.
    - Image virtuálních počítačů, které laboratoře můžou použít k vytváření virtuálních počítačů. Můžete mít například sadu cvičení, které potřebují přístup k imagi Azure Marketplace [Data Science VM pro Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) .

    Pokud má každá z vašich cvičení jedinečné požadavky zásad, může být výhodné vytvořit samostatné účty testovacího prostředí pro správu jednotlivých testovacích prostředí zvlášť.

- **Přiřazení samostatného rozpočtu ke každému účtu testovacího prostředí**
  
    Místo vykazování všech nákladů testovacího prostředí prostřednictvím jednoho účtu testovacího prostředí může být potřeba přesnější rozdělení rozpočtu. Můžete například vytvořit samostatné účty testovacího prostředí pro matematické oddělení školy, počítačové vědecké oddělení a tak dále, abyste mohli rozdělit rozpočet mezi jednotlivá oddělení.  Náklady na jednotlivé účty testovacího prostředí pak můžete zobrazit pomocí [Azure cost management](../cost-management-billing/cost-management-billing-overview.md).

- **Izolace pilotních cvičení z aktivních nebo produkčních cvičení**
  
    Můžou nastat případy, kdy chcete pilotní zásady pro účet testovacího prostředí, aniž by to mělo vliv na aktivní nebo produkční laboratoře. V tomto typu scénáře vytvoření samostatného testovacího účtu pro účely pilotního nasazení umožňuje izolovat změny. 

## <a name="lab"></a>Testovací prostředí

Testovací prostředí obsahuje virtuální počítače, které jsou přiřazeny k jednomu studentovi.  Obecně můžete očekávat:

- Mít jedno testovací prostředí pro každou třídu.
- Vytvořte novou sadu testovacích prostředí pro každý pololetí, čtvrtletí nebo jiný školní systém, který používáte. Pro třídy, které potřebují použít stejný obrázek, byste měli použít [galerii sdílených imagí](#shared-image-gallery). Tímto způsobem můžete opakovaně používat image napříč laboratořemi a školními obdobími.

Při určování způsobu strukturování cvičení Vezměte v úvahu následující body:

- **Všechny virtuální počítače v testovacím prostředí se nasazují se stejnou imagí, která je publikovaná.**

    V důsledku toho, pokud máte třídu, která vyžaduje, aby byly současně publikovány jiné image testovacího prostředí, je nutné pro každý obrázek vytvořit samostatné testovací prostředí.
  
- **Kvóta využití je nastavená na úrovni testovacího prostředí a platí pro všechny uživatele v rámci testovacího prostředí.**

    Pro nastavení různých kvót pro uživatele musíte vytvořit samostatné laboratoře. Po nastavení kvóty je však možné přidat další hodiny pro konkrétní uživatele.
  
- **Plán spuštění nebo vypnutí se nastaví na úrovni testovacího prostředí a vztahuje se na všechny virtuální počítače v testovacím prostředí.**

    Podobně jako u nastavení kvóty, pokud potřebujete nastavit různé plány pro uživatele, musíte pro každý plán vytvořit samostatné testovací prostředí.

Ve výchozím nastavení má každé testovací prostředí svou vlastní virtuální síť.  Pokud máte povolen partnerský vztah virtuální sítě, bude mít každé testovací zařízení vlastní podsíť s určenou virtuální sítí.

## <a name="shared-image-gallery"></a>Galerie sdílených imagí

Galerie sdílených imagí je připojená k účtu testovacího prostředí a slouží jako centrální úložiště pro ukládání imagí. Obrázek se uloží do galerie, když se Educator rozhodne exportovat z virtuálního počítače šablony testovacího prostředí. Pokaždé, když Educator provede změny v virtuálním počítači šablony a exportuje je, uloží se nové verze image a zachovají se předchozí verze.

Instruktoři můžou publikovat verzi image z Galerie sdílených imagí, když vytvoří nové testovací prostředí. I když Galerie ukládá více verzí obrazu, můžou učitelé při vytváření testovacího prostředí vybrat jenom nejnovější verzi.

Služba Galerie sdílených imagí je volitelný prostředek, který možná nebudete potřebovat hned, pokud začínáte jenom s několika cvičeními. Galerie sdílených imagí ale nabízí spoustu výhod, které jsou užitečné při horizontálním navýšení kapacity až do dalších cvičení:

- **Můžete ukládat a spravovat verze image virtuálního počítače šablony.**

    Je vhodné vytvořit vlastní image nebo provést změny (software, konfigurace a tak dále) k imagi z veřejné galerie Azure Marketplace.  Například pro pedagogy je běžné, že potřebují instalovat jiný software nebo nástroje. Místo toho, aby si studenti nemuseli tyto požadavky ručně nainstalovat na vlastní, různé verze image virtuálního počítače se dají exportovat do galerie sdílených imagí. Tyto verze imagí pak můžete použít při vytváření nových cvičení.

- **Image virtuálních počítačů šablon můžete sdílet a opakovaně používat napříč laboratořemi.**

    Můžete uložit a znovu použít obrázek, abyste ho nemuseli konfigurovat od začátku při každém vytvoření nového testovacího prostředí. Například pokud více tříd potřebuje použít stejný obrázek, můžete ho vytvořit jednou a exportovat do galerie sdílených imagí, aby bylo možné je sdílet mezi laboratořemi.

- **Dostupnost Image je zajištěna prostřednictvím automatické replikace.**

    Když uložíte obrázek z testovacího prostředí do galerie sdílených imagí, automaticky se replikuje do ostatních [oblastí ve stejné zeměpisné oblasti](https://azure.microsoft.com/global-infrastructure/regions/). Pokud dojde k výpadku oblasti, publikování image do vašeho testovacího prostředí není nijak ovlivněno, protože lze použít repliku bitové kopie z jiné oblasti.  Publikování virtuálních počítačů z více replik může také pomáhat s výkonem.

Chcete-li logicky seskupovat sdílené image, můžete provést jednu z následujících akcí:

- Vytvořte několik galerií sdílených imagí. Každý účet testovacího prostředí se může připojit jenom k jedné galerii sdílených imagí, takže tato možnost také vyžaduje vytvoření více účtů testovacího prostředí.
- Použijte jednu galerii sdílených imagí, kterou sdílí několik účtů testovacího prostředí. V takovém případě každý účet testovacího prostředí může povolit jenom image, které se vztahují na cvičení v daném účtu.

## <a name="naming"></a>Pojmenování

Po zahájení práce s Azure Lab Services doporučujeme zřídit zásady vytváření názvů pro skupiny prostředků, účty testovacího prostředí, laboratoře a galerii sdílených imagí. I když zásady vytváření názvů, které vytvoříte, budou jedinečné pro potřeby vaší organizace, v následující tabulce najdete obecné pokyny:

| Typ prostředku | Role | Navrhovaný vzor | Příklady |
| ------------- | ---- | ----------------- | -------- | 
| Skupina prostředků | Obsahuje jeden nebo víc účtů testovacího prostředí a jednu nebo víc galerií sdílených imagí. | \<organization short name\>-\<environment\>– RG<ul><li>**Zkrácený název organizace** Určuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro daný prostředek, jako je *pilotní* nebo *produkční*.</li><li>**RG** představuje *skupinu prostředků* typu prostředek.</li></ul> | contosouniversitylabs – RG<br/>contosouniversitylabs-pilot – RG<br/>contosouniversitylabs-prod-RG |
| Účet testovacího prostředí | Obsahuje jednu nebo více cvičení | \<organization short name\>-\<environment\>– La<ul><li>**Zkrácený název organizace** Určuje název organizace, kterou skupina prostředků podporuje.</li><li>**Prostředí** identifikuje prostředí pro daný prostředek, jako je *pilotní* nebo *produkční*.</li><li>**La** představuje *účet testovacího prostředí* typu prostředku.</li></ul> | contosouniversitylabs – La<br/>mathdeptlabs – La<br/>sciencedeptlabs-pilot – La<br/>sciencedeptlabs-prod-La |
| Testovací prostředí | Obsahuje jeden nebo víc virtuálních počítačů. |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Název třídy** identifikuje název třídy, kterou laboratoř podporuje.</li><li>**Časový rámec** identifikuje časový rámec, ve kterém je třída nabízena.</li>**Identifikátor Educator** identifikuje Educator, který vlastní testovací prostředí.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Galerie sdílených imagí | Obsahuje jednu nebo více verzí imagí virtuálních počítačů. | \<organization short name\>seznamů | contosouniversitylabsgallery |

Další informace o pojmenování dalších prostředků Azure najdete v tématu zásady [vytváření názvů pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Když nastavíte prostředky Azure Lab Services, budete muset zadat oblast nebo umístění datového centra, které bude hostovat prostředky. Další části popisují, jak může oblast nebo umístění ovlivnit jednotlivé prostředky, které jsou součástí nastavení testovacího prostředí.

### <a name="resource-group"></a>Skupina prostředků

Oblast určuje datacentrum, kde se ukládají informace o skupině prostředků. Prostředky Azure obsažené v rámci skupiny prostředků mohou být v jiné oblasti než její nadřazená položka.

### <a name="lab-account"></a>Účet testovacího prostředí

Umístění účtu testovacího prostředí indikuje oblast, ve které prostředek existuje.  

### <a name="lab"></a>Testovací prostředí

Umístění, v němž existuje testovací prostředí, se liší v závislosti na následujících faktorech:

  - **Účet testovacího prostředí má partnerský vztah s virtuální sítí.**
  
    Můžete [vytvořit Partnerský účet s virtuální sítí](./how-to-connect-peer-virtual-network.md) , pokud se nachází ve stejné oblasti.  Když je účet testovacího prostředí navázaný na partnerský vztah s virtuální sítí, laboratoře se automaticky vytvoří ve stejné oblasti jako účet testovacího prostředí i ve virtuální síti.

    > [!NOTE]
    > Pokud má účet testovacího prostředí partnerský vztah s virtuální sítí, nastavení nastavit **umístění testovacího prostředí pro výběr umístění testovacího** prostředí je zakázané. Další informace najdete v tématu [Povolení služby Lab Creator pro výběr umístění pro testovací prostředí](./allow-lab-creator-pick-lab-location.md).
    
  - **Žádná virtuální síť nemá partnerský vztah *a* tvůrci testovacího prostředí nemůžou vybrat umístění testovacího prostředí.**
  
    Pokud není k dispozici *žádná* virtuální síť s účtem testovacího prostředí a [tvůrci testovacího prostředí *nemůžou* vybrat umístění testovacího prostředí](./allow-lab-creator-pick-lab-location.md), laboratoře se automaticky vytvoří v oblasti, která má dostupnou kapacitu virtuálního počítače.  Konkrétně Azure Lab Services vyhledá dostupnost v [oblastech, které se nacházejí ve stejné zeměpisné oblasti jako účet testovacího prostředí](https://azure.microsoft.com/global-infrastructure/regions).

  - **Žádná virtuální síť není s partnerským vztahem *a* tvůrci testovacího prostředí můžou vybrat umístění testovacího prostředí.**
       
    Pokud není *žádná* virtuální síť sdílená s partnerským vztahem a [tvůrci testovacího *prostředí můžou* vybrat umístění testovacího prostředí](./allow-lab-creator-pick-lab-location.md), závisí na dostupné kapacitě umístění, která může vybrat tvůrce testovacího prostředí.

> [!NOTE]
> Aby bylo zajištěno, že oblast má dostatečnou kapacitu virtuálního počítače, je důležité při vytváření testovacího prostředí nejdřív vyžádat kapacitu prostřednictvím účtu testovacího prostředí.

Obecným pravidlem je nastavit oblast prostředku na jednu, která je nejblíže svým uživatelům. V případě cvičení to znamená vytvoření testovacího prostředí, které je nejblíže vašim studentům. Pro online kurzy, jejichž studenti se nacházejí po celém světě, využijte své nejlepší rozhodnutí k vytvoření testovacího prostředí, které je centrálně umístěné. Nebo můžete rozdělit třídu na více cvičení podle oblastí vašeho studenta.

## <a name="vm-sizing"></a>Změna velikosti virtuálního počítače

Když správci nebo tvůrci testovacího prostředí vytvoří testovací prostředí, můžou vybírat z nejrůznějších velikostí virtuálních počítačů v závislosti na potřebách jejich učebnosti. Pamatujte, že dostupnost výpočetní velikosti závisí na oblasti, ve které se nachází váš účet testovacího prostředí.

| Velikost | Specifikace | Řada | Navrhované použití |
| ---- | ----- | ------ | ------------- |
| Malá| <ul><li>2 &nbsp; jádra</li><li>3,5 gigabajtů (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Nejvhodnější pro příkazový řádek, otevírání webového prohlížeče, webové servery s nízkým provozem, malé až střední databáze. |
| Střední | <ul><li>4 &nbsp; jádra</li><li>7 &nbsp; GB &nbsp; paměti RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy. |
| Střední (vnořená virtualizace) | <ul><li>4 &nbsp; jádra</li><li>16 &nbsp; GB &nbsp; paměti RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy.
| Velká | <ul><li>8 &nbsp; jader</li><li>16 &nbsp; GB &nbsp; paměti RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti.  Tato velikost také podporuje vnořenou virtualizaci. |
| Velký (vnořená virtualizace) | <ul><li>8 &nbsp; jader</li><li>32 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti. |
| Malý grafický procesor (vizualizace) | <ul><li>6 &nbsp; jader</li><li>56 &nbsp; GB &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Nejvhodnější pro vzdálenou vizualizaci, streamování, hraní a kódování pomocí platforem, jako je OpenGL a DirectX. |
| Malý grafický procesor (COMPUTE) | <ul><li>6 &nbsp; jader</li><li>56 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Nejvhodnější pro aplikace náročné na počítač, jako je AI a obsáhlý Learning. |
| Střední GPU (vizualizace) | <ul><li>12 &nbsp; jader</li><li>112 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Nejvhodnější pro vzdálenou vizualizaci, streamování, hraní a kódování pomocí platforem, jako je OpenGL a DirectX. |

## <a name="manage-identity"></a>Správa identity

Díky [řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/overview.md) pro přístup k účtům testovacího prostředí a laboratořím můžete přiřadit následující role:

- **Vlastník** účtu testovacího prostředí

    Správce, který vytvoří účet testovacího prostředí, automaticky přiřadí roli vlastníka účtu testovacího prostředí. Role vlastníka může:
     - Změňte nastavení účtu testovacího prostředí.
     - Udělte ostatním správcům přístup k účtu testovacího prostředí jako vlastník nebo Přispěvatel.
     - Udělte učitelům přístup k laboratořím jako tvůrce, vlastníka nebo přispěvatele.
     - Vytvořte a spravujte všechny laboratoře v účtu testovacího prostředí.

- **Přispěvatel** účtu testovacího prostředí

    Správce, který má přiřazenou roli přispěvatele, může:
    - Změňte nastavení účtu testovacího prostředí.
    - Vytvořte a spravujte všechny laboratoře v účtu testovacího prostředí.

    Přispěvatel ale *nemůže* udělit přístup k účtům testovacího prostředí ani laboratoři jiným uživatelům.

- **Autor testovacího prostředí**

    Aby bylo možné vytvořit Labs v rámci účtu testovacího prostředí, musí být Educator členem role testovacího prostředí.  Educator, který vytváří testovací prostředí, se automaticky přidá jako vlastník testovacího prostředí. Další informace najdete v tématu [Přidání uživatele do role testovacího prostředí](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Vlastník** nebo **Přispěvatel** testovacího prostředí
  
    Educator v rámci vlastníka testovacího prostředí nebo role přispěvatele může zobrazit a změnit nastavení testovacího prostředí. Uživatel musí být také členem role čtenář účtu testovacího prostředí.

    Klíčový rozdíl mezi vlastníkem testovacího prostředí a role přispěvatele spočívá v tom, že přístup jiných uživatelů ke správě testovacího prostředí může udělit jenom vlastník. Přispěvatel *nemůže* udělit přístup ke správě testovacího prostředí jiným uživatelům.

- **Galerie sdílených imagí**

    Když připojíte galerii sdílených imagí k účtu testovacího prostředí, vlastníci, přispěvatelé a tvůrci testovacího prostředí, vlastníci testovacího prostředí a přispěvatelům testovacího prostředí automaticky udělí přístup k zobrazení a uložení obrázků v galerii.

Při přiřazování rolí pomáhá postupovat podle těchto tipů:

   - Obvykle by měly být členy role vlastníka účtu testovacího prostředí nebo skupiny Přispěvatelé. Účet testovacího prostředí může mít více než jednoho vlastníka nebo přispěvatele.
   - Chcete-li dát učitelům možnost vytvářet nové laboratoře a spravovat laboratoře, které vytvoří, je nutné přiřadit je pouze roli tvůrce testovacího prostředí.
   - Chcete-li dát učitelům možnost spravovat konkrétní cvičení, ale *ne* možnost vytvářet nové laboratoře, přiřaďte jim roli vlastníka nebo přispěvatele pro každé testovací prostředí, které bude spravovat. Můžete například chtít, aby profesor a Pomocník pro vzdělávání spoluvlastní testovací prostředí. Další informace najdete v tématu [Přidání vlastníků do testovacího prostředí](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Ceny

### <a name="azure-lab-services"></a>Azure Lab Services

Další informace o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Sdílená galerie obrázků

Pokud plánujete používat Galerie sdílených imagí pro ukládání a správu verzí imagí, musíte zvážit i ceny služby Galerie sdílených imagí. 

Vytvoření sdílené Galerie imagí a její připojení k účtu testovacího prostředí je bezplatné. Neúčtují se žádné náklady, dokud neuložíte verzi image do galerie. Ceny za použití sdílené Galerie imagí jsou obvykle poměrně zanedbatelné, ale je důležité pochopit, jak se počítá, protože nejsou zahrnuté do cen pro Azure Lab Services.  

#### <a name="storage-charges"></a>Poplatky za úložiště

Aby bylo možné ukládat verze imagí, používá Galerie sdílených imagí standardní disky spravované pevným diskem (HDD). Velikost disku spravovaného PEVNÝm diskem, který se používá, závisí na velikosti uložené verze image. Další informace o cenách najdete v tématu [ceny za spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Poplatky za replikaci a přenos sítě

Když ukládáte verzi Image pomocí virtuálního počítače šablony testovacího prostředí, Azure Lab Services je nejdřív uloží do zdrojové oblasti a pak automaticky replikuje verzi zdrojového obrazu do jedné nebo více cílových oblastí. 

Je důležité si uvědomit, že Azure Lab Services automaticky replikuje verzi zdrojového obrázku do všech [cílových oblastí v rámci geografického](https://azure.microsoft.com/global-infrastructure/regions/) umístění, ve kterém je testovací prostředí. Pokud je vaše laboratoř v oblasti USA, je verze image replikována do každé z osmi oblastí, které v USA existují.

Poplatek za výstupní síť se stane při replikaci verze image ze zdrojové oblasti do dalších cílových oblastí. Účtovaná částka vychází z velikosti verze bitové kopie, když je data na obrázku zpočátku převedena ze zdrojové oblasti.  Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Pro zákazníky s [vzdělávacími řešeními](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) se můžou účtovat poplatky za výstup. Pokud se chcete dozvědět víc, obraťte se na svého správce účtu. 

Další informace najdete v tématu "jaké programy pro přenos dat pro školní zákazníky existují a jak si je můžu kvalifikovat". v části Nejčastější dotazy stránky [programy pro vzdělávací instituce](https://azure.microsoft.com/pricing/details/bandwidth/) .

#### <a name="pricing-example"></a>Příklad ceny

Pojďme se podívat na příklad nákladů na uložení image virtuálního počítače z šablony do galerie sdílených imagí. Předpokládejme následující scénáře:

- Máte jednu vlastní image virtuálního počítače.
- Ukládáte dvě verze image.
- Vaše testovací prostředí je v USA, což má celkem osm oblastí.
- Každá verze image má velikost 32 GB; v důsledku toho cena za disk spravovaný PEVNÝm diskem je $1,54 za měsíc.

Celkové náklady za měsíc se odhadují takto:

* *Počet obrázků s počtem verzí s počtem &times; verzí &times; replik &times; řízených diskových replikami = Celková cena za měsíc*

V tomto příkladu jsou náklady následující:

* 1 vlastní image (32 GB) &times; 2 verze &times; 8 USA – oblasti &times; $1,54 = $24,64 za měsíc

> [!NOTE]
> Předchozí výpočet je příkladem pouze pro účely. Pokrývá náklady na úložiště spojené s používáním Galerie sdílených imagí a *nezahrnuje náklady* na výstup. Skutečné ceny za úložiště najdete v tématu [Managed disks Price](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Správa nákladů

Pro správce účtu testovacího prostředí je důležité spravovat náklady rutinním odstraněním nepotřebných verzí imagí z galerie. 

Neodstraňujte replikaci do konkrétních oblastí jako způsob, jak snížit náklady, i když tato možnost existuje v galerii sdílených imagí. Změny replikace můžou mít negativní vliv na schopnost Azure Lab Services publikovat virtuální počítače z imagí uložených v galerii sdílených imagí.

## <a name="next-steps"></a>Další kroky

Další informace o nastavení a správě Labs najdete v těchto tématech:

- [Průvodce nastavením účtu testovacího prostředí](account-setup-guide.md)  
- [Průvodce nastavením testovacího prostředí](setup-guide.md)  
- [Správa nákladů pro testovací prostředí](cost-management-guide.md)  
- [Použití Azure Lab Services v týmech](lab-services-within-teams-overview.md)
