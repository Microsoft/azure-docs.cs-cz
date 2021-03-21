---
title: Místní upgrade Red Hat Enterprise Linux imagí v Azure
description: Přečtěte si, jak provést místní upgrade z imagí Red Hat Enterprise 7. x na nejnovější verzi 8. x.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676064"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux místní upgrady

Tento článek poskytuje pokyny, jak provést místní upgrade z Red Hat Enterprise Linux (RHEL) 7 na Red Hat Enterprise Linux 8. Pokyny používají `leapp` Nástroj v Azure. V rámci místního upgradu je existující operační systém RHEL 7 nahrazen verzí RHEL 8.

>[!Note] 
> Nabídky SQL Server v Red Hat Enterprise Linux nepodporují místní upgrady v Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Co očekávat během upgradu
Během upgradu se systém několikrát restartuje. Posledním restartováním se upgraduje virtuální počítač na nejnovější dílčí verzi RHEL 8. 

Proces upgradu může trvat až 20 minut až 2 hodiny. Celková doba závisí na několika faktorech, například na velikosti virtuálního počítače a počtu balíčků nainstalovaných v systému.

## <a name="preparations"></a>Přípravy
Red Hat a Azure doporučuje použít místní upgrade k přechodu systému na další hlavní verzi. 

Než začnete s upgradem, pamatujte na následující skutečnosti. 

>[!Important] 
> Než začnete s upgradem, pořídit snímek image.

* Ujistěte se, že používáte nejnovější verzi RHEL 7. V současné době je nejnovější verze RHEL 7,9. Pokud používáte uzamčenou verzi a nemůžete upgradovat na RHEL 7,9, postupujte podle [těchto kroků a přepněte do úložiště jiného typu než EUS (podpora rozšířené aktualizace)](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Spusťte následující příkaz, který zkontroluje upgrade a zjistí, jestli se úspěšně nedokončí. Příkaz by měl generovat soubor */var/log/leapp/leapp-report.txt* . Tento soubor vysvětluje proces, co se děje, a to, jestli je upgrade možné.

    >[!NOTE]
    > Pomocí kořenového účtu spusťte příkazy v tomto článku. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Ujistěte se, že je konzola sériového rozhraní funkční. Tuto konzolu budete používat k monitorování během procesu upgradu.

* Povolit kořenový přístup SSH v */etc/ssh/sshd_config*:
    1. Otevřete soubor */etc/ssh/sshd_config*.
    1. Vyhledejte `#PermitRootLogin yes`.
    1. Odeberte znak číslo ( `#` ) a odkomentujte řetězec.

## <a name="upgrade-steps"></a>Postup upgradu

Postupujte pečlivě podle těchto kroků. Doporučujeme vyzkoušet upgrade na testovacím počítači, než ho zkusíte v produkčních instancích.

1. `yum`Aktualizujte, aby se načetly nejnovější balíčky klientů.
    ```bash
    yum update -y
    ```

1. Nainstalujte `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Na [portálu Red Hat](https://access.redhat.com/articles/3664871)Získejte soubor *leapp-data. tar. gz* , který obsahuje *repomap.csv* a *pes-events.jsna*. Extrahujte soubor *leapp-data. tar. gz* .
    1. Stáhněte si soubor *leapp-data. tar. gz* .
    1. Extrahujte obsah a odeberte soubor. Použijte následující příkaz:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Přidejte `answers` soubor pro `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Spusťte upgrade.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Po `leapp upgrade` úspěšném dokončení příkazu restartujte systém, aby se proces dokončil. Systém není k dispozici, protože se restartuje několikrát. Monitorujte proces pomocí konzoly sériového portu.

1.  Ověřte, že upgrade byl úspěšně dokončen.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Po dokončení upgradu odeberte kořenový přístup SSH:
    1. Otevřete soubor */etc/ssh/sshd_config*.
    1. Vyhledejte `#PermitRootLogin yes`.
    1. Přidejte znak čísla ( `#` ), který bude řetězec komentovat.

1. Změny se projeví až po restartování služby SSHD.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Běžné problémy

K následujícím chybám obvykle dochází v případě, že dojde k `leapp preupgrade` chybě procesu nebo `leapp upgrade` dojde k chybě procesu:

* **Chyba**: nebyly nalezeny žádné shody pro následující zakázané vzory modulu plug-in.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Řešení**: zakažte modul plug-in Správce předplatného. Zakažte ho úpravou souboru */etc/yum/pluginconf.d/Subscription-Manager.conf* a změnou `enabled` na `enabled=0` .

    K této chybě dochází, když modul plug-in Správce předplatného `yum` , který je povolený, se pro `PAYG` virtuální počítače nepoužívá.

* **Chyba**: možné problémy se vzdáleným přihlášením pomocí kořenového adresáře.

    Tato chyba se může zobrazit, když `leapp preupgrade` dojde k chybě:

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
    **Řešení**: povolení přístupu root v */etc/sshd_config*.

    K této chybě dojde, když není v */etc/sshd_config* povolený kořenový přístup SSH. Další informace najdete v části [přípravy](#preparations) v tomto článku. 


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [obrázcích Red Hat v Azure](./redhat-images.md).
* Přečtěte si další informace o [infrastruktuře aktualizací Red Hat](./redhat-rhui.md).
* Přečtěte si další informace o [nabídce RHEL BYOS](./byos.md).
* Další informace o procesech místního upgradu Red Hat najdete v tématu [upgrade z RHEL 7 na RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) v dokumentaci k Red Hat.
* Další informace o zásadách podpory Red Hat pro všechny verze RHEL najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) v dokumentaci k Red Hat.