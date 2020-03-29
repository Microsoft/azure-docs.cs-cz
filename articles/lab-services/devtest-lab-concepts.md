---
title: Koncepty DevTest Labs | Dokumenty společnosti Microsoft
description: Naučte se základní koncepty DevTest Labs a jak to může usnadnit vytváření, správu a monitorování virtuálních počítačů Azure
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
ms.openlocfilehash: 22fd78ccd58be1790fcd167da396600e8b876564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428917"
---
# <a name="devtest-labs-concepts"></a>Koncepce DevTest Labs
## <a name="overview"></a>Přehled
Následující seznam obsahuje klíčové koncepty a definice devTest Labs:

## <a name="labs"></a>Testovací prostředí
Testovací prostředí je infrastruktura, která zahrnuje skupinu prostředků, jako jsou virtuální počítače (Virtuální počítače), která umožňuje lépe spravovat tyto prostředky zadáním omezení a kvót.

## <a name="virtual-machine"></a>Virtuální počítač
Virtuální počítač Azure je jedním z několika typů [škálovatelných výpočetních prostředků na vyžádání,](/azure/architecture/guide/technology-choices/compute-decision-tree) které Azure nabízí. Virtuální počítače Azure poskytují flexibilitu virtualizace bez nutnosti kupovat a udržovat fyzický hardware, který jej spouští, i když stále potřebujete udržovat virtuální počítač prováděním určitých úloh, jako je konfigurace, opravy a instalace softwaru, který běží na jej.

[Přehled virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) poskytuje informace o tom, co byste měli zvážit před vytvořením virtuálního počítače, jak ho vytvořit a jak ho spravujete.

## <a name="claimable-vm"></a>Nárokovatelný virtuální ms
Azure Claimable VM je virtuální počítač, který je k dispozici pro všechny uživatele testovacího prostředí s oprávněními. Správce testovacího prostředí může připravit virtuální počítačs s konkrétní základní image a artefakty a uložit je do sdíleného fondu. Uživatel testovacího prostředí pak může nárokovat pracovní virtuální hod z fondu, když ho potřebuje s danou konkrétní konfigurací.

Virtuální počítač, který je nárokovatelný není původně přiřazena k žádné konkrétní uživatele, ale zobrazí se v seznamu každého uživatele v části "Nárokovatelné virtuální počítače". Poté, co je virtuální počítač nárokován uživatelem, je přesunut do oblasti "Moje virtuální počítače" a už není nárokovatelný jiným uživatelem.

## <a name="environment"></a>Prostředí
V DevTest Labs prostředí odkazuje na kolekci prostředků Azure v testovacím prostředí. [Tento příspěvek blogu](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) popisuje, jak vytvořit prostředí s více virtuálními počítači z vašich šablon Azure Resource Manager.

## <a name="base-images"></a>Základní image
Základní image jsou image virtuálních počítače se všemi nástroji a nastaveními předinstalovanými a nakonfigurovanými tak, aby rychle vytvořily virtuální hod. Virtuální počítač můžete zřídit výběrem existující základny a přidáním artefaktu pro instalaci testovacího agenta. Potom můžete uložit zřízeného virtuálního virtuálního ženy jako základ tak, aby základní lze použít bez nutnosti přeinstalovat testovacíagent pro každé zřizování virtuálního soudu.

## <a name="artifacts"></a>Artefakty
Artefakty se používají k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

* Nástroje, které chcete nainstalovat na virtuální počítač – například agenti, šumava a Visual Studio.
* Akce, které chcete spustit na virtuálním počítači – například klonování repo.
* Aplikace, které chcete testovat.

Artefakty jsou soubory JSON [Azure Resource Manager,](../azure-resource-manager/management/overview.md) které obsahují pokyny k provedení nasazení a použití konfigurace.

## <a name="artifact-repositories"></a>Úložiště artefaktů
Úložiště artefaktů jsou git repozitáře, kde jsou artefakty se změnami. Úložiště artefaktů lze přidat do více testovacích prostředí ve vaší organizaci, která umožňují opakované použití a sdílení.

## <a name="formulas"></a>Vzorce
Vzorce, kromě základních bitových kopií, poskytují mechanismus pro rychlé zřizování virtuálních montovek. Vzorec v DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření virtuálního virtuálního virtuálního zařízení testovacího prostředí.
Pomocí vzorců lze virtuální počítače se stejnou sadou vlastností – jako je základní bitová kopie, velikost virtuálního počítače, virtuální síť a artefakty – vytvořit bez nutnosti pokaždé tyto vlastnosti zadávat. Při vytváření virtuálního virtuálního soudu ze vzorce lze výchozí hodnoty použít tak, jak jsou, nebo upravit.

## <a name="policies"></a>Zásady
Zásady pomáhají při řízení nákladů ve vaší laboratoři. Můžete například vytvořit zásadu pro automatické vypnutí virtuálních počítačů na základě definovaného plánu.

## <a name="caps"></a>Caps
Caps je mechanismus pro minimalizaci odpadu ve vaší laboratoři. Můžete například nastavit limit pro omezení počtu virtuálních uživatelů, které lze vytvořit na uživatele nebo v testovacím prostředí.

## <a name="security-levels"></a>Úrovně zabezpečení
Přístup k zabezpečení je určen řízením přístupu na základě rolí Azure (RBAC). Chcete-li pochopit, jak funguje přístup, pomáhá pochopit rozdíly mezi oprávnění, role a obor, jak je definováno RBAC.

* Oprávnění – Oprávnění je definovaný přístup ke konkrétní akci (např. přístup pro čtení ke všem virtuálním počítačům).
* Role – Role je sada oprávnění, která lze seskupit a přiřadit uživateli. Například role *vlastníka předplatného* má přístup ke všem prostředkům v rámci předplatného.
* Obor – obor je úroveň v rámci hierarchie prostředku Azure, jako je například skupina prostředků, jedno testovací prostředí nebo celé předplatné.

V rámci devTest Labs existují dva typy rolí definovat uživatelská oprávnění: vlastník testovacího prostředí a uživatel testovacího prostředí.

* Vlastník testovacího prostředí – vlastník testovacího prostředí má přístup k libovolným prostředkům v rámci testovacího prostředí. Vlastník testovacího prostředí proto můžete upravit zásady, číst a zapisovat všechny virtuální počítače, změnit virtuální síť a tak dále.
* Uživatel testovacího prostředí – uživatel testovacího prostředí může zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě, ale nemůže měnit zásady ani žádné virtuální počítače vytvořené jinými uživateli.

Jak vytvořit vlastní role v laboratořích DevTest Labs, podívejte se do článku [Udělení uživatelských oprávnění konkrétním zásadám testovacího prostředí](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Vzhledem k tomu, že obory jsou hierarchické, pokud má uživatel oprávnění v určitém oboru, jsou jim automaticky udělena tato oprávnění v každém oboru nižší úrovně, který je zahrnut. Například pokud je uživatel přiřazen k roli vlastníka předplatného, pak mají přístup ke všem prostředkům v předplatném, které zahrnují všechny virtuální počítače, všechny virtuální sítě a všechna testovací prostředí. Vlastník předplatného proto automaticky zdědí roli vlastníka testovacího prostředí. Opak však není pravdou. Vlastník testovacího prostředí má přístup k testovacímu prostředí, což je nižší obor než úroveň předplatného. Vlastník testovacího prostředí proto nebude moci zobrazit virtuální počítače nebo virtuální sítě nebo žádné prostředky, které jsou mimo testovací prostředí.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Všechny koncepty popsané v tomto článku lze nakonfigurovat pomocí šablon Azure Resource Manager, které umožňují definovat infrastrukturu/konfiguraci vašeho řešení Azure a opakovaně jej nasadit v konzistentním stavu.

[Pochopit strukturu a syntaxi šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) popisuje strukturu šablony Azure Resource Manager a vlastnosti, které jsou k dispozici v různých částech šablony.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
[Vytvoření testovacího prostředí v devtest labs](devtest-lab-create-lab.md)
