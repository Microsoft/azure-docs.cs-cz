---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772949"
---
Pokud při provádění jakýchkoli operací se sdílenými galeriemi imagí, definicemi imagí a verzemi imagí narazíte na problémy, spusťte příkaz, který selhal, znovu v režimu ladění. Režim ladění se aktivuje předáním **přepínače -debug** s CLI a přepínačem **-Debug** s prostředím PowerShell. Po naprávě chyby postupujte podle tohoto dokumentu a vyřešte je.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nejde vytvořit sdílenou galerii imagí

Možné příčiny:

*Název galerie je neplatný.*

Povolené znaky pro název galerie jsou velká nebo malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky. Změňte název galerie a akci opakujte. 

*Název galerie není v rámci předplatného jedinečný.*

Vyberte jiný název galerie a akci opakujte.


## <a name="unable-to-create-an-image-definition"></a>Nejde vytvořit definici image 

Možné příčiny:

*název definice obrázku je neplatný.*

Povolené znaky pro definici obrazu jsou velká nebo malá písmena, číslice, tečky, pomlčky a tečky. Změňte název definice obrázku a akci opakujte.

*Povinné vlastnosti pro vytvoření definice bitové kopie nejsou naplněny.*

Vlastnosti, jako je název, vydavatel, nabídka, sku a typ operačního serveru jsou povinné. Ověřte, zda jsou všechny vlastnosti předávány.

Ujistěte se, že **OSType**, Linux nebo Windows, definice bitové kopie je stejný jako zdroj spravované image, které používáte k vytvoření verze bitové kopie. 


## <a name="unable-to-create-an-image-version"></a>Nejde vytvořit verzi image 

Možné příčiny:

*Název verze obrázku je neplatný.*

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32bitové celé číslo. Formát: *MajorVersion.minorVersion.Patch*. Změňte název verze obrázku a akci opakujte.

*Zdrojová spravovaná bitová kopie, ze které je vytvářena verze bitové kopie, nebyla nalezena.* 

Zkontrolujte, zda zdrojový obraz existuje a je ve stejné oblasti jako verze obrázku.

*Spravované image není hotovo zřizování.*

Ujistěte se, že stav zřizování zdrojové spravované bitové kopie je **succeeded**.

*Seznam cílových oblastí neobsahuje zdrojovou oblast.*

Seznam cílové oblasti musí obsahovat zdrojovou oblast verze bitové kopie. Ujistěte se, že jste zahrnuli zdrojovou oblast do seznamu cílových oblastí, do kterých chcete Azure replikovat verzi vaší image.

*Replikace do všech cílových oblastí, které nebyly dokončeny.*

Pomocí příznaku **--expand ReplicationStatus** zkontrolujte, zda byla replikace do všech zadaných cílových oblastí dokončena. Pokud ne, počkejte až 6 hodin na dokončení úlohy. Pokud se nezdaří, spusťte příkaz znovu vytvořit a replikovat verzi bitové kopie. Pokud existuje mnoho cílových oblastí verze bitové kopie je replikována do, zvažte provedení replikace ve fázích.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nejde vytvořit virtuální počítač nebo škálovací sadu 

Možné příčiny:

*Uživatel, který se pokouší vytvořit škálovací sadu virtuálního počítače nebo virtuálního počítače, nemá přístup pro čtení k verzi bitové kopie.*

Obraťte se na vlastníka předplatného a požádejte je, aby poskytl přístup pro čtení k verzi bitové kopie nebo nadřazeným prostředkům (jako je galerie sdílených obrázků nebo definice obrázku) prostřednictvím [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Verze obrázku nebyla nalezena.*

Ověřte, že oblast, ve které se pokoušíte vytvořit virtuální počítač nebo měřítko virtuálního počítače, je zahrnuta v seznamu cílových oblastí verze bitové kopie. Pokud oblast je již v seznamu cílových oblastí, ověřte, zda byla dokončena úloha replikace. Pomocí příznaku **-ReplicationStatus** můžete zkontrolovat, zda byla replikace do všech zadaných cílových oblastí dokončena. 

*Vytvoření škálovací sady virtuálního počítače nebo virtuálního počítače trvá dlouhou dobu.*

Ověřte, zda **typ ostype** verze bitové kopie, ze které se pokoušíte vytvořit škálovací sadu virtuálního počítače nebo virtuálního počítače, má stejný **typ OSType** zdrojové spravované bitové kopie, který jste použili k vytvoření verze bitové kopie. 

## <a name="unable-to-share-resources"></a>Nejde sdílet prostředky

Sdílení sdílené galerie obrázků, definice bitové kopie a prostředků verze bitové kopie napříč předplatnými je povoleno pomocí [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>Replikace je pomalá

Pomocí příznaku **--expand ReplicationStatus** zkontrolujte, zda byla replikace do všech zadaných cílových oblastí dokončena. Pokud ne, počkejte až 6 hodin na dokončení úlohy. Pokud se nezdaří, spusťte příkaz znovu vytvořit a replikovat verzi bitové kopie. Pokud existuje mnoho cílových oblastí verze bitové kopie je replikována do, zvažte provedení replikace ve fázích.

## <a name="azure-limits-and-quotas"></a>Omezení a kvóty Azure 

[Limity a kvóty Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) platí pro všechny sdílené image galerie, definice bitové kopie a prostředků verze bitové kopie. Ujistěte se, že jste v mezích pro vaše předplatná. 



