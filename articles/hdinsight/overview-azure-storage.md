---
title: Přehled Azure Storage v HDInsight
description: Přehled Azure Storage v HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 419b4939c19ee03651a406db44244897e17087f2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865464"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Přehled Azure Storage v HDInsight

Azure Storage je robustní řešení úložiště pro obecné účely, které se bezproblémově integruje se službou HDInsight. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní HDFS může úplná sada komponent ve službě HDInsight pracovat přímo se strukturovanými nebo nestrukturovanými daty uloženými jako objekty blob.

Pro vaše výchozí úložiště clusteru a firemní data doporučujeme používat samostatné kontejnery úložiště. Oddělení IT izoluje protokoly HDInsight a dočasné soubory z vlastních obchodních dat. Doporučujeme také odstranit výchozí kontejner objektů blob, který obsahuje protokoly aplikací a systému, a to po každém použití ke snížení nákladů na úložiště. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Pokud se rozhodnete zabezpečit svůj účet úložiště s omezeními **bran firewall a virtuální sítě** u **vybraných sítí**, Nezapomeňte povolit výjimku **Povolit důvěryhodné služby společnosti Microsoft...**. Výjimkou je to, že HDInsight má přístup k vašemu účtu úložiště.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram nabízí abstraktní zobrazení architektury HDInsight Azure Storage:

:::image type="content" source="./media/overview-azure-storage/storage-architecture.png" alt-text="Architektura HDInsight Storage" border="false":::

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

`hdfs://<namenodehost>/<path>`

Prostřednictvím HDInsight můžete také přistupovat k datům v Azure Storage. Syntaxe je následující:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

Při použití účtu Azure Storage s clustery HDInsight Vezměte v úvahu následující principy:

* **Kontejnery v účtech úložiště, které jsou připojeny ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou během vytváření přidružené  ke clusteru, máte plný přístup k objektům blob v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které nejsou připojené ke clusteru:** K objektům blob v kontejnerech máte oprávnění jen pro čtení.
  
  > [!NOTE]
  > Veřejné kontejnery umožňují získat seznam všech objektů blob, které jsou v daném kontejneru k dispozici, a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [Správa anonymního přístupu pro čtení do kontejnerů a objektů BLOB](../storage/blobs/anonymous-read-access-configure.md).

* **Privátní kontejnery v účtech úložiště, které nejsou připojené ke clusteru:** Nemůžete získat přístup k objektům blob v kontejnerech, pokud nedefinujete účet úložiště při odeslání úloh WebHCat.

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. Ve výchozím nastavení používá HDInsight účty úložiště definované v souboru core-site.xml. Toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Několik úloh WebHCat, včetně Apache Hive. MapReduce, Apache Hadoop streamování a Apache prasete, obsahují popis účtů úložiště a metadat. (Tento aspekt je aktuálně true pro prasete s účty úložiště, ale ne pro metadata.) Další informace najdete v tématu [použití clusteru HDInsight s alternativním účtem úložiště a metaúložiště](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů BLOB ukládají data jako páry klíč/hodnota a nemají žádnou hierarchii adresářů. Název klíče ale může obsahovat lomítko (/), aby se zobrazil jako při uložení souboru do adresářové struktury. Klíč objektu BLOB může být například `input/log1.txt` . Neexistuje žádný skutečný `input` adresář, ale z důvodu znaku lomítka v názvu klíče vypadá klíč jako cesta k souboru.

## <a name="benefits-of-azure-storage"></a>Výhody služby Azure Storage

Výpočetní clustery a prostředky úložiště, které nejsou společně umístěny, mají předpokládané náklady na výkon. Tyto náklady jsou zmírněny způsobem, kterým se výpočetní clustery vytvářejí blízko prostředků účtu úložiště v oblasti Azure. V této oblasti můžou výpočetní uzly efektivně přistupovat k datům přes vysokorychlostní síť v rámci Azure Storage.

Když ukládáte data v Azure Storage místo HDFS, získáte několik výhod:

* **Opakované použití dat a sdílení:** data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data v Azure Storage naproti tomu mají k dispozici prostřednictvím rozhraní API HDFS nebo rozhraní REST API služby Blob Storage. Z důvodu tohoto uspořádání lze k vytváření a využívání dat použít větší sadu aplikací (včetně dalších clusterů HDInsight) a nástroje.

* **Archivace dat:** Když jsou data uložená v Azure Storage, clustery HDInsight používané pro výpočty se dají bezpečně odstranit, aniž by došlo ke ztrátě uživatelských dat.

* **Náklady na úložiště dat:** Ukládání dat v systému souborů DFS je z dlouhodobého hlediska dražší než ukládání dat v Azure Storage. Vzhledem k tomu, že náklady na výpočetní cluster jsou vyšší než náklady na Azure Storage. I když se data pro každou generaci výpočetních clusterů nemusí znovu načíst, ukládají se i náklady na načítání dat.

* **Elastické škálování:** I když HDFS poskytuje škálovaný systém souborů, škála se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna měřítka může být složitější než možnosti elastického škálování, které se automaticky zobrazí v Azure Storage.

* **Geografická replikace:** Vaše Azure Storage může být geograficky replikované. I když geografická replikace poskytuje geografické obnovení a redundanci dat, převzetí služeb při selhání geograficky replikovaným umístěním má vážně vliv na váš výkon a může to mít za následek další náklady. Proto vyberte geografickou replikaci obezřetně a jenom v případě, že hodnota dat odůvodňuje dodatečné náklady.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které byste nemuseli ukládat v Azure Storage. V takovém případě se můžete rozhodnout ukládat data do místního HDFS. HDInsight používá DFS pro několik těchto mezilehlých výsledků v úlohách podregistru a dalších procesech.

> [!NOTE]  
> Většina příkazů HDFS (například,, `ls` `copyFromLocal` a `mkdir` ) pracuje podle očekávání v Azure Storage. Pouze příkazy, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), jako například `fschk` a `dfsadmin` , zobrazují v Azure Storage různé chování.

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
* [Přehled služby Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
