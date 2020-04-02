---
title: Průvodce nastavením zrychleného laboratorního účtu pro služby Azure Lab Services
description: Tato příručka pomáhá správcům rychle nastavit účet testovacího prostředí pro použití v rámci své školy.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547623"
---
# <a name="lab-account-setup-guide"></a>Průvodce nastavením účtu lab

Prvním krokem, který musí správci dokončit, je nastavení účtu testovacího prostředí v rámci předplatného Azure.  Laboratorní účet je kontejner pro vaše učebny laboratoře a trvá jen několik minut nastavit.

## <a name="understand-your-schools-lab-account-requirements"></a>Seznamte se s požadavky na laboratorní účet vaší školy

Chcete-li pochopit, jak nakonfigurovat účet testovacího prostředí na základě potřeb vaší školy, měli byste zvážit následující otázky:

**Mám přístup k předplatnému Azure?**

K vytvoření účtu testovacího prostředí budete potřebovat přístup k předplatnému Azure, které je nakonfigurované pro vaši školu. vaše škola může mít jedno nebo více předplatných.  Předplatné se používá ke správě fakturace a zabezpečení pro všechny prostředky Azure\služby, které se v něm používají, včetně účtů testovacího prostředí.

**Kolik laboratorních účtů je třeba vytvořit?**

Chcete-li začít rychle, rozumný mů e být vytvořit jeden účet testovacího prostředí a později vytvořit další testovací účty podle potřeby.  Můžete se například vyvinout tak, že budete mít jeden laboratorní účet na oddělení.

**Kdo by měl být vlastníky a přispěvateli účtu testovacího prostředí?**

Správci jsou obvykle vlastníci\přispěvatelé pro účet testovacího prostředí, protože jsou zodpovědní za správu zásad, které se vztahují na všechna testovací prostředí obsažená v účtu testovacího prostředí.  Osoba, která vytvoří účet testovacího prostředí, je automaticky vlastníkem.  Můžete přidat další vlastníky\přispěvatele (obvykle z klienta AAD přidruženého k vašemu předplatnému) a pomoci tak spravovat účet testovacího prostředí přiřazením role Vlastník\Přispěvatel na úrovni účtu testovacího prostředí.

**Kdo bude moci vytvářet\spravovat testovací prostředí?**

Můžete se rozhodnout, že členové administrátorů a\nebo členů fakulty vytvoří a budou spravovat testovací prostředí. tito uživatelé (obvykle z klienta AAD přidruženého k vašemu předplatnému) jsou přiřazeni k roli Tvůrce testovacího prostředí v rámci účtu testovacího prostředí.

**Chcete dát tvůrcům testovacího prostředí možnost ukládat obrázky, které lze sdílet v rámci testovacích prostředí?**

Galerie sdílených obrázků je úložiště, které můžete použít k ukládání a sdílení obrázků.  Výhodou je, že pokud máte několik tříd, které potřebují stejné obrázky, tvůrci testovacího prostředí můžete vytvořit obrázek jednou a sdílet je mezi laboratořemi.  Chcete-li však začít, je naprosto rozumné začít bez galerie sdílených obrázků; a vždy se můžete rozhodnout přidat později.

Pokud jste na tuto otázku odpověděli ano, budete muset vytvořit a\nebo připojit galerii sdílených obrázků k účtu testovacího prostředí.  Pokud jste odpověděli: "Nevím", pak můžete toto rozhodnutí odložit na později.

Pokud máte k účtu testovacího prostředí připojenou sdílenou galerii obrázků

**Které image na Azure Marketplace budou vaše učebny používat?**

Azure Marketplace poskytuje stovky ibi, které můžete povolit, aby tvůrci testovacího prostředí mohli použít image k vytvoření testovacího prostředí.  Některé obrázky mohou obsahovat vše, co laboratoř již potřebuje.  V ostatních případech můžete použít bitovou kopii jako výchozí bod a tvůrce testovacího prostředí jej může přizpůsobit instalací dalších aplikací, nástrojů atd.

Pokud nevíte, které obrázky budete muset použít, můžete se k tomu vrátit později, abyste je povolili.  Nejlepší způsob, jak zjistit, které obrázky jsou k dispozici, je nejprve vytvořit účet testovacího prostředí – získáte tak přístup, abyste si mohli prohlédnout seznam dostupných obrázků a jejich obsah.  Více informací je uvedeno níže.
  
**Musí mít virtuální počítače (VM) testovacího prostředí přístup k jiným prostředkům Azure nebo on-prem?**

Když nastavíte účet testovacího prostředí, máte také možnost peer s virtuální sítí (VNet).  Pokud se chcete rozhodnout, jestli potřebujete spolupracovat s virtuální sítí, zvažte následující otázky:

- **Potřebujete poskytnout přístup k licenčnímu serveru?**
  
   Pokud plánujete používat image Azure Marketplace, náklady na licenci operačního systému jsou spojeny do cen pro služby Lab, takže *nemusíte* poskytovat licence pro samotný operační systém.  Pro další software\aplikace, které jsou nainstalovány, však budete muset poskytnout licenci podle potřeby.

- **Potřebují virtuální počítačové s testovacím prostředím přístup k dalším prostředkům on-prem, jako je například sdílená složka, databáze atd.?**

   Virtuální síť je potřeba vytvořit k poskytování přístupu k prostředkům on-prem, obvykle pomocí brány virtuální sítě site-to-site.  Pokud nemáte nakonfigurovanou virtuální síť, bude potřeba do toho investovat další čas.  Další informace o tom, jak toto nastavení nastavit, naleznete níže.

- **Potřebují virtuální počítače testovacího prostředí přístup k dalším prostředkům Azure, které jsou umístěné v rámci virtuální sítě?**

    Pokud potřebujete přístup k prostředkům Azure, které *nejsou* zabezpečené v rámci virtuální sítě, pak můžete přistupovat k těmto prostředkům prostřednictvím veřejného internetu bez provedení jakékoli partnerské vztahy.

    Pokud jste odpověděli "Ano" na jednu nebo více otázek, budete muset peer účet testovacího prostředí do virtuální sítě.  Pokud jste odpověděli, 'Nevím', pak můžete odložit toto rozhodnutí na později, protože můžete vždy zvolit peer virtuální sítě po vytvoření účtu testovacího prostředí.

## <a name="set-up-your-lab-account"></a>Nastavení účtu testovacího prostředí

Jakmile porozumíte požadavkům na účet testovacího prostředí, jste připraveni jej nastavit.  Podle odkazů v této části se podívejte, jak nastavit účet testovacího prostředí:

1. **Vytvoření účtu testovacího prostředí**

   Pokyny naleznete v kurzu o [vytvoření účtu testovacího prostředí.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)

   Při vytváření účtu testovacího prostředí může být užitečné seznámit se s prostředky Azure, které se týkají; Další informace a pokyny k vytváření těchto zdrojů naleznete v následujícím seznamu:

   - [Předplatné](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Skupina prostředků](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Laboratorní účet](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Učebna Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Výběr oblasti\Umístění](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Pokyny pro pojmenování pro zdroje](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Přidání uživatelů do role Tvůrce testovacího prostředí**

   Pokyny naleznete v kurzu o [přidávání uživatelů do role Tvůrce testovacího prostředí.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)

   Další informace o různých rolích, které lze přiřadit uživatelům, kteří budou spravovat účty testovacího prostředí a testovací prostředí, naleznete v [příručce ke správě identity](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Připojení k partnerské virtuální síti**

   Pokyny najdete v návodu k [připojení k síti testovacího prostředí s druhou virtuální sítí.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)

   Možná budete také muset odkazovat na pokyny ke [konfiguraci rozsahu adres virtuálních počítačích testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Povolení a kontrola obrázků**

    Pokyny najdete v návodu k [povolení irek marketplace pro tvůrce testovacích prostředí.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

    Chcete-li zkontrolovat obsah jednotlivých obrázků marketplace, klikněte na název obrázku.  Podívejte se například na následující snímek obrazovky, který zobrazuje podrobnosti o obrázku virtuálního účtu Ubuntu Data Science:

    ![Projděte si obrázky marketplace](../media/setup-guide/review-marketplace-images.png)

    Pokud máte k účtu testovacího prostředí připojenou galerii sdílených obrázků a chcete povolit sdílení vlastních obrázků tvůrci testovacího prostředí, budete muset provést podobné kroky, jak je znázorněno na následujícím snímku obrazovky:

    ![Povolení vlastních obrázků v Galerii sdílených obrázků](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)

- [Průvodce nastavením učebny v laboratoři](setup-guide.md)
