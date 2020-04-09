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
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879577"
---
# <a name="lab-account-setup-guide"></a>Průvodce nastavením účtu lab

Jako první krok by správci měli nastavit účet testovacího prostředí v rámci vašeho předplatného Azure. Laboratorní účet je kontejner pro vaše učebny laboratoře a trvá jen několik minut nastavit.

## <a name="understand-your-schools-lab-account-requirements"></a>Seznamte se s požadavky na laboratorní účet vaší školy

Chcete-li pochopit, jak nakonfigurovat účet testovacího prostředí na základě potřeb vaší školy, měli byste zvážit tyto otázky.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Mám přístup k předplatnému Azure?

K vytvoření účtu testovacího prostředí potřebujete přístup k předplatnému Azure, které je nakonfigurované pro vaši školu. Vaše škola může mít jedno nebo více předplatných. Předplatné slouží ke správě fakturace a zabezpečení pro všechny prostředky a služby Azure, včetně testovacích účtů.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Kolik laboratorních účtů je třeba vytvořit?

Chcete-li začít rychle, vytvořte jeden účet testovacího prostředí a později vytvořte další účty testovacího prostředí podle potřeby. Například nakonec můžete mít jeden účet testovacího prostředí na oddělení.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kdo by měl být vlastníky a přispěvateli účtu testovacího prostředí?

Správci jsou obvykle vlastníky a přispěvateli pro účet testovacího prostředí. Jsou zodpovědní za správu zásad, které platí pro všechna testovací prostředí obsažená v účtu testovacího prostředí. Osoba, která vytvoří účet testovacího prostředí, je automaticky vlastníkem. Můžete přidat další vlastníky a přispěvatele, obvykle z tenanta Azure Active Directory (Azure AD) přidruženého k vašemu předplatnému. To může být užitečné pro pomoc při správě účtu testovacího prostředí přiřazením role vlastníka nebo přispěvatele na úrovni účtu testovacího prostředí.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Kdo bude moci vytvářet a spravovat laboratoře?

Můžete se rozhodnout, že správci a členové fakulty budou vytvářet a spravovat testovací prostředí. Tito uživatelé (obvykle z tenanta Azure AD přidruženého k vašemu předplatnému) jsou přiřazeni k roli Tvůrce testovacího prostředí v rámci účtu testovacího prostředí.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Chcete dát tvůrcům testovacího prostředí možnost ukládat obrázky, které lze sdílet v rámci testovacích prostředí?

Sdílená galerie obrázků je úložiště, které můžete použít k ukládání a sdílení obrázků. Pokud máte několik tříd, které potřebují stejné obrázky, tvůrci testovacího prostředí můžete vytvořit obrázek jednou a sdílet je mezi laboratořemi. Chcete-li však začít, nemusíte nutně potřebovat sdílenou galerii obrázků, protože ji můžete vždy přidat později.

Pokud jste na tuto otázku odpověděli "ano", musíte k účtu testovacího prostředí vytvořit nebo připojit sdílenou galerii obrázků. Pokud jste odpověděli "Nevím", můžete toto rozhodnutí odložit na později.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Které image na Azure Marketplace budou vaše učebny používat?

Azure Marketplace poskytuje stovky ibi, které můžete povolit, aby tvůrci testovacího prostředí mohli použít image k vytvoření testovacího prostředí. Některé obrázky mohou obsahovat vše, co laboratoř již potřebuje. V ostatních případech můžete použít bitovou kopii jako výchozí bod a tvůrce testovacího prostředí jej pak může přizpůsobit instalací dalších aplikací nebo nástrojů.

Pokud nevíte, které obrázky budete muset použít, můžete se k tomu vrátit později, abyste je povolili. Také nejlepší způsob, jak zjistit, které obrázky jsou k dispozici, je nejprve vytvořit účet testovacího prostředí. Získáte tak přístup, takže si můžete prohlédnout seznam dostupných obrázků a jejich obsah.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Musí mít virtuální počítače testovacího prostředí přístup k jiným prostředkům Azure nebo místního prostředí?

Při nastavovat účet testovacího prostředí, máte také možnost peer s virtuální sítí. Chcete-li se rozhodnout, zda to potřebujete, zvažte následující otázky:

- **Potřebujete poskytnout přístup k licenčnímu serveru?**
  
   Pokud plánujete používat image Azure Marketplace, náklady na licenci operačního systému jsou sdružené do cen za laboratorní služby. Proto není nutné poskytovat licence pro samotný operační systém. Pro další software a aplikace, které jsou nainstalovány, je však nutné zadat licenci podle potřeby.

- **Potřebují virtuální počítačové s testovacím prostředím přístup k dalším místním prostředkům, jako je sdílená složka nebo databáze?**

   Vytvoření virtuální sítě, která poskytuje přístup k místním prostředkům, obvykle pomocí brány virtuální sítě site-to-site. Pokud nemáte nakonfigurovanou virtuální síť, musíte do toho investovat další čas.

- **Potřebují virtuální počítače testovacího prostředí přístup k dalším prostředkům Azure, které jsou umístěné ve virtuální síti?**

   Pokud potřebujete přístup k prostředkům Azure, které *nejsou* zabezpečené v rámci virtuální sítě, pak můžete přistupovat k těmto prostředkům prostřednictvím veřejného internetu, aniž byste dělali jakýkoli partnerský vztah.

Pokud jste odpověděli "ano" na jednu nebo více otázek, budete muset peer účet testovacího prostředí do virtuální sítě. Pokud jste odpověděli "Nevím", pak můžete odložit toto rozhodnutí na později. Po vytvoření účtu testovacího prostředí můžete vždy zvolit partnerský vztah virtuální sítě.

## <a name="set-up-your-lab-account"></a>Nastavení účtu testovacího prostředí

Až porozumíte požadavkům na účet testovacího prostředí, můžete ho nastavit.

1. **Vytvořte si účet v testovacím prostředí.** Pokyny naleznete v kurzu o [vytvoření účtu testovacího prostředí.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)

   Když vytváříte účet testovacího prostředí, může být užitečné seznámit se s prostředky Azure, které se týkají. Další informace najdete v těchto článcích:

   - [Předplatné](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Skupina prostředků](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Laboratorní účet](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Učebna laboratoř](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Výběr oblasti a umístění](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Pokyny k pojmenování pro prostředky](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Přidejte uživatele do role tvůrce testovacího prostředí.** Pokyny naleznete v [tématu přidání uživatelů do role tvůrce testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Další informace o různých rolích, které lze přiřadit uživatelům, kteří budou spravovat účty testovacího prostředí a testovací prostředí, naleznete v [příručce ke správě identity](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Připojte se k partnerské virtuální síti.** Pokyny najdete v [tématu připojení sítě testovacího prostředí k partnerské virtuální síti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Možná budete také muset odkazovat na pokyny ke [konfiguraci rozsahu adres virtuálních počítačích testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Povolte a zkontrolujte obrázky.** Pokyny najdete v [tématu povolení image Azure Marketplace pro tvůrce testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Pokud chcete zkontrolovat obsah každé image Azure Marketplace, vyberte název bitové kopie. Například následující snímek obrazovky zobrazuje podrobnosti o obrázku virtuálního účtu Ubuntu Data Science:

   ![Snímek obrazovky s obrázky Recenze Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Pokud máte k účtu testovacího prostředí připojenou sdílenou galerii obrázků a chcete povolit sdílení vlastních obrázků tvůrci testovacího prostředí, proveďte podobné kroky jako na následujícím snímku obrazovky:

   ![Snímek obrazovky s povolením vlastních obrázků ve sdílené galerii obrázků](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Další kroky

- [Správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)

- [Průvodce nastavením učebny v laboratoři](setup-guide.md)
