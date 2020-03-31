---
title: Regionální zotavení po havárii pro Azure Databricks
description: Tento článek popisuje přístup k řešení zotavení po havárii v Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161932"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionální zotavení po havárii pro clustery Azure Databricks

Tento článek popisuje architekturu zotavení po havárii užitečnou pro clustery Azure Databricks a kroky k dosažení tohoto návrhu.

## <a name="azure-databricks-architecture"></a>Architektura Azure Databricks

Na vysoké úrovni při vytváření pracovního prostoru Azure Databricks z portálu Azure se [spravované zařízení](../azure-resource-manager/managed-applications/overview.md) nasadí jako prostředek Azure ve vašem předplatném ve zvolené oblasti Azure (například západní USA). Toto zařízení se nasadí ve [virtuální síti Azure](../virtual-network/virtual-networks-overview.md) se [skupinou zabezpečení sítě](../virtual-network/manage-network-security-group.md) a účtem azure storage, který je k dispozici ve vašem předplatném. Virtuální síť poskytuje zabezpečení na úrovni obvodu pracovního prostoru Databricks a je chráněna skupinou zabezpečení sítě. V rámci pracovního prostoru můžete vytvořit clustery Databricks poskytnutím typu pracovního a ovladače typu virtuálního zařízení a databricks runtime verze. Trvalá data jsou dostupná ve vašem účtu úložiště, což můžou být Azure Blob Storage nebo Azure Data Lake Storage. Po vytvoření clusteru můžete spouštět úlohy prostřednictvím poznámkových bloků, rozhraní REST API, koncových bodů ODBC/JDBC jejich připojením k určitému clusteru.

Rovina řízení Databricks spravuje a monitoruje prostředí pracovního prostoru Databricks. Všechny operace správy, jako je například vytvoření clusteru, budou zahájeny z roviny ovládacího prvku. Všechna metadata, jako jsou naplánované úlohy, se ukládají v databázi Azure s geografickou replikací pro odolnost proti chybám.

![Architektura Databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Jednou z výhod této architektury je, že uživatelé mohou připojit Azure Databricks k libovolnému prostředku úložiště ve svém účtu. Klíčovou výhodou je, že výpočetní prostředky (Azure Databricks) a úložiště lze škálovat nezávisle na sobě.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Jak vytvořit regionální topologii zotavení po havárii

Jak jste si všimli v popisu předchozí architektury, existuje řada komponent používaných pro kanál velkých objemů dat s Azure Databricks: Azure Storage, Azure Database a další zdroje dat. Azure Databricks je *výpočetní prostředky* pro kanál velkých objemů dat. Je *dočasný* charakter, což znamená, že zatímco vaše data jsou stále k dispozici ve službě Azure Storage, *výpočetní (Cluster* Azure Databricks) může být ukončena, takže nemusíte platit za výpočetní prostředky, když je nepotřebujete. *Výpočetní prostředky* (Azure Databricks) a zdroje úložiště musí být ve stejné oblasti, aby úlohy nezůraly s vysokou latencí.  

Chcete-li vytvořit vlastní regionální topologii zotavení po havárii, postupujte podle následujících požadavků:

   1. Zřizování více pracovních prostorů Azure Databricks v samostatných oblastech Azure. Můžete například vytvořit primární pracovní prostor Azure Databricks v east US2. Vytvořte sekundární pracovní prostor Azure Databricks pro zotavení po havárii v samostatné oblasti, jako je například Západní USA.

   2. Použijte [geograficky redundantní úložiště](../storage/common/storage-redundancy.md). Data přidružená k Datům Azure se ve výchozím nastavení ukládají ve službě Azure Storage. Výsledky úloh Databricks jsou také uloženy v azure blob storage, takže zpracovaná data jsou trvalá a zůstane vysoce dostupná po ukončení clusteru. Jako cluster storage a Databricks jsou umístěny společně, musíte použít geograficky redundantní úložiště, aby data přístupv sekundární oblasti, pokud primární oblast již není přístupná.

   3. Po vytvoření sekundární oblasti je nutné migrovat uživatele, složky uživatelů, poznámkové bloky, konfiguraci clusteru, konfiguraci úloh, knihovny, úložiště, init skripty a překonfigurovat řízení přístupu. Další podrobnosti jsou popsány v následující části.

## <a name="detailed-migration-steps"></a>Podrobný postup migrace

1. **Nastavení rozhraní příkazového řádku Databricks v počítači**

   Tento článek ukazuje řadu příkladů kódu, které používají rozhraní příkazového řádku pro většinu automatizovaných kroků, protože se jedná o snadno uživatelské obálky přes rozhraní API REST Azure Databricks.

   Před provedením jakýchkoli kroků migrace nainstalujte databricks-cli do stolního počítače nebo virtuálního počítače, ve kterém chcete pracovat. Další informace naleznete [v tématu Instalace funkce CLI Databricks](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Očekává se, že všechny skripty pythonu uvedené v tomto článku budou fungovat s Pythonem 2.7+ < 3.x.

2. **Konfigurace dvou profilů.**

   Nakonfigurujte jeden pro primární pracovní prostor a druhý pro sekundární pracovní prostor:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Bloky kódu v tomto článku přepínají mezi profily v každém následujícím kroku pomocí odpovídajícího příkazu pracovního prostoru. Ujistěte se, že názvy profilů, které vytvoříte, jsou nahrazeny do každého bloku kódu.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   V případě potřeby můžete ručně přepnout na příkazovém řádku:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrace uživatelů služby Azure Active Directory**

   Ručně přidejte stejné uživatele služby Azure Active Directory do sekundárního pracovního prostoru, které existují v primárním pracovním prostoru.

4. **Migrace uživatelských složek a poznámkových bloků**

   Následující kód pythonu použijte k migraci uživatelských prostředí v izolovaném prostoru, která zahrnují vnořenou strukturu složek a poznámkové bloky na uživatele.

   > [!NOTE]
   > Knihovny nejsou zkopírovány v tomto kroku, jako základní rozhraní API nepodporuje ty.

   Zkopírujte a uložte následující skript pythonu do souboru a spusťte jej v příkazovém řádku Databricks. Například, `python scriptname.py`.

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

   Po migraci poznámkových bloků můžete volitelně migrovat konfigurace clusteru do nového pracovního prostoru. Je to téměř plně automatizovaný krok pomocí databricks-cli, pokud byste chtěli udělat selektivní cluster config migrace spíše než pro všechny.

   > [!NOTE]
   > Bohužel neexistuje žádný koncový bod konfigurace clusteru vytvořit a tento skript se pokusí vytvořit každý cluster hned. Pokud není k dispozici dostatek jader v rámci vašeho předplatného, může dojít k selhání vytvoření clusteru. Selhání lze ignorovat, tak dlouho, dokud je konfigurace úspěšně přenesena.

   Následující skript za předpokladu, vytiskne mapování ze starého na nové ID clusteru, které by mohly být použity pro migraci úloh později (pro úlohy, které jsou nakonfigurovány pro použití existujících clusterů).

   Zkopírujte a uložte následující skript pythonu do souboru a spusťte jej v příkazovém řádku Databricks. Například, `python scriptname.py`.

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

   Pokud jste v předchozím kroku migrovali konfigurace clusteru, můžete se rozhodnout pro migraci konfigurací úloh do nového pracovního prostoru. Jedná se o plně automatizovaný krok pomocí databricks-cli, pokud byste chtěli dělat selektivní práci config migrace spíše než dělat to pro všechny úlohy.

   > [!NOTE]
   > Konfigurace pro naplánovanou úlohu obsahuje také informace o plánu, takže ve výchozím nastavení začne fungovat podle nakonfigurovaného časování, jakmile se migruje. Proto následující blok kódu odebere všechny informace o plánu během migrace (aby se zabránilo duplicitní spuštění přes staré a nové pracovní prostory). Jakmile budete připraveni k přeškrtnutí, nakonfigurujte plány pro takové úlohy.

   Konfigurace úlohy vyžaduje nastavení pro nový nebo existující cluster. Pokud používáte existující cluster, níže uvedený skript /kód se pokusí nahradit staré ID clusteru novým ID clusteru.

   Zkopírujte a uložte následující skript pythonu do souboru. Nahraďte `old_cluster_id` hodnotu pro a `new_cluster_id`, výstupem z migrace clusteru provedeného v předchozím kroku. Spusťte jej v příkazovém řádku databricks-cli, `python scriptname.py`například .

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

   V současné době neexistuje žádný jednoduchý způsob migrace knihoven z jednoho pracovního prostoru do druhého. Místo toho přeinstalujte tyto knihovny do nového pracovního prostoru ručně. Je možné automatizovat pomocí kombinace [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) nahrát vlastní knihovny do pracovního prostoru a [knihovny CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrace úložiště objektů blob Azure a připojení Azure Data Lake Storage**

   Ručně znovu připojte všechny [azure blob úložiště](/azure/databricks/data/data-sources/azure/azure-storage) a [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) přípojné body pomocí řešení založené na poznámkových bloutech. Prostředky úložiště by byly připojeny v primárním pracovním prostoru a to se musí opakovat v sekundárním pracovním prostoru. Neexistuje žádné externí rozhraní API pro připojení.

9. **Migrace skriptů init clusteru**

   Všechny skripty inicializace clusteru lze migrovat ze starého do nového pracovního prostoru pomocí [cli DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Nejprve zkopírujte potřebné `dbfs:/dat abricks/init/..` skripty z místní plochy nebo virtuálního počítače. Dále zkopírujte tyto skripty do nového pracovního prostoru na stejné cestě.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Ručně překonfigurujte a znovu použijte řízení přístupu.**

    Pokud je váš stávající primární pracovní prostor nakonfigurován tak, aby používal úroveň Premium (SKU), je pravděpodobné, že používáte také [funkci Řízení přístupu](/azure/databricks/administration-guide/access-control/index).

    Pokud používáte funkci Řízení přístupu, ručně znovu použít řízení přístupu k prostředkům (poznámkové bloky, clustery, úlohy, tabulky).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Zotavení po havárii pro váš ekosystém Azure

Pokud používáte jiné služby Azure, nezapomeňte implementovat osvědčené postupy pro zotavení po havárii pro tyto služby. Pokud se například rozhodnete použít externí instanci metaúložiště Hive, měli byste zvážit zotavení po havárii pro [Azure SQL Server](../sql-database/sql-database-disaster-recovery.md), Azure [HDInsight](../hdinsight/hdinsight-high-availability-linux.md)a/nebo [Azure Database for MySQL](../mysql/concepts-business-continuity.md). Obecné informace o zotavení po havárii najdete [v tématu Zotavení po havárii pro aplikace Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci k Azure Databricks](index.yml).