---
title: Průvodce nastavením akcelerovaného účtu testovacího prostředí pro Azure Lab Services
description: Tato příručka správcům pomáhá rychle nastavit účet testovacího prostředí pro použití v rámci své školy.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4d9a64fe23c3e5b74e77e704154f5e74bf2066d9
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490555"
---
# <a name="lab-account-setup-guide"></a>Průvodce nastavením účtu testovacího prostředí
Aby bylo možné nastavit prostředí Azure Lab Services, musí správci nejprve nastavit **účet testovacího** prostředí v rámci vašeho předplatného Azure. Účet testovacího prostředí je kontejner pro vaši laboratoř a nastavuje se jenom pár minut.

Tato příručka obsahuje tři části:
-  První část se zaměřuje na požadavky, které je potřeba provést *před* nastavením účtu testovacího prostředí.
-  Druhá část uvádí pokyny k plánování nastavení účtu testovacího prostředí.
-  Třetí část poskytuje podrobné pokyny k nastavení účtu testovacího prostředí.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Předpoklady pro nastavení účtu testovacího prostředí
V této části najdete popis požadavků, které je třeba provést před nastavením účtu testovacího prostředí.

### <a name="obtain-an-azure-subscription"></a>Získání předplatného Azure
K vytvoření účtu testovacího prostředí potřebujete přístup k předplatnému Azure, které je nastavené pro vaši školu. Vaše škola může mít jeden nebo víc předplatných. Předplatné se používá ke správě fakturace a zabezpečení pro všechny prostředky a služby Azure, včetně účtů testovacího prostředí.  Předplatná Azure jsou obvykle spravovaná vaším IT oddělením.  Další informace najdete v následujícím tématu:
 - [Příručka pro správce – předplatné](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Odhad počtu virtuálních počítačů a velikostí virtuálních počítačů, které potřebujete
Budete potřebovat odhadnout počet virtuálních počítačů a [velikosti virtuálních počítačů](https://docs.microsoft.com/azure/lab-services/administrator-guide#vm-sizing) , které vaše škola potřebuje.  V tomto blogovém příspěvku najdete pokyny ke strukturování labs\images..  Tento Blogový příspěvek vám pomůže určit počet virtuálních počítačů a velikosti virtuálních počítačů, které budete potřebovat:
- [Přechod z fyzického testovacího prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Podívejte se také na tento článek, který vysvětluje další doprovodné materiály ke strukturování Labs:
- [Příručka pro správce – testovací prostředí](https://docs.microsoft.com/azure/lab-services/administrator-guide#classroom-lab)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Vysvětlení omezení virtuálních počítačů s předplatným a kapacity regionálního virtuálního počítače
Jakmile budete mít odhad počtu virtuálních počítačů a velikostí virtuálních počítačů pro cvičení, budete potřebovat:

- Ujistěte se, že limit kapacity vašeho předplatného Azure umožňuje počet virtuálních počítačů a velikost virtuálních počítačů, které v laboratoři plánujete použít.

- Vytvořte účet testovacího prostředí v oblasti, která má dostatečnou dostupnou kapacitu virtuálního počítače.

Další informace najdete v tomto blogovém příspěvku: [omezení pro předplatné virtuálních počítačů a regionální kapacitu](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Rozhodněte, kolik účtů testovacího prostředí se má vytvořit.

Pokud chcete rychle začít, vytvořte v rámci vlastní skupiny prostředků jeden účet testovacího prostředí.  Později můžete podle potřeby vytvořit další účty testovacího prostředí (a skupiny prostředků). Můžete například nakonec mít jeden účet testovacího prostředí a skupinu prostředků pro každé oddělení jako způsob, jak jasně oddělit náklady.  Další informace o účtech testovacího prostředí, skupinách prostředků a rozdělení nákladů najdete v následujících článcích:
- [Příručka pro správce – skupina prostředků](https://docs.microsoft.com/azure/lab-services/administrator-guide#resource-group)
- [Příručka pro správce – účet testovacího prostředí](https://docs.microsoft.com/azure/lab-services/administrator-guide#lab-account) 
- [Správa nákladů pro Azure Lab Services](https://docs.microsoft.com/azure/lab-services/cost-management-guide)

## <a name="planning-your-lab-accounts-settings"></a>Plánování nastavení účtu testovacího prostředí

Pokud chcete naplánovat nastavení účtu testovacího prostředí, měli byste zvážit následující otázky.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kdo by měl být vlastníky a přispěvateli účtu testovacího prostředí?

   Správci IT ve vaší škole jsou obvykle vlastníci a Přispěvatelé pro účet testovacího prostředí. Zodpovídá za správu zásad, které se vztahují na všechny laboratoře obsažené v rámci účtu testovacího prostředí. Osoba, která vytváří účet testovacího prostředí, je automaticky vlastníkem. Můžete přidat další vlastníky a přispěvatele z klienta Azure Active Directory (AD), který je přidružený k vašemu předplatnému. Další informace o vlastníkovi účtu testovacího prostředí a rolích přispěvatele najdete v těchto článku:
   -  [Příručka pro správce – Správa identit](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Uživatelé testovacího prostředí vidí jenom jeden seznam virtuálních počítačů, ke kterým mají přístup přes klienty v rámci Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Kdo bude moci vytvářet Labs?

   Můžete se rozhodnout, že vaše IT a vyučující členové budou vytvářet Labs. Když uživatel vytvoří testovací prostředí, automaticky se přiřadí jako vlastník testovacího prostředí.  Aby bylo možné vytvářet testovací prostředí, musí být uživatelé (obvykle z klienta Azure AD přidruženého k vašemu předplatnému) přiřazeni k roli programu Lab Creator v rámci účtu testovacího prostředí.  Další informace o roli tvůrce testovacího prostředí najdete v článku:
   -  [Příručka pro správce – Správa identit](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Kdo bude moct vlastnit a spravovat cvičení?

   Můžete se také rozhodnout, že členové IT a vyučující own\manage Labs, *aniž by* jim umožnili vytvářet Labs.  V takovém případě se uživatelům z tenanta Azure AD vašeho předplatného přiřadí pro existující cvičení buď vlastník, nebo Přispěvatel.  Další informace o rolích vlastníka a přispěvatele testovacího prostředí najdete tady:
   - [Příručka pro správce – Správa identit](https://docs.microsoft.com/azure/lab-services/administrator-guide#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Chcete uložit obrázky, které se dají sdílet napříč laboratořemi?
Galerie sdílených imagí je úložiště, které můžete použít k ukládání a sdílení imagí. Pro třídy, které potřebují stejný obrázek, můžou tvůrci testovacích prostředí vytvořit image a pak ji exportovat do galerie sdílených imagí.  Jakmile se image exportují do galerie sdílených imagí, dají se použít k vytvoření nové laboratoře.

Můžete také chtít vytvořit své image ve svém fyzickém prostředí a pak je importovat do galerie sdílených imagí.  Další podrobnosti o tomto procesu najdete v tomto blogovém příspěvku: 
- [Importovat vlastní image do galerie sdílených imagí](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Pokud se rozhodnete, že budete potřebovat použít sdílenou galerii imagí, budete muset vytvořit nebo připojit galerii sdílených imagí k vašemu účtu testovacího prostředí. Toto rozhodnutí můžete také odložit do pozdějšího období, protože ho můžete kdykoli připojit k účtu testovacího prostředí.  Další informace o galerii sdílených imagí najdete v těchto článku:
- [Příručka pro správce – Galerie sdílených imagí](https://docs.microsoft.com/azure/lab-services/administrator-guide#shared-image-gallery)
- [Příručka pro správce – ceny pro galerii sdílených imagí](https://docs.microsoft.com/azure/lab-services/administrator-guide#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Které image v Azure Marketplace budou vaše cvičení používat?
Azure Marketplace poskytuje stovky imagí, které můžete povolit, aby tvůrci testovacího prostředí mohli image použít k vytvoření testovacího prostředí. Některé obrázky můžou zahrnovat všechno, co testovací laboratoř už potřebuje. V jiných případech můžete jako výchozí bod použít obrázek a potom ho tvůrce testovacího prostředí může přizpůsobit instalací dalších aplikací nebo nástrojů.

Pokud si nejste jisti, které image potřebujete, můžete se později vrátit a povolit je. Nejlepším způsobem, jak zjistit, jaké image jsou k dispozici, je také vytvoření účtu testovacího prostředí. Tím získáte přístup, abyste mohli zkontrolovat seznam dostupných imagí a jejich obsah.  Další informace o imagích Marketplace najdete v těchto článku:
- [Určení imagí Marketplace dostupných pro tvůrci testovacího prostředí](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Musí mít virtuální počítače v testovacím prostředí přístup k jiným prostředkům Azure nebo místním prostředkům?
Při nastavování účtu testovacího prostředí můžete také vytvořit Partnerský účet s virtuální sítí (VNet).  Pamatujte, že váš účet virtuální sítě a testovacího prostředí musí být umístěný ve stejné oblasti.  Pokud se chcete rozhodnout, jestli potřebujete vytvořit partnerský vztah k virtuální síti, vezměte v úvahu následující scénáře:

- **Přístup k licenčnímu serveru**
  
   Když použijete Azure Marketplace image, náklady na licenci na operační systém se začlení do cen za služby testovacího prostředí. Nemusíte ale poskytovat licence pro samotný operační systém. Pro další nainstalované software a aplikace je však nutné poskytnout licenci podle potřeby.  Přístup k licenčnímu serveru:
   - Můžete se rozhodnout, že se budete chtít připojit k místnímu licenčnímu serveru.  Připojení k místnímu licenčnímu serveru vyžaduje další nastavení.
   - Další možností, která je pro nastavení rychlejší, je vytvoření licenčního serveru, který hostuje na virtuálním počítači Azure.  Virtuální počítač Azure se nachází ve virtuální síti, kterou jste nacházeli z vašeho účtu testovacího prostředí.

- **Přístup k dalším místním prostředkům, jako je například sdílená složka nebo databáze**

   Vytvoříte virtuální síť, která bude poskytovat přístup k místním prostředkům, obvykle pomocí brány virtuální sítě typu Site-to-site. Nastavení tohoto typu prostředí bude trvat déle.

- **Přístup k dalším prostředkům Azure, které se nacházejí mimo virtuální síť**

   Pokud potřebujete přístup k prostředkům Azure, které nejsou *zabezpečené v* rámci virtuální sítě, můžete k těmto prostředkům přistupovat prostřednictvím veřejného Internetu bez jakýchkoli partnerských vztahů.

Další informace o virtuálních sítích najdete v těchto článku:
- [Základy architektury – Virtual Network](https://docs.microsoft.com/azure/lab-services/classroom-labs-fundamentals#virtual-network)
- [Jak se připojit k virtuální síti](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network)
- [Postup vytvoření testovacího prostředí se sdíleným prostředkem v Azure Lab Services](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource)

## <a name="set-up-your-lab-account"></a>Nastavení účtu testovacího prostředí

Po dokončení plánování jste připraveni nastavit účet testovacího prostředí.  Tyto stejné kroky platí pro nastavení Azure Lab Services testovacího prostředí [s týmy](https://docs.microsoft.com/azure/lab-services/lab-services-within-teams-overview).

1. **Vytvořte účet testovacího prostředí.** Pokyny najdete v kurzu [Vytvoření účtu testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) .
   
    Další pokyny k pojmenování najdete v následujícím článku:

   - [Pokyny pro pojmenování prostředků](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Přidejte uživatele do role testovacího tvůrce.** Pokyny najdete v tématu [Přidání uživatelů do role testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).


3. **Připojte se k partnerské virtuální síti.** Pokyny najdete v tématu [propojení sítě testovacího prostředí s partnerskými virtuálními sítěmi](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Může se také vyžadovat, abyste odkazovali na pokyny týkající se [Konfigurace rozsahu adres virtuálních počítačů v testovacím prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Povolte a zkontrolujte obrázky.** Pokyny najdete v tématu [Povolení imagí Azure Marketplace pro tvůrci testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Chcete-li zkontrolovat obsah jednotlivých Azure Marketplace imagí, vyberte název bitové kopie. Například následující snímek obrazovky ukazuje podrobnosti pro Ubuntu bitovou kopii Data Science VM:

   ![Snímek obrazovky s revizemi Azure Marketplace imagí](./media/setup-guide/review-marketplace-images.png)

   Pokud je galerie sdílených imagí připojená k účtu testovacího prostředí a chcete povolit sdílení vlastních imagí pomocí tvůrců testovacích prostředí, postupujte podle podobných kroků, jak je znázorněno na následujícím snímku obrazovky:

   ![Snímek obrazovky s povolením vlastních imagí v galerii sdílených imagí](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Další kroky

Další kroky, které jsou společné pro nastavení testovacího prostředí:

- [Správa účtů testovacího prostředí](how-to-manage-lab-accounts.md)
- [Průvodce nastavením testovacího prostředí](setup-guide.md)
