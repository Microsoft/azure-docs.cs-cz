---
title: Místní zotavení po havárii pro Azure Databricks
description: Tento článek popisuje přístup až po provádění zotavení po havárii v Azure Databricks.
services: azure-databricks
author: jasonwhowell
ms.author: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2018
ms.openlocfilehash: 46cb9eaee1d56a96801065ae0a349aa0b1be85e0
ms.sourcegitcommit: baed5a8884cb998138787a6ecfff46de07b8473d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43115237"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Místní zotavení po havárii pro clustery Azure Databricks

Tento článek popisuje architekturu zotavení po havárii, která je pro clustery Azure Databricks a kroky k provedení tohoto návrhu.

## <a name="control-plan-architecture"></a>Architektura ovládacího prvku plán

Na vysoké úrovni, když vytvoříte pracovní prostor Azure Databricks na portálu Azure [spravovaných zařízení](../managed-applications/overview.md) nasazený jako prostředek Azure v rámci vašeho předplatného, a to v oblasti Azure (například USA – západ) vyberete. Toto zařízení je nasazena v [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) s [skupinu zabezpečení sítě](../virtual-network/manage-network-security-group.md) a účet služby Azure Storage k dispozici v rámci vašeho předplatného. Virtuální síť poskytuje úroveň zabezpečení perimetru do pracovního prostoru Databricks a je chráněný přes skupinu zabezpečení sítě. V pracovním prostoru můžete vytvořit Databricks clusterů zadáním pracovních procesů a ovladačů typu virtuálního počítače a modulem runtime Databricks verze. Trvalých dat je k dispozici ve vašem účtu úložiště, který může být Azure Blob Storage nebo Azure Data Lake Store. Jakmile je cluster vytvořen, můžete spustit úlohy prostřednictvím koncových bodů rozhraní ODBC/JDBC poznámkových bloků, rozhraní REST API, připojením na konkrétním clusteru.

Rovina řízení Databricks spravuje a monitoruje prostředí pracovního prostoru Databricks. Všechny operace správy, které, jako je vytvoření clusteru se iniciovalo v rovině řízení. Všechna metadata, jako je například naplánované úlohy, je uložena v databázi Azure s geografické replikace pro odolnost proti chybám.

![Architektura rovina řízení Databricks](media/howto-regional-disaster-recovery/databricks-control-plane.png)

Jednou z výhod této architektury je, se můžou uživatelé připojit k jakémukoli prostředku úložiště do svého účtu Azure Databricks. Klíčovou výhodou je, že oba compute (Azure Databricks) a úložiště je možné škálovat nezávisle na sobě.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Postup vytvoření topologie zotavení po havárii místních

Jak si všimnete, že v předchozím popisu architektury, existují určité komponenty pro velké objemy dat kanál s Azure Databricks: Azure Storage, Azure Database a další zdroje dat. Azure Databricks je *compute* pro velké objemy dat profilace. Je *dočasné* ze své podstaty, to znamená, že zatímco vaše data jsou stále k dispozici ve službě Azure Storage *compute* (Azure Databricks pro cluster) lze ukončit, takže nemusíte platit za compute, kdy jste ho nepotřebujete. *Compute* (Azure Databricks) a úložiště zdroje musí být ve stejné oblasti tak, aby úlohy není docházet k vysoké latenci.  

Pokud chcete vytvořit vlastní regionálního obnovení topologie, postupujte podle těchto požadavků:

   1. Můžete zřídíte několik pracovních prostorů Azure Databricks v oblastech Azure. Například vytvořte primární pracovní prostor Azure Databricks ve východní USA 2. Vytvoření pracovního prostoru Azure Databricks sekundární zotavení po havárii v samostatné oblasti, třeba západní USA.

   2. Použití [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Data, která přidružené Azure Databricks je uložen ve výchozím nastavení ve službě Azure Storage. Výsledky z úloh Databricks také ukládají ve službě Azure Blob Storage tak, aby zpracovaná data byla odolná a zůstane po ukončení clusteru s vysokou dostupností. Jak clusteru úložiště a Databricks jsou umístěné, je nutné použít geograficky redundantní úložiště tak, aby data je přístupná v sekundární oblasti, pokud primární oblast už není dostupný.

   3. Jakmile se vytvoří sekundární oblasti, musí migrovat na uživatele, uživatelské složky, poznámkové bloky, konfigurace clusteru, úlohy konfigurace, knihovny, úložiště, init skripty a znovu nakonfigurovat řízení přístupu. Další podrobnosti jsou popsány v následující části.

## <a name="detailed-migration-steps"></a>Kroky migrace podrobné

1. **Nastavení rozhraní příkazového řádku Databricks ve vašem počítači**

   Tento článek popisuje některé příklady kódu, které používají rozhraní příkazového řádku pro většinu automatizované kroky, protože ho přes rozhraní REST API služby Azure Databricks je obálka-uživatel.

   Před provedením kroků migrace, nainstalujte na počítač nebo virtuální počítač, kde ji plánujete vykonávají práci databricks – rozhraní příkazového řádku. Další informace najdete v tématu [nainstalovat rozhraní příkazového řádku Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Python skripty v tomto článku se očekává, pracovat s Pythonem 2.7 + < 3.x.

2. **Konfigurovat dva profily.**

   Nakonfigurujte jeden pro primární pracovní prostor a jinou pro sekundární pracovní prostor:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Bloky kódu v tomto článku přepínání mezi profily v každého následného kroku pomocí příslušného příkazu pracovního prostoru. Ujistěte se, že názvy profilů, které vytvoříte jsou nahrazeny do každý blok kódu.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Můžete ručně nepřepnete na příkazovém řádku v případě potřeby:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrace uživatelů Azure Active Directory**

   Stejné uživatele Azure Active Directory ručně přidáte do sekundární pracovní prostor, který neexistuje v primárním pracovním prostorem.

4. **Migrace uživatelských složek a poznámkové bloky**

   Pomocí následujícího kódu python k migraci prostředí uživatele v izolovaném prostoru, mezi které patří vnořených složek strukturu a poznámkové bloky na uživatele.

   > [!NOTE]
   > Knihovny se zkopíruje v tomto kroku jako ty základního rozhraní API nepodporuje.

   Zkopírujte a uložte následující skript jazyka python do souboru a spustíte ho v příkazovém řádku Databricks. Například, `python scriptname.py`.

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

5. **Migrace konfigurace clusteru**

   Po migraci poznámkových bloků, můžete volitelně migrovat Clusterové konfigurace k novému pracovnímu prostoru. Je téměř plně automatizované krok pomocí databricks rozhraní příkazového řádku, pokud chcete provést migraci konfigurace selektivním clusteru, a nikoli pro všechny.

   > [!NOTE]
   > Bohužel neexistuje žádný koncový bod vytvořit cluster config a tento skript se pokusí vytvořit každý cluster okamžitě. Pokud nejsou k dispozici dostatek jader dostupných v rámci vašeho předplatného, vytvoření clusteru může selhat. Chyby můžete ignorovat, tak dlouho, dokud je konfigurace úspěšně převeden.

   Následující skript uvedený vytiskne mapování ze starého do nového clusteru ID, které by mohly být použity úlohy migrace později (pro úlohy, které jsou nakonfigurovány pro použití stávajících clusterů).

   Zkopírujte a uložte následující skript jazyka python do souboru a spustíte ho v příkazovém řádku Databricks. Například, `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **Konfigurace úlohy migrace**

   Pokud jste migrovali konfigurace clusteru v předchozím kroku, můžete se rozhodnout konfigurací úlohy migrace na nový pracovní prostor. Je plně automatizované kroku pomocí – rozhraní příkazového řádku databricks, pokud chcete provést migraci konfigurace selektivním úlohy, spíše než to pro všechny úlohy.

   > [!NOTE]
   > Konfigurace pro naplánované úlohy obsahuje údaje "plán", tak ve výchozím nastavení, která se spustí práce podle nakonfigurovaného časování ihned poté, co byla migrována. Proto následující blok kódu odebere všechny informace o plánu během migrace (aby se zabránilo duplicitní spuštění napříč staré a nové pracovní prostory). Konfigurovat plány pro takové úlohy, až budete připravení na přímou migraci.

   Úlohy konfigurace vyžaduje nastavení pro novou nebo existující cluster. Pokud používáte existující cluster, následující skript /code pokusí nahradit původní ID clusteru nové ID clusteru.

   Zkopírujte a uložte následující python skriptu do souboru. Nahraďte hodnotu pro `old_cluster_id` a `new_cluster_id`, s výstupem z Migrace clusteru v předchozím kroku. Spustit v rozhraní příkazového řádku databricks příkazového řádku, například `python scriptname.py`.

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

7. **Migrace knihovny**

   Aktuálně neexistuje žádná jednoduchý způsob, jak migrovat knihovny z jednoho pracovního prostoru do jiného. Znovu nainstalujte tyto knihovny do nového pracovního prostoru. Tento krok je proto většinou ruční. To je možné automatizovat pomocí kombinace [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) nahrát vlastní knihovny do pracovního prostoru a [knihovny rozhraní příkazového řádku](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrace úložiště objektů blob v Azure a připojí k Azure Data Lake Store**

   Ručně znovu připojit všechny [úložiště objektů Blob v Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) a [Azure Data Lake Store (1. generace)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) přípojné body pomocí řešení založené na poznámkového bloku. Prostředky úložiště by byl připojený v primárním pracovním prostorem a má jej opakovat sekundární pracovního prostoru. Neexistuje žádné externí rozhraní API pro připojení.

9. **Migrace clusteru init skriptů**

   Skripty inicializace clusteru je možné migrovat ze starého do nového pomocí pracovního prostoru [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Nejprve zkopírujte potřebné skripty z "dbfs: / dat abricks/init /.." do místní pracovní plocha nebo virtuálního počítače. V dalším kroku zkopírujte tyto skripty na nový pracovní prostor na stejné cestě.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Ručně překonfigurovat a znovu zásadu použijte řízení přístupu.**

   Pokud váš existující primární pracovní prostor je nakonfigurován pro použití na úrovni Premium (SKU), je pravděpodobné, také použijete [funkce řízení přístupu](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

   Pokud používáte funkci řízení přístupu, ručně znovu použijte řízení přístupu k prostředkům (poznámkové bloky, clustery, úlohy, tabulky).

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [dokumentace k Azure Databricks](https://docs.azuredatabricks.net/user-guide/index.html).
