---
title: Konfigurace replikace pro virtuální počítače Azure v Azure Site Recovery
description: Naučte se konfigurovat replikaci do jiné oblasti pro virtuální počítače Azure pomocí Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 49929cfe0abc634dc4b704aba1c7b11a5d7dd777
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383581"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikace virtuálních počítačů Azure do jiné oblasti Azure


Tento článek popisuje, jak povolit replikaci virtuálních počítačů Azure z jedné oblasti Azure do jiné.

## <a name="before-you-start"></a>Než začnete

V tomto článku se předpokládá, že jste připraveni na nasazení Site Recovery, jak je popsáno v [kurzu Azure to Azure pro zotavení po havárii](azure-to-azure-tutorial-enable-replication.md).

Požadavky by měly být zavedeny a měli byste vytvořit trezor Recovery Services.


## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci. Tento postup předpokládá, že primární oblast Azure je Východní Asie, a sekundární oblast je jih Východní Asie.

1. V trezoru klikněte na **+ replikovat**.
2. Všimněte si následujících polí:
   - **Zdroj**: bod původu virtuálních počítačů, v tomto případě je to **Azure**.
   - **Zdrojové umístění**: oblast Azure, ze které chcete chránit virtuální počítače. Pro tuto ilustraci je zdrojovým umístěním ' Východní Asie '
   - **Model nasazení**: model nasazení Azure zdrojových počítačů.
   - **Zdrojové předplatné**: předplatné, ke kterému patří vaše zdrojové virtuální počítače. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
   - **Skupina prostředků**: Skupina prostředků, do které patří vaše zdrojové virtuální počítače. V dalším kroku jsou uvedené pro ochranu všechny virtuální počítače v rámci vybrané skupiny prostředků.

     ![Snímek obrazovky, který zvýrazní pole potřebná pro konfiguraci replikace.](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. V **Virtual Machines > vyberte virtuální počítače**, klikněte na a vyberte všechny virtuální počítače, které chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak klikněte na **OK**.
    ![Snímek obrazovky, který zvýrazní, kde vybíráte virtuální počítače.](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. V **Nastavení** můžete volitelně nakonfigurovat nastavení cílového webu:

   - **Cílové umístění**: umístění, kam se budou replikovat vaše zdrojová data virtuálního počítače. V závislosti na umístění vybraných počítačů vám Site Recovery poskytne seznam vhodných cílových oblastí. Doporučujeme ponechat cílové umístění stejné jako umístění trezoru Recovery Services.
   - **Cílové předplatné:** Cílové předplatné, které se použije pro zotavení po havárii. Ve výchozím nastavení bude cílové předplatné stejné jako zdrojové předplatné.
   - **Cílová skupina prostředků**: Skupina prostředků, do které patří všechny replikované virtuální počítače.
       - Ve výchozím nastavení Site Recovery vytvoří novou skupinu prostředků v cílové oblasti s příponou ASR v názvu.
       - Pokud skupina prostředků vytvořená Site Recovery už existuje, použije se znovu.
       - Můžete přizpůsobit nastavení skupiny prostředků.
       - Umístění cílové skupiny prostředků může být libovolná oblast Azure, s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače.
   - **Cílová virtuální síť**: ve výchozím nastavení Site Recovery vytvoří novou virtuální síť v cílové oblasti s příponou ASR v názvu. To je namapováno na vaši zdrojovou síť a používá se pro jakoukoliv budoucí ochranu. [Přečtěte si další informace](./azure-to-azure-network-mapping.md) o mapování sítě.
   - **Cílové účty úložiště (zdrojový virtuální počítač nepoužívá spravované disky)**: ve výchozím nastavení Site Recovery vytvoří nový cílový účet úložiště mimicking konfiguraci úložiště zdrojového virtuálního počítače. V případě, že účet úložiště již existuje, bude znovu použit.
   - **Disky spravované replikou (zdrojový virtuální počítač používá spravované disky)**: Site Recovery vytvoří nové disky spravované replikou v cílové oblasti pro zrcadlení spravovaných disků zdrojového virtuálního počítače se stejným typem úložiště (Standard nebo Premium) jako spravovaný disk zdrojového virtuálního počítače.
   - **Účty úložiště mezipaměti**: Site Recovery potřebuje další účet úložiště, který se označuje jako úložiště mezipaměti ve zdrojové oblasti. Všechny změny, které probíhají ve zdrojových virtuálních počítačích, se sledují a odesílají do účtu úložiště mezipaměti před jejich replikací do cílového umístění. Tento účet úložiště by měl být standardní.
   - **Cílové skupiny dostupnosti**: ve výchozím nastavení Site Recovery vytvoří novou skupinu dostupnosti v cílové oblasti s příponou "Azure Site Recovery" v názvu pro virtuální počítače, které jsou součástí skupiny dostupnosti ve zdrojové oblasti. Pokud skupina dostupnosti, kterou vytvořil Site Recovery, už existuje, použije se znovu.
     >[!NOTE]
     >Při konfiguraci cílových skupin dostupnosti nakonfigurujte prosím pro virtuální počítače s různou velikostí jiné skupiny dostupnosti. 
     >
   - **Cílové zóny dostupnosti**: ve výchozím nastavení Site Recovery přiřadí stejné číslo zóny jako zdrojová oblast v cílové oblasti, pokud cílová oblast podporuje zóny dostupnosti.

     Pokud cílová oblast nepodporuje zóny dostupnosti, cílové virtuální počítače se ve výchozím nastavení nakonfigurují jako samostatné instance. V případě potřeby můžete tyto virtuální počítače nakonfigurovat tak, aby byly součástí skupin dostupnosti v cílové oblasti, kliknutím na přizpůsobit.

     >[!NOTE]
     >Po povolení replikace nemůžete změnit typ dostupnosti – jediná instance, Skupina dostupnosti nebo zóna dostupnosti. Chcete-li změnit typ dostupnosti, je třeba zakázat a povolit replikaci.
     >

   - **Zásady replikace**: definuje nastavení pro historii uchovávání bodů obnovení a četnost snímků konzistentních vzhledem k aplikacím. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozími nastaveními 24 hodin pro uchování bodu obnovení a 4 hodiny pro četnost snímků konzistentních vzhledem k aplikacím.

     ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Povolení replikace pro přidané disky

Pokud přidáváte disky do virtuálního počítače Azure, pro který je povolená replikace, dojde k následujícímu:
-   Stav replikace pro virtuální počítač zobrazuje upozornění a poznámky informující o tom, že je k dispozici jeden nebo více disků pro ochranu.
-   Pokud povolíte ochranu pro přidané disky, bude upozornění po počáteční replikaci disku zmizí.
-   Pokud se rozhodnete Nepovolit replikaci pro disk, můžete vybrat možnost Zavřít upozornění.


    ![Byl přidán nový disk.](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Pokud chcete povolit replikaci pro přidaný disk, udělejte toto:

1.  V trezoru > **replikované položky** klikněte na virtuální počítač, ke kterému jste přidali disk.
2.  Klikněte na **disky** a potom vyberte datový disk, pro který chcete povolit replikaci (tyto disky mají stav **Nechráněno** ).
3.  V **podrobnostech o disku** klikněte na **Povolit replikaci**.

    ![Povolit replikaci pro přidaný disk](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Po spuštění úlohy povolit replikaci a dokončení počáteční replikace dojde k odebrání upozornění na stav replikace pro problém disku.



## <a name="customize-target-resources"></a>Přizpůsobení cílových prostředků

Můžete upravit výchozí nastavení cíle, které používá Site Recovery.

1. Pokud chcete změnit výchozí cílové předplatné, klikněte na **přizpůsobit:** vedle na cílové předplatné. Vyberte předplatné ze seznamu všech předplatných dostupných ve stejném klientovi Azure Active Directory (AAD).

2. Pokud chcete upravit výchozí nastavení, klikněte na **přizpůsobit:**
    - V části **cílová skupina prostředků** vyberte skupinu prostředků ze seznamu všech skupin prostředků v cílovém umístění předplatného.
    - V části **cílová virtuální síť** vyberte síť ze seznamu všech virtuálních sítí v cílovém umístění.
    - V části skupina **dostupnosti** můžete do virtuálního počítače přidat nastavení skupiny dostupnosti, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - V části **cílové účty úložiště** vyberte účet, který chcete použít.

        ![Snímek obrazovky, který ukazuje, jak přizpůsobit nastavení cílového předplatného.](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klikněte na **přizpůsobit:** a upravte nastavení replikace.
4. V části **konzistence více virtuálních počítačů** vyberte virtuální počítače, které chcete replikovat společně.
    - Všechny počítače v replikační skupině budou mít v případě převzetí služeb při selhání sdílené body obnovení konzistentní pro případ chyby a konzistentní vzhledem k aplikacím.
    - Povolení konzistence s více virtuálními počítači může mít vliv na výkon úloh (v důsledku náročnosti na procesor). Měla by být povolená jenom v případě, že počítače používají stejnou úlohu a potřebujete konzistenci napříč několika počítači.
    - Pokud má aplikace například 2 SQL Server virtuální počítače a dva webové servery, měli byste do replikační skupiny přidat jenom SQL Server virtuálních počítačů.
    - Můžete zvolit, aby v replikační skupině bylo maximálně 16 virtuálních počítačů.
    - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004.
    - Ujistěte se, že neexistuje žádné zařízení brány firewall blokující interní komunikaci mezi virtuálními počítači přes port 20004.
    - Pokud chcete, aby virtuální počítače se systémem Linux byly součástí replikační skupiny, zajistěte ruční otevření odchozího provozu na portu 20004 podle pokynů pro konkrétní verzi systému Linux.
![Snímek obrazovky, který zobrazuje nastavení konzistence pro více virtuálních počítačů.](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klikněte na **vytvořit cílový prostředek**  >  **Povolit replikaci**.
6. Po povolení replikace virtuálních počítačů můžete ověřit stav stavu virtuálního počítače v části **replikované položky** .

>[!NOTE]
>
> - Během počáteční replikace může aktualizace stavu trvat delší dobu, aniž by došlo k jejímu pokroku. Chcete-li získat nejnovější stav, klikněte na tlačítko **aktualizovat** .
> - Pokud bod obnovení nebyl za posledních 60 minut vygenerován, stav replikace virtuálního počítače se stane kritickým.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](site-recovery-test-failover-to-azure.md) o spuštění testovacího převzetí služeb při selhání.
