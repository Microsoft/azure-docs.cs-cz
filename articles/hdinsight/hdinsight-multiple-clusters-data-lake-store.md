---
title: Více clusterů HDInsight & jednom účtu Azure Data Lake Storage
description: Přečtěte si, jak používat více než jeden cluster HDInsight s jedním účtem úložiště datového jezera
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495759"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Použití více clusterů HDInsight s účtem Azure Data Lake Storage

Počínaje HDInsight verze 3.5, můžete vytvořit HDInsight clustery s účty Azure Data Lake Storage jako výchozí souborový systém.
Data Lake Storage podporuje neomezené úložiště, které je ideální nejen pro hostování velkého množství dat; ale také pro hostování více clusterů HDInsight, které sdílejí jeden účet úložiště data lake. Pokyny k vytvoření clusteru HDInsight s úložištěm data lake jako úložiště najdete v [tématu Úvodní příručka: Nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Tento článek obsahuje doporučení pro správce úložiště datového jezera pro nastavení jednoho a sdíleného účtu úložiště datového jezera, který lze použít ve více **aktivních** clusterech HDInsight. Tato doporučení se vztahují na hostování více zabezpečených i nezabezpečených clusterů Apache Hadoop na sdíleném účtu Úložiště datového jezera.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>AKL alokace alokace na úrovni úložiště datových jezer a složek

Zbývající část tohoto článku předpokládá, že máte dobrou znalost souborů a složek úrovně ACL s azure data lake storage, který je podrobně popsán na [řízení přístupu v Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Nastavení úložiště datového jezera pro více clusterů HDInsight

Vezměme si hierarchii dvouúrovňových složek, abychom vysvětlili doporučení pro použití více clusterů HDInsight s účtem Úložiště datového jezera. Vezměte v úvahu, že máte účet úložiště datového jezera se strukturou složek **/clusters/finance**. S touto strukturou mohou všechny clustery vyžadované finanční organizací použít /clusters/finance jako umístění úložiště. Pokud chce jiná organizace, řekněme Marketing, v budoucnu vytvořit clustery HDInsight pomocí stejného účtu Úložiště datového jezera, mohou vytvořit /clusters/marketing. Pro tuto chvíli, pojďme jen použít **/clusters/finance**.

Aby bylo možné tuto strukturu složek efektivně používat clustery HDInsight, musí správce úložiště datového jezera přiřadit příslušná oprávnění, jak je popsáno v tabulce. Oprávnění zobrazená v tabulce odpovídají seznamu ACS aplikace Access a nikoli výchozím hodnotám ACL.

|Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Pojmenovaný uživatel | Oprávnění pojmenovaných uživatelů | Pojmenovaná skupina | Oprávnění pojmenované skupiny |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Instanční objekt |--x  |FINGRP   |r-x         |
|/clustery | rwxr-x--x |admin |admin |Instanční objekt |--x  |FINGRP |r-x         |
|/clustery/finance | rwxr-x--t |admin |FINGRP  |Instanční objekt |Rwx  |-  |-     |

V tabulce

- **admin** je tvůrcea a správce účtu Data Lake Storage.
- **Instanční objekt** je instanční objekt služby Azure Active Directory (AAD) přidružený k účtu.
- **FINGRP** je skupina uživatelů vytvořená v aad, která obsahuje uživatele z organizace Finance.

Pokyny k vytvoření aplikace AAD (která také vytvoří instanční objekt) naleznete v [tématu Vytvoření aplikace AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Pokyny k vytvoření skupiny uživatelů v aad, najdete v [tématu Správa skupin ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Některé klíčové body, aby zvážila.

- Před **použitím** účtu úložiště pro**clustery**musí správce úložiště datového jezera vytvořit a zřídit ji s příslušnými oprávněními. Tato struktura není vytvořena automaticky při vytváření clusterů.
- Výše uvedený příklad doporučuje nastavit vlastnící skupinu **/clusters/finance** jako **FINGRP** a povolit **r-x** přístup k FINGRP k celé hierarchii složek počínaje kořenem. Tím je zajištěno, že členové FINGRP můžete procházet strukturu složek od kořenového adresáře.
- V případě, že různé objekty zabezpečení služby AAD mohou vytvářet clustery pod **/clusters/finance**, sticky-bit (při nastavení ve složce **finance)** zajišťuje, že složky vytvořené jedním objektem zabezpečení služby nemohou být odstraněny jinými.
- Jakmile je struktura složek a oprávnění na místě, proces vytváření clusteru HDInsight vytvoří umístění úložiště specifické pro cluster v části **/clusters/finance/**. Například úložiště pro cluster s názvem fincluster01 může být **/clusters/finance/fincluster01**. Vlastnictví a oprávnění pro složky vytvořené clusterem HDInsight jsou zobrazeny v tabulce zde.

    |Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Pojmenovaný uživatel | Oprávnění pojmenovaných uživatelů | Pojmenovaná skupina | Oprávnění pojmenované skupiny |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Instanční objekt |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Doporučení pro vstupní a výstupní data úlohy

Doporučujeme, aby vstupní data do úlohy a výstupy z úlohy být uloženy ve složce mimo **/clusters**. Tím je zajištěno, že i v případě, že složka specifické pro cluster uskladnění některé úložný prostor, vstupy úloh a výstupy jsou stále k dispozici pro budoucí použití. V takovém případě zajistěte, aby hierarchie složek pro ukládání vstupů a výstupů úloh umožňuje odpovídající úroveň přístupu pro instanční objekt.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Omezení clusterů sdílejících jeden účet úložiště

Omezení počtu clusterů, které mohou sdílet jeden účet úložiště datového jezera, závisí na zatížení spuštěném v těchto clusterech. S příliš mnoho clusterů nebo velmi velké úlohy v clusterech, které sdílejí účet úložiště může způsobit, že příchozí přenos dat/odchozí přenos dat úložiště získat omezení.

## <a name="support-for-default-acls"></a>Podpora výchozích hodnot ACL

Při vytváření instančního objektu s přístupem pojmenovaného uživatele (jak je uvedeno v tabulce výše) doporučujeme **nepřidávat** pojmenovaného uživatele s výchozím seznamu ACL. Zřizování přístupu pojmenovaného uživatele pomocí výchozích seznamu ACL má za následek přiřazení 770 oprávnění pro vlastnící uživatele, vlastnící skupinu a další. Zatímco tato výchozí hodnota 770 neodebere oprávnění od vlastnící uživatele (7) nebo vlastnící skupiny (7), odebere všechna oprávnění pro ostatní (0). To má za následek známý problém s jedním konkrétním případem použití, který je podrobně popsán v části [Známé problémy a řešení.](#known-issues-and-workarounds)

## <a name="known-issues-and-workarounds"></a>Známé problémy a řešení

V této části jsou uvedeny známé problémy s používáním hdinsightu s úložištěm datových jezer a jejich řešení.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Veřejně viditelné lokalizované prostředky Apache Hadoop YARN

Když se vytvoří nový účet Azure Data Lake Storage, kořenový adresář se automaticky zřídí s bity oprávnění Access-ACL nastavenými na 770. Vlastnící uživatel kořenové složky je nastaven na uživatele, který účet vytvořil (správce úložiště datového jezera), a vlastnící skupina je nastavena na primární skupinu uživatele, který účet vytvořil. Pro "ostatní" není k dispozici žádný přístup.

Je známo, že tato nastavení ovlivňují jeden konkrétní případ použití HDInsight zachycený v [yarn247](https://hwxmonarch.atlassian.net/browse/YARN-247). Odeslání úlohy může selhat s chybovou zprávou podobnou této:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Jak je uvedeno v YARN JIRA propojeny dříve, při lokalizaci veřejných prostředků, lokalizace ověří, že všechny požadované prostředky jsou skutečně veřejné kontrolou jejich oprávnění na vzdáleném souborovém systému. Všechny LocalResource, který neodpovídá této podmínce je odmítnut pro lokalizaci. Kontrola oprávnění zahrnuje přístup pro čtení do souboru pro "ostatní". Tento scénář nefunguje out-of-the-box při hostování clusterů HDInsight na Azure Data Lake, protože Azure Data Lake odepře veškerý přístup k "ostatní" na úrovni kořenové složky.

#### <a name="workaround"></a>Alternativní řešení

Nastavte oprávnění ke **others** čtení a spuštění pro ostatní **/** prostřednictvím hierarchie, například na , **/clusters** a **/clusters/finance,** jak je znázorněno v tabulce výše.

## <a name="see-also"></a>Viz také

- [Rychlý start: Nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
