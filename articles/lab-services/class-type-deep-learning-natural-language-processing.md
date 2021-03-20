---
title: Nastavení testovacího prostředí zaměřeného na hloubkové učení pomocí Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí zaměřené na obsáhlý Learning při zpracování přirozeného jazyka (NLP) pomocí Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 01199e76b7b9cb1a6360b5aba010d0cfd8936c23
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251452"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Nastavení testovacího prostředí zaměřeného na obsáhlý Learning při zpracování v přirozeném jazyce pomocí Azure Lab Services
V tomto článku se dozvíte, jak nastavit testovací prostředí zaměřené na obsáhlý Learning při zpracování přirozeného jazyka (NLP) pomocí Azure Lab Services. Zpracování přirozeného jazyka (NLP) je forma umělé Intelligence (AI), která umožňuje počítačům s překlady, rozpoznáváním řeči a dalšími možnostmi porozumění jazyku.  

Studenti, kteří přebírají třídu NLP, získají virtuální počítač se systémem Linux, aby se dozvěděli, jak použít síťové algoritmy neuronové pro vývoj modelů hloubkového učení, které se používají k analýze napsaného lidského jazyka. 

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Jakmile budete mít předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services, nebo použít existující účet testovacího prostředí. V následujícím kurzu najdete postup vytvoření nového účtu testovacího prostředí: [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).
 
Po vytvoření účtu testovacího prostředí povolte v účtu testovacího prostředí následující nastavení: 

| Nastavení účtu testovacího prostředí | Pokyny |
| ----------- | ------------ |  
| Image Marketplace | Povolte Image Data Science Virtual Machine pro Linux (Ubuntu) pro použití v rámci vašeho účtu testovacího prostředí.  Pokyny najdete v následujícím článku: [určení imagí Marketplace dostupných pro tvůrce testovacích prostředí](specify-marketplace-images.md). | 

Podle [tohoto kurzu](tutorial-setup-classroom-lab.md) vytvořte nové testovací prostředí a použijte následující nastavení:

| Nastavení testovacího prostředí | Hodnota/pokyny | 
| ------------ | ------------------ |
| Velikost virtuálního počítače (VM) | **Malý grafický procesor (COMPUTE)**. Tato velikost se nejlépe hodí pro aplikace náročné na výpočetní výkon a síťové prostředky, jako je umělá a obsáhlá výuka. |
| Image virtuálního počítače | [Data Science Virtual Machine pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Tento obrázek poskytuje architektury a nástroje pro hloubkové učení pro Machine Learning a datové vědy. Úplný seznam nainstalovaných nástrojů na tomto obrázku si můžete prohlédnout v následujícím článku: [co je součástí DSVM?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Povolit připojení ke vzdálené ploše | <p>Image pro datové vědy je už nakonfigurovaná tak, aby používala X2Go, aby se učitelé a studenti mohli připojit pomocí vzdálené plochy grafického uživatelského rozhraní.  X2Go nevyžaduje *, aby bylo* povolené nastavení **Povolit připojení ke vzdálené ploše** .  Toto nastavení je potřeba povolit jenom v případě, že se rozhodnete místo toho používat protokol RDP.

>**Důležité**: i když se rozhodnete použít X2Go s imagí pro datové vědy, pokud se rozhodnete místo toho používat protokol RDP, budete se muset poprvé připojit k virtuálnímu počítači Linux pomocí SSH a nainstalovat balíčky RDP a GUI.  Pak se můžete sami nebo studenti připojit k virtuálnímu počítači se systémem Linux pomocí protokolu RDP později.  Další informace najdete v tématu [Povolení grafické vzdálené plochy pro virtuální počítače se systémem Linux](how-to-enable-remote-desktop-linux.md).

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

