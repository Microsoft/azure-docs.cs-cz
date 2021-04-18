---
title: Přesun šifrovaných virtuálních počítačů Azure napříč oblastmi pomocí Azure Resource stěhovací
description: Přečtěte si, jak přesunout šifrované virtuální počítače Azure do jiné oblasti pomocí Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600688"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Kurz: přesouvání šifrovaných virtuálních počítačů Azure napříč oblastmi

Tento článek popisuje, jak přesunout šifrované virtuální počítače Azure do jiné oblasti Azure pomocí [Azure Resource stěhovací](overview.md). 

Šifrované virtuální počítače se dají popsat buď takto:

- Virtuální počítače s disky s povoleným Azure Disk Encryption. Další informace najdete v tématu [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Portal](../virtual-machines/windows/disk-encryption-portal-quickstart.md).
- Virtuální počítače, které používají klíče spravované zákazníkem (CMKs) pro šifrování v klidovém umístění nebo šifrování na straně serveru. Další informace najdete v tématu [použití Azure Portal k povolení šifrování na straně serveru pomocí klíčů spravovaných zákazníkem pro spravované disky](../virtual-machines/disks-enable-customer-managed-keys-portal.md).


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte požadavky. 
> * Pro virtuální počítače s povoleným Azure Disk Encryption zkopírujte klíče a tajné klíče z trezoru klíčů zdrojové oblasti do trezoru klíčů cílové oblasti.
> * Připravte se na přesunutí virtuálních počítačů a vyberte prostředky ve zdrojové oblasti, ze kterých je chcete přesunout.
> * Vyřešte závislosti prostředků.
> * Pro virtuální počítače s povoleným Azure Disk Encryption ručně přiřaďte cílový Trezor klíčů. Pro virtuální počítače, které používají šifrování na straně serveru, pomocí klíčů spravovaných zákazníkem, ručně přiřaďte sadu šifrování disků v cílové oblasti.
> * Přesuňte Trezor klíčů nebo sadu šifrování disku.
> * Připravte a přesuňte zdrojovou skupinu prostředků. 
> * Příprava a přesun dalších prostředků.
> * Rozhodněte, jestli se má přesun zrušit nebo potvrdit. 
> * Volitelně můžete po přesunutí odebrat prostředky ve zdrojové oblasti.

> [!NOTE]
> Tento kurz ukazuje nejrychlejší cestu pro vyzkoušení scénáře. Používá pouze výchozí možnosti. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/). Pak se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Požadavek |Podrobnosti
--- | ---
**Oprávnění předplatného** | Zkontrolujte, zda máte přístup *vlastníka* k předplatnému, které obsahuje prostředky, které chcete přesunout.<br/><br/> *Proč potřebuji přístup k vlastníkovi?* Při prvním přidání prostředku pro konkrétní dvojici zdroje a cíle v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types), která se dřív jmenovala jako identita spravované služby (MSI). Tato identita je pro předplatné důvěryhodná. Než budete moct vytvořit identitu a přiřadit jí požadované role (*Přispěvatel* a *Správce přístupu uživatele* ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* v předplatném. Další informace najdete v tématu [role správců pro klasický odběr, role Azure a role Azure AD](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).
**Podpora virtuálních počítačů** | Zkontrolujte, jestli jsou virtuální počítače, které chcete přesunout, podporované následujícím způsobem:<li>[Ověřte](support-matrix-move-region-azure-vm.md#windows-vm-support) podporované virtuální počítače s Windows.<li>[Ověřte](support-matrix-move-region-azure-vm.md#linux-vm-support) podporované virtuální počítače se systémem Linux a verze jádra.<li>Ověřte podporovaná nastavení [COMPUTE](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [úložiště](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)a [sítě](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
**Požadavky na Trezor klíčů (Azure Disk Encryption)** | Pokud jste pro virtuální počítače povolili Azure Disk Encryption, budete potřebovat Trezor klíčů ve zdrojové i cílové oblasti. Další informace najdete v tématu [Vytvoření trezoru klíčů](../key-vault/general/quick-create-portal.md).<br/><br/> Pro trezory klíčů ve zdrojové a cílové oblasti budete potřebovat tato oprávnění:<li>Klíčová oprávnění: operace správy klíčů (získání, seznam) a kryptografické operace (dešifrování a šifrování)<li>Tajná oprávnění: operace správy tajných kódů (získat, vypsat a nastavit)<li>Certifikát (list a Get)
**Sada šifrování disků (šifrování na straně serveru s CMK)** | Pokud používáte virtuální počítače se šifrováním na straně serveru, které používá CMK, budete potřebovat sadu šifrování disku ve zdrojové i cílové oblasti. Další informace najdete v tématu [Vytvoření sady šifrování disku](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Přesun mezi oblastmi není podporován, pokud používáte modul hardwarového zabezpečení (klíče HSM) pro klíče spravované zákazníkem.
**Kvóta cílové oblasti** | Předplatné potřebuje dostatek kvót k vytvoření prostředků, které přesouváte v cílové oblasti. Pokud nemá kvótu, [požádejte o další omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Poplatky za cílovou oblast** | Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte virtuální počítače. Použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-permissions-in-the-key-vault"></a>Ověření oprávnění v trezoru klíčů

Pokud přesouváte virtuální počítače, které mají povolenou Azure Disk Encryption, je třeba spustit skript, jak je uvedeno v části [zkopírování klíčů do cílového úložiště klíčů](#copy-the-keys-to-the-destination-key-vault) . Uživatelé, kteří spouštějí skript, by měli mít příslušná oprávnění. Chcete-li zjistit, jaká oprávnění jsou potřebná, přečtěte si následující tabulku. Možnosti změny oprávnění najdete tak, že přejdete do trezoru klíčů v Azure Portal. V části **Nastavení** vyberte **zásady přístupu**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Snímek obrazovky s odkazem na zásady přístupu v podokně nastavení trezoru klíčů" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Pokud uživatelská oprávnění nejsou na místě, vyberte **Přidat zásady přístupu** a pak zadejte oprávnění. Pokud uživatelský účet už má zásadu, nastavte v části **uživatel** oprávnění podle pokynů v následující tabulce.

Virtuální počítače Azure, které používají Azure Disk Encryption, můžou mít následující variace a vy budete muset nastavit oprávnění podle jejich relevantních součástí. Virtuální počítače můžou mít tyto:
- Výchozí možnost, kde je disk zašifrovaný pouze pomocí tajných klíčů.
- Bylo přidáno zabezpečení, které používá klíč [šifrovacího klíče (KEK)](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).

### <a name="source-region-key-vault"></a>Trezor klíčů zdrojové oblasti

Pro uživatele, kteří spouštějí skript, nastavte oprávnění pro následující komponenty: 

Součást | Potřebná oprávnění
--- | ---
Tajné kódy |  *Získat* <br></br> Vyberte **tajný klíč oprávnění**  >  **tajné operace správy** a pak vyberte **získat**. 
Klíče <br></br> Pokud používáte KEK, budete potřebovat tato oprávnění kromě oprávnění k tajným klíčům. | *Získat* a *dešifrovat* <br></br> Vyberte **klíč oprávnění**  >  **operace správy klíčů** a pak vyberte **získat**. V případě **kryptografických operací** vyberte možnost **dešifrovat**.

### <a name="destination-region-key-vault"></a>Trezor klíčů cílové oblasti

Ujistěte se, že je v **zásadách přístupu** zapnutá možnost **Azure Disk Encryption pro šifrování svazku** . 

Pro uživatele, kteří spouštějí skript, nastavte oprávnění pro následující komponenty: 

Součást | Potřebná oprávnění
--- | ---
Tajné kódy |  *Set* <br></br> Vyberte **tajný klíč oprávnění**  >  **tajné operace správy** a pak vyberte **nastavit**. 
Klíče <br></br> Pokud používáte KEK, budete potřebovat tato oprávnění kromě oprávnění k tajným klíčům. | *Získání*, *Vytvoření* a *šifrování* <br></br> Vyberte **klíč oprávnění**  >  **operace správy klíčů** a potom vyberte **získat** a **vytvořit** . V případě **kryptografických operací** vyberte možnost **Šifrovat**.

<br>

Kromě výše uvedených oprávnění je třeba v cílovém trezoru klíčů přidat oprávnění pro [identitu spravovaného systému](./common-questions.md#how-is-managed-identity-used-in-resource-mover) , kterou pracovní postup prostředků používá pro přístup k prostředkům Azure vaším jménem. 

1. V části **Nastavení** vyberte **Přidat zásady přístupu**. 
1. V části **Vybrat objekt zabezpečení** vyhledejte soubor MSI. Název MSI je ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. Pro soubor MSI přidejte následující oprávnění:

    Součást | Potřebná oprávnění
    --- | ---
    Tajné kódy|  *Získání* a *Výpis* <br></br> Vyberte **tajný klíč oprávnění**  >  **tajné operace správy** a pak vyberte **získat** a **seznam**. 
    Klíče <br></br> Pokud používáte KEK, budete potřebovat tato oprávnění kromě oprávnění k tajným klíčům. | *Získání* a *Výpis* <br></br> Vyberte **klíč oprávnění**  >  **operace správy klíčů** a pak vyberte **načíst** a **seznam**.

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Zkopírujte klíče do cílového trezoru klíčů.

Zkopírujte šifrovací klíče a klíče ze zdrojového trezoru klíčů do cílového trezoru klíčů pomocí [skriptu](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) , který poskytujeme.

- Spusťte skript v prostředí PowerShell. Doporučujeme použít nejnovější verzi prostředí PowerShell.
- Konkrétně skript vyžaduje tyto moduly:
    - Az.Compute
    - AZ. klíčů trezor (verze 3.0.0)
    - AZ. Accounts (verze 2.2.3)

Chcete-li spustit skript, postupujte následovně:

1. Otevřete [skript](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) na GitHubu.
1. Zkopírujte obsah skriptu do místního souboru a pojmenujte ho *Copy-keys.ps1*.
1. Spusťte skript.
1. Přihlaste se k webu Azure Portal.
1. V rozevíracích seznamech v okně **vstupy uživatele** vyberte zdrojové předplatné, skupinu prostředků a zdrojový virtuální počítač a potom vyberte cílové umístění a cílové trezory pro šifrování disků a klíčů.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Snímek obrazovky okna uživatelské vstupy pro zadání hodnot skriptu" :::

1. Vyberte tlačítko **Vybrat**. 
   
   Po skončení běhu skriptu se zobrazí zpráva s upozorněním, že CopyKeys bylo úspěšné.

## <a name="prepare-vms"></a>Příprava virtuálních počítačů

1. Až se ujistíte, že virtuální počítače splní [požadavky](#prerequisites), ujistěte se, že jsou virtuální počítače, které chcete přesunout, zapnuté. Všechny disky virtuálních počítačů, které chcete mít k dispozici v cílové oblasti, musí být připojené a inicializované na virtuálním počítači.
1. Pokud chcete zajistit, aby virtuální počítače měly nejnovější důvěryhodné kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů (CRL), udělejte toto:
    - Na virtuálních počítačích s Windows nainstalujte nejnovější aktualizace Windows.
    - V případě virtuálních počítačů se systémem Linux postupujte podle pokynů k distributorům, aby počítače měly nejnovější certifikáty a seznam CRL. 
1. Pokud chcete povolit odchozí připojení z virtuálních počítačů, proveďte jednu z následujících akcí:
    - Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, [povolte přístup k adresám URL](support-matrix-move-region-azure-vm.md#url-access).
    - Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě (NSG), vytvořte tato [pravidla značek služeb](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-the-resources-to-move"></a>Vyberte prostředky, které chcete přesunout.

- Libovolný typ podporovaného prostředku můžete vybrat ve všech skupinách prostředků ve zdrojové oblasti, kterou vyberete.  
- Prostředky můžete přesunout do cílové oblasti, která je ve stejném předplatném jako zdrojová oblast. Pokud chcete změnit předplatné, můžete to udělat po přesunu prostředků.

Chcete-li vybrat prostředky, postupujte následovně:

1. V Azure Portal vyhledejte **prostředek Resource stěhovací**. Pak v části **služby** vyberte **Azure Resource stěhovací**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Snímek obrazovky s výsledky hledání pro prostředek Azure Resource stěhovací v Azure Portal." :::

1. V podokně **Přehled** stěhovacího systému prostředků Azure vyberte **Přesun mezi oblastmi**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Snímek obrazovky s tlačítkem přesunout přes oblasti pro přidání prostředků pro přesun do jiné oblasti" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. V podokně **přesunout prostředky** vyberte kartu **zdroj + cíl** . Pak v rozevíracích seznamech vyberte zdrojové předplatné a oblast.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Stránku pro výběr zdrojové a cílové oblasti.." :::

1. V části **cíl** vyberte oblast, do které chcete virtuální počítače přesunout, a pak vyberte **Další**.

1. Vyberte kartu **prostředky k přesunutí** a potom vyberte **vybrat prostředky**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Snímek obrazovky podokna přesunout prostředky a tlačítko vybrat prostředky]." :::

1. V podokně **vybrat prostředky** vyberte virtuální počítače, které chcete přesunout. Jak je uvedeno v části [výběr prostředků k přesunutí](#select-the-resources-to-move) , můžete přidat pouze prostředky, které jsou podporovány pro přesun.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Snímek obrazovky podokna vybrat prostředky pro výběr virtuálních počítačů, které se mají přesunout" :::

    > [!NOTE]
    >  V tomto kurzu vybíráte virtuální počítač, který používá šifrování na straně serveru (Rayne-VM) s klíčem spravovaným zákazníkem, a virtuální počítač s povoleným šifrováním disku (Rayne-VM-ADE).

1. Vyberte **Hotovo**.
1. Vyberte kartu **prostředky, které chcete přesunout** , a pak vyberte **Další**.
1. Vyberte kartu **Revize** a pak zkontrolujte nastavení zdroje a cíle. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Snímek obrazovky podokna pro kontrolu nastavení zdroje a cíle" :::

1. Vyberte **pokračovat** a začněte přidávat prostředky.
1. Vyberte ikonu oznámení a sledujte průběh. Po úspěšném dokončení procesu vyberte v podokně **oznámení** **Přidat prostředky k přesunutí**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Snímek obrazovky s podoknem oznámení pro potvrzení, že se prostředky úspěšně přidaly" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Po výběru oznámení zkontrolujte prostředky na stránce **napříč oblastmi** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Snímek obrazovky přidaných prostředků se stavem Příprava čeká na vyřízení." :::

> [!NOTE]
> - Prostředky, které přidáte, jsou umístěny do stavu *Příprava čeká na vyřízení* .
> - Skupina prostředků pro virtuální počítače se přidá automaticky.
> - Pokud upravíte položky **Konfigurace cíle** tak, aby používaly prostředek, který již existuje v cílové oblasti, stav prostředku je nastaven na *potvrzení čeká na vyřízení*, protože pro něj nemusíte inicializovat přesun.
> - Pokud chcete odebrat prostředek, který se přidal, bude metoda, kterou použijete, záviset na tom, kde se nacházíte v procesu přesunutí. Další informace najdete v tématu [Správa přesunů kolekcí a skupin prostředků](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Vyřešit závislosti

1. Pokud se ve sloupci **problémy** zobrazí zpráva *ověřit závislosti* , vyberte tlačítko **ověřit závislosti** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Snímek obrazovky se zobrazeným tlačítkem ověřit závislosti" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Spustí se proces ověřování.
1. Pokud se najde závislosti, vyberte **přidat závislosti**.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Snímek obrazovky s tlačítkem přidat závislosti" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. V podokně **přidat závislosti** ponechte výchozí možnost **Zobrazit všechny závislosti** .

    - **Zobrazit všechny závislosti** prochází všemi přímými a nepřímými závislostmi prostředku. Například pro virtuální počítač se zobrazuje síťové rozhraní, virtuální síť, skupiny zabezpečení sítě (skupin zabezpečení sítě) atd.
    - **Zobrazit závislosti první úrovně** zobrazuje pouze přímé závislosti. Například pro virtuální počítač zobrazuje síťovou kartu, ale ne virtuální síť.
 
1. Vyberte závislé prostředky, které chcete přidat, a pak vyberte **přidat závislosti**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Snímek obrazovky se seznamem závislostí a tlačítkem přidat závislosti" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Znovu ověřte závislosti. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Snímek obrazovky podokna pro opětovné ověření závislostí" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Přiřazení cílových prostředků

Je nutné ručně přiřadit cílové prostředky, které jsou přidruženy k šifrování.

- Pokud přesouváte virtuální počítač, který má povolenou Azure Disk Encryption, jako závislost se zobrazí Trezor klíčů v cílové oblasti.
- Pokud přesouváte virtuální počítač se šifrováním na straně serveru, které používá CMKs, zobrazí se jako závislost sada šifrování disku nastavená v cílové oblasti. 
- Vzhledem k tomu, že tento kurz ukazuje přesunutí virtuálního počítače, který má povolenou Azure Disk Encryption a který používá CMK, zobrazí se jako závislosti jak úložiště cílového klíče, tak nastavení šifrování disku.

Pokud chcete přiřadit cílové prostředky ručně, udělejte toto:

1. V položce sada pro šifrování disku vyberte **prostředek není přiřazený** ve sloupci **Konfigurace cíle** .
1. V **nastavení konfigurace** vyberte možnost sada šifrování cílového disku a pak vyberte **Uložit změny**.
1. Můžete uložit a ověřit závislosti pro prostředek, který upravujete, nebo můžete uložit jenom změny a pak ověřit všechno, co upravíte současně.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Snímek obrazovky s podoknem cílová konfigurace pro uložení změn v cílové oblasti" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Po přidání cílového prostředku se změní stav sady šifrování disku na *potvrzení přesunutí čeká na vyřízení*.

1. V položce trezoru klíčů vyberte **prostředek není přiřazený** ve sloupci **Konfigurace cíle** . V části **nastavení konfigurace** vyberte cílový Trezor klíčů a uložte provedené změny. 

V této fázi se sada pro šifrování disku a stav trezoru klíčů změní na *čeká na přesunutí*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Snímek obrazovky podokna pro přípravu dalších prostředků" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

K potvrzení a dokončení procesu přesunutí pro prostředky šifrování postupujte takto:

1. V **různých oblastech** vyberte prostředek (sada šifrování disku nebo Trezor klíčů) a pak vyberte **Potvrdit přesunutí**.
1. V **Přesunutí prostředků** vyberte **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunu se stav prostředku změní na čeká se na *odstranění zdroje*.


## <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků 

Než budete moct připravit a přesunout virtuální počítače, musí být v cílové oblasti přítomna skupina prostředků virtuálních počítačů. 

### <a name="prepare-to-move-the-source-resource-group"></a>Příprava na přesun zdrojové skupiny prostředků

V průběhu přípravného procesu Resource dogeneruje šablony Azure Resource Manager (ARM) z nastavení skupiny prostředků. Prostředky ve skupině prostředků nejsou nijak ovlivněny.

Pokud chcete připravit přesun zdrojové skupiny prostředků, udělejte toto:

1. V **různých oblastech** vyberte zdrojovou skupinu prostředků a pak vyberte **připravit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Snímek obrazovky s tlačítkem příprava v podokně Příprava prostředků" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. V **Příprava prostředků** vyberte **připravit**.

> [!NOTE]
> Po přípravě přesunu se stav skupiny prostředků změní na *zahájit přesun čeká na vyřízení*. 

 
### <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků

Zahajte přesun zdrojové skupiny prostředků následujícím způsobem:

1. V podokně **napříč oblastmi** vyberte skupinu prostředků a pak vyberte **Spustit přesun**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Snímek obrazovky s tlačítkem zahájit přesun v podokně různé oblasti" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. V podokně **přesunout prostředky** vyberte možnost **Spustit přesun**. Stav skupiny prostředků se změní na *zahájit přesun* probíhá.   
1. Po zahájení přesunu se vytvoří cílová skupina prostředků na základě vygenerované šablony ARM. Stav zdrojové skupiny prostředků se změní na *čeká se na potvrzení přesunutí*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Snímek obrazovky s podoknem přesunutí prostředků, kde se změnil stav skupiny prostředků na hodnotu Potvrdit přesunutí čeká." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Pokud chcete potvrdit přesunutí a dokončit proces, udělejte toto:

1. V podokně **napříč oblastmi** vyberte skupinu prostředků a pak vyberte **Potvrdit přesunutí**.
1. V podokně **přesunout prostředky** vyberte **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunutí se stav zdrojové skupiny prostředků změní na čeká se na *odstranění zdroje*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Snímek obrazovky zdrojové skupiny prostředků zobrazující stav změněn na čeká na odstranění zdroje." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Příprava prostředků k přesunu

Po přesunutí prostředků šifrování a zdrojové skupiny prostředků se můžete připravit na přesun dalších prostředků, u kterých je aktuální stav *připravený na Příprava*.


1. V podokně **napříč oblastmi** ověřte znovu přesun a vyřešte všechny problémy.
1. Chcete-li upravit nastavení cíle před zahájením přesunutí, vyberte odkaz ve sloupci **Konfigurace cíle** pro daný prostředek a pak upravte nastavení. Pokud upravíte nastavení cílového virtuálního počítače, velikost cílového virtuálního počítače by neměla být menší než velikost zdrojového virtuálního počítače.
1. U prostředků, které mají stav *Příprava čeká* na přesunutí, vyberte **připravit**.
1. V podokně **připravit prostředky** vyberte **připravit**.

    - Během přípravy se na virtuální počítače nainstalují agent Azure Site Recovery mobility, který je bude replikovat.
    - Data virtuálních počítačů se pravidelně replikují do cílové oblasti. To nemá vliv na zdrojový virtuální počítač.
    - Možnost přesunout prostředek generuje šablony ARM pro ostatní zdrojové prostředky.

> [!NOTE]
> Po přípravě prostředků se jejich stav změní na *zahájit probíhající přesun*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Snímek obrazovky s podoknem &quot;Příprava prostředků&quot;, ve kterém se zobrazují prostředky ve stavu &quot;zahájení přesunu čeká na vyřízení&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Zahájit přesun

Teď, když jste připravili připravené prostředky, můžete zahájit přesun. 

1. V podokně **napříč oblastmi** vyberte prostředky, jejichž stav je *iniciování probíhajících přesunů*, a pak vyberte **Spustit přesun**.
1. V podokně **přesunout prostředky** vyberte možnost **Spustit přesun**.
1. Sledujte průběh přesunu na panelu oznámení.

    - Virtuální počítače repliky se v cílové oblasti vytvoří. Zdrojový virtuální počítač je vypnutý a dojde k nějakému výpadku (obvykle minuty).
    - Resource stěhovací znovu vytvoří další prostředky pomocí připravených šablon ARM. Obvykle se nejedná o výpadky.
    - Po přesunutí prostředků se jejich stav změní na *nedokončené přesunutí*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Snímek obrazovky se seznamem prostředků se stavem potvrzení přesunutí čeká na vyřízení." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Zahodit nebo potvrdit?

Po prvním přesunu se můžete rozhodnout, jestli ho chcete přesunout nebo zahodit. 

- **Zahodit**: přesunutí můžete zrušit, pokud ho testujete a nechcete skutečně přesunout zdrojový prostředek. Zrušením přesunutí se přesune prostředek, který *iniciuje stav přesunutí čeká na vyřízení* .
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení zdrojového prostředku se jeho stav změní na čeká se na *odstranění zdroje* a Vy se můžete rozhodnout, jestli ho chcete odstranit.


## <a name="discard-the-move"></a>Zrušit přesun 

Chcete-li zrušit přesunutí, postupujte následovně:

1. V podokně **napříč oblastmi** vyberte prostředky, jejichž stav je *potvrzení přesunutí čeká na vyřízení*, a pak vyberte **zrušit přesun**.
1. V podokně **Zrušit přesunutí** vyberte **Zrušit**.
1. Sledujte průběh přesunu na panelu oznámení.


> [!NOTE]
> Po zahození prostředků se stav virtuálního počítače změní na *nedokončené přesunutí*.

## <a name="commit-the-move"></a>Potvrdit přesun

Chcete-li dokončit proces přesunutí, potvrďte přesunutí následujícím způsobem: 

1. V podokně **napříč oblastmi** vyberte prostředky, jejichž stav je *potvrzení přesunutí čeká na vyřízení*, a pak vyberte **Potvrdit přesunutí**.
1. V podokně **potvrzení prostředků** vyberte **Potvrdit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Snímek obrazovky se seznamem prostředků pro potvrzení přesunutí prostředků" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Sledujte průběh potvrzení na panelu oznámení.

> [!NOTE]
> - Po potvrzení přesunu se virtuální počítače přestanou replikovat. Zdrojový virtuální počítač není ovlivněný potvrzením změn.
> - Proces potvrzení nemá vliv na zdrojové síťové prostředky.
> - Po potvrzení přesunutí se stav prostředku změní na čeká se na *odstranění zdroje*.



## <a name="configure-settings-after-the-move"></a>Konfigurovat nastavení po přesunutí

- Služba mobility není automaticky odinstalována z virtuálních počítačů. Odinstalujte ji ručně nebo ji nechte v případě, že plánujete přesunout server znovu.
- Po přesunutí upravte pravidla řízení přístupu na základě role (RBAC) v Azure.

## <a name="delete-source-resources-after-commit"></a>Odstranit zdrojové prostředky po potvrzení změn

Po přesunutí můžete případně Odstranit prostředky ve zdrojové oblasti. 

1. V podokně **napříč oblastmi** vyberte všechny zdrojové prostředky, které chcete odstranit, a pak vyberte **Odstranit zdroj**.
1. V možnosti **Odstranit zdroj** zkontrolujte, co se chystáte odstranit, a v **Potvrdit odstranění** zadejte **Ano**. Tato akce je nevratná, proto pečlivě zkontrolujte!
1. Po zadání **Ano** vyberte **Odstranit zdroj**.

> [!NOTE]
>  Na portálu pro přesun prostředků nemůžete odstranit skupiny prostředků, trezory klíčů ani instance SQL Server. Každou z nich je nutné odstranit jednotlivě ze stránky vlastností.


## <a name="delete-resources-that-you-created-for-the-move"></a>Odstranit prostředky, které jste vytvořili pro přesun

Po přesunutí můžete ručně odstranit kolekci přesunů a Site Recovery prostředky, které jste vytvořili během tohoto procesu.

- Kolekce přesunů je ve výchozím nastavení skrytá. Pokud se chcete podívat, jak to potřebujete, zapněte skryté prostředky.
- Úložiště mezipaměti má zámek, který je třeba odstranit, aby jej bylo možné odstranit.

Pokud chcete prostředky odstranit, postupujte takto: 
1. Vyhledejte prostředky ve skupině prostředků ```RegionMoveRG-<sourceregion>-<target-region>``` .
1. Zkontrolujte, jestli se všechny virtuální počítače a další zdrojové prostředky ve zdrojové oblasti přesunuly nebo odstranily. Tento krok zajistí, že neexistují žádné nedokončené prostředky, které je používají.
1. Odstranit prostředky:

    - Název kolekce přesunutí: ```movecollection-<sourceregion>-<target-region>```
    - Název účtu úložiště mezipaměti: ```resmovecache<guid>```
    - Název trezoru: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Šifrované virtuální počítače Azure a jejich závislé prostředky byly přesunuty do jiné oblasti Azure.


V dalším kroku zkuste přesunout databáze SQL Azure a elastické fondy do jiné oblasti.

> [!div class="nextstepaction"]
> [Přesunout prostředky SQL Azure](./tutorial-move-region-sql.md)
