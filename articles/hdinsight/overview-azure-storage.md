---
title: Přehled úložiště Azure v HDInsightu
description: Přehled Azure Storage ve HDInsightu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873378"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Přehled úložiště Azure v HDInsightu

Azure Storage je robustní řešení úložiště pro obecné účely, které se bezproblémově integruje s HDInsightem. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní HDFS může úplná sada součástí v HDInsight pracovat přímo na strukturovaných nebo nestrukturovaných datech uložených jako objekty BLOB.

Doporučujeme používat samostatné kontejnery úložiště pro výchozí úložiště clusteru a obchodní data. Oddělení je izolovat protokoly HDInsight a dočasné soubory z vlastních obchodních dat. Doporučujeme také po každém použití smazat výchozí kontejner objektů blob, který obsahuje protokoly aplikací a systému, aby se snížily náklady na úložiště. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Pokud se rozhodnete zabezpečit účet úložiště pomocí **omezení brány firewall a virtuálních sítí** ve vybraných **sítích**, nezapomeňte povolit výjimku **Povolit důvěryhodné služby společnosti Microsoft...**. Výjimkou je, aby HDInsight měl přístup k vašemu účtu úložiště.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram poskytuje abstraktní zobrazení architektury HDInsight služby Azure Storage:

!["Architektura úložiště HDInsight"](./media/overview-azure-storage/storage-architecture.png "Architektura úložiště HDInsight")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Prostřednictvím HDInsightu můžete také přistupovat k datům ve službě Azure Storage. Syntaxe je následující:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Při používání účtu Azure Storage s clustery HDInsight zvažte následující principy:

* **Kontejnery v účtech úložiště, které jsou připojeny ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou během vytváření přidružené  ke clusteru, máte plný přístup k objektům blob v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které nejsou připojené ke clusteru:** Máte oprávnění jen pro čtení k objektům BLOB v kontejnerech.
  
  > [!NOTE]  
  > Veřejné kontejnery umožňují získat seznam všech objektů BLOB, které jsou k dispozici v tomto kontejneru a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace naleznete v [tématu Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](../storage/blobs/storage-manage-access-to-resources.md).

* **Soukromé kontejnery v účtech úložiště, které nejsou připojené ke clusteru:** K objektům BLOB v kontejnerech nelze přistupovat, pokud nedefinujete účet úložiště při odesílání úloh WebHCat.

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. Ve výchozím nastavení používá HDInsight účty úložiště definované v souboru core-site.xml. Toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Několik úloh WebHCat, včetně Apache Hive. A MapReduce, Apache Hadoop streaming a Apache Pig nesou popis účtů úložiště a metadat. (Tento aspekt v současné době platí pro Pig s účty úložiště, ale ne pro metadata.) Další informace naleznete [v tématu Použití clusteru HDInsight s alternativními účty úložiště a metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů blob ukládají data jako dvojice klíč/hodnota a nemají žádnou hierarchii adresářů. Název klíče však může obsahovat znak lomítka ( / ), aby vypadal, jako by byl soubor uložen v adresářové struktuře. Klíč objektu blob může být `input/log1.txt`například . Neexistuje `input` žádný skutečný adresář, ale kvůli znaku lomítka v názvu klíče klíč vypadá jako cesta k souboru.

## <a name="benefits-of-azure-storage"></a>Výhody služby Azure Storage

Výpočetní clustery a prostředky úložiště, které nejsou společně přiděleny, mají předpokládané náklady na výkon. Tyto náklady jsou zmírněny způsobem, jakým se vytvoří výpočetní clustery v blízkosti prostředků účtu úložiště v oblasti Azure. V této oblasti výpočetní uzly můžete efektivně přistupovat k datům přes vysokorychlostní sítě uvnitř Azure Storage.

Když ukládáte data ve službě Azure Storage místo HDFS, získáte několik výhod:

* **Opakované použití dat a sdílení:** data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. K datům ve službě Azure Storage lze naopak přistupovat prostřednictvím rozhraní API HDFS nebo rozhraní REST rozhraní API úložiště objektů blob. Z důvodu tohoto uspořádání větší sadu aplikací (včetně jiných clusterů HDInsight) a nástroje lze použít k výrobě a využití dat.

* **Archivace dat:** Když jsou data uložená ve službě Azure Storage, clustery HDInsight používané pro výpočty lze bezpečně odstranit bez ztráty uživatelských dat.

* **Náklady na ukládání dat:** Dlouhodobé ukládání dat v dfs je nákladnější než ukládání dat ve službě Azure Storage. Protože náklady na výpočetní cluster jsou vyšší než náklady na Azure Storage. Také vzhledem k tomu, že data nemusí být znovu načtena pro každou generaci výpočetního clusteru, šetříte také náklady na načítání dat.

* **Elastické škálování:** I když HDFS poskytuje škálovaný systém souborů, škála se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna škálování může být složitější než možnosti elastického škálování, které získáte automaticky ve službě Azure Storage.

* **Geografická replikace:** Vaše Azure Storage může být geograficky replikované. Přestože geografická replikace poskytuje geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může vám vzniknout další náklady. Takže zvolte geografickou replikaci opatrně a pouze v případě, že hodnota dat odůvodňuje dodatečné náklady.

Některé úlohy a balíčky MapReduce mohou vytvářet průběžné výsledky, které byste nechtěli ukládat ve službě Azure Storage. V takovém případě můžete zvolit uložení dat v místním HDFS. HDInsight používá dfs pro několik z těchto zprostředkujících výsledků v úlohách Hive a dalších procesech.

> [!NOTE]  
> Většina příkazů HDFS (například `ls`, `copyFromLocal`, a `mkdir`) pracovat podle očekávání v Azure Storage. Pouze příkazy, které jsou specifické pro nativní implementaci HDFS (která `fschk` `dfsadmin`se označuje jako DFS), jako jsou například a , zobrazit různé chování ve službě Azure Storage.

## <a name="next-steps"></a>Další kroky

* [Úvod do úložiště datových jezer Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
* [Přehled úložiště datového jezera Azure1 – přehled](./overview-data-lake-storage-gen1.md)