---
title: Řešení potíží s chybami přidělení virtuálních počítačů Azure | Dokumenty společnosti Microsoft
description: Poradce při potížích s chybami přidělení při vytváření, restartování nebo změně velikosti virtuálního počítače v Azure
services: virtual-machines
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: cjiang
ms.openlocfilehash: b4750ad9fdfa214aa4d7b6a0355c319e7eb1d9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484396"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Řešení potíží s chybami přidělení při vytváření, restartování nebo změně velikosti virtuálních počítačů v Azure

Když vytvoříte virtuální počítač (VM), restartování zastavených (sakreditovaných) virtuálních počítačích nebo změníte velikost virtuálního počítače, Microsoft Azure přidělí výpočetní prostředky do vašeho předplatného. Neustále investujeme do další infrastruktury a funkcí, abychom zajistili, že budeme mít vždy k dispozici všechny typy virtuálních počítačů, které podporují poptávku zákazníků. Občas však může dojít k selhání přidělení prostředků z důvodu bezprecedentní růst poptávky po službách Azure v konkrétních oblastech. K tomuto problému může dojít při pokusu o vytvoření nebo spuštění virtuálních discích v oblasti, zatímco virtuální chody zobrazit následující kód chyby a zprávy:

**Kód chyby**: AllocationFailed nebo ZonalAllocationFailed

**Chybová zpráva**: Přidělení se nezdařilo. Nemáme dostatečnou kapacitu pro požadovanou velikost virtuálního počítače v této oblasti. Přečtěte si více o zvýšení\/pravděpodobnosti alokace úspěch u https: /aka.ms/allocation-guidance"

Tento článek vysvětluje příčiny některých běžných selhání přidělení a navrhuje možné opravné prostředky.

Pokud váš problém s Azure není vyřešen v tomto článku, navštivte [fóra Azure na MSDN a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Svůj problém můžete zveřejnit na těchto @AzureSupport fórech nebo na Twitteru. Žádost o podporu Azure můžete taky podat tak, že vyberete Získat podporu na webu [podpory Azure.](https://azure.microsoft.com/support/options/)

Dokud váš preferovaný typ virtuálního počítače není k dispozici ve vaší upřednostňované oblasti, doporučujeme zákazníkům, kteří se setkávají s problémy s nasazením, aby považovali pokyny v následující tabulce za dočasné řešení. 

Identifikujte scénář, který nejlépe odpovídá vašemu případu, a opakujte požadavek na přidělení pomocí odpovídajícího navrhovaného řešení, abyste zvýšili pravděpodobnost úspěchu přidělení. Případně můžete vždy opakovat později. Důvodem je, že v clusteru, oblasti nebo zóně bylo uvolněno dostatečné množství prostředků, aby bylo možné vyhovět vašemu požadavku. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Změna velikosti virtuálního počítače nebo přidání virtuálních počítačů do existující skupiny dostupnosti

### <a name="cause"></a>Příčina

Požadavek na změna velikosti virtuálního počítače nebo přidání virtuálního počítače do existující skupiny dostupnosti musí být vyzkoušen v původním clusteru, který je hostitelem existující skupiny dostupnosti. Požadovaná velikost virtuálního počítače je podporována clusterem, ale cluster nemusí mít aktuálně dostatečnou kapacitu. 

### <a name="workaround"></a>Alternativní řešení

Pokud virtuální ho může být součástí jiné skupiny dostupnosti, vytvořte virtuální hovado v jiné sadě dostupnosti (ve stejné oblasti). Tento nový virtuální počítač pak můžete přidat do stejné virtuální sítě.

Zastavit (navrátit) všechny virtuální počítače ve stejné sadě dostupnosti a potom restartovat každý z nich.
Ukončení: Klikněte na skupiny prostředků > [vaše skupina prostředků] > zdroje > [vaše sada dostupnosti] > virtuální počítače > [virtuální můle] > Zastavit.
Po zastavení všech virtuálních disekvaklů vyberte první virtuální ms a klikněte na Start.
Tento krok zajistí spuštění nového pokusu o přidělení a že lze vybrat nový cluster, který má dostatečnou kapacitu.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavených (uvolněných) virtuálních počítačů

### <a name="cause"></a>Příčina

Částečné deallocation znamená, že jste zastavili (osvošené) jeden nebo více, ale ne všechny virtuální chody v sadě dostupnosti. Při navrátit virtuální ho, přidružené prostředky jsou uvolněny. Restartování virtuálních aplikací v částečně úbytné dostupnosti je stejné jako přidání virtuálních aplikací do existující skupiny dostupnosti. Proto požadavek na přidělení musí být vyzkoušen v původním clusteru, který je hostitelem existující skupiny dostupnosti, která nemusí mít dostatečnou kapacitu.

### <a name="workaround"></a>Alternativní řešení

Zastavit (navrátit) všechny virtuální počítače ve stejné sadě dostupnosti a potom restartovat každý z nich.
Ukončení: Klikněte na skupiny prostředků > [vaše skupina prostředků] > zdroje > [vaše sada dostupnosti] > virtuální počítače > [virtuální můle] > Zastavit.
Po zastavení všech virtuálních disekvaklů vyberte první virtuální ms a klikněte na Start.
Tím zajistíte, že je spuštěn nový pokus o přidělení a že lze vybrat nový cluster, který má dostatečnou kapacitu.

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartování úplně zastavených (uvolněných) virtuálních počítačů

### <a name="cause"></a>Příčina

Úplné deallocation znamená, že jste zastavili (sakreditované) všechny virtuální chody v sadě dostupnosti. Požadavek na přidělení restartování těchto virtuálních počítačů se zaměří na všechny clustery, které podporují požadovanou velikost v rámci oblasti nebo zóny. Změňte požadavek na přidělení podle návrhů v tomto článku a opakujte požadavek, abyste zvýšili pravděpodobnost úspěchu přidělení. 

### <a name="workaround"></a>Alternativní řešení

Pokud používáte starší řady virtuálních počítačů nebo velikosti, jako je Dv1, DSv1, Av1, D15v2 nebo DS15v2, zvažte přechod na novější verze. Viz tato doporučení pro konkrétní velikosti virtuálních počítače.
Pokud nemáte možnost použít jinou velikost virtuálního počítače, zkuste nasazení do jiné oblasti v rámci stejné geografické. Další informace o dostupných velikostech virtuálních počítače v jednotlivých oblastech na adresehttps://aka.ms/azure-regions

Pokud používáte zóny dostupnosti, zkuste jinou zónu v rámci oblasti, která může mít k dispozici kapacitu pro požadovanou velikost virtuálního počítače.

Pokud je váš požadavek na přidělení velký (více než 500 jader), přečtěte si pokyny v následujících částech, kde je požadavek rozdělen na menší nasazení.

Zkuste [znovu nasadit virtuální hospo-](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows) Opětovné nasazení virtuálního virtuálního soudu přiděluje virtuální ho do nového clusteru v rámci oblasti.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Selhání přidělení u starších velikostí virtuálních počítačů (Av1, Dv1, DSv1, D15v2, DS15v2 atd.)

Při rozšiřování infrastruktury Azure nasazujeme hardware novější generace, který je navržený pro podporu nejnovějších typů virtuálních počítačů. Některé starší sériové virtuální počítačky neběží na naší infrastruktuře nejnovější generace. Z tohoto důvodu zákazníci mohou občas dojít k selhání přidělení pro tyto starší skum. Abychom se tomuto problému vyhnuli, doporučujeme zákazníkům, kteří používají starší řady virtuálních počítačů, aby zvážili přechod na ekvivalentní novější virtuální počítače podle následujících doporučení: Tyto virtuální počítače jsou optimalizované pro nejnovější hardware a umožní vám lépe využívat ceny a výkonnost. 

|Starší řady/velikost virtuálních počítače|Doporučená novější řada/velikost virtuálních počítače|Další informace|
|----------------------|----------------------------|--------------------|
|Řada Av1|[Av2-series](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Řada Dv1 nebo DSv1 (D1 až D5)|[Řada Dv3 nebo DSv3](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Řada Dv1 nebo DSv1 (D11 až D14)|[Řada Ev3 nebo ESv3](../ev3-esv3-series.md)|
|D15v2 nebo DS15v2|Pokud používáte model nasazení Resource Manager, abyste mohli využít větší velikosti virtuálních počítače, zvažte přechod na D16v3/DS16v3 nebo D32v3/DS32v3. Ty jsou navrženy tak, aby běžely na nejnovější generaci hardwaru. Pokud používáte model nasazení Resource Manager ujistíte, že instance virtuálního počítače je izolovaná na hardware vyhrazený pro jednoho zákazníka, zvažte přechod na nové izolované velikosti virtuálních zařízení, E64i_v3 nebo E64is_v3, které jsou navrženy tak, aby běžely na hardwaru nejnovější generace. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Selhání přidělení u rozsáhlých nasazení (více než 500 jader)

Snižte počet instancí požadované velikosti virtuálního počítače a opakujte operaci nasazení. Navíc pro větší nasazení můžete chtít vyhodnotit [škálovací sady virtuálních strojů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Počet instancí virtuálních počítače může automaticky zvýšit nebo snížit v reakci na poptávku nebo definovaný plán a máte větší šanci na úspěch přidělení, protože nasazení lze rozdělit do více clusterů. 

## <a name="background-information"></a>Základní informace
### <a name="how-allocation-works"></a>Jak funguje přidělení
Servery v datových centrech Azure jsou rozdělené do clusterů. Za normálních okolností se požadavek na přidělení zkouší v několika clusterech, ale je možné, že určitá omezení požadavku na přidělení donutí platformu Azure, aby tento požadavek vyzkoušela jenom v jednom clusteru. V tomto článku budeme odkazovat na to jako "připnul ke clusteru." Diagram 1 níže znázorňuje případ normální přidělení, které je pokus ve více clusterech. Diagram 2 znázorňuje případ přidělení, které je připnuté ke clusteru 2, protože to je místo, kde je hostována stávající cloudová služba CS_1 nebo dostupnost.
![Diagram přidělení](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Proč dochází k chybám přidělení
Pokud je požadavek na přidělení připnutý ke clusteru, je vyšší pravděpodobnost, že se nepodaří najít volné prostředky, protože dostupný fond prostředků je menší. Pokud je váš požadavek na přidělení připnutý ke clusteru, ale požadovaný typ prostředku není tímto clusterem podporován, váš požadavek se nezdaří, i když má cluster volné prostředky. Následující diagram 3 znázorňuje případ, kdy se nezdaří připnuté přidělení, protože jediný kandidátský cluster nemá volné prostředky. Diagram 4 znázorňuje případ, kdy se nezdaří připnuté přidělení, protože jediný kandidátský cluster nepodporuje požadovanou velikost virtuálního počítače, i když má cluster volné prostředky.

![Selhání připnutého přidělení](./media/virtual-machines-common-allocation-failure/Allocation2.png)


