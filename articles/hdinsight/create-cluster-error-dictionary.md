---
title: Vytvoření slovníku chyb clusteru
description: Naučte se vytvářet slovník chyb clusteru.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483060"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: Chyby při vytváření clusteru

Tento článek popisuje řešení chyb zjištěných při vytváření clusterů. 

> [!NOTE]
> První tři chyby v seznamu níže se vyskytují v důsledku chyb ověřování, když je třída CsmDocument_2_0 používána produktem HDInsight.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Chyba**: umístění akce skriptu není možné přistupovat k identifikátoru URI:\<adresa URL akce skriptu\>

**Chybová zpráva: vzdálený server vrátil chybu: (404) Nenalezeno.**

### <a name="cause"></a>Příčina
Adresa URL akce skriptu, která je k dispozici jako součást žádosti o vytvoření clusteru, není přístupná ze služby HDInsight. Po pokusu o přístup k akci skriptu se zobrazí "ErrorMessage".

### <a name="resolution"></a>Rozlišení 
  - Adresu URL protokolu HTTP/HTTPS můžete ověřit tak, že se pokusíte otevřít adresu URL z okna prohlížeče anonymním. 
  - V případě adresy URL WASB se ujistěte, že tento skript existuje v účtu úložiště, který je zadaný v požadavku. Ujistěte se, že klíč úložiště pro tento účet úložiště je přesný. 
  - V případě adresy URL ADLS se ujistěte, že tento skript existuje v účtu úložiště.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Chyba**: umístění akce skriptu není možné přistupovat k identifikátoru URI: \<adresa URL akce skriptu\>

**Chybová zpráva: zadaný identifikátor URI skriptu \<identifikátor URI skriptu\> se nachází v ADLS, ale tento cluster nemá žádný objekt zabezpečení Data Lake Storage.**

### <a name="cause"></a>Příčina
Adresa URL akce skriptu, která je k dispozici jako součást žádosti o vytvoření clusteru, není přístupná ze služby HDInsight. Po pokusu o přístup k akci skriptu se zobrazí "ErrorMessage". 

### <a name="resolution"></a>Rozlišení

Ujistěte se, že se do clusteru přidal odpovídající Azure Data Lake Store účet 1. generace. Do clusteru se přidá taky instanční objekt, který se používá pro přístup k Azure Data Lake Storemu účtu 1. generace. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Chyba**: velikost virtuálního počítače\<zadaného zákazníka\>velikost virtuálního počítače zadaná v žádosti je neplatná nebo není podporovaná pro roli\<role\>. Platné hodnoty jsou: \<platnou velikost virtuálního počítače pro\>rolí.

### <a name="cause"></a>Příčina
Velikosti virtuálních počítačů určených zákazníkem nejsou u této role povoleny. To může být pravda, protože hodnota velikosti virtuálního počítače nefunguje podle očekávání nebo není vhodná pro roli počítače. 

### <a name="resolution"></a>Rozlišení
Chybová zpráva obsahuje seznam platných hodnot pro velikost virtuálního počítače. Vyberte jednu z těchto platných hodnot a opakujte požadavek na vytvoření clusteru. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kód chyby: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Chyba**: VirtualNetworkId není platný. VirtualNetworkId\<USER_VIRTUALNETWORKID\>*

### <a name="cause"></a>Příčina
Hodnota **VirtualNetworkId** zadaná při vytváření clusteru není ve správném formátu. 

### <a name="resolution"></a>Rozlišení
Ujistěte se, že **VirtualNetworkId** a podsíť jsou ve správném formátu. Pokud chcete získat hodnotu **VirtualnetworkId** , přejděte na Azure Portal, vyberte svou virtuální síť a pak v nabídce vyberte **vlastnosti** . Vlastnost **ResourceID** je hodnota **VirtualNetworkId** . 

Příkladem ID virtuální sítě je: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Kód chyby: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Chyba**: nasazení clusteru se nepovedlo kvůli chybě v akci vlastního skriptu. Neúspěšné akce: \<SCRIPT_NAME\>, přejděte prosím do uživatelského rozhraní Ambari, abyste mohli selhání dále ladit.

### <a name="cause"></a>Příčina
Vlastní skript uživatele, který se zadal během žádosti o vytvoření clusteru, se spustí po úspěšném nasazení clusteru. Tento kód chyby označuje, že při spuštění tohoto vlastního skriptu s názvem \<SCRIPT_NAME\>došlo k chybě.   

### <a name="resolution"></a>Rozlišení
Vzhledem k tomu, že se jedná o vlastní skript uživatele, by uživatelé měli potíže vyřešit a v případě potřeby skript znovu spustit. Pokud chcete řešit potíže s selháním skriptu, Projděte si protokoly ve složce/var/lib/Ambari-agent/*. Nebo otevřete stránku operace v uživatelském rozhraní Ambari a pak vyberte operaci **run_customscriptionaction** pro zobrazení podrobností o chybě. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kód chyby: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Chyba**: verze schématu \<META_STORE_TYPE\> Metastore \<METASTORE_MAJOR_VERSION\> v databázi \<database_name\> není kompatibilní s verzí clusteru \<CLUSTER_VERSION

### <a name="cause"></a>Příčina
Vlastní metastore je nekompatibilní s vybranou verzí clusteru HDInsight. Cluster HDInsight 4,0 v současné době podporuje pouze metastore verze 3. *x*a HDInsight 3,6 nepodporuje metastore verze 3. *x* nebo novější. 

### <a name="resolution"></a>Rozlišení
Ujistěte se, že používáte jenom metastore verze, které podporuje každá verze clusteru HDInsight. Všimněte si, že pokud není zadán vlastní metastore, HDInsight interně vytvoří metastore. Tento metastore se ale automaticky odstraní při odstranění clusteru. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kód chyby: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Chyba**: Nelze se připojit ke koncovému bodu správy clusteru, aby bylo možné provést operaci škálování. Ověřte, že pravidla zabezpečení sítě neblokují externí přístup ke clusteru a že uživatelské rozhraní Správce clusterů (Ambari) je možné úspěšně získat.

### <a name="cause"></a>Příčina
Máte pravidlo brány firewall ve skupině zabezpečení sítě (NSG), které blokuje komunikaci clusteru s důležitými službami stavu a správy Azure. 

### <a name="resolution"></a>Rozlišení
Pokud plánujete používat **skupiny zabezpečení sítě** k řízení síťového provozu, před instalací HDInsight proveďte následující akce: 
  - Identifikujte oblast Azure, kterou plánujete použít pro HDInsight. 
  - Identifikujte IP adresy, které vyžaduje HDInsight. Další informace najdete v tématu [IP adresy pro správu služby HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight. 
    - **Skupiny zabezpečení sítě:** Povolí **příchozí** provoz na portu **443** z IP adres. Tím se zajistí, že se služby HDInsight Management budou moci připojit ke clusteru mimo virtuální síť. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kód chyby: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Chyba**: spravovaná identita nemá oprávnění k účtu úložiště. Ověřte prosím, že role vlastníka dat objektů BLOB úložiště je přiřazená k spravované identitě pro účet úložiště. Storage:/subscriptions/\<ID předplatného\>/resourceGroups/\< název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>, spravovaná identita:/subscriptions/\<ID předplatného\>/resourceGroups//\< název skupiny prostředků\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<uživatelské jméno spravované identity\>

### <a name="cause"></a>Příčina
K dispozici nejsou požadovaná oprávnění ke **správě identity.** **Uživatelem přiřazená identita** nebyla BLOB Storage role přispěvatele v účtu úložiště adls Gen2. 

### <a name="resolution"></a>Rozlišení

Otevřete Azure Portal, přejděte do svého účtu úložiště, podívejte se do části **Access Control (IAM)** a zajistěte, aby byl přiřazený Přispěvatel dat objektu BLOB úložiště nebo role vlastníka dat objektu BLOB úložiště přiřazená k **spravované identitě přiřazené uživatelem** . Další informace najdete v tématu [Nastavení oprávnění pro spravovanou identitu na účtu Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kód chyby: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Chyba**: pravidla zabezpečení ve skupině zabezpečení sítě/Subscriptions/\<SubscriptionID >\/resourceGroups/<Resource Group name> default/Providers/Microsoft. Network/networkSecurityGroups/\<název skupiny zabezpečení sítě\> nakonfigurovaná s podsítí/Subscriptions/\<SubscriptionID >\/resourceGroups/\<název skupiny prostředků > RG-westeurope-VNet-TomTom-default\/Providers/Microsoft. Network/virtualNetworks/\<Virtual Název sítě >\/podsítě/\<název podsítě\> 
nepovoluje požadované příchozí nebo odchozí připojení. Další informace najdete na stránce [plánování virtuální sítě pro Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment), nebo kontaktujte podporu. * *

### <a name="cause"></a>Příčina
Pokud k řízení příchozího provozu do clusteru HDInsight používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) nebo uživatelsky definované trasy (udr), musíte zajistit, aby cluster mohl komunikovat s důležitými službami stavu a správy Azure.

### <a name="resolution"></a>Rozlišení
Pokud plánujete používat **skupiny zabezpečení sítě** k řízení síťového provozu, před instalací HDInsight proveďte následující akce: 
  - Identifikujte oblast Azure, kterou plánujete použít pro HDInsight, a vytvořte bezpečný seznam IP adres pro vaši oblast: [služby stavu a správy: konkrétní oblasti](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Identifikujte IP adresy, které služba HDInsight vyžaduje. Další informace najdete v tématu [IP adresy správy HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight. **Skupiny zabezpečení sítě**: povolí příchozí provoz na portu **443** z IP adres. Tím se zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kód chyby: instalačnímu programu clusteru se nepodařilo nainstalovat součásti na jednoho nebo více hostitelů.

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Chyba**: instalačnímu programu clusteru se nepodařilo nainstalovat součásti na jednoho nebo více hostitelů. Opakujte prosím požadavek. 

### <a name="cause"></a>Příčina 
Tato chyba se obvykle generuje, když dojde k přechodnému problému nebo když dojde k výpadku Azure. 

### <a name="resolution"></a>Rozlišení

Podívejte se na stránku [stav Azure](https://status.azure.com/status) , kde najdete případné výpadky Azure, které by mohly mít vliv na nasazení clusteru. Pokud nedochází k výpadkům, zkuste cluster nasadit znovu. 

## <a name="next-steps"></a>Další kroky

Další informace o řešení chyb při vytváření clusteru najdete v tématu [řešení potíží s vytvářením clusteru pomocí Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
