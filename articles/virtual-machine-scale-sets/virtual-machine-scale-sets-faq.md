---
title: Nejčastější dotazy k sadách škálování virtuálního počítače Azure | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se sady škálování virtuálního počítače.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 2b0f463c009d13440f6d3eb2bbbe2315ba7b13f2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Nejčastější dotazy k sadách škálování virtuálních počítačů Azure

Získejte odpovědi na nejčastější dotazy týkající se sady škálování virtuálního počítače v Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Horní nejčastější dotazy pro sady škálování
**Otázka:** Kolik virtuálních počítačů může obsahovat škálovací sada?

**Odpověď:** Škálovací sada může obsahovat 0 až 1 000 virtuálních počítačů založených na imagích platformy nebo 0 až 300 virtuálních počítačů založených na vlastních imagích. 

**Otázka:** Podporují se ve škálovacích sadách datové disky?

**Odpověď:** Ano. Škálovací sada může definovat konfiguraci připojených datových jednotek, která se použije na všechny virtuální počítače v sadě. Další informace najdete v tématu [Škálovací sady Azure a připojené datové disky](virtual-machine-scale-sets-attached-disks.md). Další možnosti ukládání dat zahrnují:

* Soubory Azure (sdílené jednotky SMB)
* Jednotka operačního systému
* Dočasné jednotky (místní úložiště, nezálohované pomocí Azure Storage)
* Datová služba Azure (např. tabulky Azure, objekty blob Azure)
* Externí datová služba (např. vzdálená databáze)

**Otázka:** Které oblasti Azure podporují škálovací sady?

**Odpověď:** Všechny oblasti podporují škálovací sady.

**Otázka:** Jak se vytváří škálovací sada s použitím vlastní image?

**Odpověď:** Na základě virtuálního pevného disku vlastní image vytvoříte spravovaný disk, na který budete odkazovat v šabloně škálovací sady. [Tady je příklad](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**Otázka:** Pokud snížím kapacitu škálovací sady z 20 na 15, které virtuální počítače budou odebrány?

**Odpověď:** Virtuální počítače se ze škálovací sady odebírají rovnoměrně napříč aktualizačními doménami a doménami selhání, aby se maximalizovala dostupnost. Nejprve se odeberou virtuální počítače s nejvyšším ID.

**Otázka:** A co když pak zvýším kapacitu z 15 na 18?

**Odpověď:** Pokud zvýšíte kapacitu na 18, vytvoří se 3 nové virtuální počítače. ID instance virtuálního počítače se přírůstkově zvýší oproti předchozí nejvyšší hodnotě (např. 20, 21, 22). Virtuální počítače se vyvažují mezi doménami selhání a aktualizačními doménami.

**Otázka:** Pokud ve škálovací sadě používám několik rozšíření, je možné vynucovat určitou posloupnost provádění?

**Odpověď:** Ne přímo, ale u rozšíření customScript by váš skript mohl čekat na dokončení jiného rozšíření. Další pokyny k nastavení pořadí rozšíření najdete v blogovém příspěvku o [nastavení pořadí rozšíření ve škálovacích sadách virtuálních počítačů Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**Otázka:** Spolupracují škálovací sady se skupinami dostupnosti Azure?

**Odpověď:** Místní (bez oblastmi) škálovací sady používá *umístění skupiny*, z nichž každý lze nastavit tak, aby fungoval jako implicitní sada dostupnosti s pěti domén selhání a pět aktualizaci domény. Sady škálování virtuálních počítačů více než 100 span více skupin pro umístění. Další informace o skupinách umístění najdete v tématu [Práce s velkými škálovacími sadami virtuálních počítačů](virtual-machine-scale-sets-placement-groups.md). Skupina dostupnosti virtuálních počítačů může existovat ve stejné virtuální síti jako škálovací sada virtuálních počítačů. Běžnou konfigurací je umístění virtuálních počítačů řídicích uzlů, které často vyžadují jedinečnou konfiguraci, do skupiny dostupnosti, a datových uzlů do škálovací sady.

**Otázka:** Škálování pracovní sady zón. Azure dostupnosti?

**Odpověď:** Ano! Další informace najdete v tématu [sady škálování zóny doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatické škálování

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jaké jsou doporučené postupy pro škálování Azure?

Osvědčené postupy pro škálování, najdete v části [osvědčené postupy pro automatické škálování virtuálních počítačů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Kde metriky názvy pro automatické škálování, který používá metriky na hostiteli

Metriky názvy pro automatické škálování, který používá metriky na hostiteli, najdete v části [podporované metriky s Azure monitorování](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existují jakékoli příklady automatické škálování podle o délce téma a fronty Azure Service Bus?

Ano. Příklady automatické škálování podle o délce téma a fronty Azure Service Bus najdete v tématu [běžné metriky automatického škálování Azure monitorování](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Pro fronty Service Bus použijte následujícím kódu JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Pro frontu úložiště použijte následujícím kódu JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Nahraďte ukázkové hodnoty prostředku identifikátory Uniform Resource (Identifier).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Měli I škálování pomocí metriky na hostiteli nebo rozšíření diagnostiky?

Nastavení automatického škálování můžete vytvořit na virtuálním počítači používat metriky na úrovni hostitele nebo hosta metriky na základě operačního systému.

Seznam podporovaných metriky, naleznete v části [běžné metriky automatického škálování Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Úplný příklad pro sady škálování virtuálního počítače, naleznete v tématu [pokročilé škálování konfigurace pomocí šablony Resource Manageru pro sady škálování virtuálního počítače](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

Příklad používá metrika úrovni hostitele procesoru a metriky počet zpráv.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Jak nastavit pravidla výstrah na škálovací sadu virtuálních počítačů?

Výstrahy můžete vytvořit na metriky pro sady škálování virtuálního počítače pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure. Další informace najdete v tématu [ukázky rychlý start Azure PowerShell monitorování](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) a [Azure monitorování napříč platformami rozhraní příkazového řádku rychlý start ukázky](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId škálovací sadu virtuálních počítačů vypadá takto: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.COMPUTE/virtualMachineScaleSets/yourvmssname

Můžete jako metriku nastavit výstrahy pro všechny čítače výkonu virtuálních počítačů. Další informace najdete v tématu [metriky hostovaného operačního systému pro virtuální počítače na bázi správce prostředků Windows](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) a [metriky hostovaného operačního systému pro virtuální počítače s Linuxem](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) v [běžné metriky automatického škálování Azure monitorování](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)článku.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak mám nastavit automatické škálování podle škálování virtuálních počítačů, nastavit pomocí prostředí PowerShell?

Nastavit škálování na škálování virtuálních počítačů, nastavit pomocí prostředí PowerShell, naleznete v příspěvku blogu [postup přidání škálování na sadu škálování virtuálního počítače Azure](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/).




## <a name="certificates"></a>Certifikáty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Jak můžu bezpečně dodávat certifikát k virtuálnímu počítači? Jak můžu zřídit škálování virtuálního počítače nastaven na spouštění webu dodán SSL pro web bezpečně z konfigurace certifikátu? (Běžné operace otočení certifikátu by být téměř stejné jako operace aktualizace konfigurace.) Máte například o tom, jak to udělat? 

Pro bezpečné odeslání certifikát k virtuálnímu počítači, může nainstalovat certifikát zákazníka přímo do úložiště certifikátů systému Windows z trezoru klíčů zákazníka.

Použijte následujícím kódu JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Kód podporuje Windows a Linux.

Další informace najdete v tématu [vytvoření nebo aktualizace škálovací sady virtuálních počítačů](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Příklad certifikát podepsaný svým držitelem

1.  Vytvořte certifikát podepsaný svým držitelem v trezoru klíčů.

    Použijte následující příkazy prostředí PowerShell:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Tento příkaz vám vstup pro šablony Azure Resource Manageru.

    Příklad, jak vytvořit certifikát podepsaný svým držitelem v trezoru klíčů, naleznete v části [scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Změna šablony Resource Manageru.

    Přidejte tuto vlastnost, aby **virtualMachineProfile**, v rámci virtuálního počítače sady škálování prostředku:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Můžete zadat dvojici klíčů SSH používané při ověřování SSH s měřítkem virtuální počítač Linux nastavte ze šablony Resource Manageru?  

Ano. Rozhraní REST API pro **osProfile** je podobná standardní rozhraní REST API pro virtuální počítač. 

Zahrnout **osProfile** v šabloně:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Tento blok JSON se používá v [šablony rychlý start Githubu 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Profil operačního systému se také používá při [šabloně pro rychlý start Githubu grelayhost.json](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Další informace najdete v tématu [vytvoření nebo aktualizace škálovací sady virtuálních počítačů](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak odstranit zastaralé certifikáty? 

Odebrat nepoužívané certifikáty, odeberte starý certifikát z úložiště certifikátů seznamu. Ponechte všechny certifikáty, které chcete zůstat ve vašem počítači v seznamu. Certifikát nebude odstraněn ze všech virtuálních počítačů. Je také nepřidá certifikát do nové virtuální počítače, které jsou vytvořené v sadě škálování virtuálního počítače. 

Chcete-li odstranit certifikát z existujících virtuálních počítačů, napište rozšíření vlastních skriptů a certifikáty z úložiště certifikátů odeberte ručně.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Jak I vložit existující veřejný klíč SSH do vrstvy SSH sady škálování virtuálního počítače při zřizování? Chcete uložit hodnoty veřejného klíče SSH v Azure Key Vault a použijte je v šabloně moje šablona Resource Manager.

Pokud zadáte virtuální počítače pouze pomocí veřejného klíče SSH, nemusíte put veřejných klíčů v Key Vault. Veřejné klíče nejsou tajné.
 
Veřejné klíče SSH ve formátu prostého textu můžete zadat při vytváření virtuálního počítače s Linuxem.

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
Název elementu linuxConfiguration | Požaduje se | Typ | Popis
--- | --- | --- | --- |  ---
SSH | Ne | Kolekce | Určuje klíče konfigurace SSH pro operační systém Linux.
path | Ano | Řetězec | Určuje cestu souboru Linux, kde klíče SSH nebo certifikát má být umístěn
data klíče | Ano | Řetězec | Určuje kódování base64 veřejný klíč SSH

Příklad, naleznete v části [šablony rychlý start Githubu 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Při spuštění `Update-AzureRmVmss` po přidání více než jeden certifikát ze stejné trezoru klíčů, zobrazuje se následující zpráva:
 
>Update-AzureRmVmss: Tajný klíč seznam obsahuje opakované instance /subscriptions/ < my-subscription-id > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, což je zakázaná.
 
To může dojít, pokud se pokusíte znovu přidat ke stejnému trezoru místo použití nového certifikátu trezoru pro existující trezor zdroje. `Add-AzureRmVmssSecret` Příkaz nefunguje správně při přidávání dalších tajných klíčů.
 
Pokud chcete přidat další tajné klíče ze stejné trezoru klíčů, aktualizujte seznam $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Očekávané vstupní struktuře, najdete v části [vytvoření nebo aktualizaci virtuálního počítače nastavit](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Naleznete tajný klíč v objektu sady škálování virtuálního počítače, která je v trezoru klíčů. Pak přidejte váš certifikát odkaz (adresa URL a název tajný úložiště) do seznamu přidruženého k úložišti.

> [!NOTE] 
> V současné době nelze odebrat certifikáty z virtuálních počítačů pomocí rozhraní API sady škálování virtuálního počítače.
>

Nové virtuální počítače nebudou mít starý certifikát. Virtuální počítače, které jsou už nasazené, které mají certifikát, ale bude mít starý certifikát.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Můžete posouvat certifikáty škálování virtuálního počítače nastavit bez zadání hesla, když je certifikát v úložišti tajný?

Není nutné pevně hesla ve skriptech. Můžete dynamicky načíst hesla s oprávněními, které můžete použít ke spuštění skriptu nasazení. Pokud máte skript, který přesouvá certifikát z úložiště tajný klíč trezoru, úložišti tajný `get certificate` příkaz také výstupy heslo souboru PFX.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak nastavit vlastnost tajné klíče virtualMachineProfile.osProfile pro škálování virtuálních počítačů pracovní? Proč musím sourceVault hodnotu, pokud je nutné zadat absolutní identifikátor URI pro certifikát pomocí vlastnosti certificateUrl? 

Odkaz certifikát Vzdálená správa systému Windows (WinRM) se musí nacházet ve tajné klíče vlastnosti profilu operačního systému. 

Účelem označující trezoru zdroj je vynutit zásady řízení seznamu řízení přístupu, které existují v modelu Azure Cloud Service uživatele. Pokud není zadán zdroj trezoru, uživatelé, kteří nemají oprávnění k nasazení nebo přístup tajné klíče trezoru klíčů by mohli prostřednictvím poskytovatele výpočetní prostředků (CRP). Seznamy ACL existují i pro prostředky, které nejsou k dispozici.

Pokud zadáte ID trezoru nesprávný zdrojový ale adresa URL platná trezoru klíčů, dojde k chybě při dotazování operaci.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Pokud tajných klíčů lze přidat do existujícího virtuálního počítače nastavit, jsou tajné klíče vložit do existujících virtuálních počítačů, nebo pouze do nové? 

Certifikáty jsou přidány do všechny virtuální počítače, i těch, které jsou předem existující. Pokud vaše škálovací sady virtuálních počítačů upgradePolicy vlastnost nastavená na **ruční**, certifikát je přidat k virtuálnímu počítači při provádění ruční aktualizace ve virtuálním počítači.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Kde umístit certifikáty pro virtuální počítače s Linuxem?

Další postupy k nasazení certifikátů pro virtuální počítače s Linuxem najdete v tématu [certifikáty nasadit do virtuálních počítačů z trezoru klíčů spravované zákazníkem](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak přidat nový certifikát úložiště pro nový objekt, který certifikát?

Přidat certifikát trezoru k existujícím tajným klíčem, viz následující příklad PowerShell. Použijte pouze jeden objekt tajný.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co se stane certifikáty, pokud se obnovení z Image virtuálního počítače?

Pokud se obnovení z Image virtuálního počítače, certifikáty se odstraní. Obnovování disku odstraní celý operačního systému. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co se stane, když odstranit certifikát z trezoru klíčů?

Pokud tajný klíč je odstraněna z trezoru klíčů, a poté spusťte `stop deallocate` pro všechny virtuální počítače a potom je znovu spusťte, dojde k chybě. Selhání dojde, protože CRP musí načíst tajné klíče z trezoru klíčů, ale ji nelze. V tomto scénáři můžete odstranit certifikáty z modelu sady škálování virtuálního počítače. 

Součást CRP není zachována tajné klíče zákazníků. Pokud spustíte `stop deallocate` pro všechny virtuální počítače ve škálovací sadu virtuálních počítačů, mezipaměť je Odstraněná. V tomto scénáři se načítají tajné klíče z trezoru klíčů.

Tento problém není dojde při škálování, protože není v mezipaměti kopii tajný klíč v Azure Service Fabric (v modelu fabric jednoho klienta).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Proč musím zadávat přesné umístění pro adresu URL certifikátu (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), jak je uvedeno v [scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Dokumentace Azure Key Vault stavy, získání tajného klíče rozhraní API REST by měla vrátit nejnovější verzi tajný klíč, pokud není zadán verze.
 
Metoda | zprostředkovatele identity
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Nahraďte {*tajný klíč název*} s názvem a nahraďte {*tajný klíč verze*} s verzí tajného klíče můžete obnovit. Verzi tajného klíče může být vyloučeny. V takovém případě se načte aktuální verze.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Proč musím zadejte verzi certifikátu, když se používá Key Vault?

Účelem požadavek Key Vault určit verzi certifikátu je aby bylo jasné pro uživatele co certifikát je nasazený na jejich virtuální počítače.

Pokud vytvoříte virtuální počítač a pak aktualizujte váš tajný klíč v trezoru klíčů, nový certifikát, nebude stažen do virtuálních počítačů. Ale virtuální počítače se zobrazují na něj odkazovat a získat nové virtuální počítače nový sdílený tajný klíč. Abyste tomu předešli, je nutné tak, aby odkazovaly verzi tajného klíče.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Můj tým pracuje s několik certifikátů, které jsou distribuovány do us jako .cer veřejných klíčů. Co je nastaven o doporučený postup pro nasazení těchto certifikátů škálování virtuálního počítače?

K nasazení .cer nastaven veřejné klíče škálování virtuálních počítačů, může generovat soubor .pfx, který obsahuje pouze soubory s příponou CER. Chcete-li to provést, použijte `X509ContentType = Pfx`. Například načíst soubor .cer jako objekt x509Certificate2 v C# nebo prostředí PowerShell a potom volejte metodu. 

Další informace najdete v tématu [X509Certificate.Export – metoda (X509ContentType, řetězec)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Nevidím možnost uživatelům předat certifikáty jako řetězce formátu base64. Tato možnost k dispozici většina jiných poskytovatelů prostředků.

Emulovat předávání v certifikátu jako řetězec base64, můžete rozbalit verzí URL v šabloně Resource Manager. Do šablony Resource Manager zahrňte následující vlastnosti JSON:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Je nutné zalomení certifikáty v objekty JSON v trezorů klíčů?

Sady škálování virtuálního počítače a virtuální počítače musí být uzavřen certifikáty do objektů JSON. 

Také podporujeme typ obsahu application/x-pkcs12. Pokyny k používání application/x-pkcs12 najdete v tématu [certifikáty PFX v Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Aktuálně nepodporujeme souborů s příponou CER. Použití souborů .cer, exportujte je do kontejnerů .pfx.



## <a name="compliance-and-security"></a>Dodržování předpisů a zabezpečení

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Jsou kompatibilní se standardem PCI sadách škálování virtuálních počítačů?

Škálovací sady virtuálních počítačů představují tenkou vrstvu rozhraní API nad CRP. Obě komponenty jsou součástí výpočetní platformy ve stromu služeb Azure.

Z hlediska kompatibility jsou škálovací sady virtuálních počítačů základní součástí výpočetní platformy Azure. S vlastním CRP sdílejí tým, nástroje, procesy, metodiku nasazení, kontrolních mechanismy zabezpečení, kompilaci za běhu (JIT), monitorování, výstrahy a tak dále. Škálovací sady virtuálních počítačů jsou kompatibilní se standardem PCI (Payment Card Industry), protože CRP je součástí aktuální atestace standardu DSS (Data Security Standard) pro PCI.

Další informace najdete na webu [Centrum zabezpečení Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Nemá [identita spravované služby Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) práce s sady škálování virtuálního počítače?

Ano. Můžete zobrazit některé šablony příklad MSI v šablonách Azure rychlý start. Linux: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Rozšíření

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak lze odstranit rozšíření sady škálování virtuálního počítače?

Chcete-li odstranit rozšíření sady škálování virtuálního počítače, použijte následující příklad PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Můžete najít hodnotu Název_rozšíření v `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>Je k dispozici příklad šablony, který se integruje s analýzy protokolů sady škálování virtuálního počítače?

Pro škálování virtuálních počítačů, nastavte příklad šablony, který se integruje s analýzy protokolů, najdete ve druhém příkladu v [nasazení clusteru Azure Service Fabric a povolte monitorování pomocí analýzy protokolů](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Zdá se, že rozšíření spustit souběžně na sady škálování virtuálního počítače. To způsobí, že Moje rozšíření vlastních skriptů selhání. Jak tento problém lze vyřešit?

Další informace o sekvencování rozšíření v sady škálování virtuálního počítače najdete v tématu [rozšíření sekvencování v sady škálování virtuálního počítače Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Jak I resetování hesla pro virtuální počítače v mé škálovací sadu virtuálních počítačů?

Chcete-li změnit heslo pro virtuální počítače ve škálovací sady dvěma způsoby.

- Změňte model sadu škálování virtuálního počítače přímo. K dispozici s výpočetní API 2017-12-01 a novějším.

    Aktualizujte přihlašovací údaje správce přímo v modelu sady škálování (například pomocí Průzkumníka prostředků Azure, PowerShell nebo rozhraní příkazového řádku). Po aktualizované, všechny nové škálovací sadu virtuálních počítačů mají nové přihlašovací údaje. Existující virtuální počítače mít pouze nové přihlašovací údaje, pokud jsou obnovit z Image. 

- Resetování hesla pomocí rozšíření virtuálního počítače přístup.

    Použijte následující příklad PowerShellu:
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak lze přidat rozšíření ke všem virtuálním počítačům v mé škálovací sadu virtuálních počítačů?

Pokud zásady aktualizace je nastavený na **automatické**, opětovného nasazení šablona s nové vlastnosti rozšíření aktualizuje všechny virtuální počítače.

Pokud zásady aktualizace je nastavený na **ruční**, nejdřív aktualizovat rozšíření a potom ručně aktualizovat všechny instance ve virtuálních počítačů.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Pokud jsou aktualizovány rozšíření spojená se existující škálovací sadu virtuálních počítačů jsou existující virtuální počítače vliv? (To znamená, budou virtuální počítače *není* odpovídat modelu sady škálování virtuálního počítače?) Nebo se budou ignorovat? Při zacelené služby je existující počítač nebo obnovit z Image, jsou skripty, které nejsou aktuálně nakonfigurovány na škálovací sadu virtuálních počítačů provést, nebo jsou skripty, které byly nakonfigurovány při vytvořen virtuální počítač používá?

Pokud je nastavená definice rozšíření v škálování virtuálních počítačů dojde k aktualizaci modelu a upgradePolicy vlastnost nastavena na **automatické**, aktualizuje virtuálních počítačů. Pokud je vlastnost upgradePolicy nastavena **ruční**, rozšíření jsou označeny jako neodpovídá modelu. 

Pokud existující virtuální počítač zacelené služby, zobrazí se jako restartování a rozšíření nejsou znovu spusťte. Pokud ho je obnovit z Image, je jako nahraďte jednotky operačního systému se zdrojovou bitovou kopií. Všechny specializace z nejnovější modelu, jako je například rozšíření, jsou spuštěny.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak zapojit škálování virtuálních počítačů, nastavit do domény služby Active Directory?

Chcete-li připojit škálování virtuálních počítačů, nastavit k doméně služby Active Directory (AD), můžete definovat rozšíření. 

Chcete-li definovat rozšíření, použijte vlastnost JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Moje rozšíření sady škálování virtuálního počítače se snaží nainstalovat něco, co vyžaduje restartování počítače. Například "commandToExecute": "powershell.exe - ExecutionPolicy Unrestricted Install-WindowsFeature – název služby FS-Resource-Manager – IncludeManagementTools"

Pokud vaše rozšíření sady škálování virtuálního počítače se snaží nainstalovat něco, co vyžaduje restartování počítače, můžete použít rozšíření Azure Automation konfigurace požadovaného stavu (DSC automatizace). Pokud operační systém Windows Server 2012 R2, Azure vrátí v nastavení Windows Management Framework (WMF) 5.0, restartování počítače a poté pokračuje v konfiguraci. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak zapnout antimalwarových v mé škálovací sadu virtuálních počítačů?

Chcete-li zapnout antimalware na vaše škálovací sadu virtuálních počítačů, použijte následující příklad PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Je třeba provést vlastní skript, který je hostován v účtu privátní úložiště. Skript spustí úspěšně, pokud úložiště je veřejný, ale při pokusu o použití sdíleného přístupového podpisu (SAS), se nezdaří. Zobrazí se tato zpráva: "Chybějící povinné parametry pro platný podpis sdíleného přístupu". Odkaz + SAS funguje bez problémů z místní v prohlížeči.

K provedení vlastní skript, který je hostován v účtu úložiště privátní, nastavte chráněné nastavení se klíč účtu úložiště a název. Další informace najdete v tématu [vlastní skript rozšíření pro Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Sítě
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Je možné přiřadit skupinu zabezpečení sítě (NSG) k sadě škálování, tak, aby se vztahuje na všechny síťové adaptéry pro virtuální počítač v sadě?

Ano. Skupina zabezpečení sítě můžete použít přímo na škále nastavit pomocí odkazů v části Networkinterfaceconfiguration profilu sítě. Příklad:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jakým způsobem prohození virtuálních IP adres pro sady škálování virtuálního počítače ve stejném předplatném a stejné oblasti?

Pokud máte dvě sady škálování virtuálního počítače s nástroj pro vyrovnávání zatížení Azure front-end a jsou ve stejném předplatném, oblasti, můžete zrušit přidělení veřejné IP adresy z každé z nich a přiřadit na druhý. V tématu [prohození virtuálních IP adres: modrá zelená nasazení ve službě Správce prostředků Azure](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) třeba. To neznamená zpoždění ale prostředky jsou přidělené v síti jejich navrácena úroveň. Rychlejší možností je použití Azure Application Gateway se dvěma back-endové fondy a pravidel směrování. Alternativně může hostování vaší aplikace s [služby Azure App service](https://azure.microsoft.com/services/app-service/) která poskytuje podporu pro rychlé přepínání mezi sloty pracovní a provozní.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Jak určit rozsah privátních IP adres pro statického přidělování privátní IP adresu?

IP adresy jsou vybrány z podsítě, který určíte. 

Metoda přidělování adres IP sady škálování virtuálního počítače je vždy "dynamická", ale který neznamená, že tyto IP adresy můžou změnit. V tomto případě "dynamická" pouze znamená, nezadávejte IP adresu v požadavku PUT. Zadejte statickou nastavit pomocí podsítě. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Nasazení škálování virtuálních počítačů, nastavte na existující virtuální síť Azure 

Nasazení škálování virtuálních počítačů, nastavte na existující virtuální síť Azure najdete v tématu [nasadit škálovací sady virtuálních počítačů k existující virtuální síti](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Jak přidat IP adresu první virtuální počítač v škálování virtuálního počítače nastavit pro výstup šablony?

Chcete-li přidat IP adresu první virtuální počítač v škálování virtuálního počítače nastavit pro výstup šablony, najdete v části [Azure Resource Manager: sadách škálování virtuálních počítačů Get privátních IP adres](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Můžete použít sady škálování pomocí Accelerated sítě?

Ano. Pokud chcete použít urychlené sítě, nastavení enableAcceleratedNetworking na hodnotu true ve vaší škálovací sady Networkinterfaceconfiguration. Například
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak můžete nakonfigurovat servery DNS používá škálovací sada?

K vytvoření škálování virtuálních počítačů, nastavit pomocí vlastní konfigurace DNS, přidejte do části Networkinterfaceconfiguration sady škálování paket dnsSettings JSON. Příklad:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Konfigurování sad přiřadit veřejnou IP adresu pro každý virtuální počítač škálování

Chcete-li vytvořit sadu škálování virtuálního počítače, který přiřazuje veřejnou IP adresu pro každý virtuální počítač, zkontrolujte, zda je verze rozhraní API Microsoft.Compute/virtualMachineScaleSets prostředku 2017-03-30 a přidejte _publicipaddressconfiguration_ JSON paket ke stupnici nastavit část konfigurace IP adresy. Příklad:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Můžete nakonfigurovat škálování nastaven pro práci s více bran aplikace?

Ano. Můžete přidat id prostředku je pro více fondů adres back-end Application Gateway na _applicationGatewayBackendAddressPools_ v seznamu _konfigurace IP adresy_ oddíl vaší měřítka nastavit síť profil.

## <a name="scale"></a>Měřítko

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>V případě, co by vytvořit škálování virtuálního počítače s méně než dva virtuální počítače?

Jedním z důvodů k vytvoření škálování virtuálního počítače s méně než dva virtuální počítače může být použití elastické vlastnosti škálovací sadu virtuálních počítačů. Například můžete nasadit nastavení nulový počet virtuálních počítačů k definování bez placení virtuálního počítače s náklady na infrastrukturu škálování virtuálního počítače. Potom až budete připravení k nasazení virtuálních počítačů, zvyšte "kapacitu" škálování virtuálního počítače nastavena na výrobní počet instancí.

Dalším důvodem, které můžete vytvořit škálování virtuálních počítačů s méně než dva virtuální počítače je, pokud máte obavy, méně s dostupností než při použití s virtuálními počítači diskrétní sadu dostupnosti. Sady škálování virtuálního počítače zadejte způsob, jak pracovat s poskytujících blíže neurčené výpočetní jednotky, které jsou zastupitelných. Toto sjednocení je klíčovým rozdílem pro sady škálování virtuálního počítače a skupiny dostupnosti. Mnoho Bezstavová zatížení do not track jednotlivé jednotky. Pokud hodnota klesne zatížení, můžete škálovat jeden výpočetní jednotky a pak vertikálně navýšit kapacitu k mnoha při zvyšuje zatížení.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Změna počet virtuálních počítačů sady škálování virtuálního počítače

Chcete-li změnit počet virtuálních počítačů v škálování virtuálních počítačů, nastavte na portálu Azure, z škálování virtuálního počítače nastavit vlastnosti oddílu, klikněte v okně "Škálování" a použijte posuvníku. Další způsoby, jak změnit počet instancí, najdete v části [změnit počet instancí škálovací sadu virtuálních počítačů](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak definuje vlastní výstrahy pro když se dosáhne určité prahové hodnoty?

Máte určitou volnost v tom, jak zpracovat výstrahy pro zadané prahové hodnoty. Například můžete definovat vlastní webhooky. Následující příklad webhooku je z šablony Resource Manageru:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

V tomto příkladu přejde výstrahu na Pagerduty.com, když je dosaženo prahové hodnoty.



## <a name="patching-and-operations"></a>Operace a oprav

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Vytvoření sad v existující skupinu prostředků škálování

Vytváření sad škálování v existující prostředek skupiny ještě není možné z portálu Azure, ale můžete zadat existující skupinu prostředků, když nasazení škálování nastavit z šablony Azure Resource Manager. Můžete také zadat existující skupinu prostředků, při vytváření škálování nastavit pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Jsme přesunout škálování nastavena na jinou skupinu prostředků?

Ano, můžete přesunout prostředky sady škálování na nové předplatné nebo skupinu prostředků.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak chcete aktualizovat Moje škálování virtuálních počítačů, nastavena na novou bitovou kopii? Jak spravovat, opravy?

Aktualizace vašeho škálování virtuálních počítačů, nastavena na novou bitovou kopii a ke správě opravy, najdete v části [upgradovat škálovací sadu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Můžete použít operaci obnovení z Image resetovat beze změny bitovou kopii virtuálního počítače? (To znamená, že chcete obnovit virtuální počítač do továrního nastavení místo na novou bitovou kopii.)

Ano, můžete operaci obnovení z Image se resetovat virtuální počítač bez změny bitovou kopii. Ale pokud vaše škálovací sady virtuálních počítačů odkazuje image platformy s `version = latest`, virtuální počítač můžete aktualizovat na novější bitové kopie operačního systému při volání `reimage`.

Další informace najdete v tématu [spravovat všechny virtuální počítače ve škálovací sadu virtuálních počítačů](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>Je možné k integraci sady škálování s Azure Log Analytics?

Ano, můžete po instalaci rozšíření analýzy protokolů do měřítka nastavit virtuální počítače. Tady je příklad rozhraní příkazového řádku Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Požadované workspaceId a workspaceKey můžete najít na portálu OMS. Na stránce Přehled klikněte na dlaždici nastavení. Klikněte na kartu připojené zdroje v horní části.

Poznámka: Pokud váš škálovací sady _upgradePolicy_ je nastaven na hodnotu ručně, potřebujete provést rozšíření ke všem virtuálním počítačům v sadě voláním upgrade na ně. V rozhraní příkazového řádku by to byl _az vmss aktualizace instance_.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak zapnout na Diagnostika spouštění?

Chcete-li na Diagnostika spouštění, nejdřív vytvořte účet úložiště. Pak, přesuňte tento blok JSON sady škálování virtuálního počítače **virtualMachineProfile**a aktualizovat sadu škálování virtuálního počítače:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Když je vytvořen nový virtuální počítač, jak vlastnost virtuálního počítače obsahuje podrobnosti pro snímek obrazovky a tak dále. Tady je příklad:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Vlastnosti virtuálního počítače

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak získat informace o vlastnosti pro každý virtuální počítač bez volání více? Například jak by získat domény selhání u každého 100 virtuálních počítačů v mé škálovací sadu virtuálních počítačů?

Chcete-li získat informace o vlastnosti pro každý virtuální počítač bez volání více, můžete zavolat `ListVMInstanceViews` pomocí rozhraní REST API `GET` na následující identifikátor URI prostředku:

/subscriptions/ < ID_ODBĚRU > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView & $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Můžete předat argumenty jiné rozšíření pro různé virtuální počítače ve škálovací sadě virtuálního počítače?

Ne, nemůžete předat jiné rozšíření argumenty různé virtuální počítače ve škálovací sadě virtuálních počítačů. Rozšíření však může fungovat podle jedinečné vlastnosti virtuálního počítače, že jsou spuštěny v, například jako na název počítače. Rozšíření také můžete dotazu instance metadata na http://169.254.169.254 získat další informace o virtuálním počítači.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Proč se mezery mezi Moje identifikátory virtuálních počítačů a názvy počítačů virtuálních počítačů sady škálování virtuálního počítače? Příklad: 0, 1, 3...

Protože vaše škálovací sady virtuálních počítačů jsou mezery mezi názvy počítačů virtuálních počítačů sady škálování virtuálního počítače a virtuálního počítače ID **overprovision** je nastavena na výchozí hodnotu **true**. Pokud předimenzování je nastavený na **true**, než požadovaná vytvářejí další virtuální počítače. Navíc za virtuální počítače, budou odstraněna. V takovém případě můžete získat nasazení vyšší spolehlivost, ale za cenu souvislý pojmenovávání a souvislý překlad adres (NAT) pravidla. 

Tuto vlastnost lze nastavit **false**. Pro malé virtuálního počítače sady škálování to nemá vliv výrazně spolehlivost nasazení.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaký je rozdíl mezi odstranění virtuálního počítače ve škálovací sadu virtuálních počítačů a rušení přidělení virtuálního počítače? Pokud by měl vybrat jeden z nich?

Hlavní rozdíl mezi odstranění virtuálního počítače ve škálovací sadu virtuálních počítačů a rušení přidělení virtuálního počítače je, že `deallocate` nedojde k odstranění virtuální pevné disky (VHD). Jsou náklady na úložiště přidružené k spuštění `stop deallocate`. Můžete použít jeden z nich pro jednu z následujících důvodů:

- Chcete zastavit platícího výpočetní náklady, ale chcete zachovat stav disku v rámci virtuálních počítačů.
- Chcete spustit sadu virtuálních počítačů rychleji, než může škálovat škálovací sadu virtuálních počítačů.
  - Související s tímto scénářem, jste mohli vytvořit vlastní modul škálování a chcete rychlejší škálování začátku do konce.
- Máte škálovací sadu virtuálních počítačů, nerovnoměrně distribuované domén selhání a aktualizace domény. Může to být proto selektivně odstranit virtuální počítače, nebo proto, že virtuální počítače byly odstraněny po předimenzování. Spuštění `stop deallocate` následuje `start` na virtuálním počítači rozděluje rovnoměrně sad škálování virtuálních počítačů mezi domén selhání nebo aktualizaci domény.

