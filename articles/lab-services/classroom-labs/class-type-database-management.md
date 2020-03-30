---
title: Nastavení testovacího prostředí pro výuku správy databází pro relační databáze | Dokumenty společnosti Microsoft
description: Naučte se, jak nastavit testovací prostředí pro výuku správy relačních databází.
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
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469914"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Nastavení testovacího prostředí pro výuku správy databází pro relační databáze

Tento článek popisuje, jak nastavit testovací prostředí pro třídu správy základních databází ve službě Azure Lab Services. Databáze pojmy jsou jedním z úvodních kurzů vyučovaných ve většině oddělení informatiky na vysoké škole. Strukturovaný dotazovací jazyk (SQL) je mezinárodní standard. SQL je standardní jazyk pro správu relačnídatabáze včetně přidávání, přístupu a správy obsahu v databázi.  To je nejvíce známý pro jeho rychlé zpracování, osvědčená spolehlivost, snadnost, a flexibilitu použití.

V tomto článku ukážeme, jak nastavit šablonu virtuálního počítače v testovacím prostředí se serverem MySQL Database Server a serverem SQL Server 2019.  [MySQL](https://www.mysql.com/) je volně dostupný open source Relační databázový management (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) je nejnovější verze rdbms společnosti Microsoft.

## <a name="lab-configuration"></a>Konfigurace laboratoře

Chcete-li nastavit toto testovací prostředí, budete potřebovat předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. Jakmile získáte předplatné Azure, můžete si vytvořit nový účet testovacího prostředí ve službách Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí naleznete [v tématu Výuka nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete také použít existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu laboratoře

Povolte nastavení popsaná v následující tabulce pro účet testovacího prostředí. Další informace o povolení bitových kopií marketplace najdete [v tématu Určení bitových kopií marketplace dostupných tvůrcům testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Nastavení účtu laboratoře | Pokyny |
| ------------------- | ------------ |
|Obrázek tržiště| Povolte bitovou kopii SQL Server 2019 Standard v systému Windows Server 2019 pro použití v rámci účtu testovacího prostředí.|

### <a name="lab-settings"></a>Nastavení laboratoře

Při nastavování učební laboratoře použijte nastavení v následující tabulce.  Další informace o vytvoření učebny naleznete v [tématu nastavení výukového programu pro testovací prostředí ve třídě](tutorial-setup-classroom-lab.md).

| Nastavení laboratoře | Hodnota/instrukce |
| ------------ | ------------------ |
|Velikost virtuálního počítače| Střední. Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy.|
|Image virtuálního počítače| SQL Server 2019 Standard na Windows Serveru 2019|

## <a name="template-machine-configuration"></a>Konfigurace počítače šablony

Chcete-li nainstalovat MySQL na Windows Server 2019, můžete postupovat podle kroků uvedených v [části Instalace a spuštění komunitního serveru MySQL na virtuálním počítači](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 je předinstalovaný v bitové kopii virtuálního počítače, kterou jsme zvolili při vytváření nové laboratoře.

## <a name="cost-estimate"></a>Odhad nákladů

Pokryjme možný odhad nákladů pro tuto třídu.  Použijeme třídu 25 studentů.  K dispozici je 20 hodin plánovaného času ve třídě.  Každý student také získá kvótu 10 hodin pro domácí úkoly nebo úkoly mimo plánovaný čas kurzu.  Velikost virtuálního počítače, kterou jsme zvolili, byla střední, což je 42 laboratorních jednotek.

Zde je příklad možného odhadu nákladů pro tuto třídu:

25 \* studentů (20 plánovaných hodin + \* 10 hodin kvóty) 0.42 USD za hodinu = 315.00 USD

Další podrobnosti o cenách najdete v tématu [Ceny služeb Azure Lab](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Závěr

Tento článek vás provede kroky nezbytné k vytvoření testovacího prostředí pro základní koncepty správy databáze pomocí MySQL a SQL Server. Podobné nastavení můžete použít pro jiné třídy databází.

## <a name="next-steps"></a>Další kroky

Další kroky jsou společné pro nastavení libovolného testovacího prostředí.

- [Vytvoření a správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mailové registrační odkazy pro studenty](how-to-configure-student-usage.md#send-invitations-to-users)
