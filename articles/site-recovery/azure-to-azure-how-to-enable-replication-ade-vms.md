---
title: Povolení replikace pro šifrované virtuální počítače Azure v Azure Site Recovery
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače s povolenou Azure Disk Encryption z jedné oblasti Azure do jiné pomocí Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: fa4d61599e102f9a2580e704ee7a02486067daa2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135792"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikace virtuálních počítačů s podporou služby Azure Disk Encryption do jiné oblasti Azure

Tento článek popisuje, jak replikovat virtuální počítače Azure s povoleným Azure Disk Encryption (ADE) z jedné oblasti Azure do jiné.

>[!NOTE]
> Site Recovery v současné době podporuje ADE s a bez Azure Active Directory (AAD) pro virtuální počítače s operačními systémy Windows. Pro operační systémy Linux podporujeme pouze ADE bez AAD. Pro počítače s ADE 1,1 (bez AAD) Navíc musí virtuální počítače používat spravované disky. Virtuální počítače s nespravovanými disky se nepodporují. Pokud přepnete z ADE 0,1 (s AAD) na 1,1, budete muset po povolení 1,1 zakázat replikaci a povolit replikaci pro virtuální počítač.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a> Požadovaná uživatelská oprávnění
Site Recovery vyžaduje, aby měl uživatel oprávnění k vytvoření trezoru klíčů v cílové oblasti a zkopírování klíčů z trezoru klíčů zdrojové oblasti do trezoru klíčů cílové oblasti.

Aby bylo možné povolit replikaci virtuálních počítačů s povoleným šifrováním disku z Azure Portal, uživatel potřebuje následující oprávnění pro trezory klíčů **zdrojové oblasti i cílové oblasti** .

- Oprávnění trezoru klíčů
    - Seznam, vytvoření a získání
    
- Oprávnění tajného klíče trezoru klíčů
    - Operace správy tajných kódů
        - Získání, vypsání a nastavení
    
- Oprávnění klíče trezoru klíčů (požadováno jenom v případě, že virtuální počítače používají šifrovací klíč klíče k šifrování šifrovacích klíčů disku)
    - Operace správy klíčů
        - Získání, vypsání a vytvoření
    - Kryptografické operace
        - Dešifrování a šifrování

Pokud chcete spravovat oprávnění, otevřete na portálu prostředek trezoru klíčů. Přidejte požadovaná oprávnění pro uživatele. Následující příklad ukazuje, jak povolit oprávnění k *ContosoWeb2Keyvault* trezoru klíčů, který je ve zdrojové oblasti.

1. Přejděte na **domovské**  >  **trezory** klíčů  >  **ContosoWeb2KeyVault > zásady přístupu**.

   ![Okno oprávnění trezoru klíčů](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Uvidíte, že neexistují žádná uživatelská oprávnění. Vyberte **Přidat nový**. Zadejte informace o uživateli a oprávněních.

   ![Oprávnění trezoru klíčů](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Pokud uživatel, který povoluje zotavení po havárii (DR), nemá oprávnění ke kopírování klíčů, může správce zabezpečení, který má příslušná oprávnění, použít následující skript ke zkopírování šifrovacích tajných klíčů a klíčů do cílové oblasti.

Pokud chcete řešit oprávnění, přečtěte si téma [problémy s oprávněními k trezoru klíčů](#trusted-root-certificates-error-code-151066) dále v tomto článku.

>[!NOTE]
>Pokud chcete povolit replikaci virtuálních počítačů s povoleným šifrováním disku z portálu, budete pro trezory klíčů, tajné klíče a klíče potřebovat alespoň oprávnění "list".

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Zkopírování šifrovacích klíčů disku do oblasti DR pomocí skriptu PowerShellu

1. [Otevřete nezpracovaný kód skriptu "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Zkopírujte skript do souboru a pojmenujte ho **Copy-keys.ps1**.
3. Otevřete aplikaci Windows PowerShell a pokračujte do složky, kam jste soubor uložili.
4. Spusťte Copy-keys.ps1.
5. Zadejte přihlašovací údaje Azure pro přihlášení.
6. Vyberte **předplatné Azure** vašich virtuálních počítačů.
7. Počkejte, až se skupiny prostředků načtou, a pak vyberte **skupinu prostředků** vašich virtuálních počítačů.
8. Vyberte virtuální počítače ze zobrazeného seznamu. V seznamu jsou jenom virtuální počítače, které jsou povolené pro šifrování disků.
9. Vyberte **cílové umístění**.

    - **Trezory klíčů Disk Encryption**
    - **Trezory klíčů šifrovacího klíče**

   Ve výchozím nastavení Site Recovery vytvoří nový trezor klíčů v cílové oblasti. Název trezoru má příponu ASR, která je založená na šifrovacích klíčích disků zdrojového virtuálního počítače. Pokud již existuje Trezor klíčů, který byl vytvořen Site Recovery, je znovu použit. V případě potřeby vyberte v seznamu jiný Trezor klíčů.

## <a name="enable-replication"></a>Povolení replikace

V tomto příkladu je primární oblast Azure Východní Asie a sekundární oblast je jih Východní Asie.

1. V trezoru vyberte **+ replikovat**.
2. Všimněte si následujících polí.
    - **Zdroj**: bod původu virtuálních počítačů, v tomto případě je to **Azure**.
    - **Zdrojové umístění**: oblast Azure, ve které chcete chránit své virtuální počítače. V tomto příkladu je zdrojovým umístěním "Východní Asie."
    - **Model nasazení**: model nasazení Azure zdrojových počítačů.
    - **Zdrojové předplatné**: předplatné, ke kterému patří vaše zdrojové virtuální počítače. Může to být jakékoli předplatné, které je ve stejném Azure Active Directory tenant jako trezor služby Recovery Services.
    - **Skupina prostředků**: Skupina prostředků, do které patří vaše zdrojové virtuální počítače. V dalším kroku jsou uvedené pro ochranu všechny virtuální počítače ve vybrané skupině prostředků.

3. V **Virtual Machines**  >  **vyberte virtuální počítače**, vyberte všechny virtuální počítače, které chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**.

4. V **Nastavení** můžete nakonfigurovat následující nastavení cílové lokality.

    - **Cílové umístění**: umístění, kam se budou replikovat vaše zdrojová data virtuálního počítače. Site Recovery poskytuje seznam vhodných cílových oblastí na základě umístění vybraného počítače. Doporučujeme použít stejné umístění jako v umístění trezoru Recovery Services.
    - **Cílové předplatné**: cílové předplatné, které se používá pro zotavení po havárii. Ve výchozím nastavení je cílové předplatné stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků**: Skupina prostředků, do které patří všechny replikované virtuální počítače. Ve výchozím nastavení Site Recovery vytvoří novou skupinu prostředků v cílové oblasti. Název Získá příponu ASR. Pokud již existuje skupina prostředků, která byla vytvořena Azure Site Recovery, je znovu použita. Můžete ji také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolná oblast Azure s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť**: ve výchozím nastavení Site Recovery vytvoří novou virtuální síť v cílové oblasti. Název Získá příponu ASR. Je namapována na vaši zdrojovou síť a používá se pro jakoukoliv budoucí ochranu. [Přečtěte si další informace](./azure-to-azure-network-mapping.md) o mapování sítě.
    - **Cílové účty úložiště (Pokud zdrojový virtuální počítač nepoužívá spravované disky)**: ve výchozím nastavení Site Recovery vytvoří nový cílový účet úložiště tím, že mimicking konfiguraci úložiště zdrojového virtuálního počítače. Pokud účet úložiště již existuje, bude znovu použit.
    - **Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky)**: Site Recovery v cílové oblasti vytvoří nové repliky spravované disky pro zrcadlení spravovaných disků zdrojového virtuálního počítače stejného typu úložiště (Standard nebo Premium) jako spravované disky zdrojového virtuálního počítače.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje další účet úložiště s názvem *úložiště mezipaměti* ve zdrojové oblasti. Všechny změny ve zdrojových virtuálních počítačích jsou sledovány a odesílány do účtu úložiště mezipaměti. Pak se replikují do cílového umístění.
    - **Skupina dostupnosti**: ve výchozím nastavení Site Recovery vytvoří novou skupinu dostupnosti v cílové oblasti. Název má příponu ASR. Pokud skupina dostupnosti, kterou vytvořil Site Recovery, už existuje, použije se znovu.
    - **Trezory klíčů šifrovacího disku**: ve výchozím nastavení Site Recovery vytvoří nový trezor klíčů v cílové oblasti. Má příponu ASR založenou na klíčích pro šifrování disků zdrojového virtuálního počítače. Pokud Trezor klíčů, který byl vytvořen Azure Site Recovery, již existuje, bude znovu použit.
    - **Trezory klíčů šifrovacího** klíče: ve výchozím nastavení Site Recovery vytvoří nový trezor klíčů v cílové oblasti. Název má příponu ASR, která je založená na šifrovacích klíčích zdrojového virtuálního počítače. Pokud již existuje Trezor klíčů vytvořený pomocí Azure Site Recovery, bude znovu použit.
    - **Zásady replikace**: definuje nastavení pro historii uchovávání bodů obnovení a četnost snímků konzistentních vzhledem k aplikacím. Ve výchozím nastavení Site Recovery vytvoří novou zásadu replikace s výchozím nastavením *24 hodin* pro uchování bodu obnovení a *60 minut* pro frekvenci snímků konzistentní vzhledem k aplikacím.

## <a name="customize-target-resources"></a>Přizpůsobení cílových prostředků

Pomocí těchto kroků upravíte Site Recovery výchozí nastavení cíle.

1. Pokud chcete změnit výchozí cílové předplatné, vyberte **přizpůsobit** u možnosti cílové předplatné. Vyberte předplatné ze seznamu předplatných, která jsou k dispozici v tenantovi Azure AD.

2. Vyberte **přizpůsobit** vedle možnosti skupina prostředků, síť, úložiště a skupiny dostupnosti a upravte následující výchozí nastavení:
    - V poli **cílová skupina prostředků** vyberte skupinu prostředků ze seznamu skupin prostředků v cílovém umístění předplatného.
    - V poli **cílová virtuální síť** vyberte síť ze seznamu virtuálních sítí v cílovém umístění.
    - V případě **skupiny dostupnosti** můžete do virtuálního počítače přidat nastavení skupiny dostupnosti, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - U **cílových účtů úložiště** vyberte účet, který chcete použít.

2. Pokud chcete upravit následující výchozí nastavení, vyberte **přizpůsobit** u možnosti nastavení šifrování:
   - V případě **trezoru klíčů cílového disku** vyberte v seznamu trezorů klíčů v cílovém umístění předplatného možnost trezoru šifrovacího klíče cílového disku.
   - U **trezoru šifrovacího klíče cílového klíče** vyberte v seznamu trezorů klíčů v cílovém umístění předplatného možnost trezor šifrovacího klíče cílového klíče.

3. Vyberte **vytvořit cílový prostředek**  >  **Povolit replikaci**.
4. Po povolení replikace virtuálních počítačů můžete u **replikovaných položek** ověřit stav virtuálních počítačů.

>[!NOTE]
>Během počáteční replikace může aktualizace stavu nějakou dobu trvat, a to bez zjevného postupu. Kliknutím na **aktualizovat**  získáte nejnovější stav.

## <a name="update-target-vm-encryption-settings"></a>Aktualizovat nastavení šifrování cílového virtuálního počítače
V následujících scénářích budete muset aktualizovat nastavení šifrování cílového virtuálního počítače:
  - Povolili jste Site Recovery replikaci na virtuálním počítači. Později jste na zdrojovém virtuálním počítači povolili šifrování disku.
  - Povolili jste Site Recovery replikaci na virtuálním počítači. Později jste změnili šifrovací klíč disku nebo šifrovací klíč klíče na zdrojovém virtuálním počítači.

Můžete použít [skript](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) ke zkopírování šifrovacích klíčů do cílové oblasti a potom aktualizovat nastavení cílového šifrování v replikované položce repliky **služby Recovery Services**  >    >  **vlastnosti**  >  **COMPUTE a Network**.

![Dialogové okno aktualizace nastavení ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Řešení potíží s oprávněními trezoru klíčů během replikace virtuálních počítačů z Azure do Azure

Azure Site Recovery vyžaduje aspoň oprávnění ke čtení pro Trezor klíčů zdrojové oblasti a oprávnění k zápisu do trezoru klíčů cílové oblasti pro čtení tajného klíče a jeho zkopírování do trezoru klíčů cílové oblasti. 

**Příčina 1:** Nemáte oprávnění GET pro **Trezor klíčů zdrojové oblasti** pro čtení klíčů. </br>
**Jak opravit:** Bez ohledu na to, jestli jste správce předplatného, nebo ne, je důležité, abyste měli k trezoru klíčů oprávnění získat.

1. Přejděte do trezoru klíčů zdrojové oblasti, který je v tomto příkladu "ContososourceKeyvault" > **zásady přístupu** . 
2. V části **Vybrat Hlavní** přidejte uživatelské jméno. Příklad: " dradmin@contoso.com "
3. V části **oprávnění klíče** vyberte získat. 
4. V části **oprávnění tajného** kódu vyberte získat. 
5. Uložení zásad přístupu

**Příčina 2:** Nemáte požadovaná oprávnění k zápisu klíčů do **trezoru klíčů cílové oblasti** . </br>

*Například*: pokusíte se replikovat virtuální počítač, který má Trezor klíčů *ContososourceKeyvault* ve zdrojové oblasti.
Máte všechna oprávnění k trezoru klíčů zdrojové oblasti. Ale během ochrany vyberte už vytvořený Trezor klíčů ContosotargetKeyvault, který nemá oprávnění. Dojde k chybě.

Vyžaduje se oprávnění pro [cílový Trezor klíčů](#required-user-permissions) .

**Jak opravit:** Přejděte na **domácí**  >  **trezory** klíčů  >  **ContosotargetKeyvault**  >  **zásady přístupu** a přidejte příslušná oprávnění.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](site-recovery-test-failover-to-azure.md) o spuštění testovacího převzetí služeb při selhání.
