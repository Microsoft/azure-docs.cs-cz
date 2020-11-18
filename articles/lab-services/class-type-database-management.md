---
title: Nastavení testovacího prostředí pro učení správy databází pro relační databáze | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro učení správy relačních databází.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: bed43e586beff890f0aa8947140ae7d8e50439f3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659824"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Nastavení testovacího prostředí pro správu databází pro relační databáze

Tento článek popisuje, jak nastavit testovací prostředí pro třídu správy základních databází v Azure Lab Services. Koncepty databází jsou jedním z úvodních kurzů, které jsou v rámci vysokoškolského ministerstva v oblasti počítačové vědy. Jazyk SQL (Structured Query Language) (SQL) je mezinárodní standard. SQL je standardním jazykem pro relaci správy databází, včetně přidávání, přístupu a správy obsahu v databázi.  Je nejužitečnější pro své rychlé zpracování, prověřenou spolehlivost, jednoduchost a flexibilitu použití.

V tomto článku ukážeme, jak nastavit šablonu virtuálního počítače v testovacím prostředí s databázovým serverem MySQL i serverem SQL Server 2019.  [MySQL](https://www.mysql.com/) je volně dostupný open source systém pro správu relačních databází (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) je nejnovější verze RDBMS Microsoftu.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete. Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v [kurzu nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Pro účet testovacího prostředí povolte nastavení popsaná v následující tabulce. Další informace o tom, jak povolit image Marketplace, najdete v tématu [určení imagí z Marketplace dostupných pro tvůrce testovacích prostředí](./specify-marketplace-images.md).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
|Image z Marketplace| Pro použití v rámci vašeho účtu testovacího prostředí povolte image SQL Server 2019 Standard na Windows serveru 2019.|

### <a name="lab-settings"></a>Nastavení testovacího prostředí

Při nastavování testovacího prostředí učebny použijte nastavení v následující tabulce.  Další informace o tom, jak vytvořit prostředí učebny, najdete v tématu [Nastavení kurzu pro prostředí učebny](tutorial-setup-classroom-lab.md).

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ |
|Velikost virtuálního počítače| Střední. Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy.|
|Image virtuálního počítače| SQL Server 2019 standard v systému Windows Server 2019|

## <a name="template-machine-configuration"></a>Konfigurace počítače šablony

Pokud chcete nainstalovat MySQL na Windows Server 2019, můžete postupovat podle kroků uvedených v části [instalace a spuštění MySQL Community serveru na virtuálním počítači](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%252fazure%252fvirtual-machines%252fwindows%252fclassic%252ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

V imagi virtuálního počítače, kterou jsme při vytváření nového testovacího prostředí zvolili, je předinstalovaná SQL Server 2019.

## <a name="cost-estimate"></a>Odhad nákladů

Pojďme pro tuto třídu pokrýt možné náklady.  Použijeme třídu 25 studentů.  Naplánovaný čas třídy je 20 hodin.  Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy.  Velikost virtuálního počítače, kterou jsme zvolili, byla střední, což je 42 jednotek testovacího prostředí.

Zde je příklad možného odhadu nákladů pro tuto třídu:

25 studentů \* (20 naplánovaných hodin + 10 hodin kvóty) \* 0,42 USD za hodinu = 315,00 USD

Další podrobnosti o cenách najdete v článku [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

Tento článek vás vás provedl kroky potřebnými k vytvoření testovacího prostředí pro základní koncepty správy databází pomocí MySQL i SQL Server. Podobné nastavení můžete použít pro jiné třídy databází.

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)