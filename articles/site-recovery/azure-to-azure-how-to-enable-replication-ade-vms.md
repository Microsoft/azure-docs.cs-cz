---
title: Konfigurovat virtuální počítače s povolenou replikací pro Azure disk encryption (ADE) ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače ADE z jedné oblasti Azure do jiné pomocí služby Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 5d992d13a67c7b01f82b615e7131a20b84dec9e8
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52851009"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Azure disk encryption (ADE) povolena virtuální počítače replikovat do jiné oblasti Azure

Tento článek popisuje, jak povolit povolenou replikací z Azure disk encryption (ADE) virtuálních počítačů z jedné oblasti Azure do jiné.

>[!NOTE]
>Azure Site Recovery v současné době podporuje pouze virtuální počítače Azure s operačním systémem Windows a [povoleným šifrováním pomocí aplikace Azure AD](https://aka.ms/ade-aad-app).
>

## <a name="required-user-permissions"></a>Požadovaná uživatelská oprávnění

Pokud chcete povolit replikaci virtuálních počítačů ADE z portálu, musí mít uživatel následující oprávnění.
- Oprávnění služby Key vault
    - list
    - Vytvořit
    - Získat

-   Oprávnění tajného kódu trezoru klíčů
    - Seznam
    - Vytvořit
    - Získat

- Oprávnění klíče služby Key vault (nutné jenom v případě, že virtuální počítače použít šifrovací klíč klíče k šifrování klíče šifrování disku)
    - Seznam
    - Získat
    - Vytvořit
    - Šifrování
    - Dešifrovat

Přejděte k prostředku služby key vault v portálu a přidání požadovaných oprávnění pro uživatele, mohou spravovat oprávnění.

![keyvaultpermissions](./media/azure-to-azure-how-to-enable-replication-ade-vms/keyvaultpermissions.png)

Pokud uživatel povoluje zotavení po havárii (DR) nemá požadovaná oprávnění ke zkopírování klíče, níže uvedený skript může dostat k zabezpečení správce s příslušnými oprávněními ke zkopírování šifrování tajných kódů a klíčů do cílové oblasti.

>[!NOTE]
>Pokud chcete povolit replikaci virtuálního počítače ADE z portálu, potřebujete alespoň oprávnění "Přehled" na trezory klíčů, tajných kódů a klíčů
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>Zkopírujte ADE klíče do oblasti zotavení po Havárii pomocí Powershellového skriptu

1. Otevřete kód skriptu raw "CopyKeys" v okně prohlížeče po kliknutí na [tento odkaz](https://aka.ms/ade-asr-copy-keys-code).
2. Zkopírujte skript do souboru s názvem "kopie-keys.ps1".
2. Otevřete aplikaci Windows Powershellu a přejděte do složky, které se nachází soubor.
3. Spuštění "kopie – keys.ps1.
4. Zadejte přihlašovací údaje pro přihlášení k Azure.
5. Vyberte **předplatného Azure** vašich virtuálních počítačů.
6. Počkejte, skupiny prostředků pro načtení a pak vyberte **skupiny prostředků** vašich virtuálních počítačů.
7. Vyberte virtuální počítače, ze seznamu virtuálních počítačů, které jsou zobrazeny. V seznamu se zobrazí jenom virtuální počítače povoleno se šifrováním disku Azure.
8. Vyberte **cílové umístění**.
9. **Trezory klíčů pro šifrování disků:** Azure Site Recovery ve výchozím nastavení vytvoří v cílové oblasti nový trezor klíčů s příponou názvu „asr“ na základě klíčů pro šifrování disků zdrojového virtuálního počítače. Pokud již existuje trezor klíčů vytvořený službou Azure Site Recovery, použije se tento trezor. V seznamu v případě potřeby můžete vybrat jiný trezor klíčů.
10. **Trezory klíčů pro šifrování klíčů:** Azure Site Recovery ve výchozím nastavení vytvoří v cílové oblasti nový trezor klíčů s příponou názvu „asr“ na základě klíčových šifrovacích klíčů zdrojového virtuálního počítače. Pokud již existuje trezor klíčů vytvořený službou Azure Site Recovery, použije se tento trezor. V seznamu v případě potřeby můžete vybrat jiný trezor klíčů.

## <a name="enable-replication"></a>Povolení replikace

Tento postup předpokládá, že primární oblast Azure je východní Asie a jihovýchodní Asie je sekundární oblasti.

1. V trezoru, klikněte na tlačítko **+ replikovat**.
2. Mějte na paměti následující pole:
    - **Zdroj**: bod počátek virtuálních počítačů, které v tomto případě je **Azure**.
    - **Umístění zdroje**: oblast Azure, ze kterého má být ochranu virtuálních počítačů. Pro tento obrázek je zdrojové umístění: východní Asie.
    - **Model nasazení**: model nasazení Azure zdrojový počítač.
    - **Zdrojové předplatné**: předplatné, do které patří vaše zdrojové virtuální počítače. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
    - **Skupina prostředků**: skupinu prostředků, do které patří vaše zdrojové virtuální počítače. Všechny virtuální počítače v rámci vybrané skupiny prostředků jsou uvedeny pro ochranu v dalším kroku.

3. V **virtuální počítače > Výběr virtuálních počítačů**klikněte a vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Potom klikněte na **OK**.

4. V **nastavení**, Volitelně můžete nakonfigurovat nastavení cílové lokality:

    - **Cílové umístění**: umístění, kde se budou replikovat svá zdrojová data virtuálního počítače. V závislosti na vaší polohy vybrané počítače, Site Recovery získáte seznam vhodný cílové oblasti. Doporučujeme, abyste cílové umístění stejné jako umístění trezoru služby Recovery Services.
    - **Cílové předplatné:** Cílové předplatné, které se použije pro zotavení po havárii. Ve výchozím nastavení bude cílové předplatné stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků**: skupinu prostředků, na kterém jsou všechny vaše replikované virtuální počítače patří. Ve výchozím nastavení vytvoří Azure Site Recovery s názvem, který má příponu "Azure Site Recovery" v cílové oblasti novou skupinu prostředků. V případě, že skupina prostředků Azure Site Recovery vytvoří již existuje, je znovu. Můžete také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolné oblasti Azure, s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť**: ve výchozím nastavení, Site Recovery vytvoří novou virtuální síť v cílové oblasti s názvem, který má příponu "Azure Site Recovery". To je namapována na zdrojovou síť a použít pro všechny budoucí ochrany. [Další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
    - **Cílové účty úložiště (Pokud je váš zdrojový virtuální počítač nepoužívá spravované disky)**: ve výchozím nastavení vytvoří Site Recovery nový cílový účet úložiště tak napodobuje konfiguraci úložiště zdrojového virtuálního počítače. V případě, že účet úložiště už existuje, je znovu.
    - **Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky)**: Site Recovery vytvoří nové spravované disky repliky v cílové oblasti zrcadlící spravované disky zdrojového Virtuálního počítače se stejným typem úložiště (Standard nebo premium), protože zdrojový virtuální počítač spravovaný disk.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje účet úložiště s názvem úložiště mezipaměti ve zdrojové oblasti. Všechny změny aktivit ve zdrojových virtuálních počítačů jsou sledovány a odeslat do účtu úložiště mezipaměti před replikaci do cílového umístění.
    - **Skupina dostupnosti**: ve výchozím nastavení Azure Site Recovery vytvoří novou skupinu s názvem, který má příponu "Azure Site Recovery" v cílové oblasti dostupnosti. V případě, že skupina dostupnosti Azure Site Recovery vytvoří již existuje, je znovu.
    - **Trezory klíčů pro šifrování disků:** Azure Site Recovery ve výchozím nastavení vytvoří v cílové oblasti nový trezor klíčů s příponou názvu „asr“ na základě klíčů pro šifrování disků zdrojového virtuálního počítače. Pokud již existuje trezor klíčů vytvořený službou Azure Site Recovery, použije se tento trezor.
    - **Trezory klíčů pro šifrování klíčů:** Azure Site Recovery ve výchozím nastavení vytvoří v cílové oblasti nový trezor klíčů s příponou názvu „asr“ na základě klíčových šifrovacích klíčů zdrojového virtuálního počítače. Pokud již existuje trezor klíčů vytvořený službou Azure Site Recovery, použije se tento trezor.
    - **Zásady replikace**: definuje nastavení pro obnovení bodu uchování historie a aplikace konzistentní frekvence pořizování snímků. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozím nastavením ' 24 hodin pro uchování bodu obnovení a "60 minut, než se frekvence snímků konzistentní vzhledem k aplikacím aplikace.



## <a name="customize-target-resources"></a>Přizpůsobení cílové prostředky

Můžete upravit výchozí nastavení cíle používané pro Site Recovery.


1. Klikněte na tlačítko **vlastní:** vedle 'Cílové předplatné' Chcete-li změnit výchozí cílové předplatné. Vyberte předplatné ze seznamu všech předplatných, které jsou k dispozici ve stejném tenantovi Azure Active Directory (AAD).

2. Klikněte na tlačítko **vlastní:** vedle "prostředek skupiny, sítě, úložiště a skupinách dostupnosti změnit pod výchozí nastavení:
    - V **cílová skupina prostředků**, vyberte skupinu prostředků z seznam všech skupin prostředků v cílovém umístění předplatného.
    - V **cílovou virtuální sítí**, vyberte síť, ze seznamu ve virtuální síti v cílové lokalitě.
    - V **dostupnosti**, můžete přidat nastavení skupinu dostupnosti pro virtuální počítač, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - V **účty úložiště v cíli**, vyberte účet, který chcete použít.


2. Klikněte na tlačítko **vlastní:** vedle 'Nastavení šifrování' Chcete-li změnit pod výchozí nastavení:
    - V **služby key vault cílový disk encryption**, vyberte trezor klíčů šifrování disku cílového seznamu všem trezorům klíčů v cílovém umístění předplatného.
  - V **cílové šifrování klíče služby key vault**, vyberte cíl šifrování klíče služby key vault ze seznamu všem trezorům klíčů v cílovém umístění předplatného.

3. Klikněte na tlačítko **vytvořit cílový prostředek** > **povolením replikace**.
4. Po virtuální počítače jsou povolena pro replikaci, můžete zkontrolovat stav stav virtuálního počítače v rámci **replikované položky**

>[!NOTE]
>Během počáteční replikace stavu může trvat nějakou dobu aktualizovat, aniž by průběh. Klikněte na tlačítko **aktualizovat** tlačítko, chcete-li získat nejnovější stav.
>

## <a name="update-target-vm-encryption-settings"></a>Aktualizace nastavení šifrování cílového virtuálního počítače
V následující scénáře, budete muset aktualizovat nastavení šifrování cílového virtuálního počítače.
  - Povolená replikace služby Site recovery na virtuálním počítači a povolit Azure Disk Encryption (ADE) na zdrojovém virtuálním počítači později
  - Povolena replikace služby Site recovery na virtuálním počítači a později změnit šifrovací klíč disku a/nebo šifrovací klíč klíče zdrojového virtuálního počítače

Můžete použít [skript](#copy-ade-keys-to-dr-region-using-powershell-script) zkopírujte šifrovací klíče do cílové oblasti a pak aktualizujte nastavení šifrování cíle v **-> trezor služby Recovery services se replikovaná položka -> Vlastnosti -> výpočty a síť.**

![nastavení aktualizace ade](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="next-steps"></a>Další postup

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testovací převzetí služeb při selhání.
