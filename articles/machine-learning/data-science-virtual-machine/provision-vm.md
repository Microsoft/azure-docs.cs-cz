---
title: 'Rychlý Start: vytvoření Data Science Virtual Machine Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Nakonfigurujte a vytvořte Data Science Virtual Machine v Azure pro analýzy a strojové učení.
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 53c7eb899578a044ecca0900da722c594bdac8dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691837"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rychlý Start: nastavení Data Science Virtual Machine pro Windows

Začněte pracovat se systémem Windows Server 2019 Data Science Virtual Machine.

## <a name="prerequisite"></a>Požadavek

Pokud chcete vytvořit Data Science Virtual Machine Windows, musíte mít předplatné Azure. [Vyzkoušejte si Azure zdarma](https://azure.com/free).
Upozorňujeme, že bezplatné účty Azure nepodporují cenové úrovně virtuálních počítačů s GPU.

## <a name="create-your-dsvm"></a>Vytvoření DSVM

Vytvoření instance DSVM:

1. Pokud ještě nejste přihlášení, můžete přejít na [Azure Portal](https://portal.azure.com) zobrazí se výzva k přihlášení k účtu Azure.
1. Vyhledejte výpis virtuálního počítače zadáním příkazu "virtuální počítač pro datové vědy" a výběrem Data Science Virtual Machine-Windows 2019.

1. V dolní části vyberte tlačítko **vytvořit** .

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.

1. Vyplňte kartu **základy** :
      * **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Toto předplatné musí mít oprávnění vytvářet prostředky.
      * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
      * **Název virtuálního počítače**: zadejte název virtuálního počítače. Tímto způsobem se zobrazí ve vašem Azure Portal.
      * **Umístění**: vyberte příslušné datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění. Přečtěte si další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obrázek**: ponechte výchozí hodnotu.
      * **Velikost**: Tato hodnota by měla automaticky naplnit velikost, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačů s Windows v Azure](../../virtual-machines/sizes.md).
      * **Uživatelské jméno**: zadejte uživatelské jméno správce. Toto je uživatelské jméno, které použijete k přihlášení k virtuálnímu počítači, a nemusí být stejné jako uživatelské jméno Azure.
      * **Heslo**: zadejte heslo, které budete používat pro přihlášení k virtuálnímu počítači.    
1. Vyberte **Zkontrolovat a vytvořit**.
1. **Zkontrolovat a vytvořit**
   * Ověřte správnost všech zadaných informací. 
   * Vyberte **Vytvořit**.


> [!NOTE]
> * Na software, který je připravený předem načtený na virtuálním počítači, neplatíte poplatky za licence. Za velikost serveru, kterou jste zvolili v kroku **Velikost** , platíte náklady na výpočetní výkon.
> * Zřizování trvá 10 až 20 minut. Stav virtuálního počítače můžete zobrazit na Azure Portal.

## <a name="access-the-dsvm"></a>Přístup k DSVM

Až se virtuální počítač vytvoří a zřídí, postupujte podle kroků uvedených v části [připojení k virtuálnímu počítači založenému na Azure](../../marketplace/azure-vm-create-using-approved-base.md). Použijte přihlašovací údaje účtu správce, které jste nakonfigurovali v kroku **základní informace** o vytvoření virtuálního počítače. 

Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnohé z těchto nástrojů jsou dostupné prostřednictvím dlaždic v nabídce **Start** a ikon na ploše.

<a name="tools"></a>


## <a name="next-steps"></a>Další kroky

* Prozkoumejte nástroje na DSVM otevřením nabídky **Start** .
* Přečtěte si o Azure Machine Learning tak, že si přečtete, [co je Azure Machine Learning?](../overview-what-is-azure-ml.md) a vyzkoušíte si [kurzy](../index.yml).
* Přečtěte si článek [data vědu s Windows Data Science Virtual Machine v Azure](./vm-do-ten-things.md) .
