---
title: Přesuňte virtuální počítače Azure do jiné oblasti Azure pomocí Azure Site Recovery
description: Pomocí Azure Site Recovery můžete přesunout virtuální počítače Azure z jedné oblasti Azure do jiné.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394828"
---
# <a name="move-vms-to-another-azure-region"></a>Přesun virtuálních počítačů do jiné oblasti Azure

Existují situace, kdy byste chtěli přesunout stávající virtuální počítače Azure s IaaS z jedné oblasti do druhé. Například chcete zlepšit spolehlivost a dostupnost stávajících virtuálních počítačů, zlepšit spravovatelnost nebo je přesunout z důvodů správy. Další informace najdete v tématu věnovaném [Azure VM Move Overview](azure-to-azure-move-overview.md). 

Službu [Azure Site Recovery](site-recovery-overview.md) můžete použít k přesunu virtuálních počítačů Azure do sekundární oblasti.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> 
> * Ověření požadavků pro přesun
> * Příprava zdrojových virtuálních počítačů a cílové oblasti
> * Zkopírujte data a Povolte replikaci.
> * Otestování konfigurace a provedení přesunu
> * Odstraní prostředky ve zdrojové oblasti.


> [!IMPORTANT]
> K přesunutí virtuálních počítačů Azure do jiné oblasti teď doporučujeme použít [Azure Resource stěhovací](../resource-mover/tutorial-move-region-virtual-machines.md). Resource stěhovací je ve verzi Public Preview a poskytuje:
> - Jediné centrum pro přesouvání prostředků napříč oblastmi.
> - Zkrácená doba přesunutí a složitost. Všechno, co potřebujete, je na jednom místě.
> - Jednoduché a konzistentní prostředí pro přesun různých typů prostředků Azure.
> - Snadný způsob, jak identifikovat závislosti mezi prostředky, které chcete přesunout. To vám pomůže přesunout související prostředky dohromady, takže vše funguje podle očekávání v cílové oblasti, a to po přesunutí.
> - Automatické vyčištění prostředků ve zdrojové oblasti, pokud je chcete po přesunutí odstranit.
> - Testování. Můžete si vyzkoušet přesunutí a pak ho zahodit, pokud nechcete provést úplné přesunutí.



> [!NOTE]
> V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure z jedné oblasti do jiné, jak je to. Pokud potřebujete zlepšit dostupnost tím, že přesunete virtuální počítače ve skupině dostupnosti na virtuální počítače připojené k zóně v jiné oblasti, přečtěte si [kurz přesunutí virtuálních počítačů Azure do zóny dostupnosti](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že virtuální počítače Azure jsou v oblasti Azure, ze které chcete přejít.
- Ověřte, zda [je podporována kombinace zdrojové oblasti a cílové](./azure-to-azure-support-matrix.md#region-support)oblasti, a proveďte odpovídající rozhodnutí o cílové oblasti.
- Ujistěte se, že rozumíte [komponentám a architektuře řešení](azure-to-azure-architecture.md).
- Zkontrolujte [omezení podpory a požadavky](azure-to-azure-support-matrix.md).
- Ověřte oprávnění účtu. Pokud jste si vytvořili bezplatný účet Azure, jste správcem předplatného. Pokud nejste správcem předplatného, spolupracujte se správcem a přiřaďte potřebná oprávnění. Pokud chcete pro virtuální počítač povolit replikaci a v podstatě kopírovat data pomocí Azure Site Recovery, musíte mít:

    - Oprávnění k vytvoření virtuálního počítače v prostředcích Azure. Předdefinovaná role Přispěvatel virtuálních počítačů má tato oprávnění, která zahrnují:
    - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
    - Oprávnění zapisovat na vybraný účet úložiště
    
    - Oprávnění ke správě Azure Site Recoverych operací. Role Přispěvatel Site Recovery má všechna oprávnění, která jsou nutná ke správě operací Site Recovery v trezoru služby Recovery Services.

- Ujistěte se, že všechny nejnovější kořenové certifikáty jsou na virtuálních počítačích Azure, které chcete přesunout. Pokud na virtuálním počítači nejsou nejnovější kořenové certifikáty, omezení zabezpečení zabrání v kopírování dat do cílové oblasti.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
    
- Pro virtuální počítače se systémem Linux postupujte podle pokynů, které poskytuje distributor pro Linux, a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači.
- Ujistěte se, že nepoužíváte ověřovací proxy server k řízení připojení k síti pro virtuální počítače, které chcete přesunout.

- Pokud virtuální počítač, který se pokoušíte přesunout, nemá přístup k Internetu, nebo k řízení odchozího přístupu používá proxy server brány firewall, [Projděte si požadavky](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. To zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu virtuálních počítačů s velikostmi, které se shodují se zdrojovými virtuálními počítači. Pokud používáte Site Recovery ke kopírování dat do cíle, Site Recovery zvolit stejnou velikost nebo nejbližší možnou velikost cílového virtuálního počítače.

- Ujistěte se, že jste vytvořili cílový prostředek pro každou součást identifikovanou v rozložení zdrojové sítě. Tento krok je důležitý, aby se zajistilo, že vaše virtuální počítače mají všechny funkce a funkce v cílové oblasti, které jste měli ve zdrojové oblasti.

     > [!NOTE] 
     > Azure Site Recovery automaticky zjistí a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit síť a přiřadit ji k virtuálnímu počítači v toku uživatele pro povolení replikace. Jak už bylo zmíněno později, budete muset ručně vytvořit všechny další prostředky v cílové oblasti.

    Pokud chcete vytvořit nejčastěji používané síťové prostředky, které jsou relevantní pro vás na základě konfigurace zdrojového virtuálního počítače, přečtěte si následující dokumentaci:
    - [Skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md)
    - [Služby vyrovnávání zatížení](../load-balancer/index.yml)
    -  [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    - Další síťové součásti najdete v [dokumentaci k síti](../index.yml?pivot=products&panel=network).



## <a name="prepare"></a>Příprava
Následující postup ukazuje, jak připravit virtuální počítač pro přesunutí pomocí Azure Site Recovery jako řešení. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Vytvoření trezoru v libovolné oblasti s výjimkou zdrojové oblasti

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Do vyhledávacího Recovery Services zadejte > klikněte na Recovery Services trezory.
1. V nabídce Recovery Services trezory klikněte na + Přidat.
1. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více předplatných, vyberte příslušné předplatné.
1. Vytvořte skupinu prostředků **ContosoRG**.
1. Zadejte oblast Azure. Pokud chcete zjistit podporované oblasti, přečtěte si článek geografická dostupnost v [Azure Site Recovery podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
1. V **Recovery Services trezory** vyberte **ContosoVMVault**  >  **replikované položky**  >  **a replikovat**.
1. V rozevíracím seznamu vyberte **Azure Virtual Machines**.
1. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
1. Vyberte model nasazení Resource Manager. Pak vyberte **zdrojové předplatné** a **skupinu prostředků zdroje**.
1. Kliknutím na **OK** uložte nastavení.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Povolení replikace pro virtuální počítače Azure a zahájení kopírování dat

Site Recovery načte seznam virtuálních počítačů, které jsou přidružené k předplatnému a skupině prostředků.

1. V dalším kroku vyberte virtuální počítač, který chcete přesunout, a pak vyberte **OK**.
1. V **Nastavení** vyberte **zotavení po havárii**.
1. V části **Konfigurovat zotavení po havárii** > **Cílová oblast** vyberte cílovou oblast, do které chcete replikaci provést.
1. U ostatních nastavení přijměte pro účely tohoto kurzu výchozí hodnoty.
1. Vyberte **Povolit replikaci**. Tento krok spustí úlohu, která povolí replikaci pro virtuální počítač.



## <a name="move"></a>Přesunout

Následující postup ukazuje, jak provést přesun do cílové oblasti.

1. Přejít do trezoru. V **Nastavení**  >  **replikované položky** vyberte virtuální počítač a pak vyberte **převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**.
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .
4. Po dokončení úlohy ověřte, že se virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.


## <a name="discard"></a>Zahodit 

Pokud jste si přepnuli přesunutý virtuální počítač a potřebujete ho změnit na bod převzetí služeb při selhání nebo se chcete vrátit k předchozímu bodu, v **replikovaných položkách** klikněte pravým tlačítkem myši na položku virtuální počítač > **změnit bod obnovení**. Tento krok poskytuje možnost zadat jiný bod obnovení a převzetí služeb při selhání. 


## <a name="commit"></a>Potvrzení 

Jakmile jste si kontrolovali přesunutý virtuální počítač a jste připraveni tuto změnu potvrdit, v **replikovaných položkách** vyberte virtuální počítač > **potvrzením** kliknutím pravým tlačítkem. Tento krok dokončí proces přesunutí do cílové oblasti. Počkejte, než se dokončí úloha potvrzení změn.

## <a name="clean-up"></a>Vyčištění

Následující postup vás provede postupem vyčištění zdrojové oblasti a souvisejících prostředků, které se použily k přesunutí.

Pro všechny prostředky, které se použily při přesunu:

- Přejít na virtuální počítač. Vyberte **Zakázat replikaci**. Tento krok zastaví proces kopírování dat pro virtuální počítač.

   > [!IMPORTANT]
   > Je důležité provést tento krok, abyste se vyhnuli tomu, že se bude účtovat Azure Site Recovery replikace.

Pokud nemáte žádné plány k opakovanému použití žádného ze zdrojových prostředků, proveďte tyto další kroky:

1. Odstraňte všechny relevantní síťové prostředky ve zdrojové oblasti, které jste identifikovali v části [požadavky](#prerequisites).
1. Odstraňte odpovídající účet úložiště ve zdrojové oblasti.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální počítač Azure do jiné oblasti Azure. Pro virtuální počítač, který jste přesunuli, teď můžete nakonfigurovat zotavení po havárii.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)
