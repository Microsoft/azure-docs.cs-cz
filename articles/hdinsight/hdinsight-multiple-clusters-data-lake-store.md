---
title: Použití více clusterů HDInsight pomocí účtu Azure Data Lake Storage – Azure
description: Další informace o použití více než jeden cluster HDInsight pomocí jednoho účtu Data Lake Storage
keywords: hdinsight storage, hdfs, strukturovaná data, Nestrukturovaná data, data lake store
services: hdinsight,storage
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 0760d850bdc6dab84722f00f1061d53f9b95cfcf
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912414"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Použití více clusterů HDInsight s účtem úložiště Azure Data Lake

Spouští se s HDInsight verze 3.5, můžete vytvářet clustery HDInsight s účty úložiště Azure Data Lake jako výchozí systém souborů.
Data Lake Storage podporuje neomezené úložiště, díky které je ideální nejen pro velké objemy dat; hostování Můžete ale také pro hostování více HDInsight clustery tuto sdílenou složku jednoho účtu úložiště Data Lake. Pokyny o tom, jak vytvořit HDInsight cluster s Data Lake Storage jako úložiště najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Tento článek obsahuje doporučení pro Data Lake Storage správce pro nastavení jediné a sdílené účtu Data Lake Storage, který lze použít v rámci více **aktivní** clustery HDInsight. Tato doporučení se vztahují k hostování několika zabezpečených stejně jako nezabezpečené clustery systému Apache Hadoop na sdíleného účtu Data Lake Storage.


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage souborů a složek na úrovni seznamy ACL

Zbývající část tohoto článku předpokládá, že máte dobré znalosti souborům a složkám úrovně seznamy řízení přístupu v Azure Data Lake Storage, která je popsána v podrobností [řízení přístupu v Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Nastavení úložiště data Lake pro několik clusterů HDInsight
Dejte nám trvat složce dvě úrovně hierarchie k vysvětlení doporučení pro používání více clusterů HDInsight s účtem Data Lake Storage. Vezměte v úvahu s účtem Data Lake Storage s strukturu složek **/clustery/finance**. S touto strukturou můžete použít všechny clustery vyžadované finanční organizace /clusters/finance jako umístění úložiště. V budoucnu, pokud jiné organizace Řekněme, že marketingu, chce vytvořit HDInsight clustery pomocí stejného účtu Data Lake Storage, uživatel může vytvořit/clusterů nebo marketingovým oddělením. Teď použijeme **/clustery/finance**.

Pokud chcete povolit tuto strukturu složek, které bude efektivně využívat clustery HDInsight, musí správce úložiště Data Lake přiřadit příslušná oprávnění, jak je popsáno v tabulce. Oprávnění v tabulce odpovídají přístupové seznamy ACL a ne výchozí – seznamy ACL. 


|Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Jmenovaný uživatel | Oprávnění pro pojmenovaného uživatele | Pojmenované skupiny | Oprávnění s názvem skupiny |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Instanční objekt |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |admin |admin |Instanční objekt |--x  |FINGRP |r-x         |
|/ clustery/finance | rwxr-x--t |admin |FINGRP  |Instanční objekt |rwx  |-  |-     |

V tabulce

- **správce** je tvůrce a správce účtu Data Lake Storage.
- **Instanční objekt služby** je instanční objekt služby Azure Active Directory (AAD) přidružené k účtu.
- **FINGRP** je skupina uživatelů vytvořené v AAD, která obsahuje uživatele z finanční organizace.

Pokyny o tom, jak vytvořit aplikaci AAD (která také vytvoří instanční objekt), najdete v tématu [vytvořit aplikaci AAD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Pokyny o tom, jak vytvořit skupinu uživatelů v adresáři AAD najdete v tématu [Správa skupin ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Některé klíčové body ke zvážení.

- Dvě úrovně struktury složek (**/clusterů/finance/**) musí být vytvořené a zřízené s příslušnými oprávněními správce úložiště Data Lake **před** pomocí účtu úložiště pro clustery. Tato struktura není vytvořena automaticky při vytváření clusterů.
- Výše uvedený příklad doporučuje nastavení vlastnící skupina tohoto **/clustery/finance** jako **FINGRP** a jejímu **r-x** přístup k FINGRP do hierarchie celou složku spuštění z kořenového adresáře. Tím se zajistí, že členové FINGRP se můžete dostat strukturu složek, počínaje kořenový.
- V případech, kdy vytvořit clustery se v rámci různých objektů služby AAD **/clustery/finance**, sticky-bit (při nastavení na **finance** složky) zajišťuje, že vytvořeny složky podle jednoho instančního objektu nelze odstranit, druhý.
- Jakmile strukturu složek a oprávnění jsou na místě, procesu vytváření clusteru HDInsight vytvoří místo specifických pro cluster úložiště v rámci **/clusterů/finance/**. Například může být úložiště pro cluster s názvem fincluster01 **/clusters/finance/fincluster01**. Vlastnictví a oprávnění pro složky vytvoří cluster HDInsight je uveden v tabulce tady.

    |Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Jmenovaný uživatel | Oprávnění pro pojmenovaného uživatele | Pojmenované skupiny | Oprávnění s názvem skupiny |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Instanční objekt služby |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Doporučení pro úlohy vstupní a výstupní data

Doporučujeme vám, že vstupní data a úlohy a výstup z úlohy být uložena ve složce mimo **/clusterů**. Tím se zajistí, že i v případě, že uvolnit nějaké místo úložiště se odstraní složka specifická pro cluster, úlohu vstupy a výstupy jsou stále k dispozici pro budoucí použití. V takovém případě se ujistěte, že hierarchii složek pro ukládání úlohy vstupy a výstupy umožňuje odpovídající úroveň přístupu pro instanční objekt.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Omezit na clusterech sdílení jednoho účtu úložiště

Omezení počtu clusterů, které můžete sdílet jeden účet Data Lake Storage závisí na úlohy se spouští tyto clustery. S příliš mnoha clusterech nebo velmi náročné úlohy na clusterech, které sdílejí účet úložiště může způsobit, že úložiště účtu příchozí a odchozí přenos do dojde.

## <a name="support-for-default-acls"></a>Podpora pro výchozí seznamy ACL

Při vytváření instančního objektu s přístupem s názvem uživatele (jak je znázorněno v předchozí tabulce), doporučujeme **není** přidání s názvem – uživatel se výchozího seznamu ACL. Zřizování přístupu s názvem uživatelů pomocí výchozí seznamy ACL výsledky v přiřazení 770 oprávnění pro vlastnícího uživatele, vlastnící skupinu a další. Když tuto výchozí hodnotu 770 okamžitě nepřebírá oprávnění ze vlastnícího uživatele (7) nebo vlastnící skupiny (7), trvá daleko všechna oprávnění pro ostatní uživatele (0). Výsledkem je známý problém s jeden konkrétní případ použití, který je podrobně popsána [– známé problémy a řešení](#known-issues-and-workarounds) oddílu.

## <a name="known-issues-and-workarounds"></a>Známé problémy a řešení

V této části najdete seznam známých problémů pro HDInsight pomocí Data Lake Storage a jejich řešení.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Veřejně viditelné lokalizované prostředky Apache Hadoop YARN

Při vytvoření nového účtu úložiště Azure Data Lake je sada bity oprávnění přístupového seznamu ACL k 770 automaticky zřízena kořenový adresář. Kořenové složky vlastnící uživatel je nastaven na uživatele, který vytvořil účet (Správce úložiště Data Lake) a je vlastnící skupina nastavena na primární skupiny uživatele, který účet vytvořil. Žádný přístup neposkytujeme "ostatní".

Tato nastavení se ví, vliv na jeden konkrétní HDInsight případ použití zaznamenány [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Umožňují odesílání úloh může selhat s chybovou zprávou nějak takto:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Jak je uvedeno v JIRA YARN propojené dříve, při lokalizaci prostředky veřejné lokalizátora ověří, zda všechny požadované prostředky jsou skutečně veřejné jejich oprávnění ve vzdáleném systému souborů. Žádné LocalResource nevejde této podmínky je odmítnutých pro lokalizaci. Kontrola oprávnění, zahrnuje přístup pro čtení do souboru "ostatní". Tento scénář nefunguje out-of-the-box při hostování clustery HDInsight v Azure Data Lake, protože Azure Data Lake zakazuje veškerý přístup k "ostatní" na kořenové úrovni složky.

#### <a name="workaround"></a>Alternativní řešení
Sada čtení spouštěcích oprávnění k **ostatní** prostřednictvím hierarchie, například na **/**, **/clusterů** a   **/clustery/finance** jak je znázorněno v předchozí tabulce.

## <a name="see-also"></a>Další informace najdete v tématech

* [Rychlé zprovoznění: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
