---
title: Konfigurace replikace pro virtuální počítače Azure ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače Azure z jedné oblasti Azure do jiné pomocí služby Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 79ad4b3598c227ad2c2e3b76562cf95a30e82ad3
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101543"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replikace virtuálních počítačů Azure do jiné oblasti Azure



Tento článek popisuje, jak povolit replikaci virtuálních počítačů Azure z jedné oblasti Azure do jiné.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nastavili Site Recovery pro tento scénář, jak je popsáno v [Azure do Azure kurz](azure-to-azure-tutorial-enable-replication.md). Ujistěte se, že jste připravili požadavky a vytvořili trezor služby Recovery Services.



## <a name="enable-replication"></a>Povolení replikace

Povolení replikace. Tento postup předpokládá, že primární oblast Azure je východní Asie a jihovýchodní Asie je sekundární oblasti.

1. V trezoru, klikněte na tlačítko **+ replikovat**.
2. Mějte na paměti následující pole:
    - **Zdroj**: Bod počátek virtuálních počítačů, které v tomto případě je **Azure**.
    - **Umístění zdroje**: Oblast Azure z ve které chcete chránit virtuální počítače. Pro tento obrázek je zdrojové umístění: východní Asie.
    - **Model nasazení**: Model nasazení Azure zdrojový počítač.
    - **Zdrojové předplatné**: Předplatné, do které patří vaše zdrojové virtuální počítače. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
    - **Skupina prostředků**: Skupina prostředků, do které patří vaše zdrojové virtuální počítače. Všechny virtuální počítače v rámci vybrané skupiny prostředků jsou uvedeny pro ochranu v dalším kroku.

    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. V **virtuální počítače > Výběr virtuálních počítačů**klikněte a vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Potom klikněte na **OK**.
    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. V **nastavení**, Volitelně můžete nakonfigurovat nastavení cílové lokality:

    - **Cílové umístění**: Umístění, kde se budou replikovat svá zdrojová data virtuálního počítače. V závislosti na vaší polohy vybrané počítače, Site Recovery získáte seznam vhodný cílové oblasti. Doporučujeme, abyste cílové umístění stejné jako umístění trezoru služby Recovery Services.
    - **Cílové předplatné**: Cílové předplatné použité pro zotavení po havárii. Ve výchozím nastavení bude cílové předplatné stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků**: Skupina prostředků, na kterém jsou všechny vaše replikované virtuální počítače patří. Ve výchozím nastavení vytvoří Azure Site Recovery s názvem, který má příponu "Azure Site Recovery" v cílové oblasti novou skupinu prostředků. V případě, že skupina prostředků Azure Site Recovery vytvoří již existuje, je znovu. Můžete také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolné oblasti Azure, s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť**: Ve výchozím nastavení vytvoří Site Recovery novou virtuální síť v cílové oblasti s názvem, který má příponu "Azure Site Recovery". To je namapována na zdrojovou síť a použít pro všechny budoucí ochrany. [Další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
    - **Cílové účty úložiště (Pokud je váš zdrojový virtuální počítač nepoužívá spravované disky)**: Ve výchozím nastavení vytvoří Site Recovery nový cílový účet úložiště tak napodobuje konfiguraci úložiště zdrojového virtuálního počítače. V případě, že účet úložiště už existuje, je znovu.
    - **Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky)**: Site Recovery vytvoří nové spravované disky repliky v cílové oblasti zrcadlící spravované disky zdrojového Virtuálního počítače se stejným typem úložiště (Standard nebo premium), protože zdrojový virtuální počítač spravovaný disk.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje účet úložiště s názvem úložiště mezipaměti ve zdrojové oblasti. Všechny změny aktivit ve zdrojových virtuálních počítačů jsou sledovány a odeslat do účtu úložiště mezipaměti před replikaci do cílového umístění.
    - **Cílové skupiny dostupnosti**: Ve výchozím nastavení Azure Site Recovery vytvoří nové dostupnosti v cílové oblasti s názvem, který má příponu "Azure Site Recovery" pro virtuální počítače součástí skupiny dostupnosti ve zdrojové oblasti. V případě, že skupina dostupnosti Azure Site Recovery vytvoří již existuje, je znovu.
    - **Cílové zóny dostupnosti**: Ve výchozím nastavení Site Recovery přiřadí číslo zóny jako zdrojové oblasti v cílové oblasti, v případě, že cílové oblasti podporují zóny dostupnosti.

    Pokud cílová oblast nepodporuje zóny dostupnosti, jsou cílové virtuální počítače ve výchozím nastavení nakonfigurované jako jedné instance. V případě potřeby můžete nakonfigurovat tyto virtuální počítače kliknutím na "Vlastní" jako součást skupiny dostupnosti v cílové oblasti.

    >[!NOTE]
    >Typ dostupnosti - jediné instance, set nebo dostupnost zónu dostupnosti, nelze změnit po povolení replikace. Budete muset zakázat a povolit replikaci, chcete-li změnit typ dostupnosti.
    >
    
    - **Zásady replikace**: Definuje nastavení pro obnovení bodu uchování historie a aplikace konzistentní frekvence pořizování snímků. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozím nastavením ' 24 hodin pro uchování bodu obnovení a "60 minut, než se frekvence snímků konzistentní vzhledem k aplikacím aplikace.

    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
  
## <a name="customize-target-resources"></a>Přizpůsobení cílové prostředky

Můžete upravit výchozí nastavení cíle používané pro Site Recovery.

1. Klikněte na tlačítko **vlastní:** vedle 'Cílové předplatné' Chcete-li změnit výchozí cílové předplatné. Vyberte předplatné ze seznamu všech předplatných, které jsou k dispozici ve stejném tenantovi Azure Active Directory (AAD).

2. Klikněte na tlačítko **vlastní:** upravit výchozí nastavení:
    - V **cílová skupina prostředků**, vyberte skupinu prostředků z seznam všech skupin prostředků v cílovém umístění předplatného.
    - V **cílovou virtuální sítí**, vyberte síť, ze seznamu ve virtuální síti v cílové lokalitě.
    - V **dostupnosti**, můžete přidat nastavení skupinu dostupnosti pro virtuální počítač, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - V **účty úložiště v cíli**, vyberte účet, který chcete použít.

        ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
1. Klikněte na tlačítko **vlastní:** upravit nastavení replikace.
   - V **konzistenci**, vyberte virtuální počítače, které chcete replikovat společně 
   - Všechny počítače v replikační skupině budou mít v případě převzetí služeb při selhání sdílené body obnovení konzistentní pro případ chyby a konzistentní vzhledem k aplikacím. Povolení konzistence více virtuálních počítačů může mít vliv na výkon úloh (jako je intenzivní nároky na procesor) a musí být použity pouze v případě, že počítačích běží stejná úloha a potřebujete konzistenci napříč několika počítači. Například pokud má aplikace 2 virtuální počítače sql a 2 webové servery pak měli byste přidat jenom virtuální počítače sql jako součást skupiny replikace.
   - Můžete mít maximálně 16 virtuálních počítačů v replikační skupině.
   - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Ujistěte se, že žádné zařízení brány firewall neblokuje interní komunikaci mezi virtuálními počítači přes port 20004. Pokud chcete do replikační skupiny zahrnout virtuální počítače s Linuxem, nezapomeňte ručně otevřít odchozí provoz na portu 20004 podle pokynů ke konkrétní verzi Linuxu.
![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
2. Klikněte na tlačítko **vytvořit cílový prostředek** > **povolením replikace**.
3. Po virtuální počítače jsou povolena pro replikaci, můžete zkontrolovat stav stav virtuálního počítače v rámci **replikované položky**

>[!NOTE]
>Během počáteční replikace stavu může trvat nějakou dobu aktualizovat, aniž by průběh. Klikněte na tlačítko **aktualizovat** tlačítko, chcete-li získat nejnovější stav.
>

# <a name="next-steps"></a>Další postup

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testovací převzetí služeb při selhání.
