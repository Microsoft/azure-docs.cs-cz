---
title: Koncepce DevTest Labs | Dokumentace Microsoftu
description: Další informace o základních konceptech služby DevTest Labs, a jak ho mohli ji snadno vytvářet, spravovat a monitorovat virtuální počítače Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 1e35513d5a5a799b1f5e45cf9a5aa97c083e2087
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725197"
---
# <a name="devtest-labs-concepts"></a>Koncepce DevTest Labs
## <a name="overview"></a>Přehled
Následující seznam obsahuje klíčové koncepce DevTest Labs a definice:

## <a name="labs"></a>Testovací prostředí
Testovací prostředí je infrastrukturu, která zahrnuje skupinu prostředků, třeba Virtual Machines (VM), která vám umožní lépe spravovat tyto prostředky zadáním omezení a kvóty.

## <a name="virtual-machine"></a>Virtuální počítač
Virtuální počítač Azure je jedním z několika typů [výpočetních prostředků na vyžádání, škálovatelné](https://docs.microsoft.com/azure/app-service/overview-compare) , které Azure nabízí. Virtuální počítače Azure poskytují flexibilitu virtualizace bez nutnosti zakoupení a údržby fyzického hardwaru, na kterém běží, i když je stále potřeba údržbě virtuálního počítače pomocí provádí některé úkoly, jako jsou konfigurace, opravy a instalaci softwaru, který běží na ho.

[Přehled virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) poskytuje informace o co byste měli zvážit před vytvoření virtuálního počítače, jak vytvořit a jak ho spravovat.

## <a name="claimable-vm"></a>Nárokovatelných virtuálních počítačů
Nárokovatelných virtuálního počítače Azure je virtuální počítač, který je k dispozici žádný testovací prostředí uživatel s oprávněními. Správce testovacího prostředí můžete připravit virtuální počítače s konkrétním základní Image a artefakty a uložit je sdílený fond. Testovacího prostředí můžete pak deklarace identity uživatele funkčním virtuálním počítači z fondu, kdy potřebují s tuto konkrétní konfiguraci.

Virtuální počítač, který je nárokovatelných není přiřazen zpočátku na konkrétního uživatele, ale zobrazí se seznam všech uživatelů v části "Nárokovatelné virtuální počítače". Po virtuálního počítače je převzatá podle uživatele, je přesunuta do jejich "Moje virtual machines" oblasti a není už nárokovatelných žádný uživatel.

## <a name="environment"></a>Prostředí
Prostředí v DevTest Labs, odkazuje na kolekci prostředků Azure v testovacím prostředí. [Tento příspěvek na blogu](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) Tento článek popisuje postup vytvoření prostředí více virtuálních počítačů z šablon Azure Resource Manageru.

## <a name="base-images"></a>Základní image
Základní Image jsou Image virtuálních počítačů pomocí nástroje a nastavení předem nainstalován a nakonfigurován k rychlému vytvoření virtuálního počítače. Zřízení virtuálního počítače vybrat existující základ a přidáním artefakt k instalaci testovacího agenta. Potom můžete uložit zřízených virtuálních počítačů jako základ tak, aby základní třídy lze použít bez nutnosti přeinstalovat agenta testu pro každý zřizování virtuálního počítače.

## <a name="artifacts"></a>Artefakty
Artefakty se používají k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

* Nástroje, které chcete nainstalovat do virtuálního počítače – například agenti, Fiddler a Visual Studio.
* Akce, které chcete spustit na virtuálním počítači – třeba klonování úložiště.
* Aplikace, které chcete otestovat

Artefakty jsou [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) soubory JSON, které obsahují pokyny k nasazení a použití konfigurace.

## <a name="artifact-repositories"></a>Úložiště artefaktů
Úložiště artefaktů jsou úložiště git, kde se změnami artefakty. Úložiště artefaktů můžete přidat do více testovacích prostředí ve vaší organizaci povolení opakované použití a sdílení.

## <a name="formulas"></a>Vzorce
Vzorce, kromě základní Image, poskytují mechanismus pro rychlé zřizování virtuálních počítačů. Vzorec ve službě DevTest Labs je seznam hodnot výchozí vlastnost použít k vytvoření testovacího prostředí virtuálních počítačů.
Pomocí vzorců, které virtuální počítače se stejnou sadou vlastností – například základní image, velikost virtuálních počítačů, virtuální sítě a artefakty – je možné vytvářet bez nutnosti zadávat pokaždé, když tyto vlastnosti. Při vytváření virtuálního počítače ze vzorce, výchozí hodnoty může sloužit jako-je nebo upravit.

## <a name="policies"></a>Zásady
Zásady pomáhají při řízení nákladů ve vaší laboratoři. Například můžete vytvořit zásady jejich automatického vypínání virtuálních počítačů podle předem definovaného plánu.

## <a name="caps"></a>Omezení
Omezení je mechanismus pro minimalizaci plýtvání ve vaší laboratoři. Například můžete nastavit limit chcete omezit počet virtuálních počítačů, které lze vytvořit na uživatele nebo v testovacím prostředí.

## <a name="security-levels"></a>Úrovně zabezpečení
Zabezpečení přístupu je určen pomocí řízení přístupu Azure Role-Based (RBAC). Vysvětlení, jak funguje přístup, pomáhá pochopit rozdíly mezi oprávnění, role a obor dle RBAC.

* Oprávnění – oprávnění je definované přístup k určité akce (například čtení pro všechny virtuální počítače).
* Role - role je sadu oprávnění, které mohou být seskupeny a přiřazená uživateli. Například *vlastník předplatného* má role přístup ke všem prostředkům v rámci předplatného.
* Rozsah - obor je úroveň v hierarchii k prostředku Azure, jako jsou skupiny prostředků, jednoho testovacího prostředí nebo celé předplatné.

V rámci oboru DevTest Labs, existují dva typy rolí k definování uživatelských oprávnění: vlastník testovacího prostředí a uživatele testovacího prostředí.

* Vlastník testovacího prostředí – vlastník testovacího prostředí má přístup k žádným prostředkům v rámci testovacího prostředí. Proto vlastník testovacího prostředí můžete upravovat zásady, číst a zapisovat všechny virtuální počítače, změnit virtuální síť atd.
* Testovací prostředí uživatele – uživatele testovacího prostředí můžete zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě, ale nelze změnit zásady nebo všechny virtuální počítače vytvořené jinými uživateli.

Chcete-li zjistit, jak vytvořit vlastní role ve službě DevTest Labs, najdete v článku [udělení uživatelských oprávnění na určitém laboratorním zásady](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Protože obory jsou hierarchické, když má uživatel oprávnění v určitém rozsahu, je automaticky udělena oprávnění v každé nižší úrovně oboru zahrnuta. Například pokud uživatel je přiřazena role vlastníka předplatného, pak mají přístup ke všem prostředkům v rámci předplatného, které zahrnují všechny virtuální počítače, všechny virtuální sítě a všechny testovací prostředí. Proto se vlastník předplatného automaticky dědí role vlastník testovacího prostředí. Ale opak není true. Vlastník testovacího prostředí má přístup k testovacím prostředí, které je obor nižší než úroveň předplatného. Vlastník testovacího prostředí proto nebudou moci zobrazit virtuální počítače nebo virtuální sítě nebo všechny prostředky, které se nachází mimo testovacího prostředí.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Všechny principy probírané v tomto článku můžete nakonfigurovat pomocí šablon Azure Resource Manageru, které umožňují definovat infrastruktury/konfigurace vašeho řešení Azure a opakovaně nasadit v konzistentním stavu.

[Princip struktury a syntaxe šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) popisuje strukturu šablony Azure Resource Manageru a vlastnosti, které jsou k dispozici v různých částech šablony.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
[Vytvoření testovacího prostředí v DevTest Labs](devtest-lab-create-lab.md)
