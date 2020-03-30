---
title: 'Úvodní příručka: Vytvoření virtuálního počítače s datovými vědami systému Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurace a vytvoření virtuálního počítače pro datové vědy v Azure pro analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: afcb676f68e7be9d3ebef11ea2c6876a86bbd062
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281778"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Úvodní příručka: Nastavení virtuálního počítače pro datové vědy pro Windows

Zprovoznění s virtuálním počítačem pro datové vědy se systémem Windows Server 2019.

## <a name="prerequisite"></a>Požadavek

Chcete-li vytvořit virtuální počítač s Windows Data Science, musíte mít předplatné Azure. [Vyzkoušejte Azure zdarma](https://azure.com/free).
Upozorňujeme, že bezplatné účty Azure nepodporují virtuální počítače s povoleným gpu.

## <a name="create-your-dsvm"></a>Vytvořte si DSVM

Vytvoření instance DSVM:

1. Přejděte na [portál Azure,](https://portal.azure.com) pokud ještě nejste přihlášení, můžete se k vám přihlásit.
1. Najděte výpis virtuálního počítače zadáním "virtuálního počítače pro datové vědy" a výběrem možnosti "Virtuální počítač pro datové vědy – Windows 2019".

1. V dolní části vyberte tlačítko **Vytvořit.**

1. Měli byste být přesměrováni na okno "Vytvořit virtuální počítač".

1. Vyplňte kartu **Základy:**
      * **Předplatné**: Pokud máte více než jedno předplatné, vyberte předplatné, ve kterém bude počítač vytvořen a fakturován. Toto předplatné musí mít oprávnění vytvářet prostředky.
      * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující skupinu.
      * **Název virtuálního počítače**: Zadejte název virtuálního počítače. Takhle se zobrazí na vašem webu Azure Portal.
      * **Umístění**: Vyberte nejvhodnější datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže k vaší fyzické poloze. Další informace o [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obrázek**: Ponechte výchozí hodnotu.
      * **Velikost**: To by mělo automaticky naplnit s velikostí, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních aplikací pro Windows v Azure](../../virtual-machines/windows/sizes.md).
      * **Uživatelské jméno**: Zadejte uživatelské jméno správce. Toto je uživatelské jméno, které budete používat k přihlášení k virtuálnímu počítači a nemusí být stejné jako vaše uživatelské jméno Azure.
      * **Heslo**: Zadejte heslo, které použijete k přihlášení do virtuálního počítače.    
1. Vyberte **Zkontrolovat a vytvořit**.
1. **Zkontrolovat+vytvořit**
   * Ověřte správnost všech zadaných informací. 
   * Vyberte **Vytvořit**.


> [!NOTE]
> * Neplatíte licenční poplatky za software, který je dodáván předinstalovaný ve virtuálním počítači. Náklady na výpočetní prostředky zaplatíte za velikost serveru, kterou jste zvolili v kroku **Velikost.**
> * Zřizování trvá 10 až 20 minut. Můžete zobrazit stav virtuálního počítače na webu Azure Portal.

## <a name="access-the-dsvm"></a>Přístup k DSVM

Po vytvoření a zřízení virtuálního počítače se podle uvedených kroků [připojte k virtuálnímu počítači založenému na Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Použijte přihlašovací údaje účtu správce, které jste nakonfigurovali v kroku **Základy** vytvoření virtuálního počítače. 

Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnoho nástrojů lze přistupovat prostřednictvím dlaždic nabídky **Start** a ikon na ploše.

Můžete také připojit DSVM k poznámkovým blokům Azure ke spuštění poznámkových bloků Jupyter na virtuálním počítači a obejít omezení úrovně bezplatné služby. Další informace naleznete v [tématu Správa a konfigurace projektů poznámkových bloků](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Další kroky

* Prohlédněte si nástroje na DSVM otevřením nabídky **Start.**
* Další informace o Azure Machine Learning učte [Co je Azure Machine Learning?](../overview-what-is-azure-ml.md) a vyzkoušet výukové [programy](../index.yml).
* Přečtěte si článek [Deset věcí, které můžete dělat na virtuálním počítači pro datové vědy](https://aka.ms/dsvmtenthings).
* Navštivte [Galerii AI Azure](https://gallery.cortanaintelligence.com) pro ukázky strojového učení a analýzy dat, které používají Azure Machine Learning a související datové služby v Azure. Také jsme poskytli ikonu pro tuto galerii v nabídce **Start** a na ploše virtuálního počítače.

