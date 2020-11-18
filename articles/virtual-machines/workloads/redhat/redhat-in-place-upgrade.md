---
title: Místní upgrade Red Hat Enterprise Linux imagí v Azure
description: Vyhledání kroků k provedení místního upgradu z imagí Red Hat Enterprise 7. x na nejnovější verzi 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844556"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux místní upgrady

Tento článek poskytuje podrobné pokyny, jak provést místní upgrade z Red Hat Enterprise Linux 7 na Red Hat Enterprise Linux 8 pomocí nástroje ' Leapp ' v Azure. V rámci místního upgradu je existující operační systém RHEL 7 nahrazen verzí RHEL 8.

>[!Note] 
> SQL Server v Red Hat Enterprise Linuxch nabídkách nepodporují místní upgrade v Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Co očekávat během upgradu
Během upgradu bude systém několikrát restartovat a to je normální. Poslední restartováním se upgraduje virtuální počítač na nejnovější dílčí verzi RHEL 8. 

Proces upgradu může trvat až 20 minut až několik hodin. to závisí na několika faktorech, třeba na velikosti virtuálních počítačů a počtu balíčků nainstalovaných v systému.

## <a name="preparations-for-the-upgrade"></a>Příprava na upgrade
Místní upgrade je oficiálně doporučený způsob, který vám Red Hat a Azure umožní zákazníkům upgradovat systém na další hlavní verzi. Před provedením upgradu je zde několik věcí, které byste měli znát a vzít v úvahu. 

>[!Important] 
> Než provedete upgrade, podejte prosím snímek image.

>[!NOTE]
> Příkazy v tomto článku je třeba spustit pomocí kořenového účtu.

* Ujistěte se, že používáte nejnovější verzi RHEL 7, která je aktuálně RHEL 7,9. Pokud používáte uzamčenou verzi a nemůžete upgradovat na RHEL 7,9, můžete použít [postup pro přepnutí do úložiště, které není EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Spuštěním následujícího příkazu zjistíte, jak probíhá upgrade, a jestli se dokončí. Příkaz by měl vygenerovat soubor v rámci "/var/log/leapp/leapp-report.txt", který vysvětluje proces a co se děje, a pokud je upgrade možná nebo ne.
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Ujistěte se, že je konzola sériového rozhraní funkční, protože umožňuje monitorování během procesu upgradu.

* Povolit kořenový přístup SSH v `/etc/ssh/sshd_config`
    1. Otevřete soubor `/etc/ssh/sshd_config`.
    1. Hledat #PermitRootLogin Ano
    1. Odebrat znak "#" pro zrušení komentáře

## <a name="steps-for-performing-the-upgrade"></a>Kroky pro provedení upgradu

Tyto kroky proveďte pečlivě. Na testovacím počítači se před provozními instancemi doporučuje provést upgrade.

1. K načtení nejnovějších balíčků klientů proveďte aktualizaci Yumu.
    ```bash
    yum update -y
    ```

1. Nainstalujte balíček leapp-Client-Package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Použijte soubor leapp-data. tar. gz s repomap.csv a pes-events.jsv, který je k dispozici na [portálu RedHat](https://access.redhat.com/articles/3664871), a extrahujte je. 
    1. Stáhněte si soubor.
    1. Extrahujte obsah a odeberte soubor pomocí následujícího příkazu:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Přidejte soubor Answers pro ' Leapp '.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Proveďte upgrade Leapp.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Po `leapp upgrade` úspěšném dokončení příkazu restartujte systém, aby se proces dokončil. Systém bude restartován několikrát, než bude k dispozici. Monitorujte proces pomocí konzoly sériového portu.

1.  Ověřte, zda byl upgrade úspěšně dokončen.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Po dokončení upgradu odebrat kořenový přístup SSH.
    1. Otevřete soubor `/etc/ssh/sshd_config`.
    1. Hledat #PermitRootLogin Ano
    1. Přidat # do komentáře

1. Restartujte službu sshd, aby se změny projevily.
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Běžné problémy
Jedná se o některé běžné instance, které `leapp preupgrade` `leapp upgrade` může způsobit selhání procesu nebo.

**Chyba: pro následující zakázané vzory modulů plug-in nebyly nalezeny žádné shody.**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Řešení**\
Vypněte modul plug-in Správce předplatného úpravou souboru `/etc/yum/pluginconf.d/subscription-manager.conf` a změnou povoleného na `enabled=0` .

To je způsobeno tím, že je povolený modul plug-in Yumu Správce předplatného, který se nepoužívá pro virtuální počítače s PAYG.

**Chyba: možné problémy se vzdáleným přihlášením pomocí kořenového adresáře** `leapp preupgrade` Selhání se nepovedlo s následující chybou:
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**Řešení**\
Povolit přístup rootu v `/etc/sshd_config` .
To je způsobeno tím, že nepovolíte kořenový přístup SSH v nástroji `/etc/sshd_config` jako v části[Příprava na upgrade](#preparations-for-the-upgrade). 

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [obrázcích Red Hat v Azure](./redhat-images.md).
* Přečtěte si další informace o [infrastruktuře aktualizace Red Hat](./redhat-rhui.md).
* Přečtěte si další informace o [nabídce RHEL BYOS](./byos.md).
* Informace o procesech místního upgradu Red Hat najdete v dokumentaci k Red Hat, [která se upgraduje z RHEL 7 na RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) .
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
