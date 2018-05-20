---
title: Konfiguraci replikace pro virtuální počítače Azure ve službě Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak ke konfiguraci replikace pro virtuální počítače Azure z jedné oblasti Azure do druhého pomocí Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: asgang
ms.openlocfilehash: 5e1361e681c17d4106b9c79fee56efa06be2a745
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Jiné oblasti Azure replikovat virtuální počítače Azure


>[!NOTE]
>
> Replikace obnovení lokality pro virtuální počítače Azure je aktuálně ve verzi preview.

Tento článek popisuje postup povolení replikace virtuálních počítačů Azure z jedné oblasti Azure do jiné.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nastavili Site Recovery pro tento scénář, jak je popsáno v [Azure do Azure kurzu](azure-to-azure-tutorial-enable-replication.md). Ujistěte se, že jste připravili předpoklady a vytvořit trezor služeb zotavení.



## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci. Tento postup předpokládá, že primární oblast Azure je východní Asie, přičemž sekundární oblast je jihovýchodní Asie.

1. V trezoru, klikněte na tlačítko **+ replikovat**.
2. Vezměte na vědomí následující pole:
    - **Zdroj**: bodem virtuálních počítačů, které v tomto případě je **Azure**.
    - **Zdrojové umístění**: oblast Azure, ze které chcete chránit virtuální počítače. Pro tento obrázek je umístění zdroje, východní Asie.
    - **Model nasazení**: model nasazení Azure zdrojový počítač.
    - **Skupina prostředků**: skupině prostředků, do které patří zdroj virtuálních počítačů. Pro ochranu v dalším kroku jsou uvedeny všechny virtuální počítače v části s vybranou skupinou prostředků.

    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. V **virtuální počítače > vyberte virtuální počítače**, klikněte na tlačítko a vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Potom klikněte na **OK**.
    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. V **nastavení**, případně je možné konfigurovat nastavení cílové lokality:

    - **Cílové umístění**: umístění, kde bude replikován zdrojová data virtuálního počítače. V závislosti na vaší umístění vybrané počítače se Site Recovery poskytuje seznam vhodný cílové oblasti. Doporučujeme, abyste cílové umístění stejné jako umístění trezoru služeb zotavení.
    - **Cílová skupina prostředků**: skupině prostředků, na kterém jsou všechny patřit replikovaných virtuálních počítačů. Ve výchozím nastavení Azure Site Recovery vytvoří novou skupinu prostředků v cílová oblast s názvem, který má příponu "Automatické obnovení systému". Skupiny prostředků vytvořené Azure Site Recovery již existuje, je znovu použít. Můžete také přizpůsobit, jak je znázorněno v následující části. Umístění cílová skupina prostředků může být jakékoli oblast Azure s výjimkou oblast, ve kterém jsou hostované virtuální počítače zdroj.
    - **Cílové virtuální sítě**: ve výchozím nastavení, Site Recovery vytvoří novou virtuální síť v oblasti cíl s názvem, který má příponu "Automatické obnovení systému". Toto je namapovaný na zdrojové síti a používat pro všechny budoucí ochranu. [Další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
    - **Cíl účty úložiště (Pokud váš zdrojový virtuální počítač nepoužívá spravované disky)**: ve výchozím nastavení, Site Recovery vytvoří nový účet cílového úložiště mimicking konfiguraci zdrojového virtuálního počítače úložiště. V případě, že účet úložiště už existuje, je znovu použít.
    - **Repliky spravuje disky (Pokud se vaše zdrojového virtuálního počítače používá spravované disky)**: Site Recovery vytvoří nové repliky spravované disky v oblasti cíl pro zrcadlení zdrojového Virtuálního počítače spravované disků se stejným typem úložiště (Standard nebo premium), jako zdrojový virtuální počítač spravovaný disku.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje účet úložiště s názvem úložiště mezipaměti v oblasti zdroje. Všechny změny děje na zdrojové virtuální počítače jsou sledovány a odesílat k účtu úložiště mezipaměti před replikaci do cílového umístění.
    - **Skupina dostupnosti**: ve výchozím nastavení vytvoří Azure Site Recovery novou skupinou dostupnosti ve cílová oblast s názvem, který má příponu "Automatické obnovení systému". V případě, že existuje skupina dostupnosti již vytvořené Azure Site Recovery, se znovu použije.
    - **Zásady replikace**: definuje nastavení pro obnovení bodu uchování historie a aplikace snímky konzistentní s četnost. Ve výchozím nastavení vytvoří Azure Site Recovery novou zásadu replikace s výchozím nastavením: 24 hodin se pro uchování bodu obnovení a ' 60 minut frekvence snímky konzistentní s aplikací.

    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Přizpůsobení cílové prostředky

Můžete upravit výchozí nastavení cílové používat Site Recovery.

1. Klikněte na tlačítko **přizpůsobit:** upravovat výchozí nastavení:
    - V **cílová skupina prostředků**, vyberte skupinu prostředků ze seznamu všech skupin prostředků v cílovém umístění předplatného.
    - V **cílové virtuální síti**, vyberte síť, ze seznamu všechny virtuální sítě v cílovém umístění.
    - V **sadu dostupnosti**, můžete přidat dostupnosti nastavení virtuálního počítače, pokud jsou součástí v oblasti zdroj sadu dostupnosti.
    - V **účty úložiště v cíli**, vyberte účet, který chcete použít.

        ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Klikněte na tlačítko **vytvořit cílový prostředek** > **povolit replikaci**.
3. Když virtuální počítače jsou povolená pro replikaci, můžete zkontrolovat stav stavu virtuálního počítače v části **replikované položky**

>[!NOTE]
>Během úvodní replikace stavu může trvat nějakou dobu aktualizace bez průběh. Klikněte **aktualizovat** tlačítko, chcete-li získat nejnovější stav.
>

# <a name="next-steps"></a>Další postup

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění převzetí služeb při selhání.
