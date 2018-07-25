---
title: Upgrade clusteru HDInsight na novější verzi – Azure | Dokumentace Microsoftu
description: Zjistěte, jak do clusteru HDInsight Upgrade na novější verzi.
services: hdinsight
documentationcenter: ''
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: bhanupr
ms.openlocfilehash: 8845a049ebcda59bc0e6fd26618c33f51565e0ca
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225485"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Upgrade clusteru HDInsight na novější verzi
Abyste mohli využívat nejnovější funkce HDInsight, doporučujeme, abyste clustery HDInsight upgradovali na nejnovější verzi. Postupujte níže uvedených pokynů k upgradu vašeho HDInsight clusteru verze.

> [!NOTE]
> Informace o podporovaných verzích HDInsight najdete v tématu [verzí komponenty HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Úlohy upgradu
Pracovní postup pro upgrade clusteru HDInsight je následujícím způsobem.

![Diagram pracovního postupu upgradu](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Přečtěte si každá část tohoto dokumentu o změny, které mohou být vyžadovány při upgradu vašeho clusteru HDInsight.
2. Vytvoření clusteru jako assurance prostředí test nebo kvalita. Další informace týkající se vytvoření clusteru najdete v tématu [zjistěte, jak vytvářet clustery HDInsight založené na Linuxu](hdinsight-hadoop-provision-linux-clusters.md)
3. Zkopírujte do nové prostředí stávající úlohy, zdroje a jímky. Zobrazit [kopírování dat do testovacího prostředí](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) další podrobnosti.
4. Proveďte ověřovací testování, abyste měli jistotu, že vaše úlohy fungovat podle očekávání v novém clusteru.


Jakmile si ověříte, že vše funguje podle očekávání, naplánujte výpadky na migraci. Během výpadků proveďte následující akce:

1.  Zazálohujte si všechna přechodné data uložená místně na uzlech clusteru. Například pokud máte data přímo ze hlavního uzlu.
2.  Odstranění existujícího clusteru.
3.  Vytvoření clusteru ve stejné podsíti virtuální sítě s nejnovější (nebo se nepodporuje) verzi HDI pomocí stejného úložiště dat výchozí použitý v předchozím clusteru. To umožňuje nové clusteru pokračovat v práci s daty existujícího produkčního prostředí.
4.  Importujte všechny přechodné dat, který jste zazálohovali.
5.  Spuštění úlohy nebo pokračovat ve zpracování pomocí nového clusteru.

## <a name="next-steps"></a>Další kroky
* [Zjistěte, jak vytvářet clustery HDInsight založené na Linuxu](hdinsight-hadoop-provision-linux-clusters.md)
* [Připojení k HDInsight pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Správa clusteru s linuxem pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md)

