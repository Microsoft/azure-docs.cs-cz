---
title: Řešení potíží s chybami alokace virtuálních počítačů Azure | Microsoft Docs
description: Řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuálního počítače v Azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365869"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Řešení potíží s chybami přidělení při vytváření, restartování nebo změně velikosti virtuálních počítačů v Azure

Když vytvoříte virtuální počítač (VM), restartujete zastavené (navrácené) virtuální počítače nebo změníte velikost virtuálního počítače, Microsoft Azure přidělíte výpočetní prostředky vašemu předplatnému. Průběžně se investuje do další infrastruktury a funkcí, abyste měli jistotu, že všechny typy virtuálních počítačů jsou dostupné pro podporu zákaznických požadavků. Občas ale může dojít k selhání přidělení prostředků z důvodu nebývalého nárůstu poptávky pro služby Azure v určitých oblastech. K tomuto problému může dojít, když se pokusíte vytvořit nebo spustit virtuální počítače v oblasti, ale virtuální počítače zobrazí následující kód chyby a zprávu:

**Kód chyby** : AllocationFailed nebo ZonalAllocationFailed

**Chybová zpráva** : alokace se nezdařila. Pro požadovanou velikost virtuálního počítače v této oblasti nepotřebujeme dostatečnou kapacitu. Přečtěte si další informace o vylepšení pravděpodobnosti úspěchu přidělení na https: \/ /aka.MS/Allocation-Guidance.

> [!NOTE]
> Pokud řešíte řešení potíží se sadou škálování virtuálních počítačů (VMSS), proces je stejný jako standardní virtuální počítač. Chcete-li tento problém vyřešit, postupujte podle pokynů v tomto článku.
> 
>**Chybová zpráva** : alokace se nezdařila. Pokud se pokoušíte přidat nový virtuální počítač do sady škálování virtuálního počítače s jedinou skupinou umístění nebo aktualizovat nebo změnit velikost stávajícího virtuálního počítače v sadě škálování virtuálního počítače s jedinou skupinou umístění, pamatujte na to, že toto přidělení je vymezeno na jeden cluster a že je možné, že cluster nemá kapacitu. Přečtěte si další informace o vylepšení pravděpodobnosti úspěchu přidělení na adrese http: \/ /aka.MS/Allocation-Guidance.

Tento článek vysvětluje příčiny některých běžných chyb přidělení a navrhuje možné nápravy.

Pokud váš problém s Azure není v tomto článku řešen, navštivte [fóra Azure na webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Svůj problém můžete vystavit na těchto fórech nebo na @AzureSupport Twitteru. Žádost o podporu Azure si také můžete vyžádat výběrem možnosti získat podporu na webu [podpory Azure](https://azure.microsoft.com/support/options/) .

Dokud nebude upřednostňovaný typ virtuálního počítače dostupný v upřednostňované oblasti, doporučujeme zákazníkům, kteří narazí na problémy s nasazením, vzít v úvahu pokyny v následující tabulce jako dočasné alternativní řešení. 

Identifikujte scénář, který nejlépe odpovídá vašemu případu, a potom opakujte požadavek na přidělení pomocí odpovídajícího navrženého alternativního řešení a zvyšte pravděpodobnost úspěchu přidělení. Případně se můžete kdykoli pokusit později. Důvodem je to, že v clusteru, oblasti nebo zóně byly uvolněny dostatečné prostředky, které budou vyhovovat vaší žádosti. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Změna velikosti virtuálního počítače nebo přidání virtuálních počítačů do existující skupiny dostupnosti

### <a name="cause"></a>Příčina

Požadavek na změnu velikosti virtuálního počítače nebo přidání virtuálního počítače do existující skupiny dostupnosti se musí vyzkoušet v původním clusteru, který hostuje existující skupinu dostupnosti. Požadovaná velikost virtuálního počítače je clusterem podporovaná, ale cluster teď nemusí mít dostatečnou kapacitu. 

### <a name="workaround"></a>Alternativní řešení

Pokud virtuální počítač může být součástí jiné skupiny dostupnosti, vytvořte virtuální počítač v jiné skupině dostupnosti (ve stejné oblasti). Tento nový virtuální počítač se pak dá přidat do stejné virtuální sítě.

Zastavte (zrušte přidělení) všem virtuálním počítačům ve stejné skupině dostupnosti a pak každou z nich spusťte znovu.
Zastavení: klikněte na skupiny prostředků > [vaše skupina prostředků] > prostředky > [vaše skupina dostupnosti] > Virtual Machines > [váš virtuální počítač] > zastavit.
Po zastavení všech virtuálních počítačů vyberte první virtuální počítač a pak klikněte na spustit.
Tento krok zajistí, že se spustí nový pokus o přidělení a že se dá vybrat nový cluster s dostatečnou kapacitou.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavených (uvolněných) virtuálních počítačů

### <a name="cause"></a>Příčina

Částečné navracení znamená, že jste zastavili (oddělili) jednu nebo více virtuálních počítačů ve skupině dostupnosti. Po zrušení přidělení virtuálního počítače se přidávají přidružené prostředky. Restartování virtuálních počítačů v částečně oddělované skupině dostupnosti je stejné jako přidání virtuálních počítačů do existující skupiny dostupnosti. Proto musí být požadavek na přidělení proveden v původním clusteru, který je hostitelem stávající skupiny dostupnosti, která pravděpodobně nemá dostatečnou kapacitu.

### <a name="workaround"></a>Alternativní řešení

Zastavte (zrušte přidělení) všem virtuálním počítačům ve stejné skupině dostupnosti a pak každou z nich spusťte znovu.
Zastavení: klikněte na skupiny prostředků > [vaše skupina prostředků] > prostředky > [vaše skupina dostupnosti] > Virtual Machines > [váš virtuální počítač] > zastavit.
Po zastavení všech virtuálních počítačů vyberte první virtuální počítač a pak klikněte na spustit.
Tím se zajistí, že se spustí nový pokus o přidělení a že se dá vybrat nový cluster s dostatečnou kapacitou.

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartování úplně zastavených (uvolněných) virtuálních počítačů

### <a name="cause"></a>Příčina

Úplné navracení znamená, že jste zastavili (oddělili) všechny virtuální počítače ve skupině dostupnosti. Požadavek na přidělení k restartování těchto virtuálních počítačů bude mít za cíl všechny clustery, které podporují požadovanou velikost v rámci oblasti nebo zóny. Změňte svou žádost o přidělení podle návrhů v tomto článku a opakujte požadavek na zvýšení pravděpodobnosti úspěšného přidělení. 

### <a name="workaround"></a>Alternativní řešení

Pokud používáte starší řady nebo velikosti virtuálních počítačů, například Dv1, DSv1, Av1, D15v2 nebo DS15v2, zvažte přechod na novější verze. V těchto doporučeních najdete konkrétní velikosti virtuálních počítačů.
Pokud nemáte možnost použít jinou velikost virtuálního počítače, zkuste nasadit do jiné oblasti v rámci stejného geografického umístění. Další informace o dostupných velikostech virtuálních počítačů v každé oblasti https://aka.ms/azure-regions

Pokud používáte zóny dostupnosti, zkuste jinou zónu v rámci oblasti, která může mít dostupnou kapacitu pro požadovanou velikost virtuálního počítače.

Pokud je vaše žádost o přidělení velká (více než 500 jader), přečtěte si pokyny v následujících částech, kde můžete žádost rozdělit do menších nasazení.

Zkuste [virtuální počítač znovu nasadit](./redeploy-to-new-node-windows.md). Po opětovném nasazení se virtuální počítač přidělí k novému clusteru v rámci dané oblasti.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Selhání přidělení u starších velikostí virtuálních počítačů (Av1, Dv1, DSv1, D15v2, DS15v2 atd.)

Když rozšíříme infrastrukturu Azure, nasadíme novější hardware, který je navržený tak, aby podporoval nejnovější typy virtuálních počítačů. Některé virtuální počítače starší řady neběží na naší nejnovější infrastruktuře generování. Z tohoto důvodu můžou zákazníci občas zaznamenat selhání přidělení těchto starších skladových položek. Pokud se tomuto problému chcete vyhnout, doporučujeme zákazníkům, kteří používají starší verze virtuálních počítačů, zvážit přesun na ekvivalentní novější virtuální počítače na základě následujících doporučení: Tyto virtuální počítače jsou optimalizované pro nejnovější hardware a umožní vám využít lepší ceny a výkon. 

|Starší verze virtuálního počítače – řada/velikost|Doporučený novější virtuální počítač-řada/velikost|Další informace|
|----------------------|----------------------------|--------------------|
|Av1-Series|[Av2-series](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 nebo DSv1-Series (D1 až D5)|[Dv3 nebo DSv3 – Series](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 nebo DSv1-Series (D11 pro D14)|[Ev3 nebo ESv3 – Series](../ev3-esv3-series.md)|
|D15v2 nebo DS15v2|Pokud používáte model nasazení theResource Manager, abyste mohli využívat větší velikosti virtuálních počítačů, zvažte přechod na D16v3/DS16v3 nebo D32v3/DS32v3. Ty jsou navržené tak, aby běžely na nejnovějším generaci hardwaru. Pokud používáte model nasazení Správce prostředků, abyste se ujistili, že je instance virtuálního počítače izolovaná na hardwaru vyhrazeném pro jediného zákazníka, zvažte přechod na nové izolované velikosti virtuálních počítačů, E64i_v3 nebo E64is_v3, které jsou navržené tak, aby běžely na nejnovějším generaci hardwaru. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Selhání přidělení u rozsáhlých nasazení (více než 500 jader)

Snižte počet instancí požadované velikosti virtuálního počítače a pak zkuste operaci nasazení zopakovat. U větších nasazení můžete navíc chtít zhodnotit službu [Azure Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml). Počet instancí virtuálních počítačů se může automaticky zvětšit nebo zmenšit v reakci na poptávku nebo podle definovaného plánu a máte větší šanci na úspěšnost přidělení, protože nasazení je možné rozložit mezi více clusterů. 

## <a name="background-information"></a>Základní informace
### <a name="how-allocation-works"></a>Jak přidělování funguje
Servery v datových centrech Azure jsou rozdělené do clusterů. Za normálních okolností se požadavek na přidělení zkouší v několika clusterech, ale je možné, že určitá omezení požadavku na přidělení donutí platformu Azure, aby tento požadavek vyzkoušela jenom v jednom clusteru. V tomto článku odkazujeme na to, že je připnuté na cluster. Obrázek 1 níže znázorňuje případ normálního přidělení, které se pokouší o více clusterů. Diagram 2 znázorňuje případ přidělení připnuté ke clusteru 2, protože je to místo, kde je hostovaná CS_1 nebo skupina dostupnosti stávající cloudová služba.
![Diagram přidělení](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Proč dochází k chybám přidělení
Když je požadavek na přidělení připnutý ke clusteru, existuje větší šance na selhání při hledání volných prostředků, protože dostupný fond zdrojů je menší. Pokud je váš požadavek na přidělení připnutý ke clusteru, ale požadovaný typ prostředku není tímto clusterem podporován, požadavek selže, i když má cluster bezplatné prostředky. Následující diagram 3 znázorňuje případ, kdy připnuté přidělení selhává, protože jediný cluster kandidátů nemá volné prostředky. Diagram 4 znázorňuje případ, kdy připnuté přidělení selhává, protože jediný kandidát clusteru nepodporuje požadovanou velikost virtuálního počítače, i když má cluster bezplatné prostředky.

![Připnuté selhání přidělení](./media/virtual-machines-common-allocation-failure/Allocation2.png)
