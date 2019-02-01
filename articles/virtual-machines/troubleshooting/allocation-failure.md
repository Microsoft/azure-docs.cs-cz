---
title: Řešení potíží s chybami přidělení virtuálního počítače Azure | Dokumentace Microsoftu
description: Řešení potíží s přidělováním při vytváření, restartování nebo změně velikosti virtuálního počítače v Azure
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
ms.openlocfilehash: 10c5dc5614731b247b917b68307f6a2d11663461
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510472"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Řešení potíží s přidělováním při vytváření, restartování nebo změna velikosti virtuálních počítačů v Azure

Při vytvoření virtuálního počítače (VM), restartování zastaveno (přidělení zrušeno) virtuálních počítačů nebo změně velikosti virtuálního počítače, platforma Microsoft Azure přiřadí výpočetní prostředky, které vaše předplatné. Budeme průběžně společností investuje do další infrastrukturu a funkce, abyste měli jistotu, že vždy máme všechny typy virtuálních počítačů jsou dostupné pro podporu poptávky zákazníků. Však může občas docházet k selhání přidělení prostředků z důvodu nebývalému nárůstu poptávky pro služby Azure v různých oblastech. Tento problém může vzniknout při pokusu o vytvoření nebo spuštění virtuálních počítačů v oblasti, zatímco se virtuální počítače se zobrazují následující kód chyby a zprávy:

**Kód chyby:**: AllocationFailed nebo ZonalAllocationFailed

**Chybová zpráva**: "Přidělení se nezdařilo. V této oblasti nemáme pro požadovanou velikost virtuálního počítače dostatečnou kapacitu. Další informace o pravděpodobnost, že úspěch přidělení na http://aka.ms/allocation-guidance"

Tento článek popisuje některé běžné chyby v přidělení příčiny a navrhne mezi možné nápravy.

Pokud v tomto článku se řešit potíže s Azure, přejděte [fóra Azure na webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Na těchto fór nebo položky můžete publikovat svůj problém @AzureSupport na Twitteru. Také, můžete výběrem podporu získat na souboru žádost o podporu Azure [podpory Azure](https://azure.microsoft.com/support/options/) lokality.

Dokud upřednostňovaný typ virtuálního počítače není k dispozici v preferované oblasti, doporučujeme zákazníkům, kteří dojde k problémům s nasazením vzít v úvahu pokyny v následující tabulce jako dočasné řešení. 

Identifikace scénáře, který nejlépe odpovídá vašemu případu a potom opakujte požadavek na přidělení pomocí odpovídající navrhovaná alternativní řešení ke zvýšení pravděpodobnosti rozdělení úspěch. Alternativně můžete vždy opakovat později. Je to proto dostatek prostředků byl pravděpodobně uvolněn v clusteru, oblast nebo zóně tak, aby vyhovovaly vaší žádosti. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Změna velikosti virtuálního počítače nebo přidání virtuálních počítačů do existující skupiny dostupnosti

### <a name="cause"></a>Příčina

Žádost o změně velikosti virtuálního počítače nebo přidání virtuálního počítače do existující skupiny dostupnosti musí se v původním clusteru, který je hostitelem existující skupině dostupnosti nastavena. Požadovaná velikost virtuálního počítače se podporuje v clusteru, ale cluster aktuálně nemá dostatečnou kapacitu. 

### <a name="workaround"></a>Alternativní řešení

Pokud virtuální počítač může být součástí do jiné skupiny dostupnosti, vytvoření virtuálního počítače v různých dostupnosti (ve stejné oblasti). Tento nový virtuální počítač je potom možné přidat do stejné virtuální síti.

Zastavit (uvolníte) všechny virtuální počítače ve stejné skupině dostupnosti nastavena a pak restartujte každé z nich.
Zastavení: Klikněte na skupiny prostředků > [vaší skupiny prostředků] > prostředky > [dostupnosti] > virtuální počítače > [váš virtuální počítač] > Zastavit.
Po zastavení všech virtuálních počítačů, vyberte první virtuální počítač a pak klikněte na spustit.
Tento krok zajistí, že je spuštěn nový pokus o přidělení a že nového clusteru lze vybrat, který má dostatečnou kapacitu.

## <a name="restart-partially-stopped-deallocated-vms"></a>Restartování částečně zastavených (uvolněných) virtuálních počítačů

### <a name="cause"></a>Příčina

Částečné zrušení přidělení znamená, že zastaveno (přidělení zrušeno) jeden nebo více, ale ne všechny virtuální počítače ve skupině dostupnosti nastavena. Pokud můžete zrušit přidělení virtuálního počítače, jsou vydávány přidružené prostředky. Restartování virtuálních počítačů do částečně uvolněno dostupnosti je stejné jako přidávání virtuálních počítačů do stávající sady dostupnosti. Proto musí být požadavek na přidělení pokusili v původním clusteru, hostitelích, které existující skupiny dostupnosti, které nemusí mít dostatečnou kapacitu.

### <a name="workaround"></a>Alternativní řešení

Zastavit (uvolníte) všechny virtuální počítače ve stejné skupině dostupnosti nastavena a pak restartujte každé z nich.
Zastavení: Klikněte na skupiny prostředků > [vaší skupiny prostředků] > prostředky > [dostupnosti] > virtuální počítače > [váš virtuální počítač] > Zastavit.
Po zastavení všech virtuálních počítačů, vyberte první virtuální počítač a pak klikněte na spustit.
Tím zajistíte, že je spuštěn nový pokus o přidělení a že nového clusteru lze vybrat, který má dostatečnou kapacitu.

## <a name="restart-fully-stopped-deallocated-vms"></a>Restartování úplně zastavených (uvolněných) virtuálních počítačů

### <a name="cause"></a>Příčina

Úplné zrušení přidělení znamená, že jste zastavili (přidělení zrušeno) všech virtuálních počítačů ve skupině dostupnosti. Požadavek na přidělení k restartování těchto virtuálních počítačů se zaměří na všechny clustery, které podporují požadovaná velikost v oblastech nebo zónách. Změňte váš požadavek na přidělení podle doporučení v tomto článku a opakujte žádost o zvýšit šanci na úspěch přidělení. 

### <a name="workaround"></a>Alternativní řešení

Pokud používáte starší řadu virtuálních počítačů nebo velikostí, jako je například Dv1, DSv1, Av1, D15v2 nebo DS15v2, zvažte přechod na novější verze. Podívejte se na tato doporučení pro určité velikosti virtuálních počítačů.
Pokud nemáte možnost použít jinou velikost virtuálního počítače, zkuste nasazení provést do jiné oblasti v rámci stejné zeměpisné oblasti. Další informace o dostupných velikostech virtuálních počítačů v jednotlivých oblastech najdete na https://aka.ms/azure-regions

Pokud používáte zóny dostupnosti, zkuste jiné zóny v rámci oblasti, která může mít k dispozici kapacitu pro požadovanou velikost virtuálního počítače.

Pokud je váš požadavek na přidělení velký (více než 500 jádra), najdete v následujících částech k rozdělení požadavek do menších nasazení.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Selhání přidělení pro starší velikosti virtuálních počítačů (Av1, Dv1, DSv1, D15v2, DS15v2 atd.)

S rozšiřováním infrastrukturu Azure, můžeme nasadit hardwaru novější generace, která má účelem je podporovat nejnovější typy virtuálních počítačů. Některé starší virtuální počítače řady nejdou spustit na naší infrastruktuře nejnovější generace. Z tohoto důvodu zákazníkům občas setkat s přidělováním pro těchto zastaralých skladových položek. K tomuto problému vyhnout, doporučujeme zákazníkům, kteří používají starší verzi řady virtuálních počítačů do zvažte přesunutí na ekvivalentní novější virtuální počítače za následující doporučení: Tyto virtuální počítače jsou optimalizované pro nejnovější hardware a umožňuje využívat lepší ceny a výkonu. 

|Starší verze řady/velikost virtuálního počítače|Doporučené novější řady/velikost virtuálního počítače|Další informace|
|----------------------|----------------------------|--------------------|
|Av1-series|[Av2-series](../windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 nebo DSv1-series (D1 až D5)|[Dv3 nebo DSv3-series](../windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 nebo DSv1-series (D11 až D14)|[Ev3 nebo ESv3-series](../windows/sizes-memory.md#ev3-series)|
|D15v2 nebo DS15v2|Pokud používáte model nasazení Správce theResource abyste mohli využívat větší velikosti virtuálních počítačů, zvažte přesunutí D16v3/DS16v3 nebo D32v3/DS32v3. Ty jsou navržené ke spuštění na nejnovější generace hardwaru. Pokud používáte model nasazení Resource Manageru zajistit, aby že vaše instance virtuálního počítače je izolovaná na hardware vyhrazený pro jediného zákazníka, zvažte přesunutí na nový izolovaný velikosti virtuálních počítačů, E64i_v3 nebo E64is_v3, které jsou navrženy pro spouštění na nejnovější generace hardwaru. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Selhání přidělení pro velká nasazení (více než 500 jader)

Snížit počet instancí požadovanou velikost virtuálního počítače a pak zkuste operaci nasazení. Kromě toho pro rozsáhlejší nasazení, můžete chtít vyhodnotit [škálovací sady virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Počet instancí virtuálních počítačů můžete automaticky zvýšit nebo snížit v reakci na požadavky nebo definovaný plán, a budete mít větší pravděpodobnost úspěchu přidělení, protože nasazení lze distribuovat mezi více clusterů. 

## <a name="background-information"></a>Základní informace
### <a name="how-allocation-works"></a>Princip přidělování
Servery v datových centrech Azure jsou rozdělené do clusterů. Za normálních okolností se požadavek na přidělení zkouší v několika clusterech, ale je možné, že určitá omezení požadavku na přidělení donutí platformu Azure, aby tento požadavek vyzkoušela jenom v jednom clusteru. V tomto článku budeme odkazovat na tuto jako "připnout do clusteru." Diagram 1 níže ukazuje případ normální rozdělení, dojde k pokusu o více clusterů. Obrázek 2 znázorňuje velikost písmen přidělení, která má připnuté na clusteru 2, protože se jedná, kde se hostuje existující cloudové služby CS_1 nebo dostupnost sady.
![Diagram přidělení](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Proč dochází k chybám přidělení
Pokud požadavek na přidělení je připnutá na cluster, je vyšší riziko selhání Získejte zdarma zdroje informací, protože fond k dispozici zdrojů je menší. Kromě toho pokud požadavek na přidělení je připnutá na cluster, ale tento cluster nepodporuje typ prostředku, který jste požadovali, vaši žádost se nezdaří i v případě, že cluster má volné prostředky. Následující Diagram 3 ilustruje tento případ, ve kterém se připojené přidělení nezdaří, protože pouze Release candidate clusteru nemá žádné volné prostředky. Obrázek 4 ukazuje případ, kde připojené přidělení se nezdaří, protože pouze Release candidate cluster nepodporuje požadovanou velikost virtuálního počítače, i v případě, že cluster má volné prostředky.

![Došlo k chybě přidělení připnutých](./media/virtual-machines-common-allocation-failure/Allocation2.png)


