---
title: Postup upgradu Data Science Virtual Machine na Ubuntu 18,04
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si, jak upgradovat z CentOS a Ubuntu 16,04 na nejnovější Ubuntu 18,04 Data Science Virtual Machine.
keywords: obsáhlý Learning, AI, nástroje pro datové vědy, virtuální počítač pro datové vědy, vědecké zpracování týmových dat
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101659456"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Upgrade prostředí Data Science Virtual Machine na Ubuntu 18.04

Pokud máte Data Science Virtual Machine, na kterém běží starší verze, jako je například Ubuntu 16,04 nebo CentOS, měli byste migrovat DSVM na Ubuntu 18,04. Při migraci se zajistí, že získáte nejnovější opravy operačního systému, ovladače, předinstalovaný software a verze knihoven. V tomto dokumentu se dozvíte, jak migrovat z dřívějších verzí Ubuntu nebo z CentOS. 

## <a name="prerequisites"></a>Požadavky

- Znalost pomocí SSH a příkazového řádku pro Linux

## <a name="overview"></a>Přehled

Existují dva možné způsoby, jak provést migraci:

- Místní migrace, označovaná také jako migrace stejného serveru. Tato migrace upgraduje stávající virtuální počítač bez vytvoření nového virtuálního počítače. Místní migrace je snazší způsob migrace z Ubuntu 16,04 na Ubuntu 18,04.
- Souběžná migrace, označovaná taky jako migrace mezi servery. Tato migrace přenáší data ze stávajícího virtuálního počítače do nově vytvořeného virtuálního počítače. Souběžná migrace je způsob, jak migrovat z CentOS do Ubuntu 18,04. Pokud se domníváte, že se stará instalace stala zbytečně, můžete upřednostnit souběžnou migraci pro upgrade mezi Ubuntu verzemi.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Vytvoření snímku virtuálního počítače pro případ, že se potřebujete vrátit zpátky

V Azure Portal vyhledejte funkce **snímků** pomocí panelu hledání. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Snímek obrazovky znázorňující Azure Portal a panel hledání se zvýrazněnými * * snímky * *":::

1. Vyberte možnost **Přidat**, která vás převezme na stránku **vytvořit snímek** . Vyberte předplatné a skupinu prostředků virtuálního počítače. V poli **oblast** vyberte stejnou oblast, ve které existuje cílové úložiště. Vyberte disk úložiště DSVM a další možnosti zálohování. **HDD úrovně Standard** je vhodný typ úložiště pro tento scénář zálohování.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Snímek obrazovky znázorňující možnosti vytvoření snímku":::

2. Až budou všechny podrobnosti vyplněny a ověření platnosti, vyberte **zkontrolovat + vytvořit** a ověřte a vytvořte snímek. Po úspěšném dokončení snímku se zobrazí zpráva s oznámením, že nasazení je hotové.

## <a name="in-place-migration"></a>Místní migrace

Pokud migrujete starší verzi Ubuntu, můžete se rozhodnout provést místní migraci. Tato migrace nevytvoří nový virtuální počítač a obsahuje méně kroků než souběžná migrace.  Pokud chcete provést souběžnou migraci, protože potřebujete větší kontrolu nebo protože migrujete z jiné distribuce, například CentOS, přeskočte na [souběžnou migrační](#side-by-side-migration) část. 

1. Z Azure Portal spusťte DSVM a přihlaste se pomocí SSH. Provedete to tak, že vyberete **připojit** a **SSH** a budete postupovat podle pokynů pro připojení. 

1. Po připojení k relaci terminálu v DSVM spusťte následující příkaz pro upgrade:

    ```bash
    sudo do-release-upgrade
    ```

Dokončení procesu upgradu bude chvíli trvat. Po převzetí služeb při selhání bude program požádat o oprávnění k restartování virtuálního počítače. Odpovězte **Ano**. Během restartování systému se odpojíte od relace SSH.

### <a name="if-necessary-regenerate-ssh-keys"></a>V případě potřeby znovu vygenerujte klíče SSH.

> [!IMPORTANT] 
> Po upgradu a restartování může být nutné znovu vygenerovat klíče SSH.

Až se váš virtuální počítač Upgradoval a restartuje, pokusí se k němu znovu získat přístup přes SSH. IP adresa se mohla během restartování změnit, proto ji před pokusem o připojení potvrďte.

Pokud se zobrazí chyba **Identifikace vzdáleného hostitele se změnila**, budete muset znovu vygenerovat přihlašovací údaje SSH.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Snímek obrazovky PowerShellu ukazující upozornění na změnu identifikace vzdáleného hostitele":::

Provedete to tak, že na svém místním počítači spustíte příkaz:

```bash
ssh-keygen -R "your server hostname or ip"
```

Nyní byste měli být schopni se připojit pomocí SSH. Pokud stále dochází k potížím, na stránce **připojit** použijte odkaz pro **řešení potíží s připojením SSH**.

## <a name="side-by-side-migration"></a>Souběžná migrace

Pokud provádíte migraci z CentOS nebo chcete čistou instalaci operačního systému, můžete provést souběžnou migraci. Tento typ migrace má více kroků, ale poskytuje kontrolu nad tím, které soubory se přenášejí.

Migrace z jiných systémů na základě stejné sady nadřazených zdrojových balíčků by měly být poměrně jednoduché, například [Časté otázky/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Můžete se rozhodnout upgradovat části systému souborů operačního systému a opustit adresáře uživatelů, například `/home` na místě. Pokud necháte staré domovské adresáře uživatele v místě, očekáváte některé problémy s nabídkami GNOME/tam a dalšími položkami plochy. Může být nejjednodušší vytvořit nové uživatelské účty a připojit staré adresáře někam jinam v systému souborů, aby bylo možné odkazovat, kopírovat nebo propojovat materiály uživatelů po migraci.

### <a name="migration-at-a-glance"></a>První migrace

1.  Vytvořte snímek existujícího virtuálního počítače, jak je popsáno výše.

1.  Vytvoření disku z tohoto snímku

1.  Vytvořit nový Data Science Virtual Machine Ubuntu

1.  Znovu vytvořit uživatelské účty na novém virtuálním počítači

1.  Připojte disk virtuálního počítače snapshotted jako datový disk na novém Data Science Virtual Machine

1.  Ruční zkopírování požadovaných dat

### <a name="create-a-disk-from-your-vm-snapshot"></a>Vytvoření disku z snímku virtuálního počítače

Pokud jste ještě nevytvořili snímek virtuálního počítače, jak je popsáno výše, udělejte to. 

1. V Azure Portal vyhledejte **disky** a vyberte **Přidat**. tím otevřete stránku **disku** .

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Snímek obrazovky Azure Portal stránky pro hledání disků a tlačítkem Přidat":::

2. Nastavte **předplatné**, **skupinu prostředků** a **oblast** na hodnoty snímku virtuálního počítače. Vyberte **název** disku, který se má vytvořit.

3. Jako **snímek** vyberte **typ zdroje** a jako **zdrojový snímek** vyberte snímek virtuálního počítače. Zkontrolujte a vytvořte disk. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Snímek obrazovky s dialogovým oknem vytvoření disku zobrazujícím možnosti":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Vytvořit nový Data Science Virtual Machine Ubuntu

Vytvořte nový Data Science Virtual Machine Ubuntu pomocí [Azure Portal](https://portal.azure.com) nebo [šablony ARM](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Opětovné vytvoření uživatelských účtů na novém Data Science Virtual Machine

Vzhledem k tomu, že budete jenom kopírovat data ze starého počítače, budete muset znovu vytvořit jakékoli uživatelské účty a softwarová prostředí, která chcete na novém počítači použít.

Linux je dostatečně flexibilní, aby bylo možné přizpůsobit adresáře a cesty v nové instalaci, aby se mohla pořídit starý počítač. Obecně je ale snazší použít preferované rozložení moderního Ubuntu a upravit uživatelské prostředí a skripty pro přizpůsobení.

Další informace najdete v tématu [rychlý Start: nastavení data Science Virtual Machine pro Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Připojte disk virtuálního počítače snapshotted jako datový disk na novém Data Science Virtual Machine

1. V Azure Portal Ujistěte se, že je váš Data Science Virtual Machine spuštěný.

2. V Azure Portal přejdete na stránku svého Data Science Virtual Machine. Vyberte okno **disky** na levé kolejnici. Vyberte **připojit existující disky**.

3. V rozevíracím seznamu **název disku** vyberte disk, který jste vytvořili z snímku starého virtuálního počítače.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Snímek obrazovky se stránkou možností DSVM zobrazujících možnosti pro přílohy disku":::

4. Vyberte **Uložit** a aktualizujte svůj virtuální počítač.

> [!Important]
> Váš virtuální počítač by měl být spuštěný v době připojení datového disku. Pokud virtuální počítač není spuštěný, můžou se disky přidat v nesprávném pořadí, což vede k matoucímu a potenciálně nespouštěcímu systému. Pokud přidáte datový disk s vypnutým virtuálním počítačem, klikněte na tlačítko **X** vedle datového disku, spusťte virtuální počítač a znovu ho připojte.

### <a name="manually-copy-the-wanted-data"></a>Ruční zkopírování požadovaných dat 

1. Přihlaste se ke spuštěnému virtuálnímu počítači pomocí SSH.

2. Spuštěním následujícího příkazu potvrďte, že jste připojili disk vytvořený z starého snímku virtuálního počítače:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Výsledky by měly vypadat přibližně jako na následujícím obrázku. Na obrázku je disk `sda1` připojený k kořenovému adresáři a `sdb2` jedná se o `/mnt` pomocného disku. Datový disk vytvořený z snímku starého virtuálního počítače je označený jako `sdc1` , ale ještě není k dispozici, jak je doloženo chybějícím umístěním připojení. Vaše výsledky mohou mít různé identifikátory, ale měl by se zobrazit podobný model.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Snímek obrazovky s výstupem lsblk zobrazujícím nepřipojenou datovou jednotku":::
    
3. Pro přístup k datové jednotce vytvořte umístění a připojte ho. Nahraďte `/dev/sdc1` příslušnou hodnotou vrácenou `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Nyní `/datadrive` obsahuje adresáře a soubory starého data Science Virtual Machine. Přesuňte nebo zkopírujte adresáře nebo soubory z datové jednotky k novému virtuálnímu počítači, podle kterého chcete.

Další informace najdete v tématu [použití portálu k připojení datového disku k virtuálnímu počítači se systémem Linux](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Připojit a potvrdit upgrade verze

Bez ohledu na to, jestli jste provedli migraci na místě nebo souběžně, ověřte, že jste úspěšně provedli upgrade. Z relace terminálu spusťte příkaz: 

```bash
cat /etc/os-release
```

Měli byste vidět, že používáte Ubuntu 18,04.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Snímek obrazovky terminálu Ubuntu znázorňující data verze operačního systému":::

Změna verze je také zobrazena v Azure Portal.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Snímek obrazovky portálu zobrazující vlastnosti DSVM včetně verze operačního systému":::

## <a name="next-steps"></a>Další kroky

- [Datové vědy s Ubuntum počítačem pro datové vědy v Azure](./linux-dsvm-walkthrough.md)
- [Jaké nástroje jsou součástí Azure Data Science Virtual Machine?](./tools-included.md)