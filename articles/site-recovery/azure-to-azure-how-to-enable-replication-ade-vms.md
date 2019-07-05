---
title: Konfigurace replikace pro virtuální počítače s podporou Azure Disk Encryption v Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat replikace pro Azure Disk Encryption virtuální počítače s podporou z jedné oblasti Azure do jiné pomocí Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b2e9bf7fbe7d5940b517d97dcc15d21c30835001
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449214"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Azure Disk Encryption povolené virtuální počítače replikovat do jiné oblasti Azure

Tento článek popisuje, jak replikovat virtuální počítače s podporou Azure Disk Encryption z jedné oblasti Azure do jiné.

>[!NOTE]
>Azure Site Recovery aktuálně podporuje pouze virtuální počítače Azure, na kterých běží operační systém Windows a, které jsou [povoleno šifrování se službou Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a id="required-user-permissions"></a> Požadovaná uživatelská oprávnění
Site Recovery vyžaduje, aby uživatel měl oprávnění k vytvoření služby key vault v cílové oblasti a kopie klíče oblastí.

Pokud chcete povolit replikaci povolené šifrování disků virtuálních počítačů na webu Azure Portal, uživatel potřebuje následující oprávnění:

- Oprávnění služby Key vault
    - List
    - Vytvořit
    - Získat

-   Oprávnění tajného kódu trezoru klíčů
    - List
    - Vytvořit
    - Získat

- Oprávnění klíče služby Key vault (nutné jenom v případě, že virtuální počítače použít šifrovací klíč klíče k šifrování klíče pro šifrování disků)
    - List
    - Získat
    - Vytvořit
    - Šifrování
    - Dešifrování

Pokud chcete spravovat oprávnění, přejděte k prostředku služby key vault na portálu. Přidáte požadovaná oprávnění pro uživatele. Následující příklad ukazuje, jak používat sady oprávnění k trezoru klíčů *ContosoWeb2Keyvault*, což je ve zdrojové oblasti.

1. Přejděte na **Domů** > **Keyvaults** > **ContosoWeb2KeyVault > zásady přístupu**.

   ![Okno trezoru klíčů oprávnění](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Uvidíte, že neexistují žádná uživatelská oprávnění. Vyberte **přidat nový**. Zadání informací o uživatelích a oprávnění.

   ![oprávnění služby keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Pokud uživatel, který je povolení zotavení po havárii (DR) nemá oprávnění ke zkopírování klíče, správce zabezpečení, který má příslušná oprávnění můžete použít následující skript ke kopírování šifrování tajných kódů a klíčů do cílové oblasti.

Řešení potíží s oprávnění, najdete v tématu [klíče trezoru problémům s oprávněním](#trusted-root-certificates-error-code-151066) dále v tomto článku.

>[!NOTE]
>Pokud chcete povolit replikaci povolené šifrování disků virtuálních počítačů z portálu, můžete potřebovat nejméně "seznam" oprávnění k trezorům klíčů, tajných kódů a klíčů.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Zkopírujte Disk šifrovací klíče do oblasti zotavení po Havárii pomocí skriptu prostředí PowerShell

1. [Otevřete kód skriptu raw "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Zkopírujte skript do souboru a pojmenujte ho **kopírování keys.ps1**.
3. Otevřete aplikaci Windows Powershellu a přejděte do složky, kam jste soubor uložili.
4. Execute Copy-keys.ps1.
5. Zadejte přihlašovací údaje pro přihlášení Azure.
6. Vyberte **předplatného Azure** vašich virtuálních počítačů.
7. Počkejte pro skupiny prostředků pro načtení a pak vyberte **skupiny prostředků** vašich virtuálních počítačů.
8. Vyberte virtuální počítače, ze seznamu, který se zobrazí. Pouze virtuální počítače, které jsou povoleny pro šifrování disků jsou na seznamu.
9. Vyberte **cílové umístění**.

    - **Trezory klíčů šifrování disku**
    - **Trezory klíčů šifrování pomocí klíče**

   Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti nový trezor klíčů. Název trezoru má příponou "Azure Site Recovery", který je založen na klíče pro šifrování disků zdrojového virtuálního počítače. Pokud služby key vault už existuje, který byl vytvořen službou Site Recovery, použije se Tenhle certifikát. Ze seznamu v případě potřeby vyberte jiný trezor klíčů.

## <a name="enable-replication"></a>Povolení replikace

V tomto příkladu primární oblast Azure je východní Asie a jihovýchodní Asie je sekundární oblasti.

1. V trezoru, vyberte **+ replikovat**.
2. Poznámka: následující pole.
    - **Zdroj**: Bod počátek virtuálních počítačů, které v tomto případě je **Azure**.
    - **Umístění zdroje**: Oblasti Azure, ve které chcete chránit virtuální počítače. V tomto příkladu se zdrojové umístění "Východní Asie."
    - **Model nasazení**: Model nasazení Azure zdrojový počítač.
    - **Zdrojové předplatné**: Předplatné, do které patří vaše zdrojové virtuální počítače. Může být libovolné předplatné, které je ve stejném tenantovi Azure Active Directory jako váš trezor služby recovery services.
    - **Skupina prostředků**: Skupina prostředků, do které patří vaše zdrojové virtuální počítače. Pro ochranu v dalším kroku jsou uvedeny všechny virtuální počítače ve vybrané skupině prostředků.

3. V **virtuálních počítačů** > **výběr virtuálních počítačů**, vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Pak vyberte **OK**.

4. V **nastavení**, můžete nakonfigurovat následující nastavení cílové lokality.

    - **Cílové umístění**: Umístění, kde se budou replikovat svá zdrojová data virtuálního počítače. Site Recovery poskytuje seznam vhodná cílová oblastí na základě umístění vybraný počítač. Doporučujeme použít stejné umístění jako umístění trezoru služby Recovery Services.
    - **Cílové předplatné**: Cílové předplatné, který se používá pro zotavení po havárii. Ve výchozím nastavení cílové předplatné je stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků**: Skupina prostředků, na kterém jsou všechny vaše replikované virtuální počítače patří. Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou skupinu prostředků. Získá název přípony "Azure Site Recovery". Pokud skupina prostředků už existuje, který byl vytvořen pomocí Azure Site Recovery, použije se Tenhle certifikát. Můžete také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolné oblasti Azure, s výjimkou oblasti, kde jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť**: Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou virtuální síť. Získá název přípony "Azure Site Recovery". Má namapované na zdrojovou síť a použít pro všechny budoucí ochrany. [Další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
    - **Cílové účty úložiště (Pokud je váš zdrojový virtuální počítač nepoužívá spravované disky)** : Ve výchozím nastavení vytvoří Site Recovery nový cílový účet úložiště podle tak napodobuje konfiguraci úložiště zdrojového virtuálního počítače. Pokud účet úložiště už existuje, je znovu.
    - **Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky)** : Site Recovery vytvoří nové spravované disky repliky v cílové oblasti zrcadlící spravované disky zdrojového Virtuálního počítače ze stejného typu úložiště (standard nebo premium) jako spravované disky zdrojového Virtuálního počítače.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje účet úložiště, s názvem *úložiště do mezipaměti* ve zdrojové oblasti. Všechny změny na zdrojových virtuálních počítačů jsou sledovány a odešle na účet úložiště mezipaměti. Budete pak replikují do cílového umístění.
    - **Skupina dostupnosti**: Ve výchozím nastavení vytvoří Site Recovery novou v cílové oblasti skupinu dostupnosti. Název může mít příponu "Azure Site Recovery". Pokud skupinu dostupnosti, které bylo vytvořeno pomocí Site Recovery už existuje, je znovu.
    - **Trezory klíčů šifrování disku**: Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti nový trezor klíčů. Má příponou "Azure Site Recovery", který je založen na klíče pro šifrování disků zdrojového virtuálního počítače. Pokud se trezor klíčů, které bylo vytvořeno pomocí Azure Site Recovery už existuje, je znovu.
    - **Trezory klíčů šifrovací klíče**: Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti nový trezor klíčů. Název má příponou "Azure Site Recovery", který je založen na klíče šifrovací klíče zdrojového virtuálního počítače. Pokud se trezor klíčů Azure Site Recovery vytvoří již existuje, je znovu.
    - **Zásady replikace**: Definuje nastavení historie uchovávání bodů obnovení a frekvence pořizování snímků konzistentních s aplikací. Ve výchozím nastavení vytvoří Site Recovery novou zásadu replikace s výchozími nastaveními z *24 hodin* uchování bodu obnovení a *60 minut* pro frekvence pořizování snímků konzistentních s aplikací.

## <a name="customize-target-resources"></a>Přizpůsobení cílové prostředky

Následujícím postupem změnit výchozí nastavení cíle Site Recovery.

1. Vyberte **vlastní** vedle "Cílové předplatné" upravit výchozí cílové předplatné. Vyberte předplatné ze seznamu předplatných, které jsou k dispozici v tenantovi Azure AD.

2. Vyberte **vlastní** vedle "skupiny prostředků, síť, úložiště a skupiny dostupnosti" upravit následující nastavení:
    - Pro **cílová skupina prostředků**, vyberte skupinu prostředků ze seznamu skupin prostředků v cílovém umístění předplatného.
    - Pro **cílovou virtuální sítí**, vyberte ze seznamu virtuálních sítí v cílovém umístění v síti.
    - Pro **dostupnosti**, můžete přidat nastavení skupinu dostupnosti pro virtuální počítač, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - Pro **účty úložiště v cíli**, vyberte účet, který chcete použít.

2. Vyberte **vlastní** vedle "Nastavení šifrování" upravit následující nastavení:
   - Pro **služby key vault cílový disk encryption**, vyberte trezor klíčů šifrování disku cílového seznamu trezorů klíčů v cílovém umístění předplatného.
   - Pro **cílové šifrování klíče služby key vault**, vyberte cíl šifrování klíče služby key vault ze seznamu trezorů klíčů v cílovém umístění předplatného.

3. Vyberte **vytvořit cílový prostředek** > **povolením replikace**.
4. Po virtuální počítače jsou povolena pro replikaci, můžete zkontrolovat stav virtuálních počítačů v rámci **replikované položky**.

>[!NOTE]
>Během počáteční replikace stavu může trvat nějakou dobu bez zjevné průběh aktualizace. Klikněte na tlačítko **aktualizovat** získat nejnovější stav.

## <a name="update-target-vm-encryption-settings"></a>Aktualizace nastavení šifrování cílového virtuálního počítače
V následujících scénářích bude nutné aktualizovat nastavení šifrování cílového virtuálního počítače:
  - Povolování replikace Site Recovery na virtuálním počítači. Později povolit šifrování disku ve zdrojovém virtuálním počítači.
  - Povolování replikace Site Recovery na virtuálním počítači. Později můžete změnit šifrovací klíč disku nebo šifrovací klíč klíče zdrojového virtuálního počítače.

Můžete použít [skript](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) zkopírujte šifrovací klíče do cílové oblasti a pak aktualizujte nastavení šifrování cíle v **trezor služby Recovery services** > *replikovaná položka*  >  **Vlastnosti** > **výpočty a síť**.

![Aktualizace ADE nastavení dialogového okna](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Řešení potíží s trezoru klíčů oprávnění při replikaci virtuálních počítačů Azure do Azure

Azure Site Recovery vyžaduje oprávnění nejméně pro čtení v Key vault zdrojové oblasti a oprávnění k zápisu cílové oblasti trezor klíčů pro čtení tajného klíče a zkopírujte jej do trezoru klíčů cílové oblasti. 

**1. příčina:** "GET" oprávnění nemáte k dispozici na **zdrojové oblasti Key vault** ke čtení klíče. </br>
**Jak vyřešit:** Bez ohledu na to, jestli jste správcem předplatného nebo ne je důležité, abyste měli oprávnění get pro trezor klíčů.

1. Přejděte do služby Key vault zdrojové oblasti, která v tomto příkladu je "ContososourceKeyvault" > **zásady přístupu** 
2. V části **výběr objektu zabezpečení** přidejte uživatelské jméno, například: "dradmin@contoso.com"
3. V části **oprávnění klíče** vyberte získat 
4. V části **tajný kód oprávnění** vyberte získat 
5. Uložte zásadu přístupu

**2. příčina:** Nemáte požadovaná oprávnění **cílové oblasti Key vault** zapsat klíče. </br>

*Například*: Pokusu o replikaci virtuálního počítače, který má služby key vault *ContososourceKeyvault* ve zdrojové oblasti.
Máte všechna oprávnění pro trezor klíčů zdrojové oblasti. Ale při ochrany, vyberete už vytvořili trezor klíčů ContosotargetKeyvault, který nemá oprávnění. Dojde k chybě.

Oprávnění na [cílové služby Key vault](#required-user-permissions)

**Jak vyřešit:** Přejděte na **Domů** > **Keyvaults** > **ContosotargetKeyvault** > **zásady přístupu** a přidejte příslušná oprávnění.

## <a name="next-steps"></a>Další postup

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testovací převzetí služeb při selhání.
