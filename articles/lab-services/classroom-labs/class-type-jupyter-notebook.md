---
title: Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 5bf54b6975475810650aeaee4b477e60255757bf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75530676"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter

V tomto článku se dozvíte, jak nastavit počítač šablony v testovacích službách pomocí nástrojů potřebných pro učení studentů, jak používat [Jupyter poznámkové bloky](http://jupyter-notebook.readthedocs.io).  Jupyter Poznámkový blok je open source projekt, který umožňuje snadno kombinovat text a spustitelný zdrojový kód [Pythonu](https://www.python.org/) na jednom plátně s názvem Poznámkový blok.  Spuštění poznámkového bloku má za následek lineární záznam vstupů a výstupů.  Tyto výstupy můžou zahrnovat text, tabulky informací, bodové a další.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v [kurzu nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Pro účet testovacího prostředí povolte nastavení popsaná v následující tabulce. Další informace o tom, jak povolit image Marketplace, najdete v tématu [určení imagí z Marketplace dostupných pro tvůrce testovacích prostředí](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
| Obrázek Marketplace | Povolte image [Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) pro použití v rámci vašeho účtu testovacího prostředí. |

>[!TIP]
>Tento článek se zaměřuje na konfiguraci počítače šablony, který používá operační systém Windows Server.  Je také možné nastavit třídu pro datové vědy pomocí poznámkových bloků Python a Jupyter pomocí [Data Science Virtual Machine pro Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) nebo [Data Science Virtual Machine pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) imagí z Azure Marketplace.

### <a name="lab-settings"></a>Nastavení testovacího prostředí

Při nastavování testovacího prostředí učebny použijte nastavení v následující tabulce.  Další informace o tom, jak vytvořit prostředí učebny, najdete v tématu [Nastavení kurzu pro prostředí učebny](tutorial-setup-classroom-lab.md).

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ |
|Velikost virtuálního počítače| Malý grafický procesor (COMPUTE). Tato velikost se nejlépe hodí pro aplikace náročné na výpočetní výkon a síťové prostředky, jako je umělá a obsáhlá výuka. |
|Image virtuálního počítače| Virtuální počítač pro datové vědy – Windows 2016|

## <a name="template-machine"></a>Počítač šablony

Bitová kopie [Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) poskytuje nezbytné architektury a nástroje pro hloubkové učení, které jsou potřebné pro tento typ třídy.  Obrázek obsahuje poznámkové bloky Jupyter a Visual Studio Code.  [Jupyter Poznámkový blok](http://jupyter-notebook.readthedocs.io) je webová aplikace, která umožňuje odborníkům přes data přijímat nezpracované údaje, provádět výpočty a zobrazovat výsledky ve stejném prostředí.  Pro náš počítač šablony bude webová aplikace spuštěna místně.  [Visual Studio Code](https://code.visualstudio.com/) je rozhraní IDE, které poskytuje bohatě interaktivní prostředí při psaní a testování poznámkového bloku.  Další informace najdete v tématu [práce s Jupyter poznámkovým blokem v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

Zbývajícím úkolem pro nastavení třídy je poskytnutí místních poznámkových bloků.  Pokyny k použití ukázek Azure Machine Learning najdete v tématu [jak nakonfigurovat prostředí pomocí poznámkových bloků Jupyter](../../machine-learning/how-to-configure-environment.md#jupyter).  Můžete také zadat vlastní poznámkové bloky na počítači šablony.  Poznámkové bloky budou zkopírovány do všech počítačů studenta při publikování šablony.

## <a name="cost-estimate"></a>Odhad nákladů

Pojďme pro tuto třídu pokrýt možné náklady.  Použijeme třídu 25 studentů.  Naplánovaný čas třídy je 20 hodin.  Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy.  Velikost virtuálního počítače, kterou jsme zvolili, byla malá GPU (COMPUTE), což je 139 jednotek testovacího prostředí.

Zde je příklad možného odhadu nákladů pro tuto třídu:

25 studentů \* (20 naplánovaných hodin + 10 hodin) \* 139 jednotek testovacího prostředí \* 0,01 USD za hodinu = 1042,5 USD

Další podrobnosti o cenách najdete v článku [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

V tomto článku jsme vás provedl prostřednictvím kroků pro vytvoření testovacího prostředí pro třídu notebooků Jupyter. Podobné nastavení můžete použít pro jiné třídy strojového učení.

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)
