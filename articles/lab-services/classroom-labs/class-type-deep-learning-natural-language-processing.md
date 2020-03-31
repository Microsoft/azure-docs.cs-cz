---
title: Nastavení testovacího prostředí zaměřeného na hloubkové učení pomocí služby Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit testovací prostředí pro výuku skriptování prostředí na Linuxu.
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
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109391"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Nastavení testovacího prostředí zaměřeného na hloubkové učení při zpracování přirozeného jazyka pomocí služby Azure Lab Services
Tento článek ukazuje, jak nastavit testovací prostředí zaměřené na hluboké učení v zpracování přirozeného jazyka (NLP) pomocí služby Azure Lab Services. Zpracování přirozeného jazyka (NLP) je forma umělé inteligence (AI), která umožňuje počítačům s překladem, rozpoznáváním řeči a dalšími možnostmi porozumění jazykům.  

Studenti, kteří používají třídu NLP, získají virtuální počítač (VM) linuxového, aby se naučili, jak používat algoritmy neuronových sítí k vývoji modelů hlubokého učení, které se používají pro analýzu psaného lidského jazyka. 

## <a name="lab-configuration"></a>Konfigurace laboratoře
Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile budete mít předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí ve službě Azure Lab Services nebo použít existující účet testovacího prostředí. Podívejte se na následující kurz pro vytvoření nového účtu testovacího prostředí: [Kurz na nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).
 
Po vytvoření účtu testovacího prostředí povolte v účtu testovacího prostředí následující nastavení: 

| Nastavení účtu laboratoře | Pokyny |
| ----------- | ------------ |  
| Obrázky na marketplace | Povolte image virtuálního počítače pro datové vědy pro Linux (Ubuntu) pro použití v rámci vašeho laboratorního účtu.  Pokyny naleznete v následujícím článku: [Zadejte image marketplace, které jsou k dispozici tvůrcům testovacího prostředí](specify-marketplace-images.md). | 

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a použijte následující nastavení:

| Nastavení laboratoře | Hodnota/instrukce | 
| ------------ | ------------------ |
| Velikost virtuálního počítače (VM) | Malý GPU (Výpočetní). Tato velikost je nejvhodnější pro aplikace náročné na výpočetní výkon a sítě, jako je umělá inteligence a hloubkové učení. |
| Obrázek virtuálního virtuálního montova | [Data Science Virtuální stroj pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Tento obrázek poskytuje rámce hlubokého učení a nástroje pro strojové učení a datovou vědu. Chcete-li zobrazit úplný seznam nainstalovaných nástrojů na tomto obrázku, naleznete v následujícím článku: [Co je součástí DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Povolení připojení ke vzdálené ploše | Povolit. <p>Povolení tohoto nastavení umožní učitelům a studentům připojit se ke svým virtuálním počítačům (VM) pomocí vzdálené plochy (RDP).</p><p>**Důležité:** RDP je již nainstalován a nakonfigurován na virtuální monstroj datové vědy pro linuximage. V důsledku toho se učitelé/studenti můžou připojit k virtuálním kvandrovacím službám prostřednictvím programu RDP bez dalších kroků. Také, pokud potřebujete připojit k grafické ploše, tento obrázek již má [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) nainstalován na virtuálním stroji. Studenti musí nainstalovat x2Go klienta na svých místních počítačích a musí používat klienta pro připojení. Další informace naleznete v následujících průvodcích: <ul><li>[Jak získat přístup k virtuálnímu stroji pro datové vědy pro Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Připojení k virtuálnímu virtuálnímu počítače šablony k instalaci balíčků RDP a GUI](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Image virtuálního počítače pro datové vědy pro Linux poskytuje nezbytné architektury pro hluboké učení a nástroje potřebné pro tento typ třídy. V důsledku toho po vytvoření šablony stroje není nutné dále přizpůsobovat. To může být zveřejněna pro studenty k použití. Chcete-li šablonu publikovat v testovacím prostředí, vyberte tlačítko **Publikovat** na stránce šablony.  

## <a name="cost"></a>Náklady
Pokud chcete odhadnout náklady na tuto testovací prostředí, můžete použít následující příklad: 

Pro třídu 25 studentů s 20 hodinami plánovaného času ve třídě a 10 hodinami kvóty pro domácí úkoly nebo úkoly by cena za laboratoř byla - 25 studentů * (20 + 10) hodin * 139 laboratorních jednotek * 0,01 USD za hodinu = 1042,5 USD

Další podrobnosti o cenách najdete v tématu [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr
Tento článek vás provede kroky k vytvoření testovacího prostředí pro zpracování přirozeného jazyka třídy. Podobné nastavení můžete použít pro jiné třídy hlubokého učení.

## <a name="next-steps"></a>Další kroky
Další kroky jsou společné pro nastavení libovolného testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mailové registrační odkazy na studenty](how-to-configure-student-usage.md#send-invitations-to-users). 

