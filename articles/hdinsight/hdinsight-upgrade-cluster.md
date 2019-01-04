---
title: Upgrade clusteru HDInsight na novější verzi – Azure
description: Zjistěte, jak do clusteru HDInsight Upgrade na novější verzi.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 266de596633201b5590a6433d078cc05f2a267b4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994517"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Upgrade clusteru HDInsight na novější verzi
Abyste mohli využívat nejnovější funkce HDInsight, doporučujeme, abyste clustery HDInsight upgradovali na nejnovější verzi. Postupujte níže uvedených pokynů k upgradu vašeho HDInsight clusteru verze.

> [!NOTE]  
> Informace o podporovaných verzích HDInsight najdete v tématu [verzí komponenty HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

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
* [Připojení k HDInsightu pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Správa clusteru založených na Linuxu pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)

