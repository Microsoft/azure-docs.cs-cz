---
title: Použití více clusterů HDInsight pomocí účtu Azure Data Lake Store - Azure | Microsoft Docs
description: Naučte se používat více než jeden cluster HDInsight poskytují jeden účet Data Lake Store
keywords: úložiště hdinsight, hdfs, strukturovaných dat, nestrukturovaných dat, data lake store
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 1d073732b5dd9b9867813d9ffcfad5caa1131d81
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102117"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Použití více clusterů HDInsight pomocí účtu Azure Data Lake Store

Počínaje HDInsight verze 3.5, můžete vytvořit clustery HDInsight pomocí účtů Azure Data Lake Store jako výchozí systém souborů.
Data Lake Store podporuje neomezené úložiště, která usnadňuje ideální nejen pro hostování velké objemy dat; Můžete ale také pro hostování clusterů HDInsight více tuto sdílenou složku jeden účet Data Lake Store. Pokyny k vytvoření clusteru HDInsight s Data Lake Store jako úložiště najdete v tématu [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Tento článek obsahuje doporučení pro Data Lake Správce úložiště pro nastavení jednoho a sdílené Data Lake ukládání účet, který lze použít v rámci více **active** clusterů HDInsight. Tato doporučení se vztahují k hostování více clusterů systému Hadoop zabezpečené a také nezabezpečené na sdíleného účtu Data Lake store.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Data Lake Store souborů a složek úroveň seznamy ACL

Zbývající část tohoto článku předpokládá, že máte dobrou znalost souborů a složek úroveň seznamy ACL v Azure Data Lake Store, který je popsán v podrobností v [řízení přístupu v Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Data Lake Store nastavení pro více clusterů HDInsight
Dejte nám trvat složky dvojúrovňové hierarchii vysvětlení doporučení pro více clusterů HDInsight pomocí účtu Data Lake Store. Vezměte v úvahu máte účet Data Lake Store s struktura složek **nebo clustery, finance**. Tato struktura všechny clustery finanční organizace vyžaduje pomocí /clusters/finance jako umístění úložiště. V budoucnu, pokud jiné organizaci vyslovení marketingu, chce vytvořit HDInsight clustery pomocí stejného účtu Data Lake Store, uživatel může vytvořit/clustery/marketing. Prozatím se právě použijeme **nebo clustery, finance**.

Chcete-li povolit tuto strukturu složek pro efektivně používat clusterů HDInsight, Data Lake Store musí správce příslušná oprávnění, jak je popsáno v tabulce. Oprávnění uvedené v tabulce odpovídají přístup seznamy řízení přístupu a výchozí-ACL. 


|Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Jmenovaný uživatel | Jmenovaný uživatel oprávnění | S názvem skupiny | Oprávnění skupiny s názvem |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |Správce |Správce  |Instanční objekt |– x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |Správce |Správce |Instanční objekt |– x  |FINGRP |r-x         |
|nebo clustery, finance | rwxr-x--t |Správce |FINGRP  |Instanční objekt |rwx  |-  |-     |

V tabulce

- **správce** creator a správce účtu Data Lake Store.
- **Instanční objekt** je objekt služby Azure Active Directory (AAD), který je přidružený k účtu.
- **FINGRP** je skupina uživatelů vytvořené v AAD, která obsahuje uživatele z finančního organizace.

Pokyny o tom, jak vytvořit aplikaci AAD (která také vytvoří objekt služby), najdete v tématu [vytvořit aplikaci AAD](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Pokyny o tom, jak vytvořit skupiny uživatelů v AAD najdete v tématu [Správa skupin v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Některé klíčové body vzít v úvahu.

- Dvě úrovně struktura složek (**/clusterů nebo finanční nebo**) musí být vytvořen a vytváří správce Data Lake Store s příslušnými oprávněními **před** pomocí účtu úložiště pro clustery. Tato struktura není vytvořena automaticky při vytváření clusterů.
- V předchozím příkladu doporučuje nastavení vlastnícím skupiny **nebo clustery, finance** jako **FINGRP** a jejímu **r-x** přístup k FINGRP do hierarchie celou složku, ve spuštění z kořenového adresáře. Tím se zajistí, že členové FINGRP můžete přejít od kořenové struktura složek.
- V případě, že pokud různé objekty služby AAD můžete vytvořit clustery v **nebo clustery, finance**, trvalé bit (Pokud nastavíte na **finanční** složky) zajišťuje vytvořený složky podle jednoho instanční objekt nelze odstranit, jinými.
- Jakmile struktury složek a oprávnění jsou na místě, procesu vytváření clusteru HDInsight vytvoří místo specifických pro cluster úložiště v rámci **/clusterů nebo finanční nebo**. Například může být úložiště pro cluster s názvem fincluster01 **/clusters/finance/fincluster01**. Vlastnictví a oprávnění pro složky vytvořené clusteru HDInsight se zobrazují v tabulce sem.

    |Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Jmenovaný uživatel | Jmenovaný uživatel oprávnění | S názvem skupiny | Oprávnění skupiny s názvem |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Instanční objekt |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Doporučení pro úlohy vstupní a výstupní data

Doporučujeme vám, že vstupní data na úlohy a výstupy z úlohy být uložena ve složce mimo **/clusterů**. Tím se zajistí, že i v případě uvolnění některé prostor úložiště se odstranit složku na specifických pro cluster, úlohy vstupy a výstupy stále k dispozici pro budoucí použití. V takovém případě zajistěte, aby hierarchie složky pro uložení úlohy vstupy a výstupy povolovalo odpovídající úroveň přístupu pro objekt služby.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Omezit na clusterech sdílení účet jednoho úložiště

Limit počtu clusterů, které můžete sdílet jeden účet Data Lake Store, které závisí na úlohy spouštěné v těchto clusterů. Má příliš mnoho velmi náročných úloh nebo clusterů v clusterech, které sdílejí účet úložiště může způsobit, že úložiště účet vstupní/výstupní k získání omezeny.

## <a name="support-for-default-acls"></a>Podpora pro výchozí seznamy řízení přístupu

Při vytváření objektu služby pomocí přístupu s názvem uživatele (jak je uvedené v předchozí tabulce), doporučujeme **není** přidání s názvem uživatele s seznamu ACL výchozí. Zřizování přístupu s názvem uživatele pomocí výchozí ACL výsledky v přiřazení 770 oprávnění pro vlastnícího uživatele, který je vlastníkem skupiny a dalších. Při této výchozí hodnotu 770 rychle nevyžaduje oprávnění z vlastnícího uživatele (7) nebo vlastnící group (7), trvá rychle všechna oprávnění pro ostatní (0). Výsledkem je známý problém s jeden konkrétní případ použití popsaný v části [známé problémy a řešení](#known-issues-and-workarounds) části.

## <a name="known-issues-and-workarounds"></a>Známé problémy a řešení

V této části jsou uvedené známé problémy pro použití HDInsight s Data Lake Store a jejich řešení.

### <a name="publicly-visible-localized-yarn-resources"></a>Veřejně viditelný lokalizované prostředky YARN

Když je vytvořen nový účet Azure Data Lake store, je kořenový adresář automaticky zřízena sadu 770 bitů oprávnění přístupu ACL. Uživatel je nastaven na uživatele, který vytvořili účet (Data Lake Store správce) a skupině vlastnícím nastavena na primární skupiny uživatele, který vytvořili účet, který vlastní kořenové složce. Žádný přístup se poskytuje "ostatní".

Tato nastavení se ví, vliv na jeden konkrétní HDInsight případ použití zaznamenané v [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Odesílání úlohy může selhat s chybovou zprávou, která je podobná této:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Jak je uvedeno v JIRA YARN propojené dříve, při lokalizace veřejných prostředků, lokalizátora ověří, zda jsou všechny požadované prostředky skutečně veřejné kontrolou svá oprávnění na vzdálený systém souborů. Všechny LocalResource, který se nevejde této podmínky je odmítnutých pro lokalizaci. Kontrola oprávnění, zahrnuje přístup pro čtení k souboru pro "ostatní". Tento scénář nefunguje se na pole při hostování clusterů HDInsight v Azure Data Lake, protože Azure Data Lake odmítne všechny přístup na "ostatní" na kořenové úrovni složky.

#### <a name="workaround"></a>Alternativní řešení
Sada pro čtení-oprávnění ke spouštění pro **ostatní** prostřednictvím hierarchie, například na **/**, **/clusterů** a **nebo clustery, finance** jak je znázorněno v předchozí tabulce.

## <a name="see-also"></a>Další informace najdete v tématech

* [Rychlý úvod: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)


