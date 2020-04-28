---
title: Nastavení testovacího prostředí zaměřeného na hloubkové učení pomocí Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro učení skriptování prostředí v systému Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257755"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services
V tomto článku se dozvíte, jak nastavit testovací prostředí zaměřené na obsáhlý Learning při zpracování přirozeného jazyka (NLP) pomocí Azure Lab Services. Zpracování přirozeného jazyka (NLP) je forma umělé Intelligence (AI), která umožňuje počítačům s překlady, rozpoznáváním řeči a dalšími možnostmi porozumění jazyku.  

Studenti, kteří přebírají třídu NLP, získají virtuální počítač se systémem Linux, aby se dozvěděli, jak použít síťové algoritmy neuronové pro vývoj modelů hloubkového učení, které se používají k analýze napsaného lidského jazyka. 

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Jakmile budete mít předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services, nebo použít existující účet testovacího prostředí. V následujícím kurzu najdete postup vytvoření nového účtu testovacího prostředí: [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).
 
Po vytvoření účtu testovacího prostředí povolte v účtu testovacího prostředí následující nastavení: 

| Nastavení účtu testovacího prostředí | Pokyny |
| ----------- | ------------ |  
| Image Marketplace | Povolte Image Data Science Virtual Machine pro Linux (Ubuntu) pro použití v rámci vašeho účtu testovacího prostředí.  Pokyny najdete v následujícím článku: [určení imagí Marketplace dostupných pro tvůrce testovacích prostředí](specify-marketplace-images.md). | 

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a použijte následující nastavení:

| Nastavení testovacího prostředí | Hodnota/pokyny | 
| ------------ | ------------------ |
| Velikost virtuálního počítače (VM) | Malý grafický procesor (COMPUTE). Tato velikost se nejlépe hodí pro aplikace náročné na výpočetní výkon a síťové prostředky, jako je umělá a obsáhlá výuka. |
| Image virtuálního počítače | [Data Science Virtual Machine pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Tento obrázek poskytuje architektury a nástroje pro hloubkové učení pro Machine Learning a datové vědy. Úplný seznam nainstalovaných nástrojů na tomto obrázku si můžete prohlédnout v následujícím článku: [co je součástí DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Povolit připojení ke vzdálené ploše | Aby. <p>Povolením tohoto nastavení umožníte učitelům a studentům připojit se k jejich Virtual Machines (VM) pomocí vzdálené plochy (RDP).</p><p>**Důležité**: RDP je už nainstalované a nakonfigurované na image data Science Virtual Machine pro Linux. V důsledku toho se můžou učitelé a studenti připojit k virtuálním počítačům přes RDP bez jakýchkoli dalších kroků. Pokud se navíc potřebujete připojit k grafické ploše, již tato image má na virtuálním počítači nainstalovaný [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) . Studenti musí nainstalovat klienta X2Go na své místní počítače a musí používat klienta pro připojení. Další informace najdete v následujících příručkách: <ul><li>[Přístup k Data Science Virtual Machine pro Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Připojení k virtuálnímu počítači šablony pro instalaci balíčků RDP a GUI](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Image Data Science Virtual Machine pro Linux poskytuje nezbytné architektury a nástroje pro hloubkové učení, které jsou potřebné pro tento typ třídy. V důsledku toho je po vytvoření šablony nutné tento postup ještě Neupravovat. Dá se publikovat pro studenty, kteří ho budou používat. Vyberte tlačítko **publikovat** na stránce šablony a publikujte šablonu do testovacího prostředí.  

## <a name="cost"></a>Náklady
Pokud byste chtěli odhadnout náklady na toto testovací prostředí, můžete použít následující příklad: 

Pro třídu 25 studentů s 20 hodinami plánovaného času třídy a 10 hodin pro domácí nebo pracovní účely je cena za testovací prostředí-25 studentů * (20 + 10) hodin × 139 jednotek testovacího prostředí × 0,01 USD za hodinu = 1042,5 USD.

Další podrobnosti o cenách najdete v článku [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
V tomto článku se seznámíte s postupem vytvoření testovacího prostředí pro třídu zpracování přirozeného jazyka. Podobné nastavení můžete použít pro jiné třídy pro hloubkové učení.

## <a name="next-steps"></a>Další kroky
Další kroky jsou běžné pro nastavení testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users). 

