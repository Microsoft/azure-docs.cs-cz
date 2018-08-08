---
title: Použití více clusterů HDInsight pomocí účtu Azure Data Lake Store – Azure
description: Další informace o použití více než jeden cluster HDInsight pomocí jednoho účtu Data Lake Store
keywords: hdinsight storage, hdfs, strukturovaná data, Nestrukturovaná data, data lake store
services: hdinsight,storage
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 3f928ea7de4346e1860ca05ca0e5a9a8ac1052ce
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597099"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Použití více clusterů HDInsight pomocí účtu Azure Data Lake Store

Spouští se s HDInsight verze 3.5, můžete vytvářet clustery HDInsight s účty Azure Data Lake Store jako výchozí systém souborů.
Data Lake Store podporuje neomezené úložiště, díky které je ideální nejen pro velké objemy dat; hostování Můžete ale také pro hostování více HDInsight clustery tuto sdílenou složku jednoho účtu Data Lake Store. Pokyny o tom, jak vytvořit HDInsight cluster s Data Lake Store jako úložiště najdete v tématu [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Tento článek obsahuje doporučení pro Data Lake Správce úložiště pro nastavení jediné a sdílené účtu Data Lake Store, který lze použít v rámci více **aktivní** clustery HDInsight. Tato doporučení se vztahují k hostování několika zabezpečených stejně jako nezabezpečené clustery Hadoop v sdíleného účtu Data Lake store.


## <a name="data-lake-store-file-and-folder-level-acls"></a>Seznamy řízení přístupu na úrovni souborů data Lake Store a složek

Zbývající část tohoto článku předpokládá, že máte dobré znalosti souborům a složkám úrovně seznamy řízení přístupu v Azure Data Lake Store, která je popsána v podrobností [řízení přístupu v Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Data Lake Store nastavení pro několik clusterů HDInsight
Dejte nám trvat složce dvě úrovně hierarchie k vysvětlení doporučení pro používání více clusterů HDInsight s účtem Data Lake Store. Vezměte v úvahu s účtem Data Lake Store s strukturu složek **/clustery/finance**. S touto strukturou můžete použít všechny clustery vyžadované finanční organizace /clusters/finance jako umístění úložiště. V budoucnu, pokud jiné organizace Řekněme, že marketingu, chce vytvořit HDInsight clustery pomocí stejného účtu Data Lake Store, uživatel může vytvořit/clusterů nebo marketingovým oddělením. Teď použijeme **/clustery/finance**.

Pokud chcete povolit tuto strukturu složek, které bude efektivně využívat clustery HDInsight, musí správce Data Lake Store přiřadit příslušná oprávnění, jak je popsáno v tabulce. Oprávnění v tabulce odpovídají přístupové seznamy ACL a ne výchozí – seznamy ACL. 


|Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Jmenovaný uživatel | Oprávnění pro pojmenovaného uživatele | Pojmenované skupiny | Oprávnění s názvem skupiny |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |Správce |Správce  |Instanční objekt |--x  |FINGRP   |r-x         |
|/Clusters | rwxr-x--x |Správce |Správce |Instanční objekt |--x  |FINGRP |r-x         |
|/ clustery/finance | rwxr-x--t |Správce |FINGRP  |Instanční objekt |rwx  |-  |-     |

V tabulce

- **správce** je tvůrce a správce účtu Data Lake Store.
- **Instanční objekt služby** je instanční objekt služby Azure Active Directory (AAD) přidružené k účtu.
- **FINGRP** je skupina uživatelů vytvořené v AAD, která obsahuje uživatele z finanční organizace.

Pokyny o tom, jak vytvořit aplikaci AAD (která také vytvoří instanční objekt), najdete v tématu [vytvořit aplikaci AAD](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Pokyny o tom, jak vytvořit skupinu uživatelů v adresáři AAD najdete v tématu [Správa skupin ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Některé klíčové body ke zvážení.

- Dvě úrovně struktury složek (**/clusterů/finance/**) musí být vytvořené a zřízené s příslušnými oprávněními správce Data Lake Store **před** pomocí účtu úložiště pro clustery. Tato struktura není vytvořena automaticky při vytváření clusterů.
- Výše uvedený příklad doporučuje nastavení vlastnící skupina tohoto **/clustery/finance** jako **FINGRP** a jejímu **r-x** přístup k FINGRP do hierarchie celou složku spuštění z kořenového adresáře. Tím se zajistí, že členové FINGRP se můžete dostat strukturu složek, počínaje kořenový.
- V případech, kdy vytvořit clustery se v rámci různých objektů služby AAD **/clustery/finance**, sticky-bit (při nastavení na **finance** složky) zajišťuje, že vytvořeny složky podle jednoho instančního objektu nelze odstranit, druhý.
- Jakmile strukturu složek a oprávnění jsou na místě, procesu vytváření clusteru HDInsight vytvoří místo specifických pro cluster úložiště v rámci **/clusterů/finance/**. Například může být úložiště pro cluster s názvem fincluster01 **/clusters/finance/fincluster01**. Vlastnictví a oprávnění pro složky vytvoří cluster HDInsight je uveden v tabulce tady.

    |Složka  |Oprávnění  |Vlastnící uživatel  |Vlastnící skupina  | Jmenovaný uživatel | Oprávnění pro pojmenovaného uživatele | Pojmenované skupiny | Oprávnění s názvem skupiny |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Instanční objekt služby |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Doporučení pro úlohy vstupní a výstupní data

Doporučujeme vám, že vstupní data a úlohy a výstup z úlohy být uložena ve složce mimo **/clusterů**. Tím se zajistí, že i v případě, že uvolnit nějaké místo úložiště se odstraní složka specifická pro cluster, úlohu vstupy a výstupy jsou stále k dispozici pro budoucí použití. V takovém případě se ujistěte, že hierarchii složek pro ukládání úlohy vstupy a výstupy umožňuje odpovídající úroveň přístupu pro instanční objekt.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Omezit na clusterech sdílení jednoho účtu úložiště

Omezení počtu clusterů, které můžou sdílet jeden účet Data Lake Store, které závisí na úlohy se spouští tyto clustery. S příliš mnoha clusterech nebo velmi náročné úlohy na clusterech, které sdílejí účet úložiště může způsobit, že úložiště účtu příchozí a odchozí přenos do dojde.

## <a name="support-for-default-acls"></a>Podpora pro výchozí seznamy ACL

Při vytváření instančního objektu s přístupem s názvem uživatele (jak je znázorněno v předchozí tabulce), doporučujeme **není** přidání s názvem – uživatel se výchozího seznamu ACL. Zřizování přístupu s názvem uživatelů pomocí výchozí seznamy ACL výsledky v přiřazení 770 oprávnění pro vlastnícího uživatele, vlastnící skupinu a další. Když tuto výchozí hodnotu 770 okamžitě nepřebírá oprávnění ze vlastnícího uživatele (7) nebo vlastnící skupiny (7), trvá daleko všechna oprávnění pro ostatní uživatele (0). Výsledkem je známý problém s jeden konkrétní případ použití, který je podrobně popsána [– známé problémy a řešení](#known-issues-and-workarounds) oddílu.

## <a name="known-issues-and-workarounds"></a>Známé problémy a řešení

V této části najdete seznam známých problémů pro HDInsight pomocí Data Lake Store a jejich řešení.

### <a name="publicly-visible-localized-yarn-resources"></a>Veřejně viditelné lokalizovaných prostředků YARN

Při vytvoření nového účtu Azure Data Lake store je kořenový adresář automaticky zřízena sadu 770 bitů oprávnění přístupového seznamu ACL. Kořenové složky vlastnící uživatel je nastaven na uživatele, který vytvořil účet (Data Lake Store správce) a je vlastnící skupina nastavena na primární skupiny uživatele, který účet vytvořil. Žádný přístup neposkytujeme "ostatní".

Tato nastavení se ví, vliv na jeden konkrétní HDInsight případ použití zaznamenány [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Umožňují odesílání úloh může selhat s chybovou zprávou nějak takto:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Jak je uvedeno v JIRA YARN propojené dříve, při lokalizaci prostředky veřejné lokalizátora ověří, zda všechny požadované prostředky jsou skutečně veřejné jejich oprávnění ve vzdáleném systému souborů. Žádné LocalResource nevejde této podmínky je odmítnutých pro lokalizaci. Kontrola oprávnění, zahrnuje přístup pro čtení do souboru "ostatní". Tento scénář nefunguje out-of-the-box při hostování clustery HDInsight v Azure Data Lake, protože Azure Data Lake zakazuje veškerý přístup k "ostatní" na kořenové úrovni složky.

#### <a name="workaround"></a>Alternativní řešení
Sada čtení spouštěcích oprávnění k **ostatní** prostřednictvím hierarchie, například na **/**, **/clusterů** a   **/clustery/finance** jak je znázorněno v předchozí tabulce.

## <a name="see-also"></a>Další informace najdete v tématech

* [Rychlý start: Nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)


