---
title: Koncepty DevTest Labs | Microsoft Docs
description: Přečtěte si o základních konceptech DevTest Labs a o tom, jak usnadňuje vytváření, správu a monitorování virtuálních počítačů Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327854"
---
# <a name="devtest-labs-concepts"></a>Koncepce DevTest Labs
## <a name="overview"></a>Přehled
Následující seznam obsahuje klíčové pojmy a definice DevTest Labs:

## <a name="labs"></a>Testovací prostředí
Testovací prostředí je infrastruktura, která zahrnuje skupinu prostředků, například Virtual Machines (virtuální počítače), která vám umožní lépe spravovat tyto prostředky zadáním limitů a kvót.

## <a name="virtual-machine"></a>Virtuální počítač
Virtuální počítač Azure je jedním z několika typů [škálovatelných výpočetních prostředků na vyžádání](/azure/architecture/guide/technology-choices/compute-decision-tree) , které Azure nabízí. Virtuální počítače Azure vám poskytnou flexibilitu virtualizace bez nutnosti koupit a udržovat fyzický hardware, na kterém běží, i když ho budete potřebovat k údržbě určitých úloh, jako je třeba konfigurace, opravy a instalace softwaru, který na něm běží.

[Přehled virtuálních počítačů s Windows v Azure](../virtual-machines/windows/overview.md) vám poskytne informace o tom, co byste měli zvážit před vytvořením virtuálního počítače, jak ho vytvořit a jak ho spravovat.

## <a name="claimable-vm"></a>Vynucený virtuální počítač
Virtuální počítač s nárokem na Azure je virtuální počítač, který je dostupný pro každého uživatele testovacího prostředí s oprávněními. Správce testovacího prostředí může připravit virtuální počítače se specifickými základními imagemi a artefakty a uložit je do sdíleného fondu. Uživatel testovacího prostředí pak může vyžádat pracovní virtuální počítač z fondu, když ho potřebují s touto konkrétní konfigurací.

Virtuální počítač, který je vynucený, se zpočátku nepřiřazuje žádnému konkrétnímu uživateli, ale zobrazí se v seznamu všech uživatelů v části "vynucené virtuální počítače". Až uživatel požadavek na virtuální počítač vyžádá, přesune se do oblasti moje virtuální počítače a už ho nebude moct používat žádný jiný uživatel.

## <a name="environment"></a>Prostředí
V DevTest Labs prostředí odkazuje na kolekci prostředků Azure v testovacím prostředí. [Tento Blogový příspěvek](./devtest-lab-faq.md#blog-post) popisuje, jak vytvořit prostředí s více virtuálními počítači ze šablon Azure Resource Manager.

## <a name="base-images"></a>Základní image
Základní image jsou image virtuálních počítačů s předinstalovanými nástroji a nastavení, které jsou předinstalované a nakonfigurované pro rychlé vytvoření virtuálního počítače. Virtuální počítač můžete zřídit tak, že vybíráte stávající základnu a přidáte artefakt pro instalaci testovacího agenta. Zřízený virtuální počítač pak můžete uložit jako základní, aby bylo možné použít základ bez nutnosti přeinstalovat testovacího agenta pro každé zřizování virtuálního počítače.

## <a name="artifacts"></a>Artifacts
Artefakty slouží k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

* Nástroje, které chcete na virtuálním počítači nainstalovat – například agenti, Fiddler a Visual Studio.
* Akce, které chcete na virtuálním počítači spustit – například klonování úložiště.
* Aplikace, které chcete testovat.

Artefakty jsou [Azure Resource Manager](../azure-resource-manager/management/overview.md) soubory JSON, které obsahují pokyny k provedení nasazení a použití konfigurace.

## <a name="artifact-repositories"></a>Úložiště artefaktů
Úložiště artefaktů jsou úložiště Git, ve kterých jsou artefakty vrácené se změnami. Úložiště artefaktů se dají přidat do více testovacích prostředí ve vaší organizaci, která umožňují opakované použití a sdílení.

## <a name="formulas"></a>Vzorce
Vzorce, kromě základních imagí, poskytují mechanismus pro rychlé zřizování virtuálních počítačů. Vzorec v DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření testovacího virtuálního počítače.
Se vzorci jsou virtuální počítače se stejnou sadou vlastností, jako je základní image, velikost virtuálního počítače, virtuální síť a artefakty – můžete vytvořit, aniž byste museli tyto vlastnosti zadávat pokaždé. Při vytváření virtuálního počítače ze vzorce se dají výchozí hodnoty použít jako nebo upravené.

## <a name="policies"></a>Zásady
Zásady vám pomůžou řídit náklady v testovacím prostředí. Můžete například vytvořit zásadu pro automatické vypnutí virtuálních počítačů na základě definovaného plánu.

## <a name="caps"></a>Caps
CAPS je mechanismus pro minimalizaci odpadu v testovacím prostředí. Můžete například nastavit limit, který omezí počet virtuálních počítačů, které lze vytvořit pro jednotlivé uživatele, nebo v testovacím prostředí.

## <a name="security-levels"></a>Úrovně zabezpečení
Přístup k zabezpečení závisí na řízení přístupu na základě role Azure (Azure RBAC). Abyste porozuměli tomu, jak Access funguje, pomůže pochopit rozdíly mezi oprávněními, rolí a oborem definovaným v Azure RBAC.

* Oprávnění – oprávnění je definovaný přístup k určité akci (např. přístup pro čtení ke všem virtuálním počítačům).
* Role – role je sada oprávnění, která se dají seskupovat a přiřazovat uživateli. Například role *vlastníka předplatného* má přístup ke všem prostředkům v rámci předplatného.
* Scope – obor je úroveň v rámci hierarchie prostředku Azure, jako je například skupina prostředků, jedna laboratoř nebo celé předplatné.

V rámci rozsahu DevTest Labs existují dva typy rolí k definování uživatelských oprávnění: vlastník testovacího prostředí a uživatel testovacího prostředí.

* Vlastník testovacího prostředí – vlastník testovacího prostředí má přístup k jakýmkoli prostředkům v rámci testovacího prostředí. Proto může vlastník testovacího prostředí upravovat zásady, číst a zapisovat všechny virtuální počítače, měnit virtuální síť a tak dále.
* Uživatel testovacího prostředí – uživatel testovacího prostředí může zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě, ale nemůže měnit zásady ani žádné virtuální počítače vytvořené jinými uživateli.

Informace o tom, jak vytvářet vlastní role v DevTest Labs, najdete v článku o [udělení uživatelských oprávnění ke konkrétním zásadám testovacího prostředí](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Vzhledem k tomu, že obory jsou hierarchicky, když má uživatel oprávnění v určitém oboru, budou tato oprávnění automaticky udělena v každém rozsahu nižší úrovně, který zahrnuje. Pokud je například uživatel přiřazen k roli vlastníka předplatného, pak má přístup ke všem prostředkům v rámci předplatného, které zahrnují všechny virtuální počítače, všechny virtuální sítě a všechny laboratoře. Proto vlastník předplatného automaticky zdědí roli vlastníka testovacího prostředí. Opak však není pravda. Vlastník testovacího prostředí má přístup k testovacímu prostředí, což je nižší obor než úroveň předplatného. Proto vlastník testovacího prostředí nebude moci zobrazit virtuální počítače nebo virtuální sítě ani žádné prostředky, které nejsou mimo testovací prostředí.

## <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
Všechny koncepty popsané v tomto článku se dají nakonfigurovat pomocí Azure Resource Manager šablon, které vám umožní definovat infrastrukturu a konfiguraci řešení Azure a opakovaně ho nasadit v konzistentním stavu.

[Pochopení struktury a syntaxe šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md#template-format) popisuje strukturu Azure Resource Manager šablony a vlastností, které jsou k dispozici v různých částech šablony.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další kroky
[Vytvoření testovacího prostředí v DevTest Labs](devtest-lab-create-lab.md)
