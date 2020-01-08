---
title: Místní zotavení po havárii pro Azure Databricks
description: Tento článek popisuje přístup k zotavení po havárii v Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 800b51c8f900d2ea99900ea147b33010452348f5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639867"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Místní zotavení po havárii pro clustery Azure Databricks

Tento článek popisuje architekturu zotavení po havárii, která je užitečná pro Azure Databricks clustery, a postup pro provedení tohoto návrhu.

## <a name="azure-databricks-architecture"></a>Architektura Azure Databricks

Při vytváření pracovního prostoru Azure Databricks z Azure Portal se [spravované zařízení](../azure-resource-manager/managed-applications/overview.md) nasadí jako prostředek Azure v rámci vašeho předplatného ve zvolené oblasti Azure (například západní USA). Toto zařízení je nasazené v [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) se [skupinou zabezpečení sítě](../virtual-network/manage-network-security-group.md) a účtem služby Azure Storage, který je ve vašem předplatném dostupný. Virtuální síť zajišťuje zabezpečení na úrovni hraničních prostředí pro pracovní prostor datacihly a je chráněn prostřednictvím skupiny zabezpečení sítě. V pracovním prostoru můžete vytvořit clustery datacihly poskytnutím typu VM Worker a Driver a verze modulu runtime datacihly. Trvalá data jsou dostupná ve vašem účtu úložiště, což může být Azure Blob Storage nebo Azure Data Lake Storage. Po vytvoření clusteru můžete úlohy spouštět pomocí poznámkových bloků, rozhraní REST API, koncových bodů ODBC/JDBC jejich připojením ke konkrétnímu clusteru.

Rovina ovládacího prvku datacihly spravuje a monitoruje prostředí pracovního prostoru datacihly. Všechny operace správy, jako je vytvoření clusteru, se iniciují z plochy ovládacího prvku. Všechna metadata, například naplánované úlohy, jsou uložena v databázi Azure s geografickou replikací pro odolnost proti chybám.

![Architektura datacihly](media/howto-regional-disaster-recovery/databricks-architecture.png)

Jednou z výhod této architektury je, že se uživatelé můžou připojit Azure Databricks k jakémukoli prostředku úložiště v jejich účtu. Klíčovou výhodou je, že výpočetní výkon (Azure Databricks) i úložiště je možné škálovat nezávisle na sobě.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Vytvoření místní topologie zotavení po havárii

Jak vidíte v popisu předchozí architektury, je k dispozici řada komponent pro kanál velkých objemů dat s Azure Databricks: Azure Storage, Azure Database a dalšími zdroji dat. Azure Databricks je *výpočetní* prostředí pro kanál s velkými objemy dat. To znamená, že i když jsou vaše data stále k dispozici v Azure Storage, *výpočetní* prostředí (Azure Databricks cluster *) se může* ukončit, takže nemusíte platit za výpočetní výkon, když ho nepotřebujete. *Výpočetní* prostředí (Azure Databricks) a zdroje úložiště musí být ve stejné oblasti, aby úlohy nepracovaly s vysokou latencí.  

Pokud chcete vytvořit svou vlastní místní topologii zotavení po havárii, postupujte podle těchto požadavků:

   1. Zřizování více Azure Databricks pracovních prostorů v samostatných oblastech Azure. Můžete například vytvořit primární pracovní prostor Azure Databricks ve službě východní USA 2. Vytvořte sekundární pracovní prostor Azure Databricks obnovení po havárii v samostatné oblasti, například Západní USA.

   2. Použijte [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Data, která jsou Azure Databricks přidružená, se ve výchozím nastavení ukládají v Azure Storage. Výsledky z úloh datacihly se také ukládají do Azure Blob Storage, aby zpracovaná data byla trvalá a po ukončení clusteru zůstala vysoce dostupná. Vzhledem k tomu, že je cluster úložiště a datacihly společně umístěn, je nutné použít geograficky redundantní úložiště, aby k datům bylo možné přistupovat v sekundární oblasti, pokud již primární oblast nebude přístupná.

   3. Po vytvoření sekundární oblasti je nutné migrovat uživatele, složky uživatelů, poznámkové bloky, konfiguraci clusteru, konfiguraci úloh, knihovny, úložiště, skripty init a znovu nakonfigurovat řízení přístupu. Další podrobnosti jsou popsaný v následující části.

## <a name="detailed-migration-steps"></a>Podrobný postup migrace

1. **Nastavení rozhraní příkazového řádku datacihly v počítači**

   Tento článek ukazuje několik příkladů kódu, které používají rozhraní příkazového řádku pro většinu automatizovaných kroků, protože se jedná o uživatelskou obálku, která je snadno Azure Databricks REST API.

   Před provedením jakýchkoli kroků migrace nainstalujte datacihly-CLI do stolního počítače nebo virtuálního počítače, na kterém plánujete práci. Další informace najdete v tématu Instalace rozhraní příkazového [řádku (CLI) datacihly](/azure/databricks/dev-tools/databricks-cli) .

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > U všech skriptů Pythonu, které jsou součástí tohoto článku, se očekává, že budou fungovat s Pythonem 2.7 + < 3. x.

2. **Nakonfigurujte dva profily.**

   Nakonfigurujte jeden pro primární pracovní prostor a druhý pro sekundární pracovní prostor:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Bloky kódu v tomto článku přepínají mezi profily v každém dalším kroku pomocí příkazu odpovídajícím pracovnímu prostoru. Ujistěte se, že názvy profilů, které vytvoříte, jsou nahrazeny jednotlivými bloky kódu.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   V případě potřeby můžete ručně přepnout na příkazovém řádku:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrace Azure Active Directory uživatelů**

   Ručně přidejte stejné Azure Active Directory uživatele do sekundárního pracovního prostoru, který existuje v primárním pracovním prostoru.

4. **Migrace složek a poznámkových bloků uživatele**

   Pomocí následujícího kódu Pythonu migrujte uživatelská prostředí v izolovaném prostoru, která zahrnují strukturu vnořené složky a poznámkové bloky na uživatele.

   > [!NOTE]
   > Knihovny se v tomto kroku nekopírují, protože příslušné rozhraní API je nepodporuje.

   Zkopírujte a uložte následující skript Pythonu do souboru a spusťte ho v příkazovém řádku datacihly. Například, `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Migrace konfigurací clusteru**

   Po migraci poznámkových bloků můžete volitelně migrovat konfigurace clusteru do nového pracovního prostoru. Je skoro plně automatizovaný krok s použitím datacihly – CLI, pokud nechcete, aby se provede selektivní migrace konfigurace clusteru, ne vše.

   > [!NOTE]
   > Bohužel není k dispozici žádný koncový bod konfigurace clusteru a tento skript se pokusí vytvořit každý cluster hned hned. Pokud ve vašem předplatném není k dispozici dostatek jader, vytvoření clusteru může selhat. Selhání se dá ignorovat, pokud se konfigurace úspěšně přenáší.

   Následující skript poskytuje tisk mapování ze starých na nová ID clusteru, která se dají použít k migraci úlohy později (pro úlohy, které jsou nakonfigurované pro použití existujících clusterů).

   Zkopírujte a uložte následující skript Pythonu do souboru a spusťte ho v příkazovém řádku datacihly. Například, `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **Migrace konfigurace úloh**

   Pokud jste v předchozím kroku migrovali konfigurace clusteru, můžete se rozhodnout pro migraci konfigurací úloh do nového pracovního prostoru. Jedná se o plně automatizovaný krok pomocí datacihly – CLI, pokud nechcete provádět migraci selektivních konfigurací úloh místo toho, aby se to provádělo pro všechny úlohy.

   > [!NOTE]
   > Konfigurace pro naplánovanou úlohu obsahuje taky informace o plánu, takže ve výchozím nastavení začnou fungovat podle nakonfigurovaného časování, jakmile se migruje. Následující blok kódu proto v průběhu migrace odebere všechny informace o plánu (aby nedocházelo k duplicitním běhům v rámci starých a nových pracovních prostorů). Až budete připraveni na přímou migraci, nakonfigurujte plány pro tyto úlohy.

   Konfigurace úlohy vyžaduje nastavení pro nový nebo existující cluster. Při použití existujícího clusteru se skript/Code níže pokusí nahradit staré ID clusteru novým ID clusteru.

   Zkopírujte a uložte následující skript Pythonu do souboru. Nahraďte hodnotu pro `old_cluster_id` a `new_cluster_id`, s výstupem z migrace clusteru provedeným v předchozím kroku. Spusťte ji v příkazu datacihly-CLI, například `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Migrace knihoven**

   Pro migraci knihoven z jednoho pracovního prostoru do jiného teď neexistuje žádný snadný způsob. Místo toho tyto knihovny přeinstalujte do nového pracovního prostoru ručně. Pro nahrání vlastních knihoven do pracovního prostoru a [knihoven CLI](https://github.com/databricks/databricks-cli#libraries-cli)je možné automatizovat použití kombinace [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) .

8. **Migrace služby Azure Blob Storage a Azure Data Lake Storage připojení**

   Ručně znovu připojte všechny přípojné body služby [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage) a [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) pomocí řešení založeného na poznámkovém bloku. Prostředky úložiště by byly připojené k primárnímu pracovnímu prostoru a musí se opakovat v sekundárním pracovním prostoru. Pro připojení není k dispozici žádné externí rozhraní API.

9. **Migrace inicializačních skriptů clusteru**

   Všechny inicializační skripty clusteru je možné migrovat ze starých na nový pracovní prostor pomocí rozhraní příkazového [řádku DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Nejdřív Zkopírujte potřebné skripty z `dbfs:/dat abricks/init/..` do svého místního počítače nebo virtuálního počítače. Potom tyto skripty zkopírujte do nového pracovního prostoru na stejné cestě.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Ručně znovu nakonfigurujte a znovu použijte řízení přístupu.**

    Pokud je váš stávající primární pracovní prostor nakonfigurovaný tak, aby používal úroveň Premium (SKU), je možné, že používáte také [funkci Access Control](/azure/databricks/administration-guide/access-control/index).

    Pokud použijete funkci Access Control, ručně znovu použijte řízení přístupu k prostředkům (poznámkovým blokům, clusterům, úlohám, tabulkám).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Zotavení po havárii pro váš ekosystém Azure

Pokud používáte další služby Azure, ujistěte se, že implementujete osvědčené postupy zotavení po havárii pro tyto služby. Pokud se například rozhodnete použít externí instanci metastore Hive, měli byste zvážit zotavení po havárii pro [azure SQL Server](../sql-database/sql-database-disaster-recovery.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)a/nebo [Azure Database for MySQL](../mysql/concepts-business-continuity.md). Obecné informace o zotavení po havárii najdete v tématu [zotavení po havárii pro aplikace Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci Azure Databricks](index.yml).