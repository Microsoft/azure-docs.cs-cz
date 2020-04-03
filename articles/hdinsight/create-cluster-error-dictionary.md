---
title: Azure HDInsight Vytvoření clusteru – slovník chyb
description: Zjistěte, jak řešit chyby, ke kterým dochází při vytváření clusterů Azure HDInsight
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: 803783eddfbffd5c3dbab7353ee00dd7f11a09e5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618898"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Chyby při vytváření clusteru

Tento článek popisuje řešení chyb, které mohou narazit při vytváření clusterů.

> [!NOTE]
> První tři chyby popsané v tomto článku jsou chyby ověření. K nim může dojít, když produkt Azure HDInsight používá **CsmDocument_2_0** třídy.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: Dokument DeploymentDocument CsmDocument_2_0 ověření se nezdařil.

### <a name="error"></a>Chyba

"Umístění akce skriptu nelze\<získat přístup\>k adrese URI: SCRIPT ACTION URL "

#### <a name="error-message"></a>Chybová zpráva

"Vzdálený server vrátil chybu: (404) nebyl nalezen."

### <a name="cause"></a>Příčina

Služba HDInsight nemá přístup k adrese URL akce skriptu, kterou jste zadali jako součást požadavku vytvořit cluster. Služba obdrží předchozí chybovou zprávu při pokusu o přístup k akci skriptu.

### <a name="resolution"></a>Řešení

- U adresy URL protokolu HTTP nebo HTTPS ověřte adresu URL pokusem o přejděte z okna anonymního prohlížeče.
- Pro adresu URL WASB, ujistěte se, že skript existuje v účtu úložiště, který udáváte v požadavku. Také se ujistěte, že klíč úložiště pro tento účet úložiště je správný.
- Pro adresu URL ADLS se ujistěte, že skript existuje v účtu úložiště.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: Dokument DeploymentDocument CsmDocument_2_0 ověření se nezdařil.

### <a name="error"></a>Chyba

"Umístění akce skriptu nelze \<získat\>přístup k identifikátoru URI: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Chybová zpráva

"Daný SCRIPT_URI \<identifikátoru URI skriptu\> je v ADLS, ale tento cluster nemá žádný objekt zabezpečení úložiště datového jezera"

### <a name="cause"></a>Příčina

Služba HDInsight nemá přístup k adrese URL akce skriptu, kterou jste zadali jako součást požadavku vytvořit cluster. Služba obdrží předchozí chybovou zprávu při pokusu o přístup k akci skriptu.

### <a name="resolution"></a>Řešení

Přidejte odpovídající účet Azure Data Lake Storage Gen 1 do clusteru. Také přidejte instanční objekt, který přistupuje k účtu Data Lake Storage Gen 1 do clusteru.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: Dokument DeploymentDocument CsmDocument_2_0 ověření se nezdařil.

### <a name="error"></a>Chyba

"Velikost virtuálního\<\>počítače " CUSTOMER_SPECIFIED_VM_SIZE " uvedené v požadavku\<je\>neplatná nebo není podporována pro roli ' ROLE '. Platné hodnoty \<jsou: VALID_VM_SIZE_FOR_ROLE\>."

### <a name="cause"></a>Příčina

Zadaná velikost virtuálního počítače není pro roli povolena. K této chybě může dojít, protože hodnota velikosti virtuálního počítače nefunguje podle očekávání nebo není vhodná pro roli počítače.

### <a name="resolution"></a>Řešení

Chybová zpráva uvádí platné hodnoty pro velikost virtuálního počítače. Vyberte jednu z těchto hodnot a opakujte požadavek vytvořit cluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kód chyby: InvalidVirtualNetworkId  

### <a name="error"></a>Chyba

"VirtualNetworkId není platný. VirtualNetworkId\<'\>USER_VIRTUALNETWORKID '*"

### <a name="cause"></a>Příčina

Hodnota **VirtualNetworkId,** kterou jste zadali při vytváření clusteru, není ve správném formátu.

### <a name="resolution"></a>Řešení

Ujistěte se, že hodnoty **VirtualNetworkId** a podsítě jsou ve správném formátu. Chcete-li získat hodnotu **VirtualNetworkId:**

1. Přejděte na web Azure Portal.
1. Vyberte virtuální síť.
1. Vyberte položku nabídky **Vlastnosti.** Hodnota **vlastnosti ResourceID** je hodnota **VirtualNetworkId.**

Tady je příklad ID virtuální sítě:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Kód chyby: CustomizationFailedErrorCode

### <a name="error"></a>Chyba

"Nasazení clusteru se nezdařilo z důvodu chyby v akci vlastního skriptu. Neúspěšné \<akce:\>SCRIPT_NAME , Přejděte do uzuliny Ambari, abyste chybu dále ladili."

### <a name="cause"></a>Příčina

Vlastní skript, který jste zadali během požadavku vytvořit cluster, je proveden po úspěšném nasazení clusteru. Tento kód chyby označuje, že při provádění \<vlastního\>skriptu s názvem SCRIPT_NAME došlo k chybě.

### <a name="resolution"></a>Řešení

Vzhledem k tomu, že skript je váš vlastní skript, doporučujeme vyřešit problém a v případě potřeby skript znovu spustit. Chcete-li vyřešit selhání skriptu, zkontrolujte protokoly ve složce /var/lib/ambari-agent/*. Nebo otevřete stránku **Operace** v uzdu Ambari a pak vyberte **operaci run_customscriptaction** chcete-li zobrazit podrobnosti o chybě.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kód chyby: InvalidDocumentErrorCode

### <a name="error"></a>Chyba

"Verze \<\> schématu META_STORE_TYPE Metastore \<\> METASTORE_MAJOR_VERSION \<v\> databázi DATABASE_NAME \<není\>kompatibilní s verzí clusteru CLUSTER_VERSION "

### <a name="cause"></a>Příčina

Vlastní metastore není kompatibilní s vybranou verzí clusteru HDInsight. V současné době clustery HDInsight 4.0 podporují pouze Metastore verze 3.0 a novější, zatímco clustery HDInsight 3.6 nepodporují Metastore verze 3.0 a novější.

### <a name="resolution"></a>Řešení

Používejte pouze verze Metastore, které podporuje vaše verze clusteru HDInsight. Pokud nezadáte vlastní metastore, HDInsight interně vytvoří metastore a pak jej odstraní po odstranění clusteru.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kód chyby: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Chyba

"Nelze se připojit ke koncovému bodu správy clusteru k provedení operace škálování. Ověřte, zda pravidla zabezpečení sítě neblokují externí přístup ke clusteru a zda lze úspěšně získat přístup k rozhraní správce clusteru (Ambari).

### <a name="cause"></a>Příčina

Pravidlo brány firewall ve skupině zabezpečení sítě (NSG) blokuje komunikaci clusteru s kritickými službami azure health and management services.

### <a name="resolution"></a>Řešení

Pokud chcete k řízení síťového provozu použít skupiny zabezpečení sítě, před instalací HDInsightu prováďte následující akce:

- Identifikujte oblast Azure, kterou chcete použít pro HDInsight.
- Identifikujte IP adresy vyžadované hdinsightem. Další informace najdete v tématu [IP adresy pro správu služby HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které chcete nainstalovat HDInsight.
  - U skupin zabezpečení sítě povolte příchozí přenosy na portu 443 z adres IP. Tato konfigurace zajišťuje, že služby správy HDInsight mohou dosáhnout clusteru mimo virtuální síť.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kód chyby: StoragePermissionsBlockedForMsi

### <a name="error"></a>Chyba

"Spravovaná identita nemá oprávnění k účtu úložiště. Ověřte, zda je role Vlastník dat objektu blob úložiště přiřazena spravované identitě pro účet úložiště. Úložiště: \</subscriptions/ ID\> předplatného\< /resourceGroups/ Název\> skupiny prostředků /providers/Microsoft.Storage/storageAccounts/ \<Název\> \<účtu úložiště\> , Spravovaná identita: /předplatná/ ID předplatného /resourceGroups/\< / Název\> skupiny prostředků /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Příčina

Neposkytli jste oprávnění potřebná ke správě identity. Uživatelem přiřazená spravovaná identita nemá roli přispěvatele úložiště objektů blob na účtu úložiště Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Řešení

1. Otevřete web Azure Portal.
1. Přejděte na svůj účet úložiště.
1. Podívejte se do části **Řízení přístupu (IAM)**.
1. Ujistěte se, že uživatel má roli přispěvatele dat objektů blob úložiště nebo roli vlastníka dat objektu blob úložiště, která je jim přiřazena.

Další informace naleznete v tématu [Nastavení oprávnění pro spravovanou identitu v účtu Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kód chyby: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Chyba

"Pravidla zabezpečení ve skupině zabezpečení sítě\</předplatná/ SubscriptionID\>/resourceGroups/<\> název skupiny prostředků výchozí/zprostředkovatelé/Microsoft.Network/networkSecurityGroups/\<\>\<\> \<\>\<\> \<Název\> skupiny síťových zabezpečení nakonfigurovaná s podsítí /předplatná/ SubscriptionID /resourceGroups/ Název skupiny prostředků RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/ Virtual Network Name /podnets/ Podnet Name neumožňuje požadované příchozí a/nebo odchozí připojení. Další informace najdete na webu [Plánování virtuální sítě pro Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)nebo se obraťte na podporu."

### <a name="cause"></a>Příčina

Pokud skupiny zabezpečení sítě nebo uživatelem definované trasy (UDR) řídí příchozí provoz do clusteru HDInsight, ujistěte se, že váš cluster může komunikovat s kritickými službami stavu a správy Azure.

### <a name="resolution"></a>Řešení

Pokud chcete k řízení síťového provozu použít skupiny zabezpečení sítě, před instalací HDInsightu prováďte následující akce:

- Identifikujte oblast Azure, kterou chcete použít pro HDInsight, a vytvořte bezpečný seznam IP adres pro vaši oblast. Další informace naleznete v [tématu Zdraví a služby správy: Konkrétní regiony](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifikujte IP adresy, které vyžaduje HDInsight. Další informace naleznete v tématu [HDInsight management IP addresses](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které chcete nainstalovat HDInsight. U skupin zabezpečení sítě povolte příchozí přenosy na portu 443 z adres IP. Tato konfigurace zajišťuje, že služby správy HDInsight mohou dosáhnout clusteru mimo virtuální síť.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kód chyby: Instalaci součástí se nepodařilo nainstalovat do jednoho nebo více hostitelů.

### <a name="error"></a>Chyba

"Instalaci clusteru se nepodařilo nainstalovat součásti do jednoho nebo více hostitelů. Opakujte žádost."

### <a name="cause"></a>Příčina 

Tato chyba se obvykle generuje, když je přechodný problém nebo výpadek Azure.

### <a name="resolution"></a>Řešení

Zkontrolujte [stavovou](https://status.azure.com) stránku Azure pro všechny výpadky Azure, které by mohly ovlivnit nasazení clusteru. Pokud nejsou žádné výpadky, opakujte nasazení clusteru.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kód chyby: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Chyba

Nelze se připojit ke koncovému bodu správy clusteru. Opakujte akci později.

### <a name="cause"></a>Příčina

Služba HDInsight se nemůže připojit ke clusteru při pokusu o vytvoření clusteru

### <a name="resolution"></a>Řešení

Pokud používáte vlastní skupinu zabezpečení sítě virtuální sítě (NSG) a uživatelem definované trasy (UDR), ujistěte se, že váš cluster může komunikovat se službami správy HDInsight. Další informace naleznete v [tématu IP adresy hdinsight pro správu](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Kód chyby: Nasazení se nezdařila z<Resource URI>důvodu porušení zásad: "Prostředek " byl zakázán zásadami. Identifikátory zásad: '[{"policyAssignment":{"name":""id":"/providers/Microsoft.Management/managementGroups/<Policy Name> providers/Microsoft.Authorization/policyAssignments/ "},"policyDefinition":'Id":"/providers/Microsoft.ManagementGroups/providers/Microsoft.Authorization/policyAssignments/ "},"policyDefinition":'.id":"/providers/Microsoft.ManagementGroups/<Management Group Name> providers/Microsoft.Authorization/policyAssignments/<Policy Name>"},"policyDefinition":'.,<Policy Definition>

### <a name="cause"></a>Příčina

Zásady Azure založené na předplatném můžete odmítnout vytváření veřejných IP adres. K vytvoření clusteru HDInsight jsou potřeba dvě veřejné IP adresy.

Vytvoření clusteru obecně ovlivňují následující zásady:

* Zásady, které brání vytváření IP adres nebo vyrovnávání zatížení v rámci předplatného.
* Zásady, které brání vytváření účtů úložiště.
* Zásady, které brání odstranění síťových prostředků, jako jsou ip adresy nebo nástroje pro vyrovnávání zatížení.

### <a name="resolution"></a>Řešení

Při vytváření clusteru HDInsight odstraňte nebo zakažte zásady Azure založené na předplatném.

---

## <a name="next-steps"></a>Další kroky

Další informace o řešení chyb při vytváření clusteru najdete v [tématu Poradce při potížích s chybami vytváření clusteru pomocí Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
