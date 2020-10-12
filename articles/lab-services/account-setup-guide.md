---
title: Průvodce nastavením akcelerovaného účtu testovacího prostředí pro Azure Lab Services
description: Tato příručka správcům pomáhá rychle nastavit účet testovacího prostředí pro použití v rámci své školy.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c186560b27ebcb543a23785dc5fbc556614f64b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445419"
---
# <a name="lab-account-setup-guide"></a>Průvodce nastavením účtu testovacího prostředí

V prvním kroku by správci měli v rámci vašeho předplatného Azure nastavit účet testovacího prostředí. Účet testovacího prostředí je kontejner pro vaše učebny cvičení a nastavuje se jenom pár minut.

## <a name="understand-your-schools-lab-account-requirements"></a>Princip požadavků na školní účet školy

Pokud chcete pochopit, jak nakonfigurovat účet testovacího prostředí podle potřeb školy, měli byste tyto otázky vzít v úvahu.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Mám přístup k předplatnému Azure?

K vytvoření účtu testovacího prostředí potřebujete přístup k předplatnému Azure, které je nakonfigurované pro vaši školu. Vaše škola může mít jeden nebo víc předplatných. Předplatné se používá ke správě fakturace a zabezpečení pro všechny prostředky a služby Azure, včetně účtů testovacího prostředí.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Kolik účtů testovacího prostředí je potřeba vytvořit?

Pokud chcete rychle začít, vytvořte jeden účet testovacího prostředí a později podle potřeby vytvořte další účty testovacího prostředí. Můžete například nakonec mít jeden účet testovacího prostředí pro každé oddělení.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kdo by měl být vlastníky a přispěvateli účtu testovacího prostředí?

Správci jsou obvykle vlastníci a Přispěvatelé pro účet testovacího prostředí. Zodpovídá za správu zásad, které se vztahují na všechny laboratoře obsažené v rámci účtu testovacího prostředí. Osoba, která vytváří účet testovacího prostředí, je automaticky vlastníkem. Můžete přidat další vlastníky a přispěvatele, obvykle z klienta Azure Active Directory (Azure AD) přidruženého k vašemu předplatnému. To může být užitečné při správě účtu testovacího prostředí přiřazením role vlastníka nebo přispěvatele na úrovni účtu testovacího prostředí.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Kdo bude moci vytvářet a spravovat laboratoře?

Můžete zvolit, aby měli správci a členové vyučujícího možnost vytvářet a spravovat laboratoře. Tito uživatelé (obvykle z klienta Azure AD přidruženého k vašemu předplatnému) jsou přiřazeni do role testovacího prostředí v rámci účtu testovacího prostředí.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Chcete dát tvůrcům testovacího prostředí možnost ukládat obrázky, které se dají sdílet napříč laboratořemi?

Galerie sdílených imagí je úložiště, které můžete použít k ukládání a sdílení imagí. Pokud máte několik tříd, které potřebují stejné image, tvůrci testovacího prostředí můžou image vytvořit jednou a sdílet je napříč laboratořemi. Pokud ale chcete začít, nemusíte nutně potřebovat galerii sdílených imagí, protože ho můžete kdykoli přidat později.

Pokud k této otázce odpovíte "Ano", budete muset vytvořit nebo připojit galerii sdílených imagí k vašemu účtu testovacího prostředí. Pokud jste odpověděli na "nevím", můžete toto rozhodnutí odložit až později.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Jaké obrázky v Azure Marketplace vaše učebná cvičení používá?

Azure Marketplace poskytuje stovky imagí, které můžete povolit, aby tvůrci testovacího prostředí mohli image použít k vytvoření testovacího prostředí. Některé obrázky můžou zahrnovat všechno, co testovací laboratoř už potřebuje. V jiných případech můžete jako výchozí bod použít obrázek a potom ho tvůrce testovacího prostředí může přizpůsobit instalací dalších aplikací nebo nástrojů.

Pokud si nejste jisti, které image budete potřebovat použít, můžete se kdykoli vrátit k této verzi a povolit je. Nejlepším způsobem, jak zjistit, jaké image jsou k dispozici, je také vytvoření účtu testovacího prostředí. Tím získáte přístup, abyste mohli zkontrolovat seznam dostupných imagí a jejich obsah.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Musí mít virtuální počítače testovacího prostředí přístup k jiným prostředkům Azure nebo místním prostředkům?

Při nastavování účtu testovacího prostředí máte také možnost vytvořit partnerský vztah s virtuální sítí. Pokud se chcete rozhodnout, jestli to budete potřebovat, vezměte v úvahu následující otázky:

- **Potřebujete poskytnout přístup k licenčnímu serveru?**
  
   Pokud plánujete použít Azure Marketplace image, náklady na licenci na operační systém se začlení do cen za služby testovacího prostředí. Proto nemusíte zadávat licence pro samotný operační systém. Pro další nainstalované software a aplikace je však nutné poskytnout licenci podle potřeby.

- **Potřebují testovací virtuální počítače přístup k jiným místním prostředkům, jako je například sdílená složka nebo databáze?**

   Vytvoříte virtuální síť, která poskytuje přístup k místním prostředkům, obvykle pomocí brány virtuální sítě typu Site-to-site. Pokud nemáte nakonfigurovanou virtuální síť, je potřeba pro ně investovat další čas.

- **Potřebují virtuální počítače testovacího prostředí přístup k jiným prostředkům Azure, které se nacházejí ve virtuální síti?**

   Pokud potřebujete přístup k prostředkům Azure, které nejsou *zabezpečené v* rámci virtuální sítě, můžete k těmto prostředkům přistupovat prostřednictvím veřejného Internetu bez jakýchkoli partnerských vztahů.

Pokud jste na jednu nebo více otázek odpověděli na Ano, budete muset vytvořit Partnerský účet pro virtuální síť. Pokud jste odpověděli na "nevím", pak můžete toto rozhodnutí odložit až později. Po vytvoření účtu testovacího prostředí se můžete vždy rozhodnout, že se má vytvořit partnerský vztah k virtuální síti.

## <a name="set-up-your-lab-account"></a>Nastavení účtu testovacího prostředí

Po pochopení požadavků vašeho účtu testovacího prostředí jste připraveni ho nastavit.

1. **Vytvořte účet testovacího prostředí.** Pokyny najdete v kurzu [Vytvoření účtu testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .

   Při vytváření účtu testovacího prostředí vám může být užitečné se seznámit s prostředky Azure, které jsou v ní spojené. Další informace najdete v následujících článcích:

   - [Předplatné](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Skupina prostředků](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Účet testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Testovací prostředí pro učebnu](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Výběr oblasti a umístění](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Pokyny pro pojmenování prostředků](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Přidejte uživatele do role testovacího tvůrce.** Pokyny najdete v tématu [Přidání uživatelů do role testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Další informace o různých rolích, které je možné přiřadit uživatelům, kteří budou spravovat účty testovacího prostředí a Labs, najdete v [příručce ke správě identity](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Připojte se k partnerské virtuální síti.** Pokyny najdete v tématu [propojení sítě testovacího prostředí s partnerskými virtuálními sítěmi](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Může se také vyžadovat, abyste odkazovali na pokyny týkající se [Konfigurace rozsahu adres virtuálních počítačů v testovacím prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Povolte a zkontrolujte obrázky.** Pokyny najdete v tématu [Povolení imagí Azure Marketplace pro tvůrci testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Chcete-li zkontrolovat obsah jednotlivých Azure Marketplace imagí, vyberte název bitové kopie. Například následující snímek obrazovky ukazuje podrobnosti pro Ubuntu bitovou kopii Data Science VM:

   ![Snímek obrazovky s revizemi Azure Marketplace imagí](./media/setup-guide/review-marketplace-images.png)

   Pokud máte sdílenou galerii imagí připojenou k vašemu účtu testovacího prostředí a chcete povolit sdílení vlastních imagí tvůrci testovacích prostředí, proveďte kroky podobné těm, které jsou uvedené na následujícím snímku obrazovky:

   ![Snímek obrazovky s povolením vlastních imagí v galerii sdílených imagí](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Další kroky

- [Správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)

- [Průvodce nastavením testovacího prostředí pro učebnu](setup-guide.md)
