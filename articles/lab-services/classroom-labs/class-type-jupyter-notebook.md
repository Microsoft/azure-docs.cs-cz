---
title: Nastavení testovacího prostředí pro výuku datové vědy pomocí pythonových a jupyterových poznámkových bloků | Dokumenty společnosti Microsoft
description: Naučte se, jak nastavit testovací prostředí pro výuku datové vědy pomocí Pythonu a Jupyterových poznámkových bloků.
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
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444107"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Nastavení testovacího prostředí pro výuku datové vědy pomocí pythonových a jupyterových poznámkových bloků

Tento článek popisuje, jak nastavit šablonu stroj v Lab Services s nástroji potřebnými k učení studentů, jak používat [Jupyter notebooky](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks je open source projekt, který vám umožní snadno kombinovat bohatý text a spustitelný zdrojový kód [Pythonu](https://www.python.org/) na jednom plátně nazývaném notebook.  Spuštění poznámkového bloku má za následek lineární záznam vstupů a výstupů.  Tyto výstupy mohou zahrnovat text, tabulky informací, bodové obrázky a další.

## <a name="lab-configuration"></a>Konfigurace laboratoře

Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile získáte předplatné Azure, můžete si vytvořit nový účet testovacího prostředí ve službách Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí naleznete [v kurzu k nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete také použít existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu laboratoře

Povolte nastavení popsaná v následující tabulce pro účet testovacího prostředí. Další informace o povolení bitových kopií marketplace najdete [v tématu určení bitových kopií Marketplace, které jsou k dispozici tvůrcům testovacího prostředí](specify-marketplace-images.md).

| Nastavení účtu laboratoře | Pokyny |
| ------------------- | ------------ |
| Obrázek tržiště | Povolte virtuální počítač pro datové vědy – bitovou kopii [Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) pro použití v rámci vašeho účtu testovacího prostředí. |

>[!TIP]
>Tento článek se zaměří na konfiguraci počítače se šablonami, který používá operační systém Windows Server.  Je také možné nastavit třídu datové vědy s pythonovými a jupyterovými notebooky pomocí [virtuálního počítače Datové vědy pro Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) nebo [virtuálního počítače pro datové vědy pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) z Azure Marketplace.

### <a name="lab-settings"></a>Nastavení laboratoře

Při nastavování učební laboratoře použijte nastavení v následující tabulce.  Další informace o vytvoření učebny naleznete v [tématu nastavení výukového programu pro testovací prostředí ve třídě](tutorial-setup-classroom-lab.md).

| Nastavení laboratoře | Hodnota/instrukce |
| ------------ | ------------------ |
|Velikost virtuálního počítače| Malý GPU (Výpočetní). Tato velikost je nejvhodnější pro aplikace náročné na výpočetní výkon a sítě, jako je umělá inteligence a hloubkové učení. |
|Image virtuálního počítače| Virtuální počítač pro datové vědy – Windows 2016|

## <a name="template-machine"></a>Šablona stroj

Image [Virtuální počítač pro datové vědy – Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) poskytuje nezbytné architektury a nástroje pro hluboké učení potřebné pro tento typ třídy.  Obrázek obsahuje Poznámkové bloky Jupyter a kód sady Visual Studio.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) je webová aplikace, která umožňuje datovým vědcům přijímat nezpracovaná data, spouštět výpočty a vidět výsledky ve stejném prostředí.  Pro náš šablonový stroj bude webová aplikace spuštěna místně.  [Visual Studio Code](https://code.visualstudio.com/) je ide, které poskytuje bohaté interaktivní prostředí při psaní a testování poznámkového bloku.  Další informace naleznete v [tématu Práce s poznámkovými bloky Jupyter v kódu sady Visual Studio](https://code.visualstudio.com/docs/python/jupyter-support).

Zbývající úkol nastavit třídu je poskytnout místní poznámkové bloky.  Pokyny, jak používat ukázky Azure Machine Learning, najdete v [tématu jak nakonfigurovat prostředí s Jupyter notebooky](../../machine-learning/how-to-configure-environment.md#jupyter).  Můžete také zadat vlastní poznámkové bloky na šabloně počítače.  Poznámkové bloky budou při publikování šablony zkopírovány do všech studentských počítačů.

## <a name="cost-estimate"></a>Odhad nákladů

Pokryjme možný odhad nákladů pro tuto třídu.  Použijeme třídu 25 studentů.  K dispozici je 20 hodin plánovaného času ve třídě.  Každý student také získá kvótu 10 hodin pro domácí úkoly nebo úkoly mimo plánovaný čas kurzu.  Velikost virtuálního počítače, kterou jsme zvolili, byla malá GPU (výpočetní), což je 139 laboratorních jednotek.

Zde je příklad možného odhadu nákladů pro tuto třídu:

25 \* studentů (20 plánovaných hodin + \* 10 kvótových \* hodin) 139 laboratorních jednotek 0,01 USD za hodinu = 1042,5 USD

Další podrobnosti o cenách najdete v tématu [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

V tomto článku jsme prošli kroky k vytvoření testovacího prostředí pro třídu Jupyter Notebooks. Podobné nastavení můžete použít pro jiné třídy strojového učení.

## <a name="next-steps"></a>Další kroky

Další kroky jsou společné pro nastavení libovolného testovacího prostředí.

- [Vytvoření a správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mailové registrační odkazy pro studenty](how-to-configure-student-usage.md#send-invitations-to-users)
