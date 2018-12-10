---
title: Azure virtual machine scale sets s nejčastější dotazy | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy ohledně škálovacích sad virtuálních počítačů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.custom: na
ms.openlocfilehash: 1bba25d0b7fd6bbe4efeb9c2164fc663b22bed11
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139363"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure virtual machine scale sets s nejčastější dotazy

Získejte odpovědi na nejčastější dotazy ohledně škálovacích sad virtuálních počítačů v Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>TOP – nejčastější dotazy ke škálovacím sadám

**Otázka:** Kolik virtuálních počítačů může obsahovat škálovací sada?

**Odpověď:** Škálovací sada může obsahovat 0 až 1 000 virtuálních počítačů založených na imagích platformy nebo 0 až 600 virtuálních počítačů založených na vlastních imagích.

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

**Odpověď:** Vytvoření a zachycení image virtuálního počítače a pak použít jako zdroj pro svou škálovací sadu. Kurz o tom, jak vytvořit a používat vlastní image virtuálního počítače, můžete použít [rozhraní příkazového řádku Azure](tutorial-use-custom-image-cli.md) nebo [prostředí Azure PowerShell](tutorial-use-custom-image-powershell.md)

**Otázka:** Pokud snížím kapacitu škálovací sady z 20 na 15, které virtuální počítače budou odebrány?

**Odpověď:** Virtuální počítače se ze škálovací sady odebírají rovnoměrně napříč aktualizačními doménami a doménami selhání, aby se maximalizovala dostupnost. Nejprve se odeberou virtuální počítače s nejvyšším ID.

**Otázka:** A co když pak zvýším kapacitu z 15 na 18?

**Odpověď:** Pokud zvýšíte kapacitu na 18, vytvoří se 3 nové virtuální počítače. ID instance virtuálního počítače se přírůstkově zvýší oproti předchozí nejvyšší hodnotě (např. 20, 21, 22). Virtuální počítače se vyvažují mezi doménami selhání a aktualizačními doménami.

**Otázka:** Pokud ve škálovací sadě používám několik rozšíření, je možné vynucovat určitou posloupnost provádění?

**Odpověď:** Ne přímo, ale u rozšíření customScript by váš skript mohl čekat na dokončení jiného rozšíření. Další pokyny k nastavení pořadí rozšíření najdete v blogovém příspěvku o [nastavení pořadí rozšíření ve škálovacích sadách virtuálních počítačů Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**Otázka:** Spolupracují škálovací sady se skupinami dostupnosti Azure?

**Odpověď:** Místní (bez oblastmi) škálovací sada používá *skupin umístění*, které fungují jako sadu implicitní dostupnosti s pěti doménami selhání a pěti aktualizačními doménami. Škálovací sady s více než 100 virtuálních počítačů pokrývají více skupin umístění. Další informace o skupinách umístění najdete v tématu [Práce s velkými škálovacími sadami virtuálních počítačů](virtual-machine-scale-sets-placement-groups.md). Skupina dostupnosti virtuálních počítačů může existovat ve stejné virtuální síti jako škálovací sada virtuálních počítačů. Běžnou konfigurací je umístění virtuálních počítačů řídicích uzlů, které často vyžadují jedinečnou konfiguraci, do skupiny dostupnosti, a datových uzlů do škálovací sady.

**Otázka:** Spolupracují škálovací sady se zónami dostupnosti Azure?

**Odpověď:** Ano! Další informace najdete v tématu [škálovací sady zóny doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatické škálování

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jaké jsou nejlepší postupy pro automatické škálování Azure?

Osvědčené postupy pro automatické škálování, naleznete v tématu [osvědčené postupy pro automatické škálování virtuálních počítačů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Kde najdu názvy metrik pro automatické škálování využívající metriky hostitele?

Názvy metrik pro automatické škálování využívající metriky hostitele, naleznete v tématu [podporované metriky ve službě Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existují nějaké příklady, automatické škálování na základě délky témat a front Azure Service Bus?

Ano. Příklady automatické škálování na základě délky témat a front Azure Service Bus, najdete v článku [běžné metriky automatického škálování Azure monitoru](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Fronty Service Bus použijte následující kód JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Fronty úložiště použijte následující kód JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Nahraďte ukázkové hodnoty s vaším prostředkem Uniform Resource Identifier (identifikátory URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Těchto variant se mám automatického škálování využívající metriky hostitele nebo rozšíření diagnostiky?

Nastavení automatického škálování můžete vytvořit na virtuálním počítači použít metriky na úrovni hostitele nebo metrik na základě operačního systému hosta.

Seznam podporovaných metrik najdete v tématu [běžné metriky automatického škálování Azure monitoru](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Úplnou ukázku pro škálovací sady virtuálních počítačů najdete v tématu [Rozšířená konfigurace škálování pomocí šablon Resource Manageru pro škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

Ukázka používá metrika využití procesoru hostitele úroveň a metriku počet zpráv.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Nastavení pravidla upozornění na škálovací sadu virtuálních počítačů

Můžete vytvořit výstrahy týkající se metrik pro škálovací sady virtuálních počítačů pomocí Powershellu nebo rozhraní příkazového řádku Azure. Další informace najdete v tématu [ukázky Powershellu pro monitorování Azure quickstart](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) a [ukázky rychlý start – multiplatformního rozhraní příkazového řádku Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId škálovací sadu virtuálních počítačů vypadá takto:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.COMPUTE/virtualMachineScaleSets/yourvmssname

Jako metriku k nastavení pro upozornění můžete všechny čítače výkonu virtuálních počítačů. Další informace najdete v tématu [metriky hostovaného operačního systému pro virtuální počítače Windows využívající Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) a [metriky hostovaného operačního systému pro virtuální počítače s Linuxem](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) v [běžné metriky automatického škálování Azure monitoru](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)článku.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak můžu nastavit automatické škálování na škálovací sadu virtuálních počítačů, nastavit pomocí Powershellu?

Nastavení automatického škálování na škálovací sadu virtuálních počítačů pomocí prostředí PowerShell nastavte, naleznete v tématu [automaticky škálovat škálovací sadu virtuálních počítačů](tutorial-autoscale-powershell.md). Můžete také nakonfigurovat automatické škálování s [rozhraní příkazového řádku Azure](tutorial-autoscale-cli.md) a [šablony Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Pokud můžu mít zastaveno (přidělení zrušeno) virtuálního počítače, je tento virtuální počítač spuštěn jako součást operace automatického škálování?

Ne. Pokud pravidla automatického škálování vyžadují dalších instancí virtuálních počítačů jako součást škálovací sady, je vytvořena nová instance virtuálního počítače. Instance virtuálních počítačů, zastavené (přidělení zrušeno) nejsou spuštěny v rámci události automatického škálování. Ale tyto zastaveno (přidělení zrušeno) virtuálních počítačů odstraněna jako součást událost automatického škálování, která ID škálování počtu instancí, stejným způsobem, že všechny instance virtuálních počítačů, můžou se odstranit na základě pořadí instance virtuálního počítače.



## <a name="certificates"></a>Certifikáty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Jak můžu bezpečně dodání certifikátů do virtuálního počítače? Jak můžu zřídit virtuální počítač škálovací sady bezpečně běžet konfigurace certifikátu webu dodán SSL pro web? (Běžné operace obměny certifikát bude téměř stejný jako operace aktualizace konfigurace.) Máte příklad toho, jak to udělat? 

Bezpečně dodání certifikátů do virtuálního počítače, můžete nainstalovat certifikát zákazníka přímo do úložiště certifikátů Windows z trezoru klíčů zákazníka.

Použijte následující kód JSON:

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

Další informace najdete v tématu [vytvořit nebo aktualizovat škálovací sadu virtuálních počítačů nastavte](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Příklad certifikátu podepsaného svým držitelem

1.  Vytvořte certifikát podepsaný svým držitelem v trezoru klíčů.

    Použijte následující příkazy Powershellu:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Tento příkaz umožňuje vstup pro šablonu Azure Resource Manageru.

    Příklad toho, jak vytvořit certifikát podepsaný svým držitelem v trezoru klíčů, naleznete v tématu [scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Změna šablony Resource Manageru.

    Přidejte tuto vlastnost na **virtualMachineProfile**, v rámci virtuálního počítače škálovací sady prostředků:

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
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Můžete zadat pár klíčů SSH pro ověřování SSH pomocí virtuálního počítače škálovací sady s Linuxem ze šablony Resource Manageru?  

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
 
Tento blok JSON se používá v [šablonu pro rychlý start Githubu 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Profil operačního systému se také používá v [šablonu pro rychlý start Githubu grelayhost.json](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Další informace najdete v tématu [vytvořit nebo aktualizovat škálovací sadu virtuálních počítačů nastavte](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak odstranit nepoužívané certifikáty? 

Odebrat nepoužívané certifikáty, odeberte starý certifikát ze seznamu certifikátů trezor. Ponechte všechny certifikáty, které mají zůstat ve vašem počítači v seznamu. Tento certifikát neodebere ze všech virtuálních počítačů. Také nepřidává certifikát na nové virtuální počítače, které jsou vytvořeny ve škálovací sadě virtuálních počítačů. 

Odeberte certifikát z existujících virtuálních počítačů, napíšeme v rozšíření vlastních skriptů se ručně odebrat certifikáty ze svého úložiště certifikátů.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Jak můžu vložit existující veřejný klíč SSH do vrstvy SSH škálovací sady virtuálních počítačů během zřizování? Chci uložit hodnoty veřejného klíče SSH ve službě Azure Key Vault a potom je použijete v šabloně Resource Manageru.

Pokud poskytujete jenom s veřejným klíčem SSH virtuální počítače, není nutné vložit veřejných klíčů ve službě Key Vault. Veřejné klíče nejsou tajného kódu.
 
Veřejné klíče SSH ve formátu prostého textu můžete zadat při vytváření virtuálního počítače s Linuxem:

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
SSH | Ne | Kolekce | Určuje konfiguraci klíče SSH pro operační systém Linux
path | Ano | Řetězec | Určuje soubor Linuxová cesta kde klíče SSH nebo certifikát má být umístěn
data klíče | Ano | Řetězec | Určuje kódování base64 veřejný klíč SSH

Příklad najdete v tématu [šablonu pro rychlý start Githubu 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Při spuštění `Update-AzureRmVmss` po přidání více než jeden certifikát ze stejné služby key vault, zobrazí následující zpráva:
 
>Update-AzureRmVmss: List secret obsahuje opakované instance /subscriptions/ < my-subscription-id > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, což se nepovoluje.
 
To může nastat, pokud se pokusíte do stejného trezoru namísto použití nového certifikátu trezoru pro existující zdrojový trezor je znovu přidat. `Add-AzureRmVmssSecret` Příkaz nebude fungovat správně při přidávání dalších tajných kódů.
 
Pokud chcete přidat další tajné kódy ze stejného trezoru klíčů, aktualizujte seznam $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Očekávaný vstupní struktuře naleznete v tématu [vytvořit nebo aktualizovat sadu virtuálních počítačů](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Hledání objektu škálovací sady virtuálního počítače, který je ve službě key vault tajný klíč. Obnovte certifikát referenci (adresu URL a název úložiště tajných kódů) do seznamu přidružených k trezoru.

> [!NOTE] 
> V současné době nelze odebrat certifikáty z virtuálních počítačů pomocí rozhraní API škálovací sady virtuálních počítačů.
>

Nové virtuální počítače nebudou mít starý certifikát. Virtuální počítače, které mají certifikát a které jsou už nasazené, ale bude mít starý certifikát.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Můžete odeslat certifikáty do škálovací sady bez zadání hesla, když je certifikát v úložišti tajných kódů virtuálních počítačů?

Není potřeba pevně zakódovat hesla ve skriptech. Můžete dynamicky načíst heslo s oprávněními, který použijete ke spuštění skriptu nasazení. Pokud máte skript, který přesouvá certifikát z úložiště tajných kódů klíče trezoru, úložiště tajných kódů `get certificate` příkaz také výstup heslo souboru PFX.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak nastavit vlastnost tajných kódů virtualMachineProfile.osProfile pro škálovací sadu virtuálních počítačů práce? Proč se při nutné zadat absolutní identifikátor URI pro certifikát pomocí vlastnosti certificateUrl musí hodnota sourceVault? 

Odkaz na certifikát vzdálené správy Windows (WinRM) se musí nacházet ve vlastnosti tajných kódů v profilu operačního systému. 

Účelem označující zdrojový trezor je vynucovat zásady na seznamu ACL řízení přístupu, které existují v modelu cloudové služby Azure uživatele. Pokud není zadaný zdrojový trezor, uživatelé, kteří nemají oprávnění k nasazení nebo přístup k tajným klíčům k trezoru klíčů by nebudou moct přes poskytovatele výpočetních prostředků (CRP). Seznamy ACL existují i pro prostředky, které neexistují.

Pokud zadáte ID nesprávný zdrojový trezor ale adresu URL platný trezor klíčů, dojde k chybě při dotazování operace.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Když přidám tajných kódů do existujícího virtuálního počítače škálovací nastavit, některým tajným kódům vložený do existující virtuální počítače nebo pouze do nové? 

Certifikáty se přidávají do všech virtuálních počítačů, i těch, které jsou předem existující. Pokud vaše škálovací sada virtuálních počítačů upgradePolicy vlastnost nastavená na **ruční**, přidání certifikátu do virtuálního počítače při provádění ruční aktualizace na virtuálním počítači.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Kam můžu dát certifikáty pro virtuální počítače s Linuxem?

Informace o nasazení certifikátů pro virtuální počítače s Linuxem najdete v tématu [nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak přidat nové certifikátů trezor pro nový objekt certifikátu?

Pokud chcete přidat do existujícího tajného kódu certifikátů trezor, viz následující příklad Powershellu. Použijte pouze jeden objekt tajného kódu.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co se stane s certifikáty, pokud obnovení z Image virtuálního počítače?

Pokud obnovení z Image virtuálního počítače, certifikáty se odstraní. Obnovování z Image disku odstraní celý operační systém. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co se stane, když odstraníte certifikát z trezoru klíčů?

Pokud tajný kód se odstraní z trezoru klíčů, a pak spusťte `stop deallocate` pro všechny virtuální počítače a potom je znovu spusťte, dojde k chybě. Protože CRP je potřeba načíst tajné klíče z trezoru klíčů, ale není toho schopen, dojde k selhání. V tomto scénáři můžete odstranit certifikáty z modelu škálovací sady virtuálních počítačů. 

Komponenta CRP nezachová tajné klíče zákazníků. Pokud spustíte `stop deallocate` pro všechny virtuální počítače ve škálovací sadě virtuálních počítačů, do mezipaměti se odstraní. V tomto scénáři jsou načíst tajné kódy z trezoru klíčů.

Tento problém není dojde při horizontálním navýšení kapacity, protože je kopie v mezipaměti tajný klíč v Azure Service Fabric (v modelu fabric jeden tenant).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Proč musím zadávat přesné umístění pro adresu URL certifikátu (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), jak je uvedeno v [scénáře zabezpečení clusteru Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Dokumentace ke službě Azure Key Vault hlásí, že REST API služby získat tajný kód by měl vrátit nejnovější verzi tajného kódu, pokud není zadána verze.
 
Metoda | zprostředkovatele identity
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Nahradit {*název tajného kódu*} s názvem a nahrazení {*tajný kód verze*} verzi tajného klíče, které chcete načíst. Verze tajného kódu může být vyloučen. V takovém případě se načte aktuální verzi.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Proč je nutné zadat verzi certifikátu, při použití služby Key Vault?

Účelem požadavek služby Key Vault k zadání certifikátu verze je k němu vymazat uživateli jaký certifikát je nasazený na svých virtuálních počítačích.

Je-li vytvořit virtuální počítač a pak aktualizujte váš tajný klíč v trezoru klíčů, nový certifikát, nebude stažen do vašich virtuálních počítačů. Ale vaše virtuální počítače se na něj odkazovat a nových virtuálních počítačů získáte nový tajný kód. Abyste tomu předešli, musíte odkazovat na verzi tajného klíče.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Můj tým pracuje s několik certifikátů, které jsou distribuovány do nám jako CER veřejných klíčů. Co je nastaven doporučený postup při nasazení těchto certifikátů pro škálovací sadu virtuálních počítačů?

K nasazení .cer nastavené veřejného klíče pro škálovací sadu virtuálních počítačů, můžete vygenerovat soubor .pfx, který obsahuje pouze soubory .cer. Chcete-li to provést, použijte `X509ContentType = Pfx`. Například načíst soubor .cer jako objekt x509Certificate2 v jazyce C# nebo prostředí PowerShell a poté zavolejte metodu. 

Další informace najdete v tématu [X509Certificate.Export – metoda (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Nevidím možnost pro uživatele a zajistěte tak předání certifikáty jako řetězce ve formátu base64. Většina jiných poskytovatelů prostředků mají tuto možnost.

Pro emulaci předávání v certifikátu jako řetězec ve formátu base64, může extrahovat nejnovější verze adresu URL v šabloně Resource Manageru. Zahrňte následující vlastnost JSON šablony Resource Manageru:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Je nutné zabalit certifikáty v objektech JSON v trezorech klíčů?

Škálovací sady virtuálních počítačů a virtuálních počítačů musí být zabalené certifikáty v objekty JSON. 

Podporují se i typ obsahu application/x-pkcs12. 
 
Aktuálně nepodporujeme soubory .cer. Pokud chcete použít soubory .cer, je exportujte do kontejnerů .pfx.



## <a name="compliance-and-security"></a>Dodržování předpisů a zabezpečení

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Jsou virtuální počítače škálovací sady kompatibilní se standardem PCI?

Škálovací sady virtuálních počítačů představují tenkou vrstvu rozhraní API nad CRP. Obě komponenty jsou součástí výpočetní platformy ve stromu služeb Azure.

Z hlediska kompatibility jsou škálovací sady virtuálních počítačů základní součástí výpočetní platformy Azure. S vlastním CRP sdílejí tým, nástroje, procesy, metodiku nasazení, kontrolních mechanismy zabezpečení, kompilaci za běhu (JIT), monitorování, výstrahy a tak dále. Škálovací sady virtuálních počítačů jsou kompatibilní se standardem PCI (Payment Card Industry), protože CRP je součástí aktuální atestace standardu DSS (Data Security Standard) pro PCI.

Další informace najdete na webu [Centrum zabezpečení Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Nemá [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) pracovat se škálovacími sadami virtuálních počítačů?

Ano. Zobrazí se několik ukázkových šablon MSI v šabloně QuickStart pro Azure. Linux: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Rozšíření

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak odstranit rozšíření škálovací sady virtuálních počítačů?

Chcete-li odstranit rozšíření škálovací sady virtuálních počítačů, použijte následující příklad Powershellu:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Můžete najít hodnotu extensionName v `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>Je k dispozici virtuálního počítače škálovací sady příkladu šablony, která spolupracuje se službou Log Analytics?

Pro škálovací sadu virtuálních počítačů nastavte příkladu šablony, která spolupracuje se službou Log Analytics, najdete ve druhém příkladu v [nasazení clusteru Azure Service Fabric a povolte monitorování pomocí Log Analytics](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Zdá se, že rozšíření spustit souběžně na škálovací sady virtuálních počítačů. To způsobí, že Moje rozšíření vlastních skriptů k selhání. Co můžete dělat na tento problém vyřešit?

Další informace o nastavení posloupnosti rozšíření ve škálovacích sadách virtuálních počítačů najdete v tématu [nastavení posloupnosti rozšíření ve škálovacích sadách virtuálních počítačů Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Jak resetuji heslo pro virtuální počítače v mé škálovací sady virtuálních počítačů?

Existují dva hlavní způsoby, jak změnit heslo pro virtuální počítače ve škálovacích sadách.

- Změna modelu škálovací sady virtuálních počítačů přímo. K dispozici s Compute rozhraní API 2017-12-01 a novějších.

    Aktualizace přihlašovacích údajů správce přímo v modelu škálovací sady (například pomocí Průzkumníka prostředků Azure, Powershellu nebo rozhraní příkazového řádku). Jakmile se škálovací sada je aktualizovaná, všechny nové virtuální počítače mají nové přihlašovací údaje. Stávající virtuální počítače pouze mít nové přihlašovací údaje, pokud se obnoví z Image. 

- Resetovat heslo pomocí rozšíření přístupu virtuálních počítačů.

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


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak mohu přidat rozšíření na všechny virtuální počítače v mé škálovací sady virtuálních počítačů?

Pokud je aktualizace zásada nastavená na **automatické**, opětovného nasazení šablony pomocí nové vlastnosti rozšíření aktualizuje všechny virtuální počítače.

Pokud je aktualizace zásada nastavená na **ruční**, nejprve aktualizovat rozšíření a potom ručně aktualizovat všechny instance ve vašich virtuálních počítačích.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Pokud rozšíření přidružené k existující škálovací sady virtuálních počítačů jsou aktualizovány, jsou existující vliv na virtuální počítače? (To znamená, že se virtuální počítače *není* odpovídá modelu škálovací sady virtuálního počítače?) Nebo se ignorují.? Když existující počítač opraven služby je nebo znovu připojit bitovou kopii, jsou skripty, které jsou aktuálně nakonfigurované na škálovací sadu virtuálních počítačů provést nebo jsou skripty, které byly nakonfigurovány při prvotním vytvoření virtuálního počítače použít?

Pokud je nastavená definice rozšíření virtuálního počítače škálovací dojde k aktualizaci modelu a hodnota upgradePolicy vlastnost nastavena na **automatické**, aktualizuje se virtuální počítače. Pokud hodnota upgradePolicy nastavena **ruční**, rozšíření se označí jako neodpovídá modelu. 

Pokud existujícího virtuálního počítače je opraven služby, se jeví jako restartování a rozšíření nejsou spusťte znovu. Pokud se je znovu připojit bitovou kopii, je jako je výměna jednotky operačního systému zdrojového obrázku. Všechny specializace z nejnovější model, jako je například rozšíření, se spouští.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak můžu připojit virtuální počítač škálovací sady do domény služby Active Directory?

K připojení virtuálního počítače škálovací sady do domény služby Active Directory (AD), můžete definovat rozšíření. 

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
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Moje rozšíření škálovací sady virtuálních počítačů se pokouší nainstalovat něco, co vyžaduje restartování počítače. Například "commandToExecute": "- ExecutionPolicy Unrestricted powershell.exe Install-WindowsFeature – název služby FS-Resource-Manager – IncludeManagementTools"

Pokud vaše rozšíření škálovací sady virtuálních počítačů se pokouší nainstalovat cokoliv, co vyžaduje restartování, můžete použít rozšíření pro Desired State Configuration automatizace Azure (Automatizace DSC). Pokud je operační systém Windows Server 2012 R2, Azure získává v nastavení Windows Management Frameworku (WMF) 5.0, restartování počítače a pak bude pokračovat s konfigurací. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak zapnout antimalware v mé škálovací sady virtuálních počítačů?

Chcete-li zapnout antimalware na škálovací sadu virtuálních počítačů, použijte následující příklad Powershellu:

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

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Musím spustit vlastní skript, který je hostován v účtu privátního úložiště. Spuštění skriptu se úspěšně, pokud úložiště je veřejné, ale při pokusu použít sdílený přístupový podpis (SAS), se nezdaří. Zobrazí se zpráva: "Chybí povinné parametry pro platný podpis sdíleného přístupu". Odkaz + SAS funguje z místní v prohlížeči.

K provedení vlastní skript, který je hostován v účtu privátního úložiště, nastavte chráněná nastavení pro klíč účtu úložiště a názvem. Další informace najdete v tématu [vlastní skript rozšíření pro Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Sítě
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Je možné přiřadit skupinu zabezpečení sítě (NSG) ke škálovací sadě, tak, aby se vztahuje na všechny síťové karty pro virtuální počítač v sadě?

Ano. Skupina zabezpečení sítě můžete použít přímo na škálovací sadu pomocí odkazů v dnssettings profilu sítě. Příklad:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jak to provést prohození virtuálních IP adres pro škálovací sady virtuálních počítačů ve stejném předplatném a stejné oblasti?

Pokud máte dvě škálovací sady virtuálních počítačů s Azure Load Balancer front endů a jsou ve stejném předplatném a oblasti, můžete zrušit přidělení veřejné IP adresy z každé z nich a přiřadit k druhému. Zobrazit [prohození virtuálních IP adres: nasazení modrá zelená v Azure Resource Manageru](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) třeba. To neznamená zpoždění úroveň ale prostředky jsou přidělené v síti jejich přidělení. Rychlejší možností je použít Azure Application Gateway pomocí dvou fondů back-endu a pravidel směrování. Alternativně může hostovat vaši aplikaci s [služby Azure App service](https://azure.microsoft.com/services/app-service/) poskytující podporu pro rychlé přepínání slotů pracovního a produkčního prostředí.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Jak určit rozsah privátních IP adres pro statického přidělování privátní IP adresu?

IP adresy jsou vybrány z podsítě, který zadáte. 

Metoda přidělení virtuálního počítače škálovací sady IP adres je vždy "dynamické", ale to neznamená, že tyto IP adresy se mohou měnit. V tomto případě "dynamické" znamená pouze to, že v požadavku PUT nezadávejte IP adresu. Zadejte statickou nastavení s použitím podsíť. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Nasazení virtuálního počítače škálovací sady do existující virtuální sítě Azure 

Pokud chcete nasadit škálovací sadu virtuálních počítačů nastavení pro existující virtuální síť Azure, najdete v článku [nasazení virtuálního počítače škálovací sady na existující virtuální síť](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Jak přidat IP adresu z prvního virtuálního počítače ve škálovací sady do výstupu šablony virtuálních počítačů?

Chcete-li přidat IP adresu z prvního virtuálního počítače ve škálovací sady do výstupu šablony virtuálních počítačů, přečtěte si téma [Azure Resource Manageru: Get virtual machine scale sets s privátní IP adresy](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Můžete používat škálovací sady s Akcelerovanými síťovými službami?

Ano. Pokud chcete používat akcelerované síťové služby, nastavte enableAcceleratedNetworking na hodnotu true ve vaší škálovací nastavení networkInterfaceConfigurations. Například
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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak lze nakonfigurovat servery DNS používá škálovací sada?

Pokud chcete vytvořit škálovací sadu s vlastní konfigurací DNS virtuálních počítačů, přidejte paketů JSON části Networkinterfaceconfigurations do části networkInterfaceConfigurations škálovací sady. Příklad:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak vytvořit škálovací sadu přiřadit veřejnou IP adresu pro každý virtuální počítač

Pokud chcete vytvořit škálovací sadu virtuálního počítače, který se přiřadí veřejná IP adresa pro každý virtuální počítač, ujistěte se, že verze rozhraní API prostředku Microsoft.Compute/virtualMachineScaleSets je 2017-03-30 a přidejte _publicipaddressconfiguration_ JSON paket škálovací nastavení části ipConfigurations. Příklad:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Můžete nakonfigurovat škálovací sady pro práci s více aplikačních bran?

Ano. Můžete přidat id prostředku pro více fondy adres back-end služba Application Gateway na _applicationGatewayBackendAddressPools_ v seznamu _ipConfigurations_ část škálovací nastavení sítě profil.

## <a name="scale"></a>Měřítko

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>V případě co bych měl(a) vytvořit virtuální počítač škálovací sadu s méně než dva virtuální počítače?

Jedním z důvodů k vytvoření virtuálního počítače škálovací sadu s méně než dva virtuální počítače může být použití elastických vlastností škálovací sady virtuálních počítačů. Můžete třeba nasadit škálovací sadu s nulovým počtem virtuálních počítačů definovat infrastrukturu bez nutnosti platit spouštění virtuálních počítačů virtuálních počítačů. Potom až budete připravení nasadit virtuální počítače, zvýšit "kapacitu" škálovací virtuálních počítačů nastavit požadovaný počet produkčních instancí.

Dalším důvodem, které můžete vytvořit virtuální počítač škálovací sadu s méně než dva virtuální počítače je, pokud máte obavy, nižší dostupnost než v použití skupiny dostupnosti u oddělených virtuálních počítačů. Škálovací sady virtuálních počítačů vám nabízejí způsob práce s nediferencovanými výpočetními jednotkami, které jsou vzájemně zastupitelné. Tato uniformita je klíčovým rozdílem mezi škálovacími sadami virtuálních počítačů a skupiny dostupnosti. U řady bezstavových úloh do not track jednotlivé jednotky. Pokud se zatížení sníží, můžete vertikálně snížit kapacitu na jednu výpočetní jednotku a potom vertikálně navýšit kapacitu k mnoha při zvýšení zátěže.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Změna počtu virtuálních počítačů ve škálovací sadě virtuálních počítačů

Chcete-li změnit počet virtuálních počítačů ve škálovací sady na webu Azure Portal virtuálních počítačů, z virtuálního počítače škálovací nastavit vlastnosti oddílu, klikněte v okně "Škálování" a pomocí posuvníku. Další způsoby, jak změnit počet instancí, naleznete v tématu [změnit počet instancí škálovací sady virtuálních počítačů](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak definovat vlastní oznámení pro když se dosáhne určité prahové hodnoty

Máte určitou volnost v jak zpracovávat výstrahy pro zadané prahové hodnoty. Například můžete definovat vlastní webhooky. Následující příklad webhooku je z šablony Resource Manageru:

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

V tomto příkladu výstraha odkazuje Pagerduty.com po dosažení prahové hodnoty.



## <a name="patching-and-operations"></a>Operace a opravy

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Jak vytvořit škálovací sadu v existující skupinu prostředků?

Vytvoření škálovací sady v existující prostředek skupiny ještě není možné z portálu Azure portal, ale při nasazení škálovací sady z šablony Azure Resource Manageru můžete zadat existující skupinu prostředků. Při vytváření škálovací sady s použitím prostředí Azure PowerShell nebo rozhraní příkazového řádku můžete také zadat existující skupinu prostředků.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Můžeme přesunout škálovací sady do jiné skupiny prostředků?

Ano, můžete přesunout, škálovací sada prostředků pro nové předplatné nebo skupinu prostředků.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak chcete aktualizovat mé škálovací sady do nové image virtuálních počítačů? Jak můžu spravovat, použití dílčích oprav?

K aktualizaci škálovací sady do nové bitové kopie vašich virtuálních počítačů a spravujte použití dílčích oprav, najdete v části [Upgrade škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Můžete resetovat beze změny image virtuálního počítače použít operace obnovení z Image? (To znamená, že chci, aby virtuální počítač obnovit do továrního nastavení, nikoli na novou bitovou kopii.)

Ano, můžete použít operace obnovení z Image resetovat beze změny image virtuálního počítače. Nicméně pokud vaše škálovací sada virtuálních počítačů odkazuje na imagi platformy s `version = latest`, váš virtuální počítač můžete aktualizovat na novější image operačního systému při volání `reimage`.

Další informace najdete v tématu [spravovat všechny virtuální počítače ve škálovací sadě virtuálních počítačů](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>Je možné integrovat škálovací sady pomocí Azure Log Analytics?

Ano, můžete po instalaci rozšíření Log Analytics na stupnici nastavit virtuální počítače. Tady je příklad rozhraní příkazového řádku Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Požadované ID pracovního prostoru a workspaceKey najdete v pracovním prostoru Log Analytics z portálu Azure portal. Na stránce Přehled klikněte na dlaždici nastavení. Klikněte na kartu připojené zdroje v horní části.

Poznámka: Pokud ve škálovací sadě _upgradePolicy_ nastavený na ruční, budete muset použijte rozšíření pro všechny virtuální počítače v sadě voláním upgrade na ně. V rozhraní příkazového řádku by to byl _az vmss update-instances_.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak můžu zapnout diagnostiku?

Zapnout diagnostiku spouštění, nejprve vytvořte účet úložiště. Vložte tento blok JSON ve škálovací sadě virtuálních počítačů **virtualMachineProfile**a aktualizovat škálovací sadu virtuálních počítačů:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Když se vytvoří nový virtuální počítač, vlastnost InstanceView virtuální počítač zobrazuje podrobnosti o snímku obrazovky a tak dále. Tady je příklad:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Vlastnosti virtuálního počítače

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak získat informace o vlastnosti pro každý virtuální počítač bez více volání? Příklad jak by získat doménu selhání pro každý 100 virtuálních počítačů v mé škálovací sady virtuálních počítačů?

Chcete-li získat informace o vlastnosti pro každý virtuální počítač bez více volání, můžete volat `ListVMInstanceViews` tímto způsobem rozhraní REST API `GET` na následující identifikátor URI prostředku:

/subscriptions/ < subscription_id > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView & $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Můžete předat argumenty jiné rozšíření do jiných virtuálních počítačů ve škálovací sadě virtuálních počítačů?

Ne, nemůžete předat argumenty jiné rozšíření jiných virtuálních počítačů ve škálovací sadě virtuálních počítačů. Rozšíření však může fungovat podle jedinečné vlastnosti virtuálního počítače, na kterém běží, třeba jako na název počítače. Rozšíření můžete dotazovat také instance metadat na http://169.254.169.254 zobrazíte další informace o virtuálním počítači.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Proč jsou mezery mezi Moje názvy počítačů virtuálního počítače škálovací sady virtuálních počítačů a ID virtuálního počítače? Příklad: 0, 1, 3...

Vzhledem k tomu, že vaše škálovací sada virtuálních počítačů jsou mezery mezi názvy počítačů virtuálního počítače škálovací sady virtuálních počítačů a virtuálních počítačů ID **nadměrné** je nastavena na výchozí hodnotu **true**. Pokud předimenzování nastavená na **true**, než je požadováno se vytvoří další virtuální počítače. Další virtuální počítače, budou odstraněna. V tomto případě získáte spolehlivosti nasazení ale za cenu souvislých překládání adres (NAT) a souvislých názvů pravidla. 

Tuto vlastnost lze nastavit **false**. Pro škálovací sady malých virtuálních počítačů to nemá vliv na výrazně spolehlivosti nasazení.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaký je rozdíl mezi odstranění virtuálního počítače ve škálovací sadě virtuálních počítačů a rušení přidělení virtuálního počítače? Pokud jeden z nich mám zvolit?

Hlavní rozdíl mezi odstranění virtuálního počítače ve škálovací sadě virtuálních počítačů a rušení přidělení virtuálního počítače je, že `deallocate` nedojde k odstranění virtuálních pevných disků (VHD). Existují náklady na úložiště spojené s běžící `stop deallocate`. Můžete použít jeden z nich pro jednu z následujících důvodů:

- Chcete zastavit platit náklady na výpočetní výkon, ale chcete zachovat stav disku virtuálních počítačů.
- Chcete spustit sadu virtuálních počítačů rychleji, než by mohla horizontální navýšení kapacity škálovací sady virtuálních počítačů.
  - Související s tímto scénářem, pravděpodobně jste vytvořili vlastní modul automatického škálování a chcete rychlejší škálování začátku do konce.
- Máte škálovací sadu virtuálních počítačů, které nerovnoměrně distribuovaná napříč doménami selhání a aktualizačních doménách. To může být kvůli selektivně odstranění virtuální počítače, nebo virtuální počítače byly odstraněny po předimenzování. Spuštění `stop deallocate` následovaný `start` na virtuálním počítači škálovací sady se rovnoměrně distribuuje virtuálních počítačů napříč doménami selhání a aktualizačních doménách.

