---
title: Správa přístupu SSH pro účty domény v Azure HDInsight
description: Postup správy přístupu SSH pro účty Azure AD v HDInsightu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472515"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Správa přístupu SSH pro účty domény v Azure HDInsight

V zabezpečených clusterech mohou ve výchozím nastavení všichni uživatelé domény ve [službě Azure AD DS](../../active-directory-domain-services/overview.md) [ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) do uzly hlavy a okraje. Tito uživatelé nejsou součástí skupiny sudoers a nezískají root přístup. Uživatel SSH vytvořený během vytváření clusteru bude mít root přístup.

## <a name="manage-access"></a>Správa přístupu

Chcete-li upravit přístup SSH pro `/etc/ssh/sshd_config` konkrétní uživatele nebo skupiny, aktualizujte na každém z uzlů.

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Otevřete `ssh_confi`soubor g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Upravte `sshd_config` soubor podle potřeby. Pokud omezíte uživatele na určité skupiny, místní účty nemohou do tohoto uzlu svit. Následuje pouze příklad syntaxe:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Pak uložte změny: **Ctrl + X**, **Y**, **Enter**.

1. Restartujte sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Opakujte výše uvedené kroky pro každý uzel.

## <a name="ssh-authentication-log"></a>Protokol ověřování SSH

Protokol ověřování SSH `/var/log/auth.log`je zapsán do . Pokud se zobrazí jakékoli selhání přihlášení prostřednictvím SSH pro místní nebo doménové účty, budete muset projít protokolu ladit chyby. Často problém může souviset s konkrétními uživatelskými účty a je obvykle vhodné vyzkoušet jiné uživatelské účty nebo SSH pomocí výchozího uživatele SSH (místní účet) a pak se pokusit o kinit.

## <a name="ssh-debug-log"></a>Protokol ladění SSH

Chcete-li povolit podrobné protokolování, `sshd` budete `-d` muset restartovat s možností. Stejně jako `/usr/sbin/sshd -d` `sshd` můžete také spustit na vlastním portu (jako 2222), takže nemusíte zastavit hlavní Daemon SSH. Můžete také `-v` použít možnost s klientem SSH získat další protokoly (zobrazení na straně klienta selhání).

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí balíčku enterprise security package](./apache-domain-joined-manage.md)
* [Připojte se k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
