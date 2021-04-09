---
title: Apache Hadoop součásti a verze – Azure HDInsight
description: Přečtěte si informace o komponentách a verzích Apache Hadoop ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726579"
---
# <a name="azure-hdinsight-versions"></a>Verze Azure HDInsight

Sady HDInsight Apache Hadoop součásti prostředí a platformy HDInsight do balíčku, který je nasazený v clusteru. Další podrobnosti najdete v tématu [Jak funguje Správa verzí služby HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight

Tato tabulka obsahuje seznam verzí HDInsight, které jsou k dispozici v Azure Portal a dalších metodách nasazení, jako jsou PowerShell, CLI a .NET SDK.

| Verze HDInsight | Operační systém virtuálního počítače | Datum vydání| Typ podpory | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |Září 24, 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Yes |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4. dubna 2017      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Vypršení platnosti Standard Support – 30. června 2021 <br> Vypršení platnosti podpora Basic – 3. dubna 2022 |4. dubna 2022 |Yes |

* Od 1. července 2021 Microsoft nabídne podpora Basic pro určité typy clusterů HDI 3,6. Viz [verze komponent HDInsight 3,6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Poznámky k verzi

Další poznámky k verzi nástroje HDInsight najdete v [poznámkách k verzi HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Možnosti podpory pro verze HDInsight

Podpora je definovaná jako časové období, kdy služba a podpora Microsoftu podporuje verzi HDInsight. HDInsight nabízí dva typy podpory: 
- **Standard Support** je časové období, ve kterém Microsoft poskytuje aktualizace a podporu clusterů HDInsight.  
    Doporučujeme sestavovat řešení pomocí nejnovější plně podporované verze. 
- **Podpora Basic** je časové období, ve kterém Microsoft poskytne omezené obsluhy poskytovatele prostředků HDInsight. Image HDInsight a součásti open-source softwaru (OSS) nebudou obsluhované.   V clusterech HDInsight se budou opravovat jenom kritické opravy zabezpečení.  
  Společnost Microsoft nedoporučuje vytvářet nové clustery ani vytvářet žádná nová řešení, pokud je verze v podpora Basic. Doporučujeme migrovat existující clustery na nejnovější plně podporovanou verzi. 

**Vypršení platnosti podpory** znamená, že Microsoft už neposkytuje podporu pro konkrétní verzi HDInsight. A již nemusí být k dispozici prostřednictvím Azure Portal pro vytvoření clusteru.

**Vyřazení** znamená, že existující clustery verze HDInsight i nadále fungují tak, jak jsou. Nové clustery této verze se nedají vytvořit prostřednictvím žádného prostředku, který zahrnuje rozhraní příkazového řádku a sady SDK. Jiné funkce roviny ovládacího prvku, jako je ruční škálování a automatické škálování, nejsou zaručené pracovat po datu vyřazení. Podpora není k dispozici pro vyřazené verze.

## <a name="versioning-considerations"></a>Požadavky na správu verzí
- Po nasazení clusteru s imagí se tento cluster neupgraduje automaticky na novější verzi image. Při vytváření nových clusterů se nasadí nejnovější verze image.
- Zákazníci by měli při použití nové verze HDInsight testovat a ověřovat, jestli jsou aplikace správně spuštěné.
- HDInsight si vyhrazuje právo změnit výchozí verzi bez předchozího upozornění. Pokud máte závislost verze, určete verzi HDInsight při vytváření clusterů.
- HDInsight může před vyřazením verze HDInsight vyřadit verzi komponenty OSS.

## <a name="next-steps"></a>Další kroky

- [Nastavení clusteru pro Apache Hadoop, Spark a další funkce v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Balíček zabezpečení podniku](./enterprise-security-package.md)
- [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
