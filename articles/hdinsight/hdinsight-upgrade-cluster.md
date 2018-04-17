---
title: Upgrade clusteru HDInsight na novější verzi-Azure | Microsoft Docs
description: Zjistěte, jak ke clusteru HDInsight se Upgrade na novější verzi.
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
ms.openlocfilehash: d6cd0dd8b326f5340e5014bba4babc6dd945744d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Upgrade clusteru HDInsight na novější verzi
Využívat výhody nejnovějších funkcí HDInsight, doporučujeme, abyste clustery HDInsight upgradovali na nejnovější verzi. Postupujte podle níže pokyny k upgradu vaší HDInsight clusteru verze.

> [!NOTE]
> Clustery HDInsight verze 3.2 nebo 3.3 se blíží datum vyřazení. Informace o podporovanou verzi HDInsight, naleznete v části [HDInsight verze součástí](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

## <a name="upgrade-tasks"></a>Úlohy upgradu
Pracovní postup pro upgrade clusteru HDInsight je následující.

![Diagram pracovní postup upgradu](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. Přečtěte si každá část tohoto dokumentu pochopit změny, které mohou být vyžadovány při upgradu clusteru HDInsight.
2. Vytvořte cluster s podporou prostředí testovací a kvalita záruky. Další informace týkající se vytvoření clusteru najdete v tématu [Naučte se vytvářet clustery HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Zkopírujte existující úlohy, zdroje dat a jímky do nového prostředí. V tématu [kopírování dat do testovacího prostředí](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment) další podrobnosti.
4. Provádění testů pro ověření a ujistěte se, že vaše úlohy fungují v novém clusteru podle očekávání.


Jakmile si ověříte, že vše funguje podle očekávání, naplánujte dobu odstávky migrace. Během této výpadky proveďte následující akce:

1.  Zálohujte všechna přechodný data uložená místně na uzlech clusteru. Například pokud máte data uložená přímo na hlavního uzlu.
2.  Odstraňte existující cluster.
3.  Vytvoření clusteru ve stejné podsíti virtuální sítě s nejnovější (nebo podporovaných) verze HDI pomocí stejné výchozí úložiště dat, které používá předchozích clusterů. To umožňuje nového clusteru pokračovat v práci s existující provozní data.
4.  Importujte přechodný data, která jste zálohovali.
5.  Spuštění úlohy nebo pokračovat ve zpracovávání pomocí nového clusteru.

## <a name="next-steps"></a>Další kroky
* [Naučte se vytvářet clustery HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Připojení k HDInsight pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Spravovat cluster se systémem Linux pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md)

