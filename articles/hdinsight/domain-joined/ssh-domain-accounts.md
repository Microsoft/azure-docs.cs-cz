---
title: Správa přístupu SSH pro účty domén ve službě Azure HDInsight
description: Postup správy přístupu SSH pro účty Azure AD v HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 3fab06f5e269f311f798c096a6465c8c6ce75fc2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946756"
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
