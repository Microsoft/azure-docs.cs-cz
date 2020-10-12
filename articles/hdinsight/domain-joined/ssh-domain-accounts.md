---
title: Správa přístupu SSH pro účty domén ve službě Azure HDInsight
description: Postup správy přístupu SSH pro účty Azure AD v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 5be992ef8375f98b3c5978d8b71dc92ce9f91123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081498"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Správa přístupu SSH pro účty domén ve službě Azure HDInsight

V zabezpečených clusterech se ve výchozím nastavení pro všechny uživatele domény v [Azure služba AD DS](../../active-directory-domain-services/overview.md) můžou na uzly hlav a Edge povolit [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Tito uživatelé nejsou součástí skupiny sudoers a nezískají přístup root. Uživatel SSH vytvořený během vytváření clusteru bude mít root Access.

## <a name="manage-access"></a>Správa přístupu

Chcete-li upravit přístup SSH ke konkrétním uživatelům nebo skupinám, aktualizujte `/etc/ssh/sshd_config` na všech uzlech.

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Otevřete `ssh_confi` soubor g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Upravte `sshd_config` soubor podle potřeby. Pokud omezíte uživatele na určité skupiny, pak místní účty nebudou moci v tomto uzlu SSH. Následuje pouze příklad syntaxe:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Pak změny uložte: **CTRL + X**, **Y**, **ENTER**.

1. Restartujte sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Opakujte výše uvedené kroky pro každý uzel.

## <a name="ssh-authentication-log"></a>Protokol ověřování SSH

Do se zapisuje protokol ověřování SSH `/var/log/auth.log` . Pokud se zobrazí chyba přihlášení prostřednictvím protokolu SSH pro místní nebo doménové účty, budete muset projít protokolem a ladit chyby. Problém se často může vztahovat na konkrétní uživatelské účty a obvykle se jedná o dobrý postup, jak vyzkoušet jiné uživatelské účty nebo SSH pomocí výchozího uživatele SSH (místní účet) a pak se pokusit o kinit.

## <a name="ssh-debug-log"></a>Protokol ladění SSH

Pokud chcete povolit podrobné protokolování, budete se muset restartovat `sshd` s `-d` možností. Podobně jako můžete `/usr/sbin/sshd -d` spustit také `sshd` na vlastním portu (například 2222), takže nemusíte zastavit hlavní démona SSH. Pomocí `-v` Možnosti s klientem SSH můžete také získat další protokoly (zobrazení chyb na straně klienta).

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí Balíček zabezpečení podniku](./apache-domain-joined-manage.md)
* [Připojte se k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
