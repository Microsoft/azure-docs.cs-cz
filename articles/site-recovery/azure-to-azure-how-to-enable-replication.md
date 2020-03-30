---
title: Konfigurace replikace pro virtuální počítače Azure v Azure Site Recovery
description: Zjistěte, jak nakonfigurovat replikaci do jiné oblasti pro virtuální počítače Azure pomocí site recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973690"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikace virtuálních počítačů Azure do jiné oblasti Azure


Tento článek popisuje, jak povolit replikaci virtuálních počítačích Azure z jedné oblasti Azure do jiné.

## <a name="before-you-start"></a>Než začnete

Tento článek předpokládá, že jste připravili pro nasazení site recovery, jak je popsáno v [Azure to Azure zotavení po havárii kurzu](azure-to-azure-tutorial-enable-replication.md).

Požadavky by měly být na místě a měli jste vytvořit trezor služby Recovery Services.


## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci. Tento postup předpokládá, že primární oblast Azure je východní Asie a sekundární oblast je jihovýchodní Asie.

1. V úschovně klepněte na tlačítko **+Replikovat**.
2. Všimněte si následujících polí:
   - **Zdroj**: Místo původu virtuálních počítačích, což je v tomto případě **Azure**.
   - **Umístění zdroje:** Oblast Azure z místa, kde chcete chránit virtuální počítače. Pro tento obrázek je umístění zdroje "Východní Asie"
   - **Model nasazení**: Model nasazení Azure zdrojových počítačů.
   - **Zdrojové předplatné**: Předplatné, ke kterému patří vaše zdrojové virtuální počítače. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
   - **Skupina prostředků:** Skupina prostředků, do které patří vaše zdrojové virtuální počítače. Všechny virtuální virtuální ho disponiál v rámci vybrané skupiny prostředků jsou uvedeny pro ochranu v dalším kroku.

     ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Ve **virtuálních počítačích > Vyberte virtuální počítače**klikněte a vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak klikněte na **OK**.
    ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. V **nastavení**můžete volitelně konfigurovat nastavení cílového webu:

   - **Cílové umístění:** Umístění, kde budou replikována data zdrojového virtuálního počítače. V závislosti na zvolenélokalitě zařízení vám site recovery poskytne seznam vhodných cílových oblastí. Doporučujeme zachovat cílové umístění stejné jako umístění úložiště služby Recovery Services.
   - **Cílové předplatné:** Cílové předplatné, které se použije pro zotavení po havárii. Ve výchozím nastavení bude cílové předplatné stejné jako zdrojové předplatné.
   - **Cílová skupina prostředků**: Skupina prostředků, do které patří všechny replikované virtuální počítače.
       - Ve výchozím nastavení site recovery vytvoří novou skupinu prostředků v cílové oblasti s příponou "asr" v názvu.
       - Pokud skupina prostředků vytvořená obnovením webu již existuje, je znovu použita.
       - Nastavení skupiny prostředků můžete přizpůsobit.
       - Umístění cílové skupiny prostředků může být libovolné oblasti Azure, s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače.
   - **Cílová virtuální síť**: Ve výchozím nastavení site recovery vytvoří novou virtuální síť v cílové oblasti s příponou "asr" v názvu. Tato funkce je mapována do zdrojové sítě a používá se pro budoucí ochranu. [Přečtěte si další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
   - **Cílové účty úložiště (zdrojový virtuální počítač nepoužívá spravované disky)**: Ve výchozím nastavení site recovery vytvoří nový cílový účet úložiště napodobující konfiguraci úložiště zdrojového virtuálního počítače. V případě, že účet úložiště již existuje, je znovu použit.
   - **Disky spravované replikami (zdrojový virtuální modul používá spravované disky):** Site Recovery vytvoří nové disky spravované replikami v cílové oblasti, aby zrcadlily spravované disky zdrojového virtuálního počítače se stejným typem úložiště (standardní nebo prémiový) jako zdrojový spravovaný disk virtuálního počítače.
   - **Účty úložiště mezipaměti**: Obnovení webu potřebuje další účet úložiště nazývaný úložiště mezipaměti ve zdrojové oblasti. Všechny změny, ke kterým dochází na zdrojových virtuálních počítačích, jsou sledovány a odeslány do mezipaměti účtu úložiště před jejich replikací do cílového umístění. Tento účet úložiště by měl být standardní.
   - **Cílové dostupnosti :** Ve výchozím nastavení site recovery vytvoří novou sadu dostupnosti v cílové oblasti s příponou "asr" v názvu pro virtuální servery, které jsou součástí dostupnosti sady ve zdrojové oblasti. Pokud sada dostupnosti vytvořená obnovením webu již existuje, je znovu použita.
   - **Cílové zóny dostupnosti**: Ve výchozím nastavení site recovery přiřadí stejné číslo zóny jako zdrojové oblasti v cílové oblasti, pokud cílová oblast podporuje zóny dostupnosti.

     Pokud cílová oblast nepodporuje zóny dostupnosti, cílové virtuální počítače jsou ve výchozím nastavení nakonfigurovány jako jednotlivé instance. V případě potřeby můžete tyto virtuální počítače nakonfigurovat tak, aby byly součástí sad dostupnosti v cílové oblasti, kliknutím na tlačítko Přizpůsobit.

     >[!NOTE]
     >Po povolení replikace nelze změnit typ dostupnosti – jednu instanci, sadu dostupnosti nebo zónu dostupnosti. Chcete-li změnit typ dostupnosti, je třeba zakázat a povolit replikaci.
     >

   - **Zásady replikace**: Definuje nastavení pro historii uchovávání bodů obnovení a konzistentní frekvenci snímků aplikace. Ve výchozím nastavení Azure Site Recovery vytvoří nové zásady replikace s výchozím nastavením "24 hodin" pro uchovávání bodů obnovení a "4 hodiny" pro frekvenci konzistentní snímek aplikace.

     ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Povolení replikace pro přidané disky

Pokud přidáte disky do virtuálního počítače Azure, pro který je povolena replikace, dojde k následujícímu:
-   Stav replikace pro virtuální počítače zobrazuje upozornění a poznámka informuje, že jeden nebo více disků jsou k dispozici pro ochranu.
-   Pokud povolíte ochranu přidaných disků, upozornění zmizí po počáteční replikaci disku.
-   Pokud se rozhodnete nepovolit replikaci disku, můžete upozornění zavřít.


    ![Byl přidán nový disk.](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Chcete-li povolit replikaci pro přidaný disk, postupujte takto:

1.  V úschovně > **replikované položky**klikněte na virtuální ms, do kterého jste disk přidali.
2.  Klepněte na **položku Disky**a vyberte datový disk, pro který chcete povolit replikaci (tyto disky mají **stav Není chráněn).**
3.  V **části Podrobnosti disku**klepněte na tlačítko **Povolit replikaci**.

    ![Povolení replikace pro přidaný disk](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Po spuštění úlohy povolit replikaci a dokončení počáteční replikace se odebere upozornění stavu replikace pro problém s diskem.



## <a name="customize-target-resources"></a>Přizpůsobení cílových zdrojů

Můžete upravit výchozí nastavení cíle používané službou Site Recovery.

1. Chcete-li upravit výchozí cílové předplatné, klepněte na tlačítko **Přizpůsobit:** vedle možnosti Cílové předplatné. Vyberte předplatné ze seznamu všech předplatných dostupných ve stejném tenantovi Služby Azure Active Directory (AAD).

2. Chcete-li změnit výchozí nastavení, klepněte na **tlačítko Přizpůsobit:**
    - Ve **skupině cílových prostředků**vyberte skupinu prostředků ze seznamu všech skupin prostředků v cílovém umístění předplatného.
    - V **cílové virtuální síti**vyberte síť ze seznamu všech virtuálních sítí v cílovém umístění.
    - V **sadě dostupnosti**můžete do virtuálního počítače přidat nastavení sady dostupnosti, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - V **části Účty cílového úložiště**vyberte účet, který chcete použít.

        ![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Chcete-li změnit nastavení replikace, klepněte na tlačítko **Přizpůsobit.**
4. V **konzistenci více virtuálních lidí**vyberte virtuální chod, které chcete replikovat společně.
    - Všechny počítače v replikační skupině budou mít v případě převzetí služeb při selhání sdílené body obnovení konzistentní pro případ chyby a konzistentní vzhledem k aplikacím.
    - Povolení konzistence více virtuálních počítače může mít vliv na výkon pracovního vytížení (protože je náročné na procesor). Měla by být povolena pouze v případě, že počítače jsou spuštěny stejné úlohy a potřebujete konzistenci mezi více počítačů.
    - Pokud má například aplikace 2 virtuální počítače SQL Serveru a dva webové servery, měli byste do replikační skupiny přidat pouze virtuální počítače SQL Server.
    - Můžete zvolit maximálně 16 virtuálních her v replikační skupině.
    - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004.
    - Ujistěte se, že žádné zařízení brány firewall blokuje interní komunikaci mezi virtuálními počítačemi přes port 20004.
    - Pokud chcete, aby virtuální počítače SIF Byly součástí replikační skupiny, ujistěte se, že odchozí provoz na portu 20004 se ručně otevře podle pokynů pro konkrétní verzi Linuxu.
![Povolení replikace](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klepněte na tlačítko **Vytvořit cílový prostředek** > **Povolit replikaci**.
6. Po virtuálních dispozicí virtuálních aplikací pro replikaci můžete zkontrolovat stav stavu virtuálního zařízení v části **Replikované položky**

>[!NOTE]
>Během počáteční replikace může aktualizace stavu nějakou dobu trvat bez průběhu. Kliknutím na tlačítko **Aktualizovat** získáte nejnovější stav.
>

## <a name="next-steps"></a>Další kroky

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testu převzetí služeb při selhání.
