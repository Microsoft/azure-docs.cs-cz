---
title: Přesun šifrovaných virtuálních počítačů Azure mezi oblastmi pomocí Azure Resource stěhovací
description: Přečtěte si, jak přesunout šifrované virtuální počítače Azure do jiné oblasti pomocí Azure Resource stěhovacího.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0bc70e14e341d9681c75933455eae6b0278724ca
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99982171"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Kurz: přesouvání šifrovaných virtuálních počítačů Azure napříč oblastmi

V tomto článku se dozvíte, jak přesunout šifrované virtuální počítače Azure do jiné oblasti Azure pomocí nástroje [Azure Resource stěhovací](overview.md). Tady je to, co znamená šifrování:

- Virtuální počítače s povoleným diskovým šifrováním Azure [Další informace](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Nebo virtuální počítače, které používají klíče spravované zákazníkem (CMKs) pro šifrování v klidovém případě (šifrování na straně serveru). [Další informace](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte požadavky. 
> * Pro virtuální počítače s povolenou službou Azure Disk Encryption zkopírujte klíče a tajné klíče z trezoru klíčů zdrojové oblasti do trezoru klíčů cílové oblasti.
> * Připravte virtuální počítače, abyste je přesunuli, a vyberte prostředky ve zdrojové oblasti, kterou chcete přesunout.
> * Vyřešte závislosti prostředků.
> * Pro virtuální počítače s povolenou službou Azure Disk Encryption ručně přiřaďte cílový Trezor klíčů. Pro virtuální počítače, které používají šifrování na straně serveru a klíče spravované zákazníkem, ručně přiřaďte sadu šifrování disků v cílové oblasti.
> * Přesuňte Trezor klíčů nebo sadu šifrování disku.
> * Připravte a přesuňte zdrojovou skupinu prostředků. 
> * Příprava a přesun dalších prostředků.
> * Rozhodněte, zda chcete přesun zrušit nebo potvrdit. 
> * Volitelně můžete po přesunutí odebrat prostředky ve zdrojové oblasti.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/). Pak se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

**Požadavek** |**Podrobnosti**
--- | ---
**Oprávnění předplatného** | Ověřte, že máte přístup *vlastníka* k předplatnému obsahujícímu prostředky, které chcete přesunout.<br/><br/> **Proč potřebuji přístup k vlastníkovi?** Při prvním přidání prostředku pro konkrétní dvojici zdroje a cíle v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve známou jako služba zjištění spravované služby (MSI)), která je pro předplatné důvěryhodná. Pokud chcete vytvořit identitu a přiřadit jí požadovanou roli (přispěvatel a správce přístupu uživatele ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* k tomuto předplatnému. [Přečtěte si další informace](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) o rolích Azure.
**Podpora virtuálních počítačů** | Ověřte, že virtuální počítače, které chcete přesunout, jsou podporované.<br/><br/> - [Ověřte](support-matrix-move-region-azure-vm.md#windows-vm-support) podporované virtuální počítače s Windows.<br/><br/> - [Ověřte](support-matrix-move-region-azure-vm.md#linux-vm-support) podporované virtuální počítače se systémem Linux a verze jádra.<br/><br/> – Ověřte podporovaná nastavení [COMPUTE](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [úložiště](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)a [sítě](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
**Požadavky na Trezor klíčů (Azure Disk Encryption)** | Pokud máte u virtuálních počítačů povolenou službu Azure Disk Encryption, budete k tomu potřebovat Trezor klíčů v cílové oblasti. [Vytvořte Trezor klíčů](../key-vault/general/quick-create-portal.md).<br/><br/> Pro trezory klíčů ve zdrojové a cílové oblasti budete potřebovat tato oprávnění:<br/><br/> -Klíčová oprávnění: operace správy klíčů (získat, seznam); Kryptografické operace (dešifrování a šifrování).<br/><br/> -Tajné oprávnění: operace správy tajných kódů (získat, vypsat a nastavit)<br/><br/> -Certificate (list a Get).
**Sada šifrování disků (šifrování na straně serveru s CMK)** | Pokud používáte virtuální počítače s šifrováním na straně serveru pomocí CMK, budete potřebovat sadu Encryption disk nastavenou v cílové oblasti. [Vytvořte sadu šifrování disku](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Přesun mezi oblastmi není podporován, pokud používáte klíče HSM pro klíče spravované zákazníkem.
**Kvóta cílové oblasti** | Předplatné potřebuje dostatek kvót k vytvoření prostředků, které přesouváte v cílové oblasti. Pokud nemá kvótu, [požádejte o další omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Poplatky za cílovou oblast** | Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte virtuální počítače. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) vám pomůžeme.


## <a name="verify-key-vault-permissions-azure-disk-encryption"></a>Ověřit oprávnění trezoru klíčů (Azure Disk Encryption)

Pokud přesouváte virtuální počítače s povolenou službou Azure Disk Encryption, ověřte nebo nastavte oprávnění v trezorech klíčů ve zdrojové a cílové oblasti, abyste zajistili, že přesun šifrovaných virtuálních počítačů bude fungovat podle očekávání. 

1. V Azure Portal otevřete Trezor klíčů ve zdrojové oblasti.
2. V části **Nastavení** vyberte **zásady přístupu**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Tlačítko pro otevření zásad přístupu trezoru klíčů" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

3. Pokud nejsou k dispozici žádná uživatelská oprávnění, vyberte **Přidat zásadu přístupu** a zadejte oprávnění. Pokud uživatelský účet již zásadu obsahuje, nastavte oprávnění v části **uživatel**.

    - Pokud jsou virtuální počítače, které chcete přesunout, povolené pomocí služby Azure Disk Encryption (ADE), vyberte v   >  **operacích správy klíčů** oprávnění ke klíčům **načíst** a **seznam** , pokud nejsou vybrané.
    - Pokud k šifrování šifrovacích klíčů disků používaných při šifrování na straně serveru používáte klíče spravované zákazníkem (CMKs), vyberte v části **klíčová**  >  **operace správy klíčů** možnost **získat** a **seznam**. V případě **kryptografických operací** navíc vyberte **možnost dešifrovat a** **Šifrovat** .
 
    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/set-vault-permissions.png" alt-text="Rozevírací seznam pro výběr oprávnění trezoru klíčů." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/set-vault-permissions.png":::

4. V **tajných oprávněních** a  **operacích správy tajných** kódů vyberte **získat**, **vypsat** a **nastavit**. 
5. Pokud přiřazujete oprávnění k novému uživatelskému účtu, vyberte v části **Vybrat objekt zabezpečení** uživatele, kterému přiřazujete oprávnění.
6. Ujistěte se, že je v **zásadách přístupu** zapnutá možnost **Azure Disk Encryption pro šifrování svazku** .
7. Opakujte postup pro Trezor klíčů v cílové oblasti.


### <a name="copy-the-keys-to-the-destination-key-vault"></a>Zkopírujte klíče do cílového trezoru klíčů.

Pomocí skriptu, který poskytujeme, musíte zkopírovat šifrovací tajné klíče a klíče z trezoru zdrojového klíče do cílového trezoru klíčů.

- Skript spustíte v prostředí PowerShell. Doporučujeme, abyste spustili nejnovější verzi prostředí PowerShell.
- Konkrétně skript vyžaduje tyto moduly:
    - Az.Compute
    - AZ. klíčů trezor (Version 3.0.0
    - AZ. Accounts (verze 2.2.3)

Spuštění proveďte takto:

1. Přejděte ke [skriptu](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) na GitHubu.
2. Zkopírujte obsah skriptu do místního souboru a pojmenujte ho *Copy-keys.ps1*.
3. Spusťte skript.
4. Přihlaste se k Azure.
5. V místní nabídce **zadání uživatele** vyberte zdrojové předplatné, skupinu prostředků a zdrojový virtuální počítač. Pak vyberte cílové umístění a cílové trezory pro disk a šifrování klíčů.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Automaticky otevírané na vstupní hodnoty skriptu." :::


6. Po dokončení skriptu na výstupu obrazovky se zobrazí zpráva, že CopyKeys bylo úspěšné.

## <a name="prepare-vms"></a>Příprava virtuálních počítačů

1. Po [kontrole, že virtuální počítače splňují požadavky](#prerequisites), se ujistěte, že jsou zapnuté virtuální počítače, které chcete přesunout. Všechny disky virtuálních počítačů, které mají být k dispozici v cílové oblasti, musí být připojeny a inicializovány ve virtuálním počítači.
3. Ověřte, že virtuální počítače mají nejnovější důvěryhodné kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů (CRL). Použijte následující postup:
    - Na virtuálních počítačích s Windows nainstalujte nejnovější aktualizace Windows.
    - V případě virtuálních počítačů se systémem Linux postupujte podle pokynů k distributorům, aby počítače měly nejnovější certifikáty a seznam CRL. 
4. Povolte odchozí připojení z virtuálních počítačů následujícím způsobem:
    - Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto [adresám URL](support-matrix-move-region-azure-vm.md#url-access) .
    - Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě (NSG), vytvořte tato [pravidla značek služeb](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Vyberte prostředky, které se mají přesunout.


- Libovolný typ podporovaného prostředku můžete vybrat ve všech skupinách prostředků ve zdrojové oblasti, kterou vyberete.  
- Prostředky přesunete do cílové oblasti, která se nachází ve stejném předplatném jako zdrojová oblast. Pokud chcete změnit předplatné, můžete to udělat po přesunu prostředků.

Vyberte prostředky následujícím způsobem:

1. V Azure Portal vyhledejte *prostředek Resource stěhovací*. Pak v části **služby** vyberte **Azure Resource stěhovací**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Výsledky hledání pro stěhovací společnosti v Azure Portal." :::

2. V **přehledu** klikněte na **přesunout mezi oblastmi**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Tlačítko pro přidání prostředků pro přesun do jiné oblasti." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. V části **přesunout prostředky**  >  **zdroj + cíl** vyberte zdrojové předplatné a oblast.
4. V části **cíl** vyberte oblast, do které chcete virtuální počítače přesunout. Potom klikněte na **Další**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Stránku pro výběr zdrojové a cílové oblasti.." :::

5. V nabídce **prostředky k přesunutí** klikněte na **vybrat prostředky**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Tlačítko pro výběr prostředku, který chcete přesunout.]." :::

6. V části **vybrat prostředky** vyberte virtuální počítače. Přidat lze pouze prostředky, které jsou [podporovány pro Move](#prepare-vms). Pak klikněte na **Hotovo**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Na stránce vyberte virtuální počítače, které chcete přesunout." :::

    > [!NOTE]
    >  V tomto kurzu vybíráme virtuální počítač, který používá šifrování na straně serveru (Rayne-VM) s klíčem spravovaným zákazníkem, a virtuální počítač s povoleným šifrováním disku (Rayne-VM-ADE).

7.  V nabídce **prostředky k přesunutí** klikněte na **Další**.
8. V části **Kontrola** zkontrolujte nastavení zdroje a cíle. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Stránku pro kontrolu nastavení a pokračovat v přesunutí." :::

9. Klikněte na **pokračovat** a začněte přidávat prostředky.
10. Vyberte ikonu oznámení a sledujte průběh. Po úspěšném dokončení procesu přidání vyberte **Přidat prostředky pro přesun** v oznámeních.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Oznámení pro potvrzení prostředků bylo úspěšně přidáno." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Po kliknutí na oznámení zkontrolujte prostředky na stránce **napříč oblastmi** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Stránky zobrazující přidané prostředky s připraveným přípravam" :::

> [!NOTE]
> - Prostředky, které přidáte, jsou umístěny do stavu *Příprava čeká na vyřízení* .
> - Skupina prostředků pro virtuální počítače se přidá automaticky.
> - Pokud upravíte položky **Konfigurace cíle** tak, aby používaly prostředek, který již existuje v cílové oblasti, stav prostředku je nastaven na *potvrzení čeká na vyřízení*, protože pro něj nemusíte iniciovat jeho přesunutí.
> - Pokud chcete odebrat prostředek, který byl přidán, metoda, která bude záviset na tom, kde se nacházíte v procesu přesunutí. [Přečtěte si další informace](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Vyřešit závislosti

1. Pokud se ve sloupci **problémy** zobrazí zpráva *ověřit závislosti* , vyberte tlačítko **ověřit závislosti** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="NNový pro kontrolu závislostí." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Spustí se proces ověřování.
2. Pokud se najde závislosti, klikněte na **přidat závislosti** .  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Tlačítko pro přidání závislostí." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. V části **přidat závislosti** ponechte výchozí možnost **Zobrazit všechny závislosti** .

    - **Zobrazit všechny závislosti** prochází všemi přímými a nepřímými závislostmi prostředku. Například pro virtuální počítač se zobrazí síťové rozhraní, virtuální síť, skupiny zabezpečení sítě (skupin zabezpečení sítě) atd.
    - **Zobrazit závislosti na první úrovni** zobrazuje pouze přímé závislosti. Například pro virtuální počítač zobrazuje síťovou kartu, ale ne virtuální síť.
 
4. Vyberte závislé prostředky, které chcete přidat > **přidat závislosti**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Vyberte závislosti ze seznamu závislostí." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Znovu ověřte závislosti. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Znovu ověřte stránku." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Přiřazení cílových prostředků

Cílové prostředky přidružené k šifrování potřebují ruční přiřazení.

- Pokud přesouváte virtuální počítač, který má Azure Disk Encryption (ADE), zobrazí se jako závislost Trezor klíčů v cílové oblasti.
- Pokud přesouváte virtuální počítač, který má šifrování na straně serveru, které používá vlastní klíče (CMKs), zobrazí se jako závislost sada šifrování disku v cílové oblasti. 
- Vzhledem k tomu, že v tomto kurzu přesunete virtuální počítač s povoleným ADE a virtuální počítač s použitím CMK, zobrazí se jako závislosti jak úložiště cílového klíče, tak sada šifrování disku.

Přiřaďte ručně následujícím způsobem:

1. V položce sada pro šifrování disku vyberte **prostředek není přiřazený** ve sloupci **Konfigurace cíle** .
2. V části **nastavení konfigurace** vyberte možnost sada šifrování cílového disku. Pak vyberte **Uložit změny**.
3. Můžete vybrat, jestli se mají uložit a ověřit závislosti pro prostředek, který upravujete, nebo jenom Uložit změny a ověřit všechno, co v jednom přechodu upravíte.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Na stránce vyberte možnost sada šifrování disku v cílové oblasti." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Po přidání cílového prostředku se změní stav sady šifrování disku na *nedokončené přesunutí*.
3. V položce trezoru klíčů vyberte **prostředek není přiřazený** ve sloupci **Konfigurace cíle** . **Nastavení konfigurace**, vyberte cílový Trezor klíčů. Uložte změny. 

V této fázi je nastavená jak nastavení šifrování disku, tak i stav trezoru klíčů pro *potvrzení přesunutí čeká na vyřízení*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Stránku pro výběr Příprava na další prostředky." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

K potvrzení a dokončení procesu přesunutí pro prostředky šifrování.

1. V **oblasti napříč oblastmi** vyberte prostředek (sada Disk Encryption nebo Trezor klíčů) > **Potvrdit přesunutí**.
2. LN **přesunout prostředky**, klikněte na **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunutí je prostředek ve stavu odstranění zdroje, který *čeká na vyřízení* .


## <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků 

Než budete moct připravit a přesunout virtuální počítače, musí být v cílové oblasti přítomna skupina prostředků virtuálních počítačů. 

### <a name="prepare-to-move-the-source-resource-group"></a>Příprava na přesun zdrojové skupiny prostředků

Během procesu přípravy Resource dogeneruje pomocí nastavení skupiny prostředků Azure Resource Manager (ARM) šablony. Prostředky ve skupině prostředků nejsou ovlivněny.

Připravte následujícím způsobem:

1. V **různých oblastech** vyberte zdrojovou skupinu prostředků > **připravit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Připravte skupinu prostředků." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. V nabídce **Příprava prostředků** klikněte na **připravit**.

> [!NOTE]
> Po přípravě skupiny prostředků je stav *zahájit přesun čeká na vyřízení* . 

 
### <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků

Zahajte přesun následujícím způsobem:

1. V **různých oblastech** vyberte skupinu prostředků, > **zahájit přesun** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Tlačítko pro zahájení přesunu" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. LN **přesunout prostředky**, klikněte na **Spustit přesun**. Skupina prostředků se přesune do stavu *zahájení probíhajícího přesunu* .   
3. Po zahájení přesunu se vytvoří cílová skupina prostředků na základě vygenerované šablony ARM. Zdrojová skupina prostředků se přesune do stavu *čeká na přesunutí* .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Zkontrolujte stav potvrzení přesunutí čeká na vyřízení." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Potvrzení a dokončení procesu přesunutí:

1. V **oblasti napříč oblastmi** vyberte skupinu prostředků > **Potvrdit přesunutí**.
2. LN **přesunout prostředky**, klikněte na **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunutí je zdrojová skupina prostředků ve stavu *čekání na odstranění zdroje* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Zkontrolujte stav přesunutí čeká na vyřízení." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Příprava prostředků k přesunu

Po přesunutí prostředků šifrování a zdrojové skupiny prostředků se můžete připravit na přesun dalších prostředků, které jsou ve stavu *Příprava čekají* .


1. V **různých oblastech** znovu ověřte a vyřešte všechny problémy.
2. Pokud chcete před zahájením přesunu upravit nastavení cíle, vyberte odkaz ve sloupci **Konfigurace cíle** pro daný prostředek a upravte nastavení. Pokud upravíte nastavení cílového virtuálního počítače, velikost cílového virtuálního počítače by neměla být menší než velikost zdrojového virtuálního počítače.
3. Vyberte **Příprava** pro prostředky ve stavu *Příprava čeká na dokončení* , který chcete přesunout.
3. V **Příprava prostředků** vyberte **připravit** .

    - Během procesu přípravy se na virtuální počítače nainstalují agent Azure Site Recovery mobility, aby se replikoval.
    - Data virtuálních počítačů se pravidelně replikují do cílové oblasti. To nemá vliv na zdrojový virtuální počítač.
    - Možnost přesunout prostředek generuje šablony ARM pro ostatní zdrojové prostředky.

Po přípravě prostředků se stav *čeká na zahájení přesunu* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Stránka znázorňující prostředky v iniciování stavu čeká na přesunutí." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Zahájit přesun

Díky připraveným prostředkům teď můžete zahájit přesun. 

1. V **různých oblastech** vyberte prostředky se stavem *zahájit přesun čeká na vyřízení*. Pak klikněte na **Spustit přesun**.
2. V nabídce **přesunout prostředky** klikněte na možnost **Spustit přesun**.
3. Sledujte průběh přesunu na panelu oznámení.

    - Virtuální počítače repliky se v cílové oblasti vytvoří. Zdrojový virtuální počítač je vypnutý a dojde k nějakému výpadku (obvykle minuty).
    - Stěhovací společnosti znovu vytvoří další prostředky pomocí připravených šablon ARM. Obvykle se nejedná o výpadky.
    - Po přesunutí prostředků se stav přesune do stavu *čeká na potvrzení* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Stránka znázorňující prostředky ve stavu přesunutí čeká na vyřízení." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Zahodit nebo potvrdit?

Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunutí potvrdit, nebo ho zahodit. 

- **Zahodit**: při testování můžete zrušit jeho přesunutí a nechcete skutečně přesunout zdrojový prostředek. Zrušením přesunutí se daný prostředek vrátí do stavu *zahájení přesunu čeká na vyřízení*.
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení bude zdrojový prostředek ve stavu *čeká na odstranění zdroje* a Vy se můžete rozhodnout, jestli ho chcete odstranit.


## <a name="discard-the-move"></a>Zrušit přesun 

Přesunutí můžete zrušit následujícím způsobem:

1. V **různých oblastech** vyberte prostředky s *potvrzením změny stavu čeká na přesunutí* a klikněte na **zrušit přesun**.
2. V nabídce **Zrušit přesunutí** klikněte na **Zahodit**.
3. Sledujte průběh přesunu na panelu oznámení.


> [!NOTE]
> Po zahození prostředků jsou virtuální počítače v *nedokončeném stavu přesunutí* .

## <a name="commit-the-move"></a>Potvrdit přesun

Pokud chcete dokončit proces přesunutí, potvrďte přesunutí. 

1. V **různých oblastech** vyberte prostředky s *potvrzením změny stavu čeká* na potvrzení a klikněte na **Potvrdit přesunutí**.
2. V nabídce **potvrzení prostředků** klikněte na **Potvrdit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Stránku pro potvrzení přesunutí prostředků." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Sledujte průběh potvrzení na panelu oznámení.

> [!NOTE]
> - Po potvrzení přesunu se virtuální počítače přestanou replikovat. Zdrojový virtuální počítač není ovlivněný potvrzením změn.
> - Potvrzení změn nemá vliv na zdrojové síťové prostředky.
> - Po potvrzení přesunu jsou prostředky ve stavu *odstranění zdroje, který čeká na vyřízení* .



## <a name="configure-settings-after-the-move"></a>Konfigurovat nastavení po přesunutí

- Služba mobility není automaticky odinstalována z virtuálních počítačů. Odinstalujte ji ručně nebo ji nechte v případě, že plánujete přesunout server znovu.
- Upravte pravidla řízení přístupu na základě role Azure (Azure RBAC) po přesunutí.

## <a name="delete-source-resources-after-commit"></a>Odstranit zdrojové prostředky po potvrzení změn

Po přesunutí můžete případně Odstranit prostředky ve zdrojové oblasti. 

1. V **oblasti napříč oblastmi** vyberte všechny zdrojové prostředky, které chcete odstranit. pak vyberte **Odstranit zdroj**.
2. V možnosti **Odstranit zdroj** zkontrolujte, co se chystáte odstranit, a v **Potvrdit odstranění** zadejte **Ano**. Tato akce je nevratná, proto pečlivě zkontrolujte!
3. Po zadání **Ano** vyberte **Odstranit zdroj**.

> [!NOTE]
>  Na portálu pro přesun prostředků nemůžete odstranit skupiny prostředků, trezory klíčů ani SQL Server servery. Je nutné je odstranit jednotlivě ze stránky vlastností pro každý prostředek.


## <a name="delete-additional-resources-created-for-move"></a>Odstranění dalších prostředků vytvořených pro přesun

Po přesunutí můžete ručně odstranit kolekci přesunů a Site Recovery prostředky, které byly vytvořeny.

- Kolekce přesunů je ve výchozím nastavení skrytá. Pokud se chcete podívat, jak to potřebujete, zapněte skryté prostředky.
- Úložiště mezipaměti má zámek, který je třeba odstranit, aby jej bylo možné odstranit.

Odstraňte následujícím způsobem: 
1. Vyhledejte prostředky ve skupině prostředků ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Ověřte, že se všechny virtuální počítače a další zdrojové prostředky ve zdrojové oblasti přesunuly nebo odstranily. Tím se zajistí, že se nepoužívají žádné nedokončené prostředky.
2. Odstranit prostředky:

    - Název kolekce přesunutí je ```movecollection-<sourceregion>-<target-region>``` .
    - Název účtu úložiště mezipaměti je ```resmovecache<guid>```
    - Název trezoru je ```ResourceMove-<sourceregion>-<target-region>-GUID``` .
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Šifrované virtuální počítače Azure a jejich závislé prostředky byly přesunuty do jiné oblasti Azure.


Nyní se snažíte přesunout databáze SQL Azure a elastické fondy do jiné oblasti.

> [!div class="nextstepaction"]
> [Přesunout prostředky SQL Azure](./tutorial-move-region-sql.md)
