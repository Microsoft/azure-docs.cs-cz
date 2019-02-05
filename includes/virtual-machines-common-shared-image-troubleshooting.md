---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 0eb47c8ec470ef05f3c6ae37bdc75e5bb1043eb0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735941"
---
Pokud při provádění jakýchkoli operací se sdílenými galeriemi imagí, definicemi imagí a verzemi imagí narazíte na problémy, spusťte příkaz, který selhal, znovu v režimu ladění. Režim ladění je aktivován předáním **– ladění** přepnout pomocí rozhraní příkazového řádku a **– ladění** přepnout pomocí Powershellu. Jakmile jste vyhledali chyby, postupujte podle tohoto dokumentu pro řešení těchto chyb.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nelze vytvořit Galerie sdílené bitové kopie

Možné příčiny:

*Název galerie je neplatný.*

Povolené znaky pro název galerie jsou malá a velká písmena, číslice, tečky a tečky. Název galerie nemůže mít pomlčky v ní. Změňte název galerie a zkuste to znovu. 

*Název galerie není jedinečné v rámci vašeho předplatného.*

Vyberte jiný název galerie a zkuste to znovu.


## <a name="unable-to-create-an-image-definition"></a>Nelze vytvořit definici image 

Možné příčiny:

*Název definice bitové kopie je neplatný.*

Povolené znaky definici image jsou malá a velká písmena, číslice, tečky, pomlčky a tečky. Změnit název image definici a zkuste to znovu.

*Nejsou naplněny povinné vlastnosti pro vytvoření definici image.*

Vlastnosti, jako je název, vydavatele, nabídky, sku a typ operačního systému jsou povinné. Ověření, pokud jsou předávány všechny vlastnosti.

Ujistěte se, že **OSType**, Linux nebo Windows, definice image je, stejný jako zdroj spravované image, kterou používáte k vytvoření verze image. 


## <a name="unable-to-create-an-image-version"></a>Nelze vytvořit image verze 

Možné příčiny:

*Název verze bitové kopie je neplatný.*

Povolené znaky pro verze image jsou čísla a tečky. Čísla musí být v rozsahu 32bitového celého čísla. Formát: *MajorVersion.MinorVersion.Patch*. Změňte název verze image a zkuste to znovu.

*Nebyl nalezen zdroj spravované image, ze kterého se vytváří verze image.* 

Zkontrolujte, jestli zdrojový obraz existuje a je ve stejné oblasti jako verze image.

*Spravované image není Hotovo zřizuje.*

Ujistěte se, že je stav zřizování spravované image zdroj **Succeeded**.

*Zdrojová oblast se ještě nepodporuje.*

Chcete-li zobrazit, pokud se podporuje zamýšlený zdrojové oblasti, použijte následující tabulku:
<br>

| Vytvoření v galerii nebo "zdrojové oblasti"   | Replikovat verzi nebo "cílové oblasti" |
|----------------------------------------|-------------------------------------------|
| Západní střed USA                        | Všechny oblasti veřejném cloudu Azure            |
| Středojižní USA                       |                                           |
| Východní USA 2                              |                                           |
| Jihovýchodní Asie                         |                                           |
| Západní Evropa                            |                                           |

<br>

Seznam cílové oblasti, musí obsahovat zdrojové oblasti verze image. Ujistěte se, že jste zadali v seznamu cílové oblasti, kde má Azure k replikaci adresáře vaší verze image do zdrojové oblasti.

*Replikace na všechny cílové oblasti nebyla dokončena.*

Použití **--rozbalte stav replikace** příznak ke kontrole, pokud má dokončení replikace na všechny zadané cílové oblasti. Pokud ne, počkejte, až 6 hodin se úloha dokončí. Pokud selže, spusťte příkaz znovu a vytvořit replikaci verze image. Pokud existuje velké množství cílové oblasti, kterou verzi image je právě replikován pro, zvažte replikaci ve fázích.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nepodařilo se vytvořit virtuální počítač nebo škálovací sadu 

Možné příčiny:

*Uživatel pokusu o vytvoření škálovací sady virtuálního počítače nebo virtuální počítač nemá přístup pro čtení k verzi image.*

Obraťte se na vlastníka předplatného a požádejte ji o udělení oprávnění ke čtení na verzi image nebo nadřazený prostředky (jako je Galerie sdílené bitové kopie nebo bitové kopie definice) prostřednictvím [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*Nebyla nalezena verze image.*

Ověřte, že se pokoušíte vytvořit škálovací sadu virtuálního počítače nebo virtuálního počítače v oblasti součástí seznamu cílových oblastí verze image. Pokud je již oblast v seznamu cílové oblasti, ověřte, pokud byla dokončena úloha replikace. Můžete použít **stav replikace -** příznak ke kontrole, pokud má dokončení replikace na všechny zadané cílové oblasti. 

*Virtuální počítač nebo virtuální počítač škálovací sady trvá dlouhou dobu vytvoření.*

Ověřte, že **OSType** obrázku verze, kterou se pokoušíte vytvořit virtuální počítač nebo škálovací sady virtuálních počítačů z má stejnou **OSType** zdroje spravované image, kterou jste použili k vytvoření verze image. 

## <a name="unable-to-share-resources"></a>Nelze sdílet prostředky

Sdílení Galerie sdílené bitové kopie, definici image a image verze prostředků napříč předplatnými se aktivuje pomocí [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>Replikace je pomalá

Použití **--rozbalte stav replikace** příznak ke kontrole, pokud má dokončení replikace na všechny zadané cílové oblasti. Pokud ne, počkejte, až 6 hodin se úloha dokončí. Pokud selže, spustí příkaz znovu a vytvořit replikaci verze image. Pokud existuje velké množství cílové oblasti, kterou verzi image je právě replikován pro, zvažte replikaci ve fázích.

## <a name="azure-limits-and-quotas"></a>Omezení a kvóty Azure 

[Omezení a kvóty Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) platí pro všechny sdílené bitové kopie galerie, definici image a image verze zdroje. Ujistěte se, že jste v rámci vašich předplatných. 



