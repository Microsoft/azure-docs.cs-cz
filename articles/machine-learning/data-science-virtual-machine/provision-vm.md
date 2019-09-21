---
title: 'Rychlý start: Vytvoření DSVM s Windows'
description: Konfigurace a vytvořit virtuální počítač pro datové vědy v Azure pro účely analýzy a strojového učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: f543db0240d59cf99f3bd793e059b7985f1ffe22
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170664"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rychlý start: Nastavení Data Science Virtual Machine pro Windows

Začněte pracovat s Windows Data Science Virtual Machine.

## <a name="prerequisite"></a>Požadavek

Pokud chcete vytvořit Data Science Virtual Machine Windows, musíte mít předplatné Azure. [Vyzkoušejte si Azure zdarma](https://azure.com/free).

## <a name="create-your-dsvm"></a>Vytvoření DSVM

Vytvoření instance DSVM:

1. Pokud ještě nejste přihlášení, můžete přejít na [Azure Portal](https://portal.azure.com) zobrazí se výzva k přihlášení k účtu Azure.
1. Vyhledejte výpis virtuálního počítače zadáním příkazu "virtuální počítač pro datové vědy" a výběrem Data Science Virtual Machine-Windows 2016.
    ![Seznam virtuálních počítačů s Windows](./media/provision-vm/search-windows.png)
1. V dolní části vyberte tlačítko **vytvořit** .

   ![Výpis virtuálního počítače na Azure Portal s tlačítkem vytvořit](./media/provision-vm/create-windows.png)

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   ![Karta základy odpovídající virtuálnímu počítači s Windows](./media/provision-vm/review-create-windows.png)

1. Vyplňte kartu **základy** :
      * **Předplatné:** Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Toto předplatné musí mít oprávnění vytvářet prostředky.
      * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
      * **Název virtuálního počítače**: Zadejte název virtuálního počítače. Tímto způsobem se zobrazí ve vašem Azure Portal.
      * **Umístění**: Vyberte odpovídající datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění. Přečtěte si další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obrázek**: Nechte zadanou výchozí hodnotu.
      * **Velikost**: To by mělo automaticky naplnit velikost, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačů s Windows v Azure](../../virtual-machines/windows/sizes.md).
      * **Uživatelské jméno**: Zadejte uživatelské jméno správce. Toto je uživatelské jméno, které použijete k přihlášení k virtuálnímu počítači, a nemusí být stejné jako uživatelské jméno Azure.
      * **Heslo**: Zadejte heslo, které použijete k přihlášení k virtuálnímu počítači.    
1. Vyberte **Zkontrolovat a vytvořit**.
1. **Zkontrolovat a vytvořit**
   * Ověřte, zda všechny informace, které jste zadali správný. 
   * Vyberte **Vytvořit**.


> [!NOTE]
> * Na software, který je připravený předem načtený na virtuálním počítači, neplatíte poplatky za licence. Za velikost serveru, kterou jste zvolili v kroku **Velikost** , platíte náklady na výpočetní výkon.
> * Zřizování trvá 10 až 20 minut. Stav virtuálního počítače můžete zobrazit na Azure Portal.

## <a name="access-the-dsvm"></a>Přístup k DSVM

Až se virtuální počítač vytvoří a zřídí, postupujte podle kroků uvedených v části [připojení k virtuálnímu počítači založenému na Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Použijte přihlašovací údaje účtu správce, které jste nakonfigurovali v kroku **základní informace** o vytvoření virtuálního počítače. 

Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnohé z těchto nástrojů jsou dostupné prostřednictvím dlaždic v nabídce **Start** a ikon na ploše.

K Azure Notebooks také můžete připojit DSVM a spustit poznámkové bloky Jupyter na virtuálním počítači a obejít omezení bezplatné úrovně služby. Další informace najdete v tématu [Správa a konfigurace projektů poznámkových bloků](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Další kroky

* Prozkoumejte nástroje na DSVM otevřením nabídky **Start** .
* Přečtěte si o službě Azure Machine Learning, kterou si přečtete, [co je Azure Machine Learning služba?](../service/overview-what-is-azure-ml.md) a vyzkoušet si [kurzy](../index.yml).
* V Průzkumníku souborů přejděte do složky C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts, kde najdete ukázky, které používají knihovnu RevoScaleR v jazyce R, která podporuje analýzu dat v podnikovém měřítku. 
* Přečtěte si článek [deset věcí, které můžete provádět na data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Další informace o vytváření analytická řešení začátku do konce systematicky pomocí [vědecké zpracování týmových dat](../team-data-science-process/index.yml).
* Přejděte [galerii Azure AI](https://gallery.cortanaintelligence.com) služeb machine learning a datové analýzy ukázek, které využívají Azure Machine Learning a související data v Azure. K dispozici jsme také ikonu pro tuto galerii v nabídce **Start** a na ploše virtuálního počítače.
* Projděte si příslušnou [referenční dokumentaci](./reference-windows-vm.md) pro tento virtuální počítač.

