---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 24d89b617c347bc9443b437c92cb034acb3e05cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
Při vytvoření virtuálního počítače (VM), restartujte zastaveném (deallocated) virtuálních počítačů nebo změnit velikost virtuálního počítače Microsoft Azure přiřadí výpočetní prostředky do vašeho předplatného. Nemůžeme se průběžně investice do další infrastrukturu a funkce a ujistěte se, že vždy obsahuje všechny typy virtuálních počítačů jsou dostupné pro podporu poptávku zákazníků. Však může někdy dojít k selhání přidělení prostředků z důvodu nebývalá rostoucích požadavků na služby Azure v určitých oblastí. Tento problém může vzniknout při pokusu o vytvoření nebo spusťte virtuální počítače v oblasti, když virtuální počítače zobrazovat následující kód chyby a zpráva:

**Kód chyby**: AllocationFailed nebo ZonalAllocationFailed

**Chybová zpráva**: "přidělování došlo k chybě. Nemáme dostatečnou kapacitu pro požadovaná velikost virtuálního počítače v této oblasti. Další informace o vylepšení pravděpodobnost úspěchu přidělení v http://aka.ms/allocation-guidance"

Tento článek vysvětluje příčiny některých běžných chyb přidělení a navrhne možné opravy.

Pokud není Azure problém řešený v tomto článku, navštivte [fóra Azure na webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Problém můžete účtovat na tyto fóra nebo na @AzureSupport na Twitteru. Navíc můžete žádost o podporu Azure souboru výběrem podporu Get na [podporu Azure](https://azure.microsoft.com/support/options/) lokality.

Dokud upřednostňovaný typ virtuálního počítače není k dispozici v upřednostňovaná oblast, doporučujeme zákazníci, kteří dojde k potížím nasazení vzít v úvahu uvedených v následující tabulce to dočasně vyřešit. 

Identifikovat scénář, který nejlépe odpovídá váš případ a poté opakujte požadavek na přidělení pomocí odpovídající navrhovaná alternativní řešení Chcete-li zvýšit pravděpodobnost úspěchu přidělení. Alternativně můžete vždy opakovat později. To je proto dostatek prostředků, může být uvolněna v clusteru, oblasti nebo zónu pro přizpůsobení vaši žádost. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Změnit velikost virtuálního počítače nebo virtuální počítače přidat do stávající sadu dostupnosti

### <a name="cause"></a>Příčina

Nastavit požadavek na změnit velikost virtuálního počítače nebo přidat virtuální počítač pro stávající sadu dostupnosti musí se v původním clusteru, který je hostitelem existující dostupnost. Požadovaná velikost virtuálního počítače se podporuje v clusteru, ale clusteru aktuálně nemá dostatečnou kapacitu. 

### <a name="workaround"></a>Alternativní řešení

Pokud virtuální počítač může být součástí jiné skupině dostupnosti, vytvoření virtuálního počítače v různých dostupnosti nastavit (ve stejné oblasti). Tento nový virtuální počítač můžete pak přidá do stejné virtuální síti.

Zastavit (zrušit přidělení) nastavte všechny virtuální počítače ve stejné dostupnosti a pak restartujte každé z nich.
Zastavit: klikněte na skupiny prostředků > [vaší skupiny prostředků] > prostředky > [vaše skupina dostupnosti] > virtuální počítače > [virtuálního počítače] > Zastavit.
Po zastavení všech virtuálních počítačů, vyberte první virtuální počítač a potom klikněte na příkaz spustit.
Tento krok zajišťuje, že se nový pokus o přidělení spustit a které do nového clusteru můžete vybrali, má dostatečnou kapacitu.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavena (deallocated) virtuálních počítačů

### <a name="cause"></a>Příčina

Částečné navrácení znamená, že jste zastavili (deallocated) jeden nebo více, ale ne všechny, virtuální počítače ve skupině dostupnosti nastavena. Pokud jste zrušit přidělení virtuálního počítače, jsou vydávány přidružené prostředky. Restartování virtuálních počítačů v sadě dostupnosti částečně deallocated je stejný jako přidávání virtuálních počítačů do stávající sadu dostupnosti. Požadavek na přidělení musí tedy se pokusila v původním clusteru, hostitelů, pro které sadu existující dostupnosti, které nemá dostatečnou kapacitu.

### <a name="workaround"></a>Alternativní řešení

Zastavit (zrušit přidělení) nastavte všechny virtuální počítače ve stejné dostupnosti a pak restartujte každé z nich.
Zastavit: klikněte na skupiny prostředků > [vaší skupiny prostředků] > prostředky > [vaše skupina dostupnosti] > virtuální počítače > [virtuálního počítače] > Zastavit.
Po zastavení všech virtuálních počítačů, vyberte první virtuální počítač a potom klikněte na příkaz spustit.
To zajistí, že spuštění nový pokus o přidělení a které do nového clusteru můžete vybrali, má dostatečnou kapacitu.

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartujte plně zastaveném (deallocated) virtuálních počítačů

### <a name="cause"></a>Příčina

Úplné navrácení znamená, že jste zastavili (nepřiřazeném) všech virtuálních počítačích v nastavení dostupnosti. Požadavek na přidělení, znovu tyto virtuální počítače se zaměří na všechny clustery, které podporují požadovaná velikost v rámci oblasti nebo zónu. Změňte vaši žádost o přidělení podle doporučení v tomto článku a opakujte žádost ke zlepšení riziko přidělení úspěch. 

### <a name="workaround"></a>Alternativní řešení

Pokud používáte starší řady virtuálního počítače nebo velikostí, jako je například Dv1 DSv1, Av1 D15v2 nebo DS15v2, zvažte přechod na novější verze. Najdete v těchto doporučení pro specifické velikosti virtuálních počítačů.
Pokud nemáte možnost použít jinou velikost virtuálního počítače, zkuste nasazení v jiné oblasti v rámci stejné geografické. Další informace o dostupných velikostí virtuálních počítačů v každé oblasti v https://aka.ms/azure-regions

Pokud používáte dostupnost zóny, zkuste jiné zóně do oblasti, která může mít dostupné kapacity pro požadovaná velikost virtuálního počítače.

Pokud je váš požadavek na přidělení velký (více než 500 jádra), viz pokyny v následující části rozdělit požadavek do menší nasazení.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Chyby v přidělení pro starší velikosti virtuálních počítačů (Av1, Dv1, DSv1, D15v2, DS15v2 atd.)

Jak jsme rozšířit infrastrukturu Azure, můžeme nasadit novější generace hardware, který je navržený pro podporu nejnovější typů virtuálních počítačů. Některé starší řady virtuálních počítačů nelze spustit v naší nejnovější generace infrastruktury. Z tohoto důvodu se mohou zákazníci příležitostně setkat chyby v přidělení pro tyto starší verze SKU. Chcete-li se tomuto problému vyhnout, doporučujeme, aby zákazníci, kteří používají starší verze řady virtuálních počítačů k zvažte přechod na ekvivalentní novější virtuální počítače na následující doporučení: Tyto virtuální počítače jsou optimalizované pro nejnovější hardwaru a umožňuje využívat lepší ceny a výkonu. 

|Starší verze řady/velikost virtuálního počítače|Doporučená novější řady/velikost virtuálního počítače|Další informace|
|----------------------|----------------------------|--------------------|
|Av1-series|[Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 nebo DSv1-series (D1 na D5)|[Dv3 nebo DSv3-series](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 nebo DSv1-series (D11 k D14)|[Ev3 nebo ESv3-series](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)|
|D15v2 nebo DS15v2|Pokud používáte model nasazení Manager theResource Pokud chcete využít větší velikosti virtuálních počítačů, zvažte přesunutí D16v3/DS16v3 nebo D32v3/DS32v3. Tyto jsou určená ke spuštění na nejnovější hardwaru generace. Pokud používáte model nasazení Resource Manager a ujistěte se, že je vaše instance virtuálních počítačů izolované hardware vyhrazený pro jednoho zákazníka, zvažte přechod na nový izolované velikosti virtuálních počítačů, E64i_v3 nebo E64is_v3, které jsou určená ke spuštění na nejnovější hardwaru generace. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Chyby v přidělení pro velká nasazení (více než 500 jader)

Snižte počet instancí požadovaná velikost virtuálního počítače a opakujte operaci nasazení. Kromě toho pro rozsáhlejší nasazení, můžete chtít vyhodnotit [sady škálování virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Počet instancí virtuálního počítače můžete automaticky zvětšit nebo zmenšit v reakci na vyžádání nebo definovaný plán, a máte větší riziko přidělení úspěšné, protože nasazení možné rozdělit do více clusterů. 

## <a name="background-information"></a>Základní informace
### <a name="how-allocation-works"></a>Jak funguje přidělení
Servery v datových centrech Azure jsou rozdělené do clusterů. Za normálních okolností se požadavek na přidělení zkouší v několika clusterech, ale je možné, že určitá omezení požadavku na přidělení donutí platformu Azure, aby tento požadavek vyzkoušela jenom v jednom clusteru. V tomto článku budeme označovat to jako "připnuli do clusteru s podporou." Obrázek 1 níže znázorňuje malá normální přidělení, které dojde k pokusu o několik clusterů. Obrázek 2 znázorňuje malá přidělení, má připnuli k clusteru 2, protože se jedná, je hostitelem existující sady CS_1 cloudové služby nebo dostupnost.
![Diagram přidělení](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Proč nastat chyby v přidělení
Při požadavku na přidělení je ukotvena do clusteru, existuje vyšší pravděpodobnost nedaří najít volné prostředky, protože fond zdrojů k dispozici je menší. Kromě toho pokud požadavek na přidělení umístěn v clusteru, ale tento cluster nepodporuje typ prostředku, který jste požadovali, vaši žádost se nezdaří i v případě, že cluster má uvolnění prostředků. 3 následující Diagram znázorňuje tento případ, kdy připojené přidělení nezdaří, protože cluster jenom candidate nemá uvolnění prostředků. Obrázek 4 znázorňuje tento případ, kde připojené přidělení nezdaří, protože pouze candidate clusteru nepodporuje požadovaná velikost virtuálního počítače, i když cluster má uvolnění prostředků.

![Došlo k chybě definovaného přidělení](./media/virtual-machines-common-allocation-failure/Allocation2.png)


