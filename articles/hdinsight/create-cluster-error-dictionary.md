---
title: Azure HDInsight – vytvoření clusteru – chybový slovník
description: Naučte se řešit chyby, ke kterým dochází při vytváření clusterů Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 469adf1c0e5108f7a8c89a7c3a4bebba5d42b431
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183960"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Chyby při vytváření clusteru

Tento článek popisuje řešení chyb, ke kterým může dojít při vytváření clusterů.

> [!NOTE]
> První tři chyby popsané v tomto článku jsou chyby ověřování. K tomu může dojít, když produkt Azure HDInsight používá třídu **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kód chyby: DeploymentDocument ' CsmDocument_2_0 ' se nepodařilo ověřit

**Chyba**: "umístění akce skriptu nelze přistupovat k identifikátoru URI: \<SCRIPT ACTION URL\> "

### <a name="error-message-1"></a>Chybová zpráva 1

"Vzdálený server vrátil chybu: (404) Nenalezeno."

#### <a name="cause"></a>Příčina

Služba HDInsight nemá přístup k adrese URL akce skriptu, kterou jste zadali v rámci žádosti o vytvoření clusteru. Služba obdrží předchozí chybovou zprávu, když se pokusí o přístup k akci skriptu.

#### <a name="resolution"></a>Řešení

- V případě adresy URL protokolu HTTP nebo HTTPS Ověřte adresu URL tak, že se pokusíte přejít z okna prohlížeče anonymním.
- V případě adresy URL WASB se ujistěte, že tento skript existuje v účtu úložiště, který v žádosti udělíte. Ujistěte se také, že klíč úložiště pro tento účet úložiště je správný.
- V případě adresy URL ADLS se ujistěte, že tento skript existuje v účtu úložiště.

---

### <a name="error-message-2"></a>Chybová zpráva 2

"Daný identifikátor URI skriptu \<SCRIPT_URI\> je v adls, ale tento cluster nemá žádný objekt zabezpečení Data Lake Storage".

#### <a name="cause"></a>Příčina

Služba HDInsight nemá přístup k adrese URL akce skriptu, kterou jste zadali v rámci žádosti o vytvoření clusteru. Služba obdrží předchozí chybovou zprávu, když se pokusí o přístup k akci skriptu.

#### <a name="resolution"></a>Řešení

Přidejte odpovídající Azure Data Lake Storage účet 1. generace do clusteru. Přidejte taky instanční objekt, který přistupuje k Data Lake Storage účtu 1. generace ke clusteru.

---

### <a name="error-message-3"></a>Chybová zpráva 3

Velikost virtuálního počítače \<CUSTOMER_SPECIFIED_VM_SIZE\> poskytnutá v žádosti je neplatná nebo není podporovaná pro roli \<ROLE\> . Platné hodnoty jsou: \<VALID_VM_SIZE_FOR_ROLE\> . "

#### <a name="cause"></a>Příčina

Velikost virtuálního počítače, kterou jste zadali, není pro tuto roli povolená. K této chybě může dojít, protože hodnota velikosti virtuálního počítače nefunguje podle očekávání nebo není vhodná pro roli počítače.

#### <a name="resolution"></a>Řešení

Chybová zpráva obsahuje seznam platných hodnot pro velikost virtuálního počítače. Vyberte jednu z těchto hodnot a opakujte požadavek na vytvoření clusteru.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kód chyby: InvalidVirtualNetworkId  

### <a name="error"></a>Chyba

"VirtualNetworkId není platný. VirtualNetworkId \<USER_VIRTUALNETWORKID\> * "*"

### <a name="cause"></a>Příčina

Hodnota **VirtualNetworkId** , kterou jste zadali během vytváření clusteru, nemá správný formát.

### <a name="resolution"></a>Řešení

Ujistěte se, že hodnoty **VirtualNetworkId** a Subnet jsou ve správném formátu. Pokud chcete získat hodnotu **VirtualNetworkId** :

1. Přejděte na Azure Portal.
1. Vyberte svou virtuální síť.
1. Vyberte položku nabídky **vlastnosti** . Hodnota vlastnosti **ResourceID** je hodnota **VirtualNetworkId** .

Tady je příklad ID virtuální sítě:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Kód chyby: CustomizationFailedErrorCode

### <a name="error"></a>Chyba

Nasazení clusteru se nepovedlo kvůli chybě v akci vlastního skriptu. Neúspěšné akce: \<SCRIPT_NAME\> , přejděte prosím na uživatelské rozhraní Ambari, abyste mohli dál ladit selhání. "

### <a name="cause"></a>Příčina

Vlastní skript, který jste zadali během žádosti o vytvoření clusteru, se spustí po úspěšném nasazení clusteru. Tento kód chyby označuje, že při provádění vlastního skriptu s názvem došlo k chybě \<SCRIPT_NAME\> .

### <a name="resolution"></a>Řešení

Vzhledem k tomu, že skript je váš vlastní skript, doporučujeme, abyste problém vyřešíte a v případě potřeby znovu spustíte skript. Pokud chcete řešit potíže s selháním skriptu, Projděte si protokoly ve složce/var/lib/Ambari-agent/*. Nebo otevřete stránku **operace** v uživatelském rozhraní Ambari a potom vyberte operaci **run_customscriptaction** pro zobrazení podrobností o chybě.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kód chyby: InvalidDocumentErrorCode

### <a name="error"></a>Chyba

" \<META_STORE_TYPE\> Verze schématu metastore \<METASTORE_MAJOR_VERSION\> v databázi \<DATABASE_NAME\> není kompatibilní s verzí clusteru. \<CLUSTER_VERSION\> "

### <a name="cause"></a>Příčina

Vlastní metastore je nekompatibilní s vybranou verzí clusteru HDInsight. V současné době clustery HDInsight 4,0 podporují jenom metastore verze 3,0 a novější, zatímco clustery HDInsight 3,6 nepodporují metastore verze 3,0 a novější.

### <a name="resolution"></a>Řešení

Používejte jenom metastore verze, které podporuje vaše verze clusteru HDInsight. Pokud nezadáte vlastní metastore, HDInsight interně vytvoří metastore a pak ho odstraní po odstranění clusteru.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kód chyby: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Chyba

Nepovedlo se připojit ke koncovému bodu správy clusteru, aby se provedla operace škálování. Ověřte, zda pravidla zabezpečení sítě neblokují externí přístup ke clusteru a aby bylo možné získat přístup k uživatelskému rozhraní Správce clusteru (Ambari). "

### <a name="cause"></a>Příčina

Pravidlo brány firewall ve skupině zabezpečení sítě (NSG) blokuje komunikaci clusteru s důležitými službami stavu a správy Azure.

### <a name="resolution"></a>Řešení

Pokud plánujete použít skupiny zabezpečení sítě k řízení síťového provozu, před instalací HDInsight proveďte následující akce:

- Identifikujte oblast Azure, kterou plánujete použít pro HDInsight.
- Identifikujte IP adresy, které vyžaduje HDInsight. Další informace najdete v tématu [IP adresy pro správu služby HDInsight](./hdinsight-management-ip-addresses.md).
  - Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight.
  - U skupin zabezpečení sítě povolte příchozí přenosy na portu 443 z IP adres. Tato konfigurace zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kód chyby: StoragePermissionsBlockedForMsi

### <a name="error"></a>Chyba

"Spravovaná identita nemá oprávnění k účtu úložiště. Ověřte prosím, že role vlastníka dat objektů BLOB úložiště je přiřazená k spravované identitě pro účet úložiště. Storage:/subscriptions/ \<Subscription ID\> /ResourceGroups/ \< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\> , spravovaná identita:/subscriptions/ \<Subscription ID\> /resourceGroups// \< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\> "

### <a name="cause"></a>Příčina

Nezadali jste oprávnění potřebná ke správě identity. Spravovaná identita přiřazená uživatelem nemá v účtu úložiště Azure Data Lake Storage Gen2 roli Přispěvatel Blob Storage.

### <a name="resolution"></a>Řešení

1. Otevřete web Azure Portal.
1. Přejít na účet úložiště.
1. Podívejte se na **Access Control (IAM)**.
1. Ujistěte se, že má uživatel přiřazenou roli Přispěvatel dat objektu BLOB úložiště nebo přiřazenou roli vlastníka dat objektu BLOB úložiště.

Další informace najdete v tématu [Nastavení oprávnění pro spravovanou identitu na účtu Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kód chyby: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Chyba

"Pravidla zabezpečení ve skupině zabezpečení sítě/Subscriptions/ \<SubscriptionID\> /resourceGroups/<název skupiny prostředků \> Default/Providers/Microsoft. Network/networkSecurityGroups/ \<Network Security Group Name\> nakonfigurovaná s podsítí/Subscriptions/ \<SubscriptionID\> /resourceGroups/ \<Resource Group name\> RG-westeurope-VNet-TomTom-default/Providers/Microsoft. Network/virtualNetworks/ \<Virtual Network Name\> /subnets/ \<Subnet Name\> nepovoluje požadované příchozí nebo odchozí připojení. Další informace najdete v tématu [plánování virtuální sítě pro Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md), nebo kontaktování podpory.

### <a name="cause"></a>Příčina

Pokud skupiny zabezpečení sítě nebo trasy definované uživatelem (udr) řídí příchozí provoz do clusteru HDInsight, ujistěte se, že váš cluster může komunikovat s důležitými službami Azure Health a Management.

### <a name="resolution"></a>Řešení

Pokud plánujete použít skupiny zabezpečení sítě k řízení síťového provozu, před instalací HDInsight proveďte následující akce:

- Identifikujte oblast Azure, kterou plánujete použít pro HDInsight, a vytvořte bezpečný seznam IP adres pro vaši oblast. Další informace najdete v tématu [služby pro stav a správu: konkrétní oblasti](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions).
- Identifikujte IP adresy, které HDInsight vyžaduje. Další informace najdete v tématu [IP adresy správy HDInsight](./hdinsight-management-ip-addresses.md).
- Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight. U skupin zabezpečení sítě povolte příchozí přenosy na portu 443 z IP adres. Tato konfigurace zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kód chyby: instalačnímu programu clusteru se nepodařilo nainstalovat součásti na jednoho nebo více hostitelů.

### <a name="error"></a>Chyba

"Instalačnímu programu clusteru se nepodařilo nainstalovat součásti na jednoho nebo více hostitelů. Opakujte prosím požadavek.

### <a name="cause"></a>Příčina 

Tato chyba se obvykle generuje, když dojde k přechodnému problému nebo výpadku Azure.

### <a name="resolution"></a>Řešení

Podívejte se na stránku [stav Azure](https://status.azure.com) pro všechny výpadky Azure, které můžou mít vliv na nasazení clusteru. Pokud nedošlo k výpadkům, zkuste cluster nasadit znovu.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kód chyby: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Chyba

Nelze se připojit ke koncovému bodu správy clusteru. Zkuste to prosím znovu později.

### <a name="cause"></a>Příčina

Služba HDInsight se nemůže připojit ke clusteru při pokusu o vytvoření clusteru.

### <a name="resolution"></a>Řešení

Pokud používáte vlastní skupinu zabezpečení sítě VNet (skupin zabezpečení sítě) a trasy definované uživatelem (udr), ujistěte se, že váš cluster může komunikovat se službami HDInsight Management. Další informace najdete v tématu [IP adresy správy HDInsight](./hdinsight-management-ip-addresses.md).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Kód chyby: nasazení selhala kvůli porušení zásad: prostředek <Resource URI> byl zakázán zásadami. Identifikátory zásad: [{"policyAssignment": {"název": " <Policy Name> ", "ID": "/Providers/Microsoft.Management/managementgroups/. <Management Group Name> providers/Microsoft. Authorization/policyAssignments/ <Policy Name> "}, "policyDefinition": <Policy Definition>

### <a name="cause"></a>Příčina

Zásady Azure založené na předplatném můžou odepřít vytváření veřejných IP adres. K vytvoření clusteru HDInsight jsou potřeba dvě veřejné IP adresy.

Následující zásady obecně ovlivňují vytváření clusteru:

* Zásady, které zabraňují vytváření IP adres nebo nástrojů pro vyrovnávání zatížení v rámci předplatného.
* Zásady, které zabraňují vytváření účtů úložiště.
* Zásady, které zabraňují odstraňování síťových prostředků, jako jsou IP adresy nebo nástroje pro vyrovnávání zatížení.

### <a name="resolution"></a>Řešení

Při vytváření clusteru HDInsight odstraňte nebo zakažte přiřazení Azure Policy na základě předplatného.

---

## <a name="next-steps"></a>Další kroky

Další informace o chybách při vytváření clusteru najdete v tématu [řešení potíží s vytvářením clusteru pomocí Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md).