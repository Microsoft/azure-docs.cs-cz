---
title: Víc clusterů HDInsight & jeden Azure Data Lake Storage účet.
description: Naučte se používat víc než jeden cluster HDInsight s jedním Data Lake Storageovým účtem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: df28374d0f124ceb46d2f97d55218d428275deca
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533083"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Použití více clusterů HDInsight s účtem Azure Data Lake Storage

Počínaje HDInsight verze 3,5 můžete vytvořit clustery HDInsight s účty Azure Data Lake Storage jako výchozí systém souborů.
Data Lake Storage podporuje neomezené úložiště, které je ideální a nejenom pro hostování velkých objemů dat; ale taky pro hostování více clusterů HDInsight, které sdílejí jeden Data Lake Storage účet. Pokyny k vytvoření clusteru HDInsight s Data Lake Storage jako úložiště najdete v tématu [rychlý Start: nastavení clusterů ve službě HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

Tento článek poskytuje doporučení pro správce Data Lake Storage pro nastavení jednoho a sdíleného Data Lake Storage účtu, který se dá použít v několika **aktivních** clusterech HDInsight. Tato doporučení platí pro hostování více než zabezpečených Apache Hadoop clusterů na sdíleném Data Lake Storagem účtu.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage seznamů ACL na úrovni souborů a složek

Ve zbývající části tohoto článku se předpokládá, že máte dobré znalosti seznamů ACL na úrovni souborů a složek v Azure Data Lake Storage, který je podrobně popsaný v tématu [řízení přístupu v Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Nastavení Data Lake Storage pro několik clusterů HDInsight

Podíváme se na oboustrannou hierarchii složek, kde můžete vysvětlit doporučení pro používání více clusterů HDInsight s účtem Data Lake Storage. Vezměte v úvahu, že máte účet Data Lake Storage se strukturou složek **/Clusters/finance** . V této struktuře můžou všechny clustery, které finanční organizace vyžaduje, používat/Clusters/finance jako umístění úložiště. Pokud je v budoucnu další organizace, což znamená marketing, chce vytvořit clustery HDInsight pomocí stejného Data Lake Storage účtu, můžou vytvořit/Clusters/marketing. Prozatím můžeme jenom **/Clusters/finance** použít.

Aby bylo možné tuto strukturu složky efektivně využívat clustery HDInsight, musí správce Data Lake Storage přiřadit příslušná oprávnění, jak je popsáno v tabulce. Oprávnění zobrazená v tabulce odpovídají přístupovým seznamům ACL a nikoli výchozím nastavením seznamů ACL.

|Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Pojmenovaný uživatel | Pojmenovaná uživatelská oprávnění | Pojmenovaná skupina | Pojmenovaná skupina oprávnění |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |správce |správce  |Instanční objekt |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |správce |správce |Instanční objekt |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |správce |FINGRP  |Instanční objekt |RWX  |-  |-     |

V tabulce

- **správce** je tvůrcem a správcem účtu Data Lake Storage.
- **Instanční objekt** je instanční objekt služby Azure Active Directory (AAD) přidružený k účtu.
- **FINGRP** je skupina uživatelů vytvořená v AAD, která obsahuje uživatele z organizace finance.

Pokyny k vytvoření aplikace AAD (která také vytvoří instanční objekt) najdete v tématu [Vytvoření aplikace AAD](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Pokyny k vytvoření skupiny uživatelů v AAD najdete v tématu [Správa skupin v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Některé klíčové body, které je třeba vzít v úvahu.

- **Než** použijete účet úložiště pro clustery, musíte vytvořit a zřídit pomocí správce Data Lake Storage oprávnění ke struktuře dvou úrovní ( **/Clusters/finance/** ). Tato struktura není vytvořena automaticky při vytváření clusterů.
- Výše uvedený příklad doporučuje nastavit vlastnící skupinu **/Clusters/finance** jako **FINGRP** a povolení přístupu **r-x** k FINGRP do celé hierarchie složky počínaje kořenem. Tím se zajistí, že členové FINGRP mohou procházet strukturu složky počínaje kořenovým adresářem.
- V případě, že různé objekty služby AAD můžou vytvářet clustery v rámci **/Clusters/finance** , služba s rychlým bitem (Pokud je nastavená ve složce **finance** ) zajišťuje, aby složky vytvořené jedním instančním objektem nemohly odstranit druhá.
- Po vytvoření struktury složky a oprávnění proces vytváření clusteru HDInsight vytvoří umístění úložiště pro konkrétní cluster v rámci **/Clusters/finance/** . Například úložiště pro cluster s názvem fincluster01 by mohlo být **/Clusters/finance/fincluster01** . V následující tabulce jsou uvedena vlastnictví a oprávnění pro složky vytvořené clusterem HDInsight.

    |Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Pojmenovaný uživatel | Pojmenovaná uživatelská oprávnění | Pojmenovaná skupina | Pojmenovaná skupina oprávnění |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Instanční objekt |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Doporučení pro vstupní a výstupní data úlohy

Doporučujeme, aby se vstupní data do úlohy a výstupy z úlohy ukládaly do složky mimo **/Clusters** . Tím je zajištěno, že i v případě, že je odstraněna složka specifická pro konkrétní cluster, aby bylo možné uvolnit místo v úložišti, jsou vstupy a výstupy úloh stále k dispozici pro budoucí použití. V takovém případě zajistěte, aby hierarchie složek pro ukládání vstupů a výstupů úlohy umožňovala odpovídající úroveň přístupu k instančnímu objektu.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Omezení počtu clusterů, které sdílejí jeden účet úložiště

Omezení počtu clusterů, které můžou sdílet jeden Data Lake Storage účet, závisí na zatížení spouštěného na těchto clusterech. Pokud máte v clusterech, které sdílejí účet úložiště, moc velký počet clusterů nebo velmi náročné úlohy, může dojít k omezení počtu vstupně-výstupních přenosů účtu úložiště.

## <a name="support-for-default-acls"></a>Podpora Default-ACLs

Při vytváření instančního objektu s přístupem k pojmenovanému uživateli (jak je znázorněno v tabulce výše) doporučujeme **Nepřidávat** pojmenovaného uživatele s výchozím seznamem ACL. Zřizování přístupu s názvem s použitím výchozích seznamů ACL vede k přiřazení oprávnění 770 pro vlastnícího uživatele, vlastnící skupinu a další. I když tato výchozí hodnota 770 nebere v úvahu oprávnění od vlastnícího uživatele (7) nebo vlastnícího skupiny (7), zabírá všechna oprávnění pro ostatní (0). Výsledkem je známý problém s jedním konkrétním případem použití, který je podrobně popsán v části [známé problémy a alternativní řešení](#known-issues-and-workarounds) .

## <a name="known-issues-and-workarounds"></a>Známé problémy a jejich řešení

V této části jsou uvedené známé problémy pro použití služby HDInsight s Data Lake Storage a jejich alternativní řešení.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Veřejně viditelné lokalizované Apache Hadoop prostředky PŘÍZe

Při vytvoření nového účtu Azure Data Lake Storage se kořenový adresář automaticky zřídí s bity oprávnění Access-ACL nastavenou na 770. Vlastnící uživatel kořenové složky se nastaví na uživatele, který účet vytvořil (správce Data Lake Storage), a vlastnící skupinu se nastaví na primární skupinu uživatele, který účet vytvořil. Pro "ostatní" není k dispozici žádný přístup.

Tato nastavení mají vliv na jeden konkrétní případ použití HDInsight zachycený v [nitě 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Odesílání úloh může selhat s chybovou zprávou podobnou této:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Jak je uvedeno v předchozím propojení JIRA nitě a při lokalizaci veřejných prostředků lokalizátora ověřuje, že všechny požadované prostředky jsou skutečně veřejné, a to kontrolou jejich oprávnění na vzdáleném souborovém systému. Všechny LocalResource, které nevyhovují této podmínce, jsou odmítnuty pro lokalizaci. Kontroly oprávnění, včetně přístupu pro čtení do souboru pro "ostatní". Tento scénář nebude při hostování clusterů HDInsight na Azure Data Lake fungovat předem, protože Azure Data Lake zakazuje všem přístupům "ostatní" na úrovni kořenové složky.

#### <a name="workaround"></a>Alternativní řešení

Nastavte oprávnění ke čtení pro **ostatní** přes hierarchii, například na **/** **/Clusters** a **/Clusters/finance** , jak je znázorněno v tabulce výše.

## <a name="see-also"></a>Viz také

- [Rychlý start: Nastavení clusterů ve službě HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)