---
title: Nejčastější dotazy ke škálovacím sadám virtuálních počítačů Azure
description: Získejte odpovědi na nejčastější dotazy týkající se škálovacích sad virtuálních strojů v Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: manayar
ms.openlocfilehash: 74195e83e17140b67ac060e1791c580e90e720f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534435"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Nejčastější dotazy ke škálovacím sadám virtuálních počítačů Azure

Získejte odpovědi na nejčastější dotazy týkající se škálovacích sad virtuálních strojů v Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Nejčastější nejčastější dotazy pro škálovací sady

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Kolik virtuálních počítačů může obsahovat škálovací sada?

Škálovací sada může mít 0 až 1 000 virtuálních počítačů na základě iobrazek platformy nebo 0 až 600 virtuálních počítačích na základě vlastních iobrazek.

### <a name="are-data-disks-supported-within-scale-sets"></a>Podporují se ve škálovacích sadách datové disky?

Ano. Škálovací sada může definovat konfiguraci připojených datových jednotek, která se použije na všechny virtuální počítače v sadě. Další informace najdete v tématu [Škálovací sady Azure a připojené datové disky](virtual-machine-scale-sets-attached-disks.md). Další možnosti ukládání dat zahrnují:

* Soubory Azure (sdílené jednotky SMB)
* Jednotka operačního systému
* Dočasné jednotky (místní úložiště, nezálohované pomocí Azure Storage)
* Datová služba Azure (např. tabulky Azure, objekty blob Azure)
* Externí datová služba (např. vzdálená databáze)

### <a name="which-azure-regions-support-scale-sets"></a>Které oblasti Azure podporují škálovací sady?

Všechny oblasti podporují škálovací sady.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Jak se vytváří škálovací sada s použitím vlastní image?

Vytvořte a zachyťte bitovou kopii virtuálního počítače a pak ji použijte jako zdroj pro škálovací sadu. Pro kurz o tom, jak vytvořit a používat vlastní image virtuálního počítače, můžete použít [Azure CLI](tutorial-use-custom-image-cli.md) nebo [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Pokud snížím kapacitu škálovací sady z 20 na 15, které virtuální počítače budou odebrány?

Virtuální počítače se ze škálovací sady odebírají rovnoměrně napříč aktualizačními doménami a doménami selhání, aby se maximalizovala dostupnost. Nejprve se odeberou virtuální počítače s nejvyšším ID.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>A co když pak zvýším kapacitu z 15 na 18?

Pokud zvýšíte kapacitu na 18, vytvoří se 3 nové virtuální počítače. ID instance virtuálního počítače se přírůstkově zvýší oproti předchozí nejvyšší hodnotě (např. 20, 21, 22). Virtuální počítače se vyvažují mezi doménami selhání a aktualizačními doménami.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Pokud ve škálovací sadě používám několik rozšíření, je možné vynucovat určitou posloupnost provádění?

Ano, můžete použít [sekvencování rozšíření škálovací](virtual-machine-scale-sets-extension-sequencing.md)sady .

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Spolupracují škálovací sady se skupinami dostupnosti Azure?

Regionální (nezónová) škálovací sada používá *skupiny umístění*, které fungují jako implicitní sada dostupnosti s pěti doménami selhání a pěti aktualizačními doménami. Škálování sady více než 100 virtuálních počítačů rozpětí více umístění skupin. Další informace o skupinách umístění najdete v tématu [Práce s velkými škálovacími sadami virtuálních počítačů](virtual-machine-scale-sets-placement-groups.md). Skupina dostupnosti virtuálních počítačů může existovat ve stejné virtuální síti jako škálovací sada virtuálních počítačů. Běžnou konfigurací je umístění virtuálních počítačů řídicích uzlů, které často vyžadují jedinečnou konfiguraci, do skupiny dostupnosti, a datových uzlů do škálovací sady.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Fungují škálovací sady se zónami dostupnosti Azure?

Ano! Další informace naleznete v [dokumentu zóny škálovací sady](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatické škálování

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jaké jsou doporučené postupy pro automatické škálování Azure?

Doporučené postupy pro automatické škálování najdete v [tématu Doporučené postupy pro automatické škálování virtuálních počítačů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Kde najdu názvy metrik pro automatické škálování, které používá metriky založené na hostiteli?

Názvy metrik pro automatické škálování, které používají metriky založené na hostiteli, najdete v [tématu Podporované metriky s Azure Monitorem](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existují nějaké příklady automatického škálování na základě tématu azure service bus a délky fronty?

Ano. Příklady automatického škálování na základě tématu Azure Service Bus a délky fronty najdete v tématu [Azure Monitor automatické škálování běžné metriky](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Pro frontu služby Service Bus použijte následující JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Pro frontu úložiště použijte následující JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Nahraďte ukázkové hodnoty identifikátory prostředků Uniform Resource Identifiers (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Mám se automaticky škálovat pomocí metrik založených na hostiteli nebo rozšíření diagnostiky?

Můžete vytvořit nastavení automatického škálování na virtuálním počítači pro použití metriky na úrovni hostitele nebo metriky založené na hostovaném operačním systému.

Seznam podporovaných metrik najdete v tématu [Automatické škálování běžné metriky Azure Monitoru](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Úplnou ukázku škálovacích sad virtuálních počítačů najdete [v tématu Pokročilá konfigurace automatického škálování pomocí šablon Správce prostředků pro škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

Ukázka používá metriku procesoru na úrovni hostitele a metriku počtu zpráv.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Jak nastavím pravidla výstrah na škálovací sadě virtuálních strojů?

Můžete vytvářet výstrahy na metriky pro škálovací sady virtuálních strojů prostřednictvím PowerShellu nebo Azure CLI. Další informace najdete v [tématu Azure Monitor PowerShell úvodní ukázky](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) a [Azure Monitor cross-platformní příkazcli rychlé spuštění ukázky](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId škálovací sady virtuálních strojů vypadá takto:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Jako metriku můžete nastavit výstrahu jako metriku, pro kterou můžete zvolit libovolný čítač výkonu virtuálního počítače. Další informace najdete [v tématu metriky operačního systému hosta pro virtuální počítače s Windows založené na Správci prostředků](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) a [metriky hostovaného operačního systému pro virtuální počítače s Linuxem](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) v článku [automatické škálování běžné metriky Azure Monitor.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak nastavím automatické škálování na škálovací sadě virtuálních strojů pomocí PowerShellu?

Pokud chcete nastavit automatické škálování na škálovací sadě virtuálních strojů pomocí PowerShellu, přečtěte si informace [o automatickém škálování škálovací sady virtuálních strojů](tutorial-autoscale-powershell.md). Automatické škálování můžete také nakonfigurovat pomocí [předlohy](tutorial-autoscale-template.md) [Azure CLI](tutorial-autoscale-cli.md) a Azure


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Pokud jsem zastavil (navštívený) virtuální ho virtuálního zařízení, je tento virtuální virtuální měn spuštěn jako součást operace automatického škálování?

Ne. Pokud pravidla automatického škálování vyžadují další instance virtuálních virtuálních bylin jako součást škálovací sady, vytvoří se nová instance virtuálního aplikace. Instance virtuálních virtuálních zařízení, které jsou zastavené (navrácené), nejsou spuštěny jako součást události automatického škálování. Tyto zastavené (naváděné) virtuální chody však mohou být odstraněny jako součást události automatického škálování, která se škáluje v počtu instancí, stejným způsobem, jakým může být odstraněna jakákoli instance virtuálního počítače na základě pořadí ID instance virtuálního zařízení.



## <a name="certificates"></a>Certifikáty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Jak bezpečně poslat certifikát na virtuální ms?

Chcete-li bezpečně doložit certifikát do virtuálního účtu, můžete nainstalovat certifikát zákazníka přímo do úložiště certifikátů systému Windows z trezoru klíčů zákazníka.

Použijte následující JSON:

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

Další informace najdete v [tématu Vytvoření nebo aktualizace škálovací sady virtuálních strojů](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Jak se používají certifikáty podepsané svým držitelem zřízené pro clustery Azure Service Fabric?
Pro nejnovější příklad použijte následující příkaz azure CLI v rámci azure shell, přečtěte si service fabrics CLI modul Příklad dokumentace, která bude vytištěna na stdout:

```azurecli
az sf cluster create -h
```

Certifikáty podepsané svým držitelem nelze použít pro distribuovaný vztah důvěryhodnosti poskytované certifikační autoritou a neměly by být používány pro žádný cluster Service Fabric určený k hostování podnikových produkčních řešení. Další pokyny k zabezpečení service fabric, přečtěte si [azure service fabric bezpečnostní postupy](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) a service fabric scénáře zabezpečení [clusteru](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Můžu určit dvojici klíčů SSH, která se použije pro ověřování SSH se škálovací sadou virtuálních strojů Linuxu ze šablony Správce prostředků?

Ano. ROZHRANÍ REST API pro **osProfile** je podobné standardnímu rozhraní REST API virtuálního virtuálního rozhraní.

Zahrnout **osProfile** do šablony:

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

Tento blok JSON se používá v [této šabloně rychlého startu Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Další informace najdete v [tématu Vytvoření nebo aktualizace škálovací sady virtuálních strojů](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak odstraním zastaralé certifikáty?

Chcete-li odebrat zastaralé certifikáty, odeberte starý certifikát ze seznamu certifikátů úschovny. Všechny certifikáty, které chcete v počítači ponechat, ponechejte v seznamu. Tím se certifikát neodebere ze všech virtuálních počítačů. Také nepřidá certifikát do nových virtuálních počítačů, které jsou vytvořeny ve škálovací sadě virtuálních strojů.

Chcete-li odebrat certifikát z existujících virtuálních počítačů, použijte vlastní rozšíření skriptu k ručnímu odebrání certifikátů z úložiště certifikátů.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Jak vložím existující veřejný klíč SSH do vrstvy škálování virtuálních strojů SSH během zřizování?

Pokud virtuálním mům poskytujete jenom s veřejným klíčem SSH, není nutné umístit veřejné klíče do trezoru klíčů. Veřejné klíče nejsou tajné.

Při vytváření virtuálního počítače s Linuxem můžete zadat veřejné klíče SSH ve formátu prostého textu:

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
}
```

linuxNázev prvku Konfigurace | Požaduje se | Typ | Popis
--- | --- | --- | ---
Ssh | Ne | Kolekce | Určuje konfiguraci klíče SSH pro operační systém Linux.
cesta | Ano | Řetězec | Určuje cestu k souborům Linuxu, kde by měly být umístěny klíče Nebo certifikát SSH.
Keydata | Ano | Řetězec | Určuje veřejný klíč SSH kódovaný na základně64.

Například viz [šablona rychlého startu 101-vm-sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Při spuštění `Update-AzVmss` po přidání více než jeden certifikát ze stejného trezoru klíčů se zobrazí následující zpráva:

>Update-AzVmss: Tajný klíč seznamu obsahuje opakované\<instance /subscriptions/ my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, který je zakázán.

K tomu může dojít, pokud se pokusíte znovu přidat stejný trezor namísto použití nového certifikátu úschovny pro existující zdrojový trezor. Příkaz `Add-AzVmssSecret` nefunguje správně, pokud přidáváte další tajné klíče.

Chcete-li přidat další tajné kódy ze stejného trezoru klíčů, aktualizujte seznam $vmss.properties.osProfile.secrets[0].vaultCertificates.

Očekávanou vstupní strukturu najdete v [tématu Vytvoření nebo aktualizace sady virtuálních strojů](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Najděte tajný klíč v objektu škálovací sady virtuálních strojů, který je v trezoru klíčů. Potom přidejte odkaz na certifikát (adresu URL a název tajného úložiště) do seznamu přidruženého k úschovně.

> [!NOTE]
> V současné době nelze odebrat certifikáty z virtuálních počítačů pomocí rozhraní API škálovací sady virtuálních strojů.
>

Nové virtuální společnosti nebudou mít starý certifikát. Virtuální společnosti, které mají certifikát a které jsou již nasazeny bude mít starý certifikát.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Můžu vysunout certifikáty do škálovací sady virtuálních strojů bez zadání hesla, když je certifikát v tajném úložišti?

Není nutné pevně kódovat hesla ve skriptech. Můžete dynamicky načítat hesla s oprávněními, která používáte ke spuštění skriptu nasazení. Pokud máte skript, který přesune certifikát z trezoru klíčů `get certificate` tajného úložiště, příkaz úložiště tajných kódů také vyskladní heslo souboru Pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak funguje vlastnost Secrets nástroje virtualMachineProfile.osProfile pro škálovací sadu virtuálních strojů? Proč potřebuji hodnotu sourceVault, když musím zadat absolutní identifikátor URI pro certifikát pomocí vlastnosti certificateUrl?

Odkaz na certifikát vzdálené správy systému Windows (WinRM) musí být k dispozici ve vlastnosti Tajné kódy profilu operačního systému.

Účelem označení zdrojového trezoru je vynutit zásady seznamu řízení přístupu (ACL), které existují v modelu cloudové služby Azure uživatele. Pokud není zadán zdrojový trezor, uživatelé, kteří nemají oprávnění k nasazení nebo přístupu k tajným kódům do trezoru klíčů, by mohli prostřednictvím zprostředkovatele výpočetních prostředků (CRP). AcLs existují i pro prostředky, které neexistují.

Pokud zadáte nesprávné ID zdrojového trezoru, ale platnou adresu URL trezoru klíčů, bude při dotazování operace hlášena chyba.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Pokud přidám tajné klíče do existující škálovací sady virtuálních strojů, jsou tajné klíče vloženy do existujících virtuálních počítačů nebo jenom do nových?

Certifikáty se přidají ke všem virtuálním počítačům, i k již existujícím. Pokud vaše škálovací sada virtuálního počítače upgradePolicy vlastnost je nastavena na **ruční**, certifikát se přidá do virtuálního počítače při provádění ruční aktualizace na virtuálním počítači.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Kam mám umístit certifikáty pro virtuální počítače s Linuxem?

Informace o tom, jak nasadit certifikáty pro virtuální počítače s Linuxem, najdete v [tématu Nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaného zákazníkem](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak přidám nový certifikát úschovny k novému objektu certifikátu?

Pokud chcete přidat certifikát trezoru k existujícímu tajnému klíči, podívejte se na následující příklad Prostředí PowerShell. Použijte pouze jeden tajný objekt.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co se stane s certifikáty, pokud znovu obrázek virtuálního virtuálního montova?

Pokud znovu image virtuálního virtuálního míse, certifikáty se odstraní. Reimaging odstraní celý disk operačního systému.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co se stane, když odstraníte certifikát z trezoru klíčů?

Pokud je tajný klíč odstraněn z trezoru `stop deallocate` klíčů a potom spustíte všechny virtuální počítače a pak je znovu spustíte, dojde k chybě. K chybě dochází, protože crp potřebuje načíst tajné klíče z trezoru klíčů, ale nemůže. V tomto scénáři můžete odstranit certifikáty z modelu škálovací sady virtuálních strojů.

Součást CRP neuchovává tajné klíče zákazníků. Pokud spustíte `stop deallocate` pro všechny virtuální počítače ve škálovací sadě virtuálních strojů, dojde k odstranění mezipaměti. V tomto scénáři jsou načteny tajné klíče z trezoru klíčů.

K tomuto problému nedochází při škálování, protože je v mezipaměti kopii tajného klíče v Azure Service Fabric (v modelu klienta s jedním prostředků).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Proč je nutné při použití trezoru klíčů zadat verzi certifikátu?

Účelem požadavku trezoru klíčů k určení verze certifikátu je objasnit uživateli, jaký certifikát se nasadí na jejich virtuálních počítačích.

Pokud vytvoříte virtuální počítač a pak aktualizujete tajný klíč v trezoru klíčů, nový certifikát se nestáhne do virtuálních počítačů. Ale vaše virtuální počítače se zobrazí odkazovat na něj a nové virtuální počítače získat nový tajný klíč. Chcete-li tomu zabránit, je nutné odkazovat na tajnou verzi.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Můj tým pracuje s několika certifikáty, které jsou nám distribuovány jako veřejné klíče .cer. Jaký je doporučený přístup pro nasazení těchto certifikátů do škálovací sady virtuálních strojů?

Chcete-li nasadit .cer veřejné klíče do škálovací sady virtuálních strojů, můžete vygenerovat soubor .pfx, který obsahuje pouze soubory CER. Chcete-li to `X509ContentType = Pfx`provést, použijte . Například načtěte soubor CER jako objekt x509Certificate2 v prostředí C# nebo PowerShell a pak metodu zavolejte.

Další informace naleznete v tématu [X509Certificate.Export Method (X509ContentType, String).](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx))

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Jak předám certifikáty jako řetězce base64?

Chcete-li emulovat předávání certifikátu jako řetězec base64, můžete extrahovat nejnovější verzi adresy URL v šabloně Správce prostředků. Do šablony Správce prostředků zahrňte následující vlastnost JSON:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Musím zalamovat certifikáty v objektech JSON do trezorů klíčů?

Ve škálovacích sadách virtuálních počítačů a virtuálních počítačích musí být certifikáty zabaleny do objektů JSON.

Podporujeme také typ obsahu aplikace/x-pkcs12.

V současné době nepodporujeme soubory .cer. Chcete-li použít soubory CER, exportujte je do kontejnerů .pfx.



## <a name="compliance-and-security"></a>Dodržování předpisů a zabezpečení

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Jsou škálovací sady virtuálních počítačů kompatibilní s pci?

Škálovací sady virtuálních počítačů představují tenkou vrstvu rozhraní API nad CRP. Obě komponenty jsou součástí výpočetní platformy ve stromu služeb Azure.

Z hlediska kompatibility jsou škálovací sady virtuálních počítačů základní součástí výpočetní platformy Azure. S vlastním CRP sdílejí tým, nástroje, procesy, metodiku nasazení, kontrolních mechanismy zabezpečení, kompilaci za běhu (JIT), monitorování, výstrahy a tak dále. Škálovací sady virtuálních počítačů jsou kompatibilní se standardem PCI (Payment Card Industry), protože CRP je součástí aktuální atestace standardu DSS (Data Security Standard) pro PCI.

Další informace najdete na webu [Centrum zabezpečení Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Fungují [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) se škálovacími sadami virtuálních strojů?

Ano. Můžete vidět některé příklady MSI šablony v Azure Úvodní šablony pro [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) a [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Odstranění 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Budou při odstranění instancí respektovány zámky, které nastavím na místě v instancích škálovací sady virtuálních strojů?

Na webu Azure Portal máte možnost odstranit jednotlivé instance nebo hromadné odstranění výběrem více instancí. Pokud se pokusíte odstranit jednu instanci, která má zámek na místě, zámek je respektována a nebude možné odstranit instanci. Pokud však hromadně vyberete více instancí a některá z těchto instancí má zámek na místě, nebudou uzamčení respektována a všechny vybrané instance budou odstraněny. 
 
V Azure CLI máte jenom možnost odstranit jednotlivé instance. Pokud se pokusíte odstranit jednu instanci, která má zámek na místě, zámek je respektována a nebude možné odstranit tuto instanci. 

## <a name="extensions"></a>Rozšíření

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak odstraním rozšíření škálovací sady virtuálních strojů?

Chcete-li odstranit rozšíření škálovací sady virtuálních strojů, použijte následující příklad prostředí PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Hodnotu extensionName naleznete `$vmss`v .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Existuje příklad šablony škálovací sady virtuálních strojů, který se integruje s protokoly Azure Monitoru?

Příklad šablony sady škálování virtuálních strojů, který se integruje s protokoly Azure Monitor, najdete v druhém příkladu v [tématu Nasazení clusteru Azure Service Fabric a povolit monitorování pomocí protokolů Azure Monitor](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak přidám rozšíření ke všem virtuálním počítačům ve škálovací sadě virtuálních strojů?

Pokud je zásada aktualizace **nastavena**na automatické , opětovné nasazení šablony s novými vlastnostmi rozšíření aktualizuje všechny virtuální počítače.

Pokud je zásada aktualizace nastavená na **ruční**, nejprve aktualizujte rozšíření a pak ručně aktualizujte všechny instance ve virtuálních počítačích.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Pokud jsou rozšíření přidružená k existující škálovací sadě virtuálního počítače aktualizována, jsou ovlivněny stávající virtuální počítače?

Pokud je aktualizována definice rozšíření v modelu škálovací sady virtuálního počítače a vlastnost upgradePolicy je nastavena na **automatickou**, aktualizuje virtuální počítače. Pokud je vlastnost upgradePolicy nastavena na **ruční**, rozšíření jsou označena jako neodpovídající modelu.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Jsou rozšíření spuštěna znovu, když je existující počítač uzdraven nebo reimaged?

Pokud je existující virtuální počítač uzdravený službou, zobrazí se jako restartování a rozšíření se znovu nespustí. Pokud je virtuální hod reimaged, proces je podobné nahrazení jednotky operačního systému se zdrojovou bitovou kopii. Všechny specializace z nejnovějšího modelu, jako jsou rozšíření, jsou spuštěny znovu.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak se připojím k škálovací sadě virtuálních strojů k doméně služby Active Directory?

Chcete-li připojit škálovací sadu virtuálních strojů k doméně služby Active Directory (AD), můžete definovat rozšíření.

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Moje rozšíření škálovací sady virtuálních strojů se pokouší nainstalovat něco, co vyžaduje restart.

Pokud vaše rozšíření škálovací sady virtuálního počítače se pokouší nainstalovat něco, co vyžaduje restartování, můžete použít rozšíření Azure Automation Desired State Configuration (Automation DSC). Pokud je operační systém Windows Server 2012 R2, Azure natáhne 5.0 nastavení rozhraní Windows Management Framework (WMF), restartuje počítač a pak pokračuje v konfiguraci.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak můžu zapnout antimalwarový software ve škálovací sadě virtuálních strojů?

Pokud chcete ve škálovací sadě virtuálního počítače zapnout antimalwarový software, použijte následující příklad PowerShellu:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Jak spustím vlastní skript, který je hostovaný v účtu soukromého úložiště?

Chcete-li spustit vlastní skript, který je hostovaný v účtu soukromého úložiště, nastavte chráněná nastavení pomocí klíče a názvu účtu úložiště. Další informace naleznete [v tématu Custom Script Extension](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Hesla

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Jak resetuji heslo pro virtuální počítače ve škálovací sadě virtuálních strojů?

Existují dva hlavní způsoby, jak změnit heslo pro virtuální počítače ve škálovacích sadách.

- Změňte model škálovací sady virtuálních strojů přímo. K dispozici s rozhraním API 2017-12-01 a novějším.

    Aktualizujte přihlašovací údaje správce přímo v modelu škálovací sady (například pomocí Průzkumníka prostředků Azure, PowerShellu nebo CLI). Po aktualizaci škálovací sady mají všechny nové virtuální počítačy nová pověření. Existující virtuální chody mají pouze nová pověření, pokud jsou reimaged.

- Resetujte heslo pomocí rozšíření pro přístup k virtuálním montovantům.

    Použijte následující příklad PowerShellu:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Síťové služby

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Je možné přiřadit skupinu zabezpečení sítě (NSG) k škálovací sadě tak, aby se vztahovala na všechny síťové karty virtuálních počítačů v sadě?

Ano. Skupinu zabezpečení sítě lze použít přímo na škálovací sadu odkazováním v části networkInterfaceConfigurations v profilu sítě. Příklad:

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
                            },
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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jak můžu udělat VIP swap pro škálovací sady virtuálních strojů ve stejném předplatném a stejné oblasti?

Pokud máte dvě škálovací sady virtuálních počítačů s front-endy nástroje pro vyrovnávání zatížení Azure a jsou ve stejném předplatném a oblasti, můžete z každé z nich navrátit veřejné IP adresy a přiřadit k druhému. Viz [VIP Swap: Blue-green nasazení ve Správci prostředků Azure](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) například. To znamená zpoždění, i když jako prostředky jsou deallocated / přiděleny na úrovni sítě. Rychlejší možností je použití Azure Application Gateway se dvěma back-endovými fondy a pravidlem směrování. Případně můžete hostovat svou aplikaci se [službou Azure App,](https://azure.microsoft.com/services/app-service/) která poskytuje podporu pro rychlé přepínání mezi pracovními a produkčními sloty.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Jak lze zadat rozsah privátních IP adres, které se mají použít pro statické přidělení privátní IP adresy?

Adresy IP jsou vybrány z zadané podsítě.

Metoda přidělení IP adres škálovací sady virtuálních počítačů je vždy "dynamická", ale to neznamená, že se tyto IP adresy můžou změnit. V tomto případě "dynamické" znamená pouze, že nezadáte IP adresu v požadavku PUT. Určete statickou sadu pomocí podsítě.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Jak nasadím škálovací sadu virtuálních strojů do existující virtuální sítě Azure?

Pokud chcete nasadit škálovací sadu virtuálních strojů do existující virtuální sítě Azure, přečtěte si informace [o nasazení škálovací sady virtuálních strojů do existující virtuální sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Mohu škálovací sady používat se zrychlenou sítí?

Ano. Chcete-li použít zrychlené sítě, nastavte enableAcceleratedNetworking na true v nastavení síťové konfigurace škálovací sady. Například

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
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak lze nakonfigurovat servery DNS používané škálovací sadou?

Chcete-li vytvořit škálovací sadu virtuálních počítačů s vlastní konfigurací DNS, přidejte paket DNSSettings JSON do oddílu škálovatsíť NetworkInterfaceConfigurations. Příklad:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak můžu nakonfigurovat škálovací sadu tak, aby každému virtuálnímu virtuálnímu počítače přiřadila veřejnou IP adresu?

Chcete-li vytvořit škálovací sadu virtuálních počítačů, která každému virtuálnímu počítači přiřadí veřejnou IP adresu, ujistěte se, že verze rozhraní API prostředku Microsoft.Compute/virtualMachineScaleSets je 2017-03-30 a přidejte paket JSON _s veřejnou IP adresou_ do oddílu konfigurace ipconfigurations škálovací sady. Příklad:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Můžu nakonfigurovat škálovací sadu tak, aby fungovala s více aplikačními bránami?

Ano. ID prostředků pro více back-endových adres aplikační brány můžete přidat do seznamu _applicationGatewayBackendAddressPools_ v části _ipConfigurations_ v profilu sítě škálovací sady.

## <a name="scale"></a>Škálování

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>V jakém případě bych vytvořil škálovací sadu virtuálních strojů s méně než dvěma virtuálními počítači?

Jedním z důvodů, proč vytvořit škálovací sadu virtuálních strojů s méně než dvěma virtuálními počítači, by bylo použití elastických vlastností škálovací sady virtuálních strojů. Můžete například nasadit škálovací sadu virtuálních strojů s nulovými virtuálními počítači k definování infrastruktury bez placení provozních nákladů virtuálních počítačů. Potom, když jste připraveni k nasazení virtuálních počítačů, zvýšit "kapacitu" škálování virtuálního počítače nastavit počet produkčních instancí.

Dalším důvodem, proč můžete vytvořit škálovací sadu virtuálních počítačů s méně než dvěma virtuálními počítači, je, že se méně zajímáte o dostupnost než při používání sady dostupnosti s diskrétními virtuálními počítači. Škálovací sady virtuálních strojů vám poskytují způsob, jak pracovat s nediferencovanými výpočetními jednotkami, které jsou zastupitelné. Tato jednotnost je klíčovým rozlišovacím programem pro škálovací sady virtuálních strojů oproti skupiny dostupnosti. Mnoho úloh bez stavů nesleduje jednotlivé jednotky. Pokud zatížení klesne, můžete vertikálně navýšit kapacitu na jednu výpočetní jednotku a pak vertikálně navýšit kapacitu na mnoho, když se zvýší zatížení.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Jak změním počet virtuálních počítačů ve škálovací sadě virtuálních strojů?

Chcete-li změnit počet virtuálních počítačů ve škálovací sadě virtuálních strojů na webu Azure Portal, v části vlastnosti škálovací sady virtuálních strojů klikněte na okno "Škálování" a použijte panel posuvníku.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak lze definovat vlastní výstrahy pro dosažení určitých prahových hodnot?

Máte určitou flexibilitu v tom, jak zpracováváte výstrahy pro zadané prahové hodnoty. Můžete například definovat vlastní webhooky. Následující příklad webhooku je ze šablony Správce prostředků:

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
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
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
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Opravy a provoz

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Je možné vytvořit škálovací sadu v existující skupině prostředků?

Ano, můžete vytvořit škálovací sadu v existující skupině prostředků.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Je možné přesunout škálovací sadu do jiné skupiny prostředků?

Ano, prostředky škálovací sady můžete přesunout do nového předplatného nebo skupiny prostředků.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak aktualizovat měřítko virtuálního počítače nastavené na novou bitovou kopii? Jak mohu spravovat opravy?

Pokud chcete aktualizovat škálovací sadu virtuálních strojů na novou bitovou kopii a spravovat opravy, přečtěte si informace [o upgradu škálovací sady virtuálních strojů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Můžu použít operaci reimage k resetování virtuálního virtuálního zařízení bez evidence beze změny image? (To znamená, že chci obnovit virtuální ho na tovární nastavení, nikoli na novou bitovou kopii.)

Ano, operaci reimage můžete použít k resetování virtuálního virtuálního zařízení bez změny image. Pokud však škálovací sada virtuálního počítače `version = latest`odkazuje na bitovou kopii platformy , `reimage`může se virtuální počítač při volání aktualizovat na novější bitovou kopii operačního systému .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Je možné integrovat škálovací sady s protokoly Azure Monitor?

Ano, můžete nainstalovat rozšíření Azure Monitor na virtuální počítače škálovací sady. Tady je příklad Azure CLI:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Požadovaný pracovní prostor Id a pracovní klíč najdete v pracovním prostoru Analýzy protokolů na webu Azure Portal. Na stránce Přehled klikněte na dlaždici Nastavení. Klikněte na hoře na kartu Připojené zdroje.

> [!NOTE]
> Pokud vaše škálovací sada _upgradeZásady_ je nastavena na ruční, je třeba použít rozšíření pro všechny virtuální počítače v sadě voláním upgrade na ně. V CLI by to _bylo az vmss update-instance_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Řešení potíží

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak zapnu diagnostiku spouštění?

Chcete-li zapnout diagnostiku spouštění, nejprve vytvořte účet úložiště. Potom vložte tento blok JSON do škálovací sady virtuálních strojů **virtualMachineProfile**a aktualizujte škálovací sadu virtuálních strojů:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Když se vytvoří nový virtuální počítač, instanceView vlastnost virtuálního počítači zobrazí podrobnosti pro snímek obrazovky a tak dále. Tady je příklad:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Vlastnosti virtuálního počítače

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak získám informace o vlastnostech pro každý virtuální počítač bez vícenásobné volání? Například jak bych získal doménu selhání pro každý ze 100 virtuálních počítačů ve škálovací sadě virtuálních strojů?

Chcete-li získat informace o vlastnostech pro každý `ListVMInstanceViews` virtuální počítač `GET` bez více volání, můžete volat pomocí rozhraní REST API na následující identifikátor URI prostředku:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Můžu předat různé argumenty rozšíření různým virtuálním počítačům ve škálovací sadě virtuálních strojů?

Ne, nemůžete předat různé argumenty rozšíření různým virtuálním počítačům ve škálovací sadě virtuálních strojů. Rozšíření však může fungovat na základě jedinečných vlastností virtuálního počítače, které jsou spuštěny na, například na název počítače. Rozšíření také můžete dotaz ovat http://169.254.169.254 metadata instance na získat další informace o virtuálním počítači.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Proč jsou mezery mezi názvy virtuálních počítačů škálovací sady virtuálních počítačů a ID virtuálních počítačů? Například: 0, 1, 3...

Mezi názvy virtuálních počítačů škálovací sady virtuálních počítačů a ID virtuálních počítačů jsou mezery, protože vlastnost **overprovision škálování** virtuálních počítačů je nastavena na výchozí hodnotu **true**. Pokud je nadměrné zřizování nastaveno na **hodnotu true**, vytvoří se více virtuálních virtuálních disponií, než bylo požadováno. Další virtuální aplikace se pak odstraní. V takovém případě získáte zvýšenou spolehlivost nasazení, ale na úkor souvislých pravidel pro pojmenování a souvislé překlady síťových adres (NAT).

Tuto vlastnost můžete nastavit na **hodnotu false**. U malých škálovacích sad virtuálních strojů to nemá významný vliv na spolehlivost nasazení.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaký je rozdíl mezi odstraněním virtuálního počítače ve škálovací sadě virtuálního počítače a zrušením přidělení virtuálního počítače? Kdy si mám vybrat jednu před druhou?

Hlavní rozdíl mezi odstraněním virtuálního počítače ve škálovací sadě virtuálního počítače `deallocate` a zrušením přidělení virtuálního počítače je, že neodstraní virtuální pevné disky (VND). S provozem `stop deallocate`jsou spojeny náklady na úložiště . Můžete použít jeden nebo druhý z jednoho z následujících důvodů:

- Chcete přestat platit výpočetní náklady, ale chcete zachovat stav disku virtuálních počítačů.
- Chcete spustit sadu virtuálních počítačů rychleji, než byste mohli škálovat škálovací sadu virtuálních strojů.
  - V souvislosti s tímto scénářem jste pravděpodobně vytvořili vlastní modul automatického škálování a chcete rychlejší komplexní měřítko.
- Máte škálovací sadu virtuálních strojů, která je nerovnoměrně rozložena mezi doménami selhání nebo aktualizačními doménami. Může to být proto, že jste selektivně odstranili virtuální ho dýně nebo virtuální chody po nadměrném zřizování. Spuštění `stop deallocate` následuje `start` na škálovací sadě virtuálních strojů rovnoměrně distribuuje virtuální počítače mezi doménami selhání nebo aktualizačními doménami.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Jak pořídím snímek instance škálovací sady virtuálních strojů?
Vytvořte snímek z instance škálovací sady virtuálních strojů.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Vytvořte spravovaný disk ze snímku.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
