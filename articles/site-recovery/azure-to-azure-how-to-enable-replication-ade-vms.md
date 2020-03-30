---
title: Povolení replikace pro šifrované virtuální počítače Azure v Azure Site Recovery
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače s povoleným šifrováním disku Azure z jedné oblasti Azure do jiné pomocí site recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 3a59f137240eff2a3a68fa5547be8c6c25d3e5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772223"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikace virtuálních počítačů s povoleným šifrováním disku Azure do jiné oblasti Azure

Tento článek popisuje, jak replikovat virtuální počítače Azure s povolenou Azure Disk Encryption (ADE), z jedné oblasti Azure do jiné.

>[!NOTE]
> Site Recovery aktuálně podporuje ADE, s azure active directory (AAD) pro virtuální počítače s operačními systémy Windows a Linux.  U počítačů se systémem ADE 1.1 (bez aad) musí virtuální počítače používat spravované disky. Virtuální počítače s nespravovanými disky nejsou podporované. Pokud přepnete z ADE 0.1 (s AAD) na 1.1 , budete muset zakázat replikaci a povolit replikaci pro virtuální ho dohledu po povolení 1.1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Požadovaná uživatelská oprávnění
Obnovení webu vyžaduje, aby uživatel měl oprávnění k vytvoření trezoru klíčů v cílové oblasti a kopírování klíčů z trezoru klíčů zdrojové oblasti do trezoru klíčů cílové oblasti.

Chcete-li povolit replikaci virtuálních počítačů s povoleným šifrováním disku z webu Azure Portal, potřebuje uživatel následující oprávnění pro **zdrojovou oblast i** úložiště klíčů cílové oblasti.

- Oprávnění trezoru klíčů
    - Seznam, vytvoření a získání
    
- Tajná oprávnění trezoru klíčů
    - Operace tajné správy
        - Získat, Seznam a Nastavit
    
- Oprávnění klíče trezoru klíčů (vyžadováno pouze v případě, že virtuální počítače používají k šifrování šifrovacích klíčů disku klíč
    - Klíčové operace správy
        - Získat, vypsat a vytvořit
    - Kryptografické operace
        - Dešifrovat a šifrovat

Chcete-li spravovat oprávnění, přejděte na prostředek trezoru klíčů na portálu. Přidejte požadovaná oprávnění pro uživatele. Následující příklad ukazuje, jak povolit oprávnění k trezoru klíčů *ContosoWeb2Keyvault*, který je ve zdrojové oblasti.

1. Přejděte na **zásady domovských** > **trezorů** > **ContosoWeb2KeyVault > přístupu**.

   ![Okno oprávnění trezoru klíčů](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Můžete vidět, že neexistují žádná uživatelská oprávnění. Vyberte **Přidat nový**. Zadejte informace o uživateli a oprávněních.

   ![Oprávnění trezoru Keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Pokud uživatel, který umožňuje zotavení po havárii (DR), nemá oprávnění ke kopírování klíčů, správce zabezpečení, který má příslušná oprávnění, může pomocí následujícího skriptu zkopírovat tajné kódy šifrování a klíče do cílové oblasti.

Chcete-li odstranit oprávnění, podívejte se na [problémy s oprávněními trezoru klíčů](#trusted-root-certificates-error-code-151066) dále v tomto článku.

>[!NOTE]
>Chcete-li povolit replikaci virtuálních počítačů s povoleným šifrováním disku z portálu, potřebujete alespoň oprávnění "Seznam" v trezorech klíčů, tajných kódech a klíčích.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopírování klíčů šifrování disku do oblasti zotavení po Havárii pomocí skriptu PowerShell

1. [Otevřete nezpracovaný kód skriptu CopyKeys](https://aka.ms/ade-asr-copy-keys-code).
2. Zkopírujte skript do souboru a pojmenujte jej **Copy-keys.ps1**.
3. Otevřete aplikaci Prostředí Windows PowerShell a přejděte do složky, do které jste soubor uložili.
4. Spusťte copy-keys.ps1.
5. Zadejte přihlašovací údaje Azure pro přihlášení.
6. Vyberte **předplatné Azure** vašich virtuálních počítačů.
7. Počkejte, až se načtou skupiny prostředků, a pak vyberte **skupinu prostředků** virtuálních počítačů.
8. Vyberte virtuální chod ze zobrazeného seznamu. V seznamu jsou pouze virtuální počítače, které jsou povolené pro šifrování disku.
9. Vyberte **cílové umístění**.

    - **Trezory šifrovacích klíčů disku**
    - **Trezory šifrovacích klíčů**

   Ve výchozím nastavení site recovery vytvoří nový trezor klíčů v cílové oblasti. Název trezoru má příponu asr, která je založena na zdrojových šifrovacích klíčích disku virtuálního počítače. Pokud trezor klíčů již existuje, který byl vytvořen obnovením webu, je znovu použit. V případě potřeby vyberte ze seznamu jiný trezor klíčů.

## <a name="enable-replication"></a>Povolení replikace

V tomto příkladu je primární oblast Azure východní Asie a sekundární oblast je jihovýchodní Asie.

1. V úschovně vyberte **možnost +Replikovat**.
2. Poznamenejte si následující pole.
    - **Zdroj**: Místo původu virtuálních počítačích, což je v tomto případě **Azure**.
    - **Umístění zdroje:** Oblast Azure, kde chcete chránit vaše virtuální počítače. V tomto příkladu je zdrojové umístění "Východní Asie".
    - **Model nasazení**: Model nasazení Azure zdrojových počítačů.
    - **Zdrojové předplatné**: Předplatné, ke kterému patří vaše zdrojové virtuální počítače. Může se jedná o libovolné předplatné, které je ve stejném tenantovi služby Azure Active Directory jako trezor služeb pro obnovení.
    - **Skupina prostředků:** Skupina prostředků, do které patří vaše zdrojové virtuální počítače. Všechny virtuální ho disponiál ve vybrané skupině prostředků jsou uvedeny pro ochranu v dalším kroku.

3. Ve **virtuálních počítačích** > **Vyberte virtuální počítače**vyberte každý virtuální počítač, který chcete replikovat. Můžete vybrat pouze počítače, pro které je možné povolit replikaci. Potom vyberte **OK**.

4. V **nastavení**můžete nakonfigurovat následující nastavení cílového webu.

    - **Cílové umístění:** Umístění, kde budou replikována data zdrojového virtuálního počítače. Site Recovery poskytuje seznam vhodných cílových oblastí na základě umístění vybraného počítače. Doporučujeme použít stejné umístění jako umístění trezoru služby Recovery Services.
    - **Cílové předplatné**: Cílové předplatné, které se používá pro zotavení po havárii. Ve výchozím nastavení je cílové předplatné stejné jako zdrojové předplatné.
    - **Cílová skupina prostředků**: Skupina prostředků, do které patří všechny replikované virtuální počítače. Ve výchozím nastavení site recovery vytvoří novou skupinu prostředků v cílové oblasti. Název získá příponu "asr". Pokud skupina prostředků již existuje, která byla vytvořena azure site recovery, je znovu použít. Můžete také přizpůsobit, jak je znázorněno v následující části. Umístění cílové skupiny prostředků může být libovolné oblasti Azure s výjimkou oblasti, kde jsou hostované zdrojové virtuální počítače.
    - **Cílová virtuální síť**: Ve výchozím nastavení site recovery vytvoří novou virtuální síť v cílové oblasti. Název získá příponu "asr". Je mapován do zdrojové sítě a používán pro budoucí ochranu. [Přečtěte si další informace](site-recovery-network-mapping-azure-to-azure.md) o mapování sítě.
    - **Cílové účty úložiště (pokud váš zdrojový virtuální počítač nepoužívá spravované disky)**: Ve výchozím nastavení site recovery vytvoří nový cílový účet úložiště napodobováním konfigurace úložiště zdrojového virtuálního počítače. Pokud účet úložiště již existuje, je znovu použit.
    - **Replika spravované disky (pokud váš zdrojový virtuální počítač používá spravované disky):** Site Recovery vytvoří nové repliky spravované disky v cílové oblasti zrcadlit zdrojové virtuální počítače spravované disky stejného typu úložiště (standardní nebo premium) jako zdrojové spravované disky virtuálního počítače.
    - **Účty úložiště mezipaměti**: Site Recovery potřebuje další účet úložiště s názvem *úložiště mezipaměti* ve zdrojové oblasti. Všechny změny na zdrojových virtuálních počítačích jsou sledovány a odeslány do účtu úložiště mezipaměti. Pak jsou replikovány do cílového umístění.
    - **Dostupnost :** Ve výchozím nastavení site recovery vytvoří novou sadu dostupnosti v cílové oblasti. Název má příponu "asr". Pokud již existuje sada dostupnosti, která byla vytvořena službou Site Recovery, je znovu použita.
    - **Trezory šifrovacích klíčů:** Ve výchozím nastavení vytvoří obnovení webu nový trezor klíčů v cílové oblasti. Má příponu "asr", která je založena na zdrojových šifrovacích klíčích disku virtuálního počítače. Pokud trezor klíčů, který byl vytvořen azure site recovery již existuje, je znovu použít.
    - **Trezory šifrovacích klíčů**: Ve výchozím nastavení site recovery vytvoří nový trezor klíčů v cílové oblasti. Název má příponu "asr", která je založena na zdrojových šifrovacích klíčích virtuálního počítači. Pokud trezor klíčů vytvořený službou Azure Site Recovery už existuje, je znovu použitý.
    - **Zásady replikace**: Definuje nastavení pro historii uchovávání bodů obnovení a frekvenci snímků konzistentních s aplikací. Ve výchozím nastavení site recovery vytvoří nové zásady replikace s výchozím nastavením *24 hodin* pro uchovávání bodů obnovení a *60 minut* pro frekvenci snímků konzistentních s aplikací.

## <a name="customize-target-resources"></a>Přizpůsobení cílových zdrojů

Chcete-li upravit výchozí cílové nastavení obnovení webu, postupujte takto.

1. Chcete-li upravit výchozí cílové předplatné, vyberte **možnost Přizpůsobit** vedle možnosti Cílové předplatné. Vyberte předplatné ze seznamu předplatných, které jsou k dispozici v tenantovi Azure AD.

2. Chcete-li změnit následující výchozí nastavení, vyberte **možnost Přizpůsobit** vedle položky Skupiny prostředků, Sítě, Úložiště a Skupiny dostupnosti:
    - V **části Cílová skupina prostředků**vyberte skupinu prostředků ze seznamu skupin prostředků v cílovém umístění předplatného.
    - V **části Cílová virtuální síť**vyberte síť ze seznamu virtuálních sítí v cílovém umístění.
    - Pro **sadu dostupnosti**můžete přidat nastavení sady dostupnosti do virtuálního počítače, pokud jsou součástí skupiny dostupnosti ve zdrojové oblasti.
    - U **účtů cílového úložiště**vyberte účet, který chcete použít.

2. Chcete-li změnit následující výchozí nastavení, vyberte **možnost Přizpůsobit** vedle možnosti Nastavení šifrování:
   - V **případě trezoru šifrovacího klíče cílového disku**vyberte trezor šifrovacího klíče cílového disku ze seznamu trezorů klíčů v cílovém umístění předplatného.
   - V **případě trezoru šifrovacího klíče cílového klíče**vyberte trezor šifrovacího klíče cílového klíče ze seznamu trezorů klíčů v cílovém umístění předplatného.

3. Vyberte **možnost Vytvořit cílový prostředek** > **Povolit replikaci**.
4. Po virtuálních disponibilní pro replikaci, můžete zkontrolovat stav virtuálních zařízení v části **Replikované položky**.

>[!NOTE]
>Během počáteční replikace může aktualizace stavu nějakou dobu trvat bez zjevného průběhu. Chcete-li získat nejnovější stav, klepněte na tlačítko **Aktualizovat.**

## <a name="update-target-vm-encryption-settings"></a>Aktualizace nastavení šifrování cílového virtuálního počítače
V následujících scénářích budete muset aktualizovat nastavení cílového šifrování virtuálních počítače:
  - Povolili jste replikaci obnovení lokality na virtuálním počítači. Později jste povolili šifrování disku na zdrojovém virtuálním počítači.
  - Povolili jste replikaci obnovení lokality na virtuálním počítači. Později jste změnili šifrovací klíč disku nebo klíč šifrovací klíč na zdrojovém virtuálním počítači.

Pomocí [skriptu](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) můžete zkopírovat šifrovací klíče do cílové oblasti a potom aktualizovat nastavení cílového šifrování v úložišti **služby Recovery** > *Vault replikované položky* > **Properties** > **Compute and Network**.

![Aktualizovat dialogové okno nastavení ade](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Poradce při potížích s oprávněním trezoru klíčů během replikace virtuálních aplikací Azure na Azure

Azure Site Recovery vyžaduje alespoň číst oprávnění na zdrojové oblasti trezoru klíčů a zapisovat oprávnění do trezoru klíčů cílové oblasti ke čtení tajného klíče a zkopírovat do trezoru klíčů cílové oblasti. 

**Příčina 1:** Nemáte oprávnění "GET" ve **zdrojové oblasti trezor klíčů** ke čtení klíčů. </br>
**Jak opravit:** Bez ohledu na to, zda jste správce předplatného nebo ne, je důležité, abyste získali oprávnění k trezoru klíčů.

1. Přejít na zdrojoblast trezor klíčů, který v tomto příkladu je "ContososourceKeyvault" > **zásady přístupu** 
2. V **části Vybrat objekt zabezpečení** přidejte své uživatelské jméno například: "dradmin@contoso.com"
3. V části **Klíčová oprávnění** vyberte GET 
4. V části **Tajné oprávnění** vyberte ZÍSKAT 
5. Uložení zásad přístupu

**Příčina 2:** K zápisu klíčů nemáte v **trezoru klíčů cílovou oblast key** oprávnění. </br>

*Například*: Pokusíte se replikovat virtuální počítač, který má trezor klíčů *ContososourceKeyvault* ve zdrojové oblasti.
Máte všechna oprávnění k trezoru klíčů zdrojové oblasti. Ale během ochrany vyberete již vytvořený trezor klíčů ContosotargetKeyvault, který nemá oprávnění. Dojde k chybě.

V [cílovém trezoru klíčů](#required-user-permissions) je vyžadováno oprávnění.

**Jak opravit:** Přejděte na > **zásady domovských trezorů** > **ContosotargetKeyvault** > **Access** a přidejte příslušná oprávnění. **Home**

## <a name="next-steps"></a>Další kroky

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testu převzetí služeb při selhání.
