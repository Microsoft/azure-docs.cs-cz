---
title: Místní upgrade Red Hat Enterprise Linux imagí v Azure
description: Vyhledání kroků k provedení místního upgradu z imagí Red Hat Enterprise 7. x na nejnovější verzi 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447727"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux místní upgrady

Tento článek poskytuje podrobné pokyny, jak provést místní upgrade z Red Hat Enterprise Linux 7 na Red Hat Enterprise Linux 8 pomocí nástroje ' Leapp ' v Azure. V rámci místního upgradu je existující operační systém RHEL 7 nahrazen verzí RHEL 8.

>[!Note] 
> SQL Server v Red Hat Enterprise Linuxch nabídkách nepodporují místní upgrade v Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Co očekávat během upgradu
Během upgradu bude systém několikrát restartovat a to je normální. Poslední restartováním se upgraduje virtuální počítač na nejnovější dílčí verzi RHEL 8.

## <a name="preparations-for-the-upgrade"></a>Příprava na upgrade
Místní upgrady jsou oficiálně doporučeným způsobem pro Red Hat a Azure a umožňují zákazníkům upgradovat systém na další hlavní verzi. Před provedením upgradu je zde několik věcí, které byste měli znát a vzít v úvahu. 

>[!Important] 
> Než provedete upgrade, podejte prosím snímek image.

>[!NOTE]
> Příkazy v tomto článku je třeba spustit pomocí kořenového účtu.

* Ujistěte se, že používáte nejnovější verzi RHEL 7, která je aktuálně RHEL 7,9. Pokud používáte uzamčenou verzi a nemůžete upgradovat na RHEL 7,9, můžete použít [postup pro přepnutí do úložiště, které není EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Spuštěním následujícího příkazu zjistíte, jak probíhá upgrade, a jestli se dokončí. Příkaz by měl vygenerovat soubor v rámci "/var/log/leapp/leapp-report.txt", který vysvětluje proces a co se děje, a pokud je upgrade možná nebo ne.
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Kroky pro provedení upgradu

Tyto kroky proveďte pečlivě. Na testovacím počítači se před provozními instancemi doporučuje provést upgrade.

1. K načtení nejnovějších balíčků klientů proveďte aktualizaci Yumu.
    ```bash
    yum update
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
    
1. Povolit PermitRootLogin v/etc/ssh/sshd_config
    1. Otevřete soubor/etc/ssh/sshd_config
    1. Hledat #PermitRootLogin Ano
    1. Odebrat znak "#" pro zrušení komentáře



1. Proveďte upgrade Leapp.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Restartujte službu sshd, aby se změny projevily.
    ```bash
    systemctl restart sshd
    ```
1. Odkomentovat PermitRootLogin v/etc/ssh/sshd_config znovu
    1. Otevřete soubor/etc/ssh/sshd_config
    1. Hledat #PermitRootLogin Ano
    1. Přidat # do komentáře

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [obrázcích Red Hat v Azure](./redhat-images.md).
* Přečtěte si další informace o [infrastruktuře aktualizace Red Hat](./redhat-rhui.md).
* Přečtěte si další informace o [nabídce RHEL BYOS](./byos.md).
* Informace o procesech místního upgradu Red Hat najdete v dokumentaci k Red Hat, [která se upgraduje z RHEL 7 na RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) .
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .