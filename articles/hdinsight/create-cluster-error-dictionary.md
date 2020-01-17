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
ms.openlocfilehash: ee9ed5374b12c3130d952770a4be6a759e37339a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156858"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Chyby při vytváření clusteru

Tento článek popisuje řešení chyb, ke kterým může dojít při vytváření clusterů.

> [!NOTE]
> První tři chyby popsané v tomto článku jsou chyby ověřování. K tomu může dojít, když produkt Azure HDInsight používá třídu **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

### <a name="error"></a>Chyba

"Umístění akce skriptu není možné přistupovat k identifikátoru URI:\<adresa URL akce skriptu\>"

#### <a name="error-message"></a>Chybová zpráva

"Vzdálený server vrátil chybu: (404) Nenalezeno."

### <a name="cause"></a>Příčina

Služba HDInsight nemá přístup k adrese URL akce skriptu, kterou jste zadali v rámci žádosti o vytvoření clusteru. Služba obdrží předchozí chybovou zprávu, když se pokusí o přístup k akci skriptu.

### <a name="resolution"></a>Rozlišení

- V případě adresy URL protokolu HTTP nebo HTTPS Ověřte adresu URL tak, že se pokusíte přejít z okna prohlížeče anonymním.
- V případě adresy URL WASB se ujistěte, že tento skript existuje v účtu úložiště, který v žádosti udělíte. Ujistěte se také, že klíč úložiště pro tento účet úložiště je správný.
- V případě adresy URL ADLS se ujistěte, že tento skript existuje v účtu úložiště.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

### <a name="error"></a>Chyba

"Umístění akce skriptu není možné přistupovat k identifikátoru URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Chybová zpráva

"Zadané identifikátory URI \<SCRIPT_URI\> jsou v ADLS, ale tento cluster nemá žádný objekt pro data Lake Storage."

### <a name="cause"></a>Příčina

Služba HDInsight nemá přístup k adrese URL akce skriptu, kterou jste zadali v rámci žádosti o vytvoření clusteru. Služba obdrží předchozí chybovou zprávu, když se pokusí o přístup k akci skriptu.

### <a name="resolution"></a>Rozlišení

Přidejte odpovídající Azure Data Lake Storage účet 1. generace do clusteru. Přidejte taky instanční objekt, který přistupuje k Data Lake Storage účtu 1. generace ke clusteru.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

### <a name="error"></a>Chyba

Hodnota "VM Size"\<CUSTOMER_SPECIFIED_VM_SIZE\>zadaná v požadavku je neplatná nebo není podporovaná pro roli\<ROLE\>. Platné hodnoty jsou: \<VALID_VM_SIZE_FOR_ROLE\>. "

### <a name="cause"></a>Příčina

Velikost virtuálního počítače, kterou jste zadali, není pro tuto roli povolená. K této chybě může dojít, protože hodnota velikosti virtuálního počítače nefunguje podle očekávání nebo není vhodná pro roli počítače.

### <a name="resolution"></a>Rozlišení

Chybová zpráva obsahuje seznam platných hodnot pro velikost virtuálního počítače. Vyberte jednu z těchto hodnot a opakujte požadavek na vytvoření clusteru.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kód chyby: InvalidVirtualNetworkId  

### <a name="error"></a>Chyba

"VirtualNetworkId není platný. VirtualNetworkId\<USER_VIRTUALNETWORKID\>*

### <a name="cause"></a>Příčina

Hodnota **VirtualNetworkId** , kterou jste zadali během vytváření clusteru, nemá správný formát.

### <a name="resolution"></a>Rozlišení

Ujistěte se, že hodnoty **VirtualNetworkId** a Subnet jsou ve správném formátu. Pokud chcete získat hodnotu **VirtualNetworkId** :

1. Přejděte na web Azure Portal.
1. Vyberte svou virtuální síť.
1. Vyberte položku nabídky **vlastnosti** . Hodnota vlastnosti **ResourceID** je hodnota **VirtualNetworkId** .

Tady je příklad ID virtuální sítě:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Kód chyby: CustomizationFailedErrorCode

### <a name="error"></a>Chyba

Nasazení clusteru se nepovedlo kvůli chybě v akci vlastního skriptu. Neúspěšné akce: \<SCRIPT_NAME\>, přejděte prosím na uživatelské rozhraní Ambari, abyste mohli toto selhání dále ladit. "

### <a name="cause"></a>Příčina

Vlastní skript, který jste zadali během žádosti o vytvoření clusteru, se spustí po úspěšném nasazení clusteru. Tento kód chyby označuje, že došlo k chybě při provádění vlastního skriptu s názvem \<SCRIPT_NAME\>.

### <a name="resolution"></a>Rozlišení

Vzhledem k tomu, že skript je váš vlastní skript, doporučujeme, abyste problém vyřešíte a v případě potřeby znovu spustíte skript. Pokud chcete řešit potíže s selháním skriptu, Projděte si protokoly ve složce/var/lib/Ambari-agent/*. Nebo otevřete stránku **operace** v uživatelském rozhraní Ambari a potom vyberte operaci **run_customscriptaction** pro zobrazení podrobností o chybě.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kód chyby: InvalidDocumentErrorCode

### <a name="error"></a>Chyba

Verze schématu \<META_STORE_TYPE\> metastore \<METASTORE_MAJOR_VERSION\> v databázi \<database_name není kompatibilní s verzí clusteru\> \<

### <a name="cause"></a>Příčina

Vlastní metastore je nekompatibilní s vybranou verzí clusteru HDInsight. V současné době clustery HDInsight 4,0 podporují jenom metastore verze 3,0 a novější, zatímco clustery HDInsight 3,6 nepodporují metastore verze 3,0 a novější.

### <a name="resolution"></a>Rozlišení

Používejte jenom metastore verze, které podporuje vaše verze clusteru HDInsight. Pokud nezadáte vlastní metastore, HDInsight interně vytvoří metastore a pak ho odstraní po odstranění clusteru.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kód chyby: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Chyba

Nepovedlo se připojit ke koncovému bodu správy clusteru, aby se provedla operace škálování. Ověřte, zda pravidla zabezpečení sítě neblokují externí přístup ke clusteru a aby bylo možné získat přístup k uživatelskému rozhraní Správce clusteru (Ambari). "

### <a name="cause"></a>Příčina

Pravidlo brány firewall ve skupině zabezpečení sítě (NSG) blokuje komunikaci clusteru s důležitými službami stavu a správy Azure.

### <a name="resolution"></a>Rozlišení

Pokud plánujete použít skupiny zabezpečení sítě k řízení síťového provozu, před instalací HDInsight proveďte následující akce:

- Identifikujte oblast Azure, kterou plánujete použít pro HDInsight.
- Identifikujte IP adresy, které vyžaduje HDInsight. Další informace najdete v tématu [IP adresy pro správu služby HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight.
  - U skupin zabezpečení sítě povolte příchozí přenosy na portu 443 z IP adres. Tato konfigurace zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kód chyby: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Chyba

"Spravovaná identita nemá oprávnění k účtu úložiště. Ověřte prosím, že role vlastníka dat objektů BLOB úložiště je přiřazená k spravované identitě pro účet úložiště. Storage:/subscriptions/\<ID předplatného\>/resourceGroups/\< název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>, spravovaná identita:/subscriptions/\<ID předplatného\>/resourceGroups//\< název skupiny prostředků\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<uživatelské jméno spravované identity\>

### <a name="cause"></a>Příčina

Nezadali jste oprávnění potřebná ke správě identity. Spravovaná identita přiřazená uživatelem nemá v účtu úložiště Azure Data Lake Storage Gen2 roli Přispěvatel Blob Storage.

### <a name="resolution"></a>Rozlišení

1. Otevřete web Azure Portal.
1. Přejít na účet úložiště.
1. Podívejte se na **Access Control (IAM)** .
1. Ujistěte se, že role Přispěvatel dat objektu BLOB úložiště nebo role vlastníka dat objektu BLOB úložiště má přiřazený přístup k spravované identitě přiřazené uživateli pro dané předplatné.

Další informace najdete v tématu [Nastavení oprávnění pro spravovanou identitu na účtu Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kód chyby: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Chyba

"Pravidla zabezpečení ve skupině zabezpečení sítě/Subscriptions/\<SubscriptionID\>/resourceGroups/< název skupiny prostředků\> default/Providers/Microsoft. Network/networkSecurityGroups/\<název skupiny zabezpečení sítě\> nakonfigurovaná s podsítí/Subscriptions/\<SubscriptionID\>/resourceGroups/\<název skupiny prostředků\> RG-westeurope-vnet-tomtom-default/zprostředkovatelé/Microsoft. Network/virtualNetworks/\<Virtual Název sítě\>/subnets/\<název podsítě\> nepovoluje požadované příchozí nebo odchozí připojení. Další informace najdete v tématu [plánování virtuální sítě pro Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment), nebo kontaktování podpory.

### <a name="cause"></a>Příčina

Pokud skupiny zabezpečení sítě nebo trasy definované uživatelem (udr) řídí příchozí provoz do clusteru HDInsight, ujistěte se, že váš cluster může komunikovat s důležitými službami Azure Health a Management.

### <a name="resolution"></a>Rozlišení

Pokud plánujete použít skupiny zabezpečení sítě k řízení síťového provozu, před instalací HDInsight proveďte následující akce:

- Identifikujte oblast Azure, kterou plánujete použít pro HDInsight, a vytvořte bezpečný seznam IP adres pro vaši oblast. Další informace najdete v tématu [služby pro stav a správu: konkrétní oblasti](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifikujte IP adresy, které HDInsight vyžaduje. Další informace najdete v tématu [IP adresy správy HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight. U skupin zabezpečení sítě povolte příchozí přenosy na portu 443 z IP adres. Tato konfigurace zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kód chyby: instalačnímu programu clusteru se nepodařilo nainstalovat součásti na jednoho nebo více hostitelů.

###  <a name="error"></a>Chyba

"Instalačnímu programu clusteru se nepodařilo nainstalovat součásti na jednoho nebo více hostitelů. Opakujte prosím požadavek.

### <a name="cause"></a>Příčina 

Tato chyba se obvykle generuje, když dojde k přechodnému problému nebo výpadku Azure.

### <a name="resolution"></a>Rozlišení

Podívejte se na stránku [stav Azure](https://status.azure.com/status) pro všechny výpadky Azure, které můžou mít vliv na nasazení clusteru. Pokud nedošlo k výpadkům, zkuste cluster nasadit znovu.

## <a name="next-steps"></a>Další kroky

Další informace o chybách při vytváření clusteru najdete v tématu [řešení potíží s vytvářením clusteru pomocí Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
