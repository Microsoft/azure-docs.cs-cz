---
title: Průvodce nastavením akcelerovaného účtu testovacího prostředí pro Azure Lab Services
description: Tato příručka správcům pomáhá rychle nastavit účet testovacího prostředí pro použití v rámci své školy.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669385"
---
# <a name="lab-account-setup-guide"></a>Průvodce nastavením účtu testovacího prostředí
Pokud jste správce, musíte před nastavením prostředí Azure Lab Services vytvořit v rámci předplatného Azure *účet testovacího* prostředí. Účet testovacího prostředí je kontejner pro jednu nebo více cvičení a nastavení trvá jenom několik minut.

Tato příručka obsahuje tři části:
-  Předpoklady
-  Plánování nastavení účtu testovacího prostředí
-  Nastavení účtu testovacího prostředí

## <a name="prerequisites"></a>Předpoklady
Následující části popisují, co musíte udělat, než budete moct nastavit účet testovacího prostředí.


### <a name="access-your-azure-subscription"></a>Přístup k předplatnému Azure
K vytvoření účtu testovacího prostředí potřebujete přístup k předplatnému Azure, které už máte nastavené pro vaši školu. Vaše škola může mít jeden nebo víc předplatných. Předplatné se používá ke správě fakturace a zabezpečení pro všechny prostředky a služby Azure, včetně účtů testovacího prostředí.  Předplatná Azure jsou obvykle spravovaná vaším IT oddělením.  Další informace najdete v části "předplatné" tématu [Azure Lab Services – příručka pro správce](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Odhad počtu virtuálních počítačů a velikostí virtuálních počítačů, které potřebujete
Je důležité, abyste věděli, kolik [virtuálních počítačů a velikostí virtuálních](./administrator-guide.md#vm-sizing) počítačů vaše školní laboratoř vyžaduje. 

Pokyny k strukturování cvičení a imagí najdete v blogovém příspěvku, [který se přesouvá z fyzického testovacího prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Další informace o tom, jak strukturovat testovací prostředí, najdete v části "laboratoře" [Azure Lab Services příručce pro správce](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Vysvětlení omezení virtuálních počítačů s předplatným a kapacity regionálního virtuálního počítače
Po odhadu počtu virtuálních počítačů a velikostí virtuálních počítačů pro vaše cvičení budete potřebovat:

- Ujistěte se, že limit kapacity vašeho předplatného Azure umožňuje počet virtuálních počítačů a velikost virtuálních počítačů, které v laboratoři plánujete použít.
- Vytvořte účet testovacího prostředí v oblasti, která má dostatečnou dostupnou kapacitu virtuálního počítače.

Další informace najdete v tématu [omezení pro odběr virtuálních počítačů a regionální kapacitu](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Rozhodněte, kolik účtů testovacího prostředí se má vytvořit.

Pokud chcete rychle začít, vytvořte v rámci vlastní skupiny prostředků jeden účet testovacího prostředí.  Později můžete podle potřeby vytvořit další účty testovacího prostředí a skupiny prostředků. Můžete například nakonec mít jeden účet testovacího prostředí a skupinu prostředků pro každé oddělení jako způsob, jak jasně oddělit náklady. 

Další informace o účtech testovacího prostředí, skupinách prostředků a rozdělení nákladů najdete v tématech:
- Část "skupina prostředků" v [Azure Lab Services – příručka pro správce](./administrator-guide.md#resource-group)
- Část "účet testovacího prostředí" [Azure Lab Services příručka pro správce](./administrator-guide.md#lab-account) 
- [Správa nákladů pro Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Plánování nastavení účtu testovacího prostředí

Při plánování nastavení účtu testovacího prostředí Vezměte v úvahu následující otázky.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Kdo by měli být vlastníci a přispěvatelé účtu testovacího prostředí?

Správci IT ve vaší škole se obvykle zavazují o rolích vlastníka a přispěvatele pro účet testovacího prostředí. Tyto role zodpovídá za správu zásad, které se vztahují na všechny laboratoře v účtu testovacího prostředí. Osoba, která vytváří účet testovacího prostředí, je automaticky vlastníkem. Můžete přidat další vlastníky a přispěvatele z klienta Azure Active Directory (Azure AD), který je přidružený k vašemu předplatnému. 

Další informace o vlastníkovi účtu testovacího prostředí a rolích přispěvatele najdete v části Správa identit [Azure Lab Services příručce pro správce](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Uživatelé testovacího prostředí uvidí jenom jeden seznam virtuálních počítačů, ke kterým mají přístup přes klienty Azure AD v Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Kdo bude moci vytvářet Labs?

Můžete se rozhodnout, že váš IT tým nebo členové vyučujícího můžou vytvářet Labs. Pokud chcete vytvořit Labs, pak tyto osoby přiřadíte do role testovacího prostředí v rámci účtu testovacího prostředí. Tuto roli obvykle přiřadíte z klienta služby Azure AD, který je přidružený k vašemu školnímu předplatnému. Ten, kdo vytvoří testovací prostředí, se automaticky přiřadí jako vlastník testovacího prostředí.  

Další informace o roli tvůrce testovacího prostředí najdete v části "Správa identit" tématu [Azure Lab Services – příručka pro správce](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Kdo bude moct vlastnit a spravovat cvičení?

Můžete se také rozhodnout, že členové IT a vyučující own\manage Labs, *aniž by* jim umožnili vytvářet Labs.  V takovém případě se uživatelům z tenanta Azure AD vašeho předplatného přiřadí pro existující cvičení buď vlastník, nebo Přispěvatel.  

Další informace o vlastníkovi testovacího prostředí a rolích přispěvatele najdete v části "Správa identit" tématu [Azure Lab Services – příručka pro správce](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Chcete ukládat obrázky a sdílet je napříč laboratořemi?

Galerie sdílených imagí je služba, kterou můžete použít k ukládání a sdílení imagí. Pro třídy, které potřebují použít stejný obrázek, můžou tvůrci testovacích prostředí vytvořit image a pak ji exportovat do galerie sdílených imagí.  Po exportu obrázku do galerie sdílených imagí se dá použít k vytvoření nové laboratoře.

Můžete chtít vytvořit své image ve svém fyzickém prostředí a pak je importovat do galerie sdílených imagí. Další informace najdete v příspěvku blogu [Import vlastní image do galerie sdílených imagí](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Pokud se rozhodnete používat sdílenou službu Galerie imagí, budete muset vytvořit nebo připojit galerii sdílených imagí k účtu testovacího prostředí. Toto rozhodnutí teď můžete odložit, protože sdílená Galerie imagí se dá kdykoli připojit k účtu testovacího prostředí.  

Další informace naleznete v tématu:
- Část "Galerie sdílených imagí" v [Azure Lab Services – příručka pro správce](./administrator-guide.md#shared-image-gallery)
- Část "ceny" [Azure Lab Services – příručka pro správce](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Které image v Azure Marketplace budou vaše cvičení používat?

Azure Marketplace poskytuje stovky imagí, které můžete povolit, aby je tvůrci testovacího prostředí mohli použít k vytvoření svých cvičení. Některé obrázky můžou zahrnovat všechno, co testovací laboratoř už potřebuje. V jiných případech můžete jako výchozí bod použít obrázek a potom ho tvůrce testovacího prostředí může přizpůsobit instalací dalších aplikací nebo nástrojů.

Pokud si nejste jisti, které image potřebujete, můžete se později vrátit a povolit je. Nejlepším způsobem, jak zjistit, které image jsou k dispozici, je nejprve vytvořit účet testovacího prostředí. Tím získáte přístup, abyste mohli zkontrolovat seznam dostupných imagí a jejich obsah.  

Další informace najdete v tématu [určení Azure Marketplacech imagí, které jsou k dispozici pro tvůrce testovacích prostředí](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Potřebují testovací virtuální počítače přístup k jiným prostředkům Azure nebo místním prostředkům?

Při nastavování účtu testovacího prostředí můžete také vytvořit Partnerský účet pro virtuální síť.  Pamatujte, že virtuální síť a účet testovacího prostředí musí být umístěny ve stejné oblasti.  Pokud se chcete rozhodnout, jestli potřebujete vytvořit partnerský vztah k virtuální síti, vezměte v úvahu následující scénáře:

- **Přístup k licenčnímu serveru**
  
   Když použijete Azure Marketplace image, náklady na licenci na operační systém se začlení do cen za služby testovacího prostředí. Nemusíte ale poskytovat licence pro samotný operační systém. Pro další software a aplikace, které jsou nainstalovány, je nutné poskytnout licenci podle potřeby.  Přístup k licenčnímu serveru:
   - Můžete se rozhodnout, že se budete chtít připojit k místnímu licenčnímu serveru.  Připojení k místnímu licenčnímu serveru vyžaduje další nastavení.
   - Další možností, která je pro nastavení rychlejší, je vytvoření licenčního serveru, který hostuje na virtuálním počítači Azure.  Virtuální počítač Azure je umístěný ve virtuální síti, kterou máte v rámci vašeho účtu testovacího prostředí.

- **Přístup k jiným místním prostředkům, jako je například sdílená složka nebo databáze**

   Obvykle vytváříte virtuální síť, která poskytuje přístup k místním prostředkům pomocí brány virtuální sítě typu Site-to-site. Nastavení tohoto typu prostředí bude trvat déle.

- **Přístup k dalším prostředkům Azure, které se nacházejí mimo virtuální síť**

   Pokud potřebujete přístup k prostředkům Azure, které nejsou *zabezpečené v* rámci virtuální sítě, můžete k nim přistupovat přes veřejný Internet, aniž byste museli dělat žádné partnerské vztahy.

   Další informace o virtuálních sítích najdete v těchto tématech:
   - Oddíl "virtuální síť" [v základních architekturách architektury Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Připojení sítě laboratoře k partnerské virtuální síti v Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Vytvoření testovacího prostředí se sdíleným prostředkem v Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Nastavení účtu testovacího prostředí

Po dokončení plánování jste připraveni nastavit účet testovacího prostředí. Stejný postup můžete použít k nastavení [Azure Lab Services v týmech](./lab-services-within-teams-overview.md).

1. **Vytvořte účet testovacího prostředí**. Pokyny najdete v tématu [Vytvoření účtu testovacího prostředí](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Informace o konvencích vytváření názvů najdete v části "pojmenování" v [příručce pro správce Azure Lab Services](./administrator-guide.md#naming).

1. **Přidejte uživatele do role testovacího tvůrce**. Pokyny najdete v tématu [Přidání uživatelů do role testovacího tvůrce](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Připojte se k partnerské virtuální síti**. Pokyny najdete v tématu [připojení testovací sítě k partnerské virtuální síti](./how-to-connect-peer-virtual-network.md).

   Může se také vyžadovat, abyste odkazovali na pokyny pro [konfiguraci rozsahu adres virtuálních počítačů v testovacím prostředí](./how-to-configure-lab-accounts.md).

1. **Povolte a zkontrolujte obrázky**. Pokyny najdete v tématu [Určení dostupných imagí Azure Marketplace pro tvůrci testovacího prostředí](./specify-marketplace-images.md).

   Chcete-li zkontrolovat obsah jednotlivých Azure Marketplace imagí, vyberte název bitové kopie. Například následující snímek obrazovky ukazuje podrobnosti Data Science VM Image Ubuntu:

   ![Snímek obrazovky se seznamem imagí, který je k dispozici ke kontrole v Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Pokud je galerie sdílených imagí připojená k účtu testovacího prostředí a chcete povolit sdílení vlastních imagí pomocí tvůrců testovacích prostředí, postupujte podle podobných kroků, jak je znázorněno na následujícím snímku obrazovky:

   ![Snímek obrazovky se seznamem povolených vlastních imagí v galerii sdílených imagí](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Další kroky

Další informace o nastavení a správě Labs najdete v těchto tématech:

- [Správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)  
- [Průvodce nastavením testovacího prostředí](setup-guide.md)
