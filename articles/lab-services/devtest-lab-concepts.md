---
title: Koncepty DevTest Labs | Microsoft Docs
description: Seznamte se základními koncepty DevTest Labs a jak ji můžete snadno vytvářet, spravovat a monitorovat virtuální počítače Azure
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
ms.openlocfilehash: ce0c51a3b97c57095a336128919c5e27f860905b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="devtest-labs-concepts"></a>Koncepce DevTest Labs
## <a name="overview"></a>Přehled
Následující seznam obsahuje klíčové koncepty DevTest Labs a definice:

## <a name="labs"></a>Testovací prostředí
Testovacího prostředí je infrastrukturu, která zahrnuje skupinu prostředků, třeba virtuální počítače (VM), které vám umožní lépe spravovat tyto prostředky zadáním omezení a kvóty.

## <a name="virtual-machine"></a>Virtuální počítač
Virtuální počítač Azure je jedním z několika typů [na vyžádání, škálovatelných výpočetních prostředků](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) Azure nabízí. Virtuální počítače Azure poskytují flexibilitu virtualizace bez nutnosti zakoupení a údržby fyzického hardwaru, který spouští, i když stále je třeba zachovat virtuální počítač tak, že provádění určitých úloh, jako je například konfigurace, opravy a instalace softwaru, který běží na ho.

[Přehled o virtuálních počítačích s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) poskytuje informace o co byste měli zvážit před vytvořit virtuální počítač, jak vytvořit a jak spravovat.

## <a name="claimable-vm"></a>Vymahatelných virtuálních počítačů
Virtuální počítač Azure vymahatelných je virtuální počítač, který je k dispozici pro použití jakéhokoli testovacího prostředí uživatele s oprávněními. Správce testovacího prostředí můžete připravit virtuální počítače s konkrétním základní bitové kopie a artefaktů a uložit je ke sdílenému fondu. Testovacího prostředí můžete pak deklarace identity uživatele funkční virtuální počítač z fondu když potřebují jednu s konfigurací pro tuto konkrétní.

Virtuální počítač, který je vymahatelných není původně přiřazen k žádné konkrétní uživatele, ale se zobrazí v seznamu každého uživatele v části "Vymahatelných virtuální počítače". Po virtuálních počítačů je požadována uživatelem, se přesune až jejich "Moje virtuální počítače" oblasti a nadále již není vymahatelných jiným uživatelem.

## <a name="environment"></a>Prostředí
Prostředí v DevTest Labs, odkazuje na kolekci prostředků Azure v testovacím prostředí. [Tento příspěvek blogu](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) popisuje, jak vytvořit prostředí více virtuálních počítačů z šablony Azure Resource Manager.

## <a name="base-images"></a>Základní bitové kopie
Základní Image jsou Image virtuálních počítačů s všechny nástroje a nastavení předem nainstalován a nakonfigurován k rychlému vytvoření virtuálního počítače. Výběr existující základní a přidáním artefakt k instalaci agenta test agent můžete zřídit virtuální počítač. Pak můžete uložit zřízeného virtuálního počítače jako základ tak, aby základní lze použít bez nutnosti znovu nainstalujte agenta test pro každý zřizování virtuálního počítače.

## <a name="artifacts"></a>Artefakty
Artefakty slouží k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

* Nástroje, které chcete nainstalovat do virtuálního počítače – například agentů, Fiddler a Visual Studio.
* Akce, které chcete spustit na virtuálním počítači – například klonování úložišti.
* Aplikace, které chcete otestovat

Artefakty je [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) soubory JSON, které obsahují pokyny k provedení nasazení a konfiguraci použít.

## <a name="artifact-repositories"></a>Úložiště artefaktů
Úložiště artefaktů jsou úložiště git, kde se změnami artefakty. Úložiště artefaktů lze přidat do více labs ve vaší organizaci povolení opakované použití a sdílení.

## <a name="formulas"></a>Vzorce
Vzorce, kromě základní Image, poskytují mechanismus pro rychlé zřizování virtuálních počítačů. Vzorec v DevTest Labs je seznam výchozí hodnoty vlastností použít k vytvoření testovacího prostředí virtuálních počítačů.
Pomocí vzorce, virtuální počítače s jednou sadou vlastností – například základní bitovou kopii, velikost virtuálního počítače, virtuální sítě a artefakty – lze vytvořit bez nutnosti zadávat pokaždé, když tyto vlastnosti. Při vytváření virtuálního počítače ze vzorce, výchozí hodnoty lze použít jako-je nebo upravit.

## <a name="policies"></a>Zásady
Zásady pomáhají v řízení nákladů ve svém testovacím prostředí. Můžete například vytvořit zásadu, která automaticky vypnout virtuální počítače podle definovaného plánu.

## <a name="caps"></a>CAP k vzdálené ploše
CAP k vzdálené ploše je mechanismus pro minimalizaci odpady ve vašem testovacím prostředí. Například můžete nastavit cap omezit počet virtuálních počítačů, které lze vytvořit na uživatele nebo v testovacím prostředí.

## <a name="security-levels"></a>Úrovně zabezpečení
Zabezpečení přístupu je určen podle řízení řízení přístupu (RBAC). Abyste pochopili, jak přístup funguje, pomáhá pochopit rozdíly mezi oprávnění, role a obor podle definice RBAC.

* Oprávnění – oprávnění jsou definované přístup k určité akce (například-čtení pro všechny virtuální počítače).
* Role - role je sadu oprávnění, které mohou být seskupeny a přiřazen k uživateli. Například *vlastník předplatného* má role přístup ke všem prostředkům v rámci předplatného.
* Rozsah - obor je úrovní v hierarchii prostředek služby Azure, jako je například skupinu prostředků, jeden testovacího prostředí nebo celé předplatné.

V rámci oboru DevTest Labs, existují dva typy rolí k definování oprávnění uživatele: vlastníka testovacího prostředí a testovacího prostředí uživatele.

* Laboratoř vlastníka – vlastník testovacího prostředí má přístup k žádným prostředkům v testovacím prostředí. Proto vlastníka testovacího prostředí můžete upravit zásady, čtení a zápis všech virtuálních počítačů, změňte virtuální síť a tak dále.
* Uživatel Lab - uživatel testovacího prostředí můžete zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě, ale nelze změnit zásady nebo všechny virtuální počítače vytvořené jinými uživateli.

Chcete-li zjistit, jak vytvářet vlastní role v DevTest Labs, naleznete v článku [udělení uživatelských oprávnění k zásad konkrétní testovacího](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Vzhledem k tomu, že obory jsou hierarchické, když má uživatel oprávnění u určité oboru, jsou automaticky přiděleno těchto oprávnění v každé nižší úrovni oboru zahrnut. Například pokud uživatel je přiřazen k roli vlastník předplatného, pak mají přístup ke všem prostředkům v předplatném, včetně všech virtuálních počítačů, všechny virtuální sítě a všechny laboratoře. Proto vlastník předplatného automaticky převezme roli vlastníka testovacího prostředí. Jako opak však není pravda. Vlastník testovacího prostředí má přístup k testovacím prostředí, které je obor nižší než úroveň předplatného. Vlastník testovacího prostředí proto nebudou moci zobrazit virtuální počítače nebo virtuální sítě nebo všechny prostředky, které jsou mimo testovací prostředí.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Všechny principy probírané v tomto článku můžete nakonfigurovat pomocí šablony Azure Resource Manager, které umožňují definovat infrastruktury nebo konfiguraci řešení Azure a opakovaně nasadit v konzistentním stavu.

[Pochopit strukturu a syntaxe šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) popisuje strukturu šablonu Azure Resource Manager a vlastnosti, které jsou k dispozici v různých částech šablony.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
[Vytvoření testovacího prostředí v DevTest Labs](devtest-lab-create-lab.md)
