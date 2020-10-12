---
title: Řešení potíží se sdílenými bitovými kopiemi v Azure
description: Naučte se řešit problémy s galeriemi sdílených imagí.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 06/15/2020
ms.author: cynthn
ms.reviewer: cynthn
ms.openlocfilehash: 3a206a7aabee9f75524ab4715afa30ec05c612bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328059"
---
# <a name="troubleshooting-shared-image-galleries-in-azure"></a>Řešení potíží s galeriemi sdílených imagí v Azure

Pokud při provádění jakýchkoli operací se sdílenými galeriemi imagí, definicemi imagí a verzemi imagí narazíte na problémy, spusťte příkaz, který selhal, znovu v režimu ladění. Režim ladění je aktivován předáním přepínače pomocí rozhraní příkazového `--debug` řádku a `-Debug` přepínače pomocí prostředí PowerShell. Po vyhledání chyby použijte tento dokument k odstranění chyb.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nejde vytvořit sdílenou galerii imagí

Možné příčiny:

*Název galerie je neplatný.*

Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky. Změňte název galerie a zkuste to znovu. 

*Název galerie není v rámci vašeho předplatného jedinečný.*

Vyberte název jiné galerie a zkuste to znovu.


## <a name="unable-to-create-an-image-definition"></a>Nejde vytvořit definici image 

Možné příčiny:

*název definice obrázku je neplatný.*

Povolené znaky pro definici obrázku jsou velká a malá písmena, číslice, tečky, pomlčky a tečky. Změňte název definice obrázku a zkuste to znovu.

*Povinné vlastnosti pro vytvoření definice obrázku nejsou naplněny.*

Vlastnosti, jako je název, vydavatel, nabídka, SKU a typ operačního systému, jsou povinné. Ověřte, zda jsou předávány všechny vlastnosti.

Zajistěte, aby se **OSType**, buď Linux nebo Windows, definice image shoduje se zdrojem, který používáte k vytvoření verze image. 


## <a name="unable-to-create-an-image-version"></a>Nejde vytvořit verzi image 

Možné příčiny:

*Název verze Image je neplatný.*

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion. podverze. patch*. Změňte název verze image a zkuste to znovu.

*Zdrojová image, ze které se vytváří verze image, se nenašla.* 

Zkontroluje, jestli zdrojová image existuje a je ve stejné oblasti jako verze image.

*Spravovaná image se neprovádí zřízené.*

Ujistěte se, že stav zřizování zdrojové spravované Image je **úspěšný**.

*Seznam cílových oblastí neobsahuje zdrojovou oblast.*

Seznam cílových oblastí musí zahrnovat zdrojovou oblast verze image. Ujistěte se, že jste do seznamu cílových oblastí zahrnuli zdrojovou oblast, do které má Azure replikovat verzi image.

*Replikace do všech cílových oblastí není dokončená.*

Pomocí příznaku **--expand ReplicationStatus** můžete ověřit, jestli se dokončila replikace do všech zadaných cílových oblastí. V takovém případě počkejte po dobu až 6 hodin, než se úloha dokončí. Pokud dojde k chybě, spusťte příkaz znovu pro vytvoření a replikaci verze image. Pokud existuje velký počet cílových oblastí, na které se replikuje verze image, zvažte provedení replikace ve fázích.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nejde vytvořit virtuální počítač nebo škálovací sadu 

Možné příčiny:

*Uživatel, který se pokouší vytvořit virtuální počítač nebo sadu škálování virtuálního počítače, nemá přístup pro čtení k verzi image.*

Obraťte se na vlastníka předplatného a požádejte ho, aby pomocí [řízení přístupu založeného na rolích Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)poskytl přístup pro čtení k verzi Image nebo nadřazeným prostředkům (jako je například galerie sdílených imagí nebo definice obrázku). 

*Verze bitové kopie nebyla nalezena.*

Ověřte, zda je oblast, ve které se pokoušíte vytvořit virtuální počítač nebo škálování virtuálního počítače v nástroji, obsažena v seznamu cílových oblastí verze bitové kopie. Pokud je oblast již v seznamu cílových oblastí, ověřte, zda byla úloha replikace dokončena. Pomocí příznaku **-ReplicationStatus** můžete ověřit, jestli se replikace do všech zadaných cílových oblastí dokončila. 

*Vytvoření virtuálního počítače nebo sady škálování virtuálního počítače trvá dlouhou dobu.*

Ověřte, že **OSType** verze image, ze které se pokoušíte vytvořit virtuální počítač nebo sadu škálování virtuálního počítače, z nástroje má stejný **OSType** zdroje, který jste použili k vytvoření verze image. 

## <a name="unable-to-share-resources"></a>Nejde sdílet prostředky

Sdílení Galerie sdílených imagí, definice obrázku a verze imagí v rámci předplatných je povolené pomocí [řízení přístupu na základě role Azure (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

## <a name="replication-is-slow"></a>Replikace je pomalá.

Pomocí příznaku **--expand ReplicationStatus** můžete ověřit, jestli se dokončila replikace do všech zadaných cílových oblastí. V takovém případě počkejte po dobu až 6 hodin, než se úloha dokončí. Pokud dojde k chybě, znovu spusťte příkaz pro vytvoření a replikaci verze image. Pokud existuje velký počet cílových oblastí, na které se replikuje verze image, zvažte provedení replikace ve fázích.

## <a name="azure-limits-and-quotas"></a>Omezení a kvóty Azure 

[Omezení a kvóty Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) se vztahují na všechny prostředky pro galerii sdílených imagí, definice obrázků a verze imagí. Ujistěte se, že jste v omezeních pro vaše předplatná. 


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [galeriích sdílených imagí](./linux/shared-image-galleries.md).
