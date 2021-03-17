---
title: Povolení replikace šifrovaných virtuálních počítačů Azure v Azure Site Recovery
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače s povolenými disky CMK (Customer-Managed Key) z jedné oblasti Azure do jiné pomocí Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146531"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replikace počítačů s disky s podporou klíčů spravovaných zákazníkem (CMK)

Tento článek popisuje, jak replikovat virtuální počítače Azure s povolenou službou Customer-Managed Keys (CMK) a z jedné oblasti Azure do jiné.

## <a name="prerequisite"></a>Požadavek
Před povolením replikace pro virtuální počítače, které mají spravované disky s povoleným CMK, je třeba vytvořit sady Disk Encryption v cílové oblasti cílového předplatného.

## <a name="enable-replication"></a>Povolení replikace

V tomto příkladu je primární oblast Azure Východní Asie a sekundární oblast je jih Východní Asie.

1. V trezoru vyberte **+ replikovat** .
2. Všimněte si následujících polí.
    - **Zdroj** : bod původu virtuálních počítačů, v tomto případě je to **Azure** .
    - **Zdrojové umístění** : oblast Azure, ve které chcete chránit své virtuální počítače. V tomto příkladu je zdrojovým umístěním "Východní Asie."
    - **Model nasazení** : model nasazení Azure zdrojových počítačů.
    - **Zdrojové předplatné** : předplatné, ke kterému patří vaše zdrojové virtuální počítače. Může to být jakékoli předplatné, které je ve stejném Azure Active Directory tenant jako trezor služby Recovery Services.
    - **Skupina prostředků** : Skupina prostředků, do které patří vaše zdrojové virtuální počítače. V dalším kroku jsou uvedené pro ochranu všechny virtuální počítače ve vybrané skupině prostředků.

3. V **Virtual Machines**  >  **vyberte virtuální počítače** , vyberte všechny virtuální počítače, které chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK** .

4. V **Nastavení** můžete nakonfigurovat následující nastavení cílové lokality.

    - **Cílové umístění** : umístění, kam se budou replikovat vaše zdrojová data virtuálního počítače. Site Recovery poskytuje seznam vhodných cílových oblastí na základě umístění vybraného počítače. Doporučujeme použít stejné umístění jako v umístění trezoru Recovery Services.
    - **Cílové předplatné** : cílové předplatné, které se používá pro zotavení po havárii. Ve výchozím nastavení je cílové předplatné stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků** : Skupina prostředků, do které patří všechny replikované virtuální počítače. Ve výchozím nastavení Site Recovery vytvoří novou skupinu prostředků v cílové oblasti. Název získá `asr` příponu. Pokud již existuje skupina prostředků, která byla vytvořena Azure Site Recovery, je znovu použita. Můžete ji také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolná oblast Azure s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť** : ve výchozím nastavení Site Recovery vytvoří novou virtuální síť v cílové oblasti. Název získá `asr` příponu. Je namapována na vaši zdrojovou síť a používá se pro jakoukoliv budoucí ochranu. [Přečtěte si další informace](./azure-to-azure-network-mapping.md) o mapování sítě.
    - **Cílové účty úložiště (Pokud zdrojový virtuální počítač nepoužívá spravované disky)** : ve výchozím nastavení Site Recovery vytvoří nový cílový účet úložiště tím, že mimicking konfiguraci úložiště zdrojového virtuálního počítače. Pokud účet úložiště již existuje, bude znovu použit.
    - **Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky)** : Site Recovery v cílové oblasti vytvoří nové repliky spravované disky pro zrcadlení spravovaných disků zdrojového virtuálního počítače stejného typu úložiště (Standard nebo Premium) jako spravované disky zdrojového virtuálního počítače.
    - **Účty úložiště mezipaměti** : Site Recovery potřebuje další účet úložiště s názvem *úložiště mezipaměti* ve zdrojové oblasti. Všechny změny ve zdrojových virtuálních počítačích jsou sledovány a odesílány do účtu úložiště mezipaměti. Pak se replikují do cílového umístění.
    - **Skupina dostupnosti** : ve výchozím nastavení Site Recovery vytvoří novou skupinu dostupnosti v cílové oblasti. Název má `asr` příponu. Pokud skupina dostupnosti, kterou vytvořil Site Recovery, už existuje, použije se znovu.
    - **Šifrovací sady disků (des)** : Site Recovery potřebuje, aby se pro replikování a cílení na spravované disky používaly sady disků pro šifrování. Před povolením replikace musíte v cílovém předplatném a v cílové oblasti předem vytvořit algoritmus DES. Ve výchozím nastavení není algoritmus DES vybrán. Pro výběr DES na zdrojový disk musíte kliknout na přizpůsobit.
    - **Zásady replikace** : definuje nastavení pro historii uchovávání bodů obnovení a četnost snímků konzistentních vzhledem k aplikacím. Ve výchozím nastavení Site Recovery vytvoří novou zásadu replikace s výchozím nastavením *24 hodin* pro uchování bodu obnovení a *60 minut* pro frekvenci snímků konzistentní vzhledem k aplikacím.

    ![Povolení replikace pro počítač s disky s povoleným CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Přizpůsobení cílových prostředků

Pomocí těchto kroků upravíte Site Recovery výchozí nastavení cíle.

1. Pokud chcete změnit výchozí cílové předplatné, vyberte **přizpůsobit** u možnosti cílové předplatné. Vyberte předplatné ze seznamu předplatných, která jsou k dispozici v tenantovi Azure AD.

2. Vyberte **přizpůsobit** vedle možnosti skupina prostředků, síť, úložiště a skupiny dostupnosti a upravte následující výchozí nastavení:
    - V poli **cílová skupina prostředků** vyberte skupinu prostředků ze seznamu skupin prostředků v cílovém umístění předplatného.
    - V poli **cílová virtuální síť** vyberte síť ze seznamu virtuálních sítí v cílovém umístění.
    - V případě **skupiny dostupnosti** můžete do virtuálního počítače přidat nastavení skupiny dostupnosti, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - U **cílových účtů úložiště** vyberte účet, který chcete použít.

3. Vyberte **přizpůsobit** u možnosti šifrování úložiště a vyberte cílovou normu des pro všechny spravované klíče zákazníka (CMK). V době výběru budete také moci zjistit, ke kterému cílovému trezoru klíčů je algoritmus DES přidružen.

4. Vyberte **vytvořit cílový prostředek**  >  **Povolit replikaci** .
5. Po povolení replikace virtuálních počítačů můžete u **replikovaných položek** ověřit stav virtuálních počítačů.

![Snímek obrazovky, který ukazuje, kde se mají kontrolovat stav virtuálních počítačů](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Během počáteční replikace může aktualizace stavu nějakou dobu trvat, a to bez zjevného postupu. Kliknutím na **aktualizovat**  získáte nejnovější stav.

## <a name="faqs"></a>Nejčastější dotazy

* Povolil (a) jsem CMK u existující replikované položky, jak mohu zajistit, aby se v cílové oblasti taky používalo CMK?

    Můžete zjistit název spravovaného disku repliky (vytvořený Azure Site Recovery v cílové oblasti) a připojit DES k tomuto disku repliky. Až ho ale připojíte, nebudete moct podrobnosti DES zobrazit v okně disky. Případně můžete zvolit možnost zakázat replikaci virtuálního počítače a znovu ji povolit. V okně disky pro replikovanou položku se zobrazí podrobnosti o DES a trezoru klíčů.

* Přidal (a) jsem nový disk s podporou CMK do replikované položky. Jak mohu tento disk replikovat pomocí Azure Site Recovery?

    Přidání nového disku s povoleným CMK do existující replikované položky není podporováno. Zakažte replikaci a znovu povolte replikaci pro virtuální počítač.

* Jak mám povolit platformu i spravované klíče zákazníka, jak můžu chránit svoje disky?

    Povolení dvojitého šifrování s suppprtedmi platforem i zákaznických klíčů je Site Recovery. Pokud chcete chránit počítač, postupujte podle pokynů v tomto článku. V cílové oblasti musíte předem vytvořit algoritmus DES s povoleným šifrováním. V době povolení replikace tohoto virtuálního počítače můžete tomuto algoritmu DES poskytnout Site Recovery.

