---
title: Nejčastější dotazy ke škálovacím sadám virtuálních počítačů Azure
description: Získejte odpovědi na nejčastější dotazy týkající se služby Virtual Machine Scale Sets v Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: faq
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 8170cfcbbf200c6ba5030aff5716f46b537d8c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080467"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Nejčastější dotazy ke škálovacím sadám virtuálních počítačů Azure

Získejte odpovědi na nejčastější dotazy týkající se služby Virtual Machine Scale Sets v Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Nejčastější dotazy k sadám škálování

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Kolik virtuálních počítačů může obsahovat škálovací sada?

Sada škálování může mít 0 až 1 000 virtuálních počítačů založených na imagích platforem nebo 0 až 600 virtuálních počítačů na základě vlastních imagí.

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

Vytvořte a zaznamenejte image virtuálního počítače a pak ji použijte jako zdroj pro sadu škálování. Kurz o tom, jak vytvořit a použít vlastní image virtuálního počítače, můžete použít v [Azure CLI](tutorial-use-custom-image-cli.md) nebo [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Pokud snížím kapacitu škálovací sady z 20 na 15, které virtuální počítače budou odebrány?

Ve výchozím nastavení se virtuální počítače ze sady škálování rovnoměrně odeberou v rámci zón dostupnosti (Pokud je sada škálování nasazená v konfiguraci oblastí) a domény selhání, aby se maximalizovala dostupnost. Nejprve se odeberou virtuální počítače s nejvyšším ID.

Pořadí odebrání virtuálního počítače můžete změnit zadáním [zásad škálování](virtual-machine-scale-sets-scale-in-policy.md) na úrovni pro sadu škálování.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>A co když pak zvýším kapacitu z 15 na 18?

Pokud zvýšíte kapacitu na 18, vytvoří se 3 nové virtuální počítače. ID instance virtuálního počítače se přírůstkově zvýší oproti předchozí nejvyšší hodnotě (např. 20, 21, 22). Virtuální počítače jsou vyvážené napříč doménami selhání.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Pokud ve škálovací sadě používám několik rozšíření, je možné vynucovat určitou posloupnost provádění?

Ano, můžete použít [sekvencování rozšíření](virtual-machine-scale-sets-extension-sequencing.md)sady škálování.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Spolupracují škálovací sady se skupinami dostupnosti Azure?

Oblastní sada pro škálování (mimo oblast) používá *skupiny umístění*, které fungují jako implicitní Skupina dostupnosti s pěti doménami selhání a pěti aktualizačními doménami. Sady škálování s více než 100 virtuálními počítači přesahují více skupin umístění. Další informace o skupinách umístění najdete v tématu [Práce s velkými škálovacími sadami virtuálních počítačů](virtual-machine-scale-sets-placement-groups.md). Skupina dostupnosti virtuálních počítačů může existovat ve stejné virtuální síti jako škálovací sada virtuálních počítačů. Běžnou konfigurací je umístění virtuálních počítačů řídicích uzlů, které často vyžadují jedinečnou konfiguraci, do skupiny dostupnosti, a datových uzlů do škálovací sady.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Fungují sady škálování s využitím zón dostupnosti Azure?

Ano! Další informace najdete v [dokumentu zóna sady škálování](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatické škálování

### <a name="what-are-best-practices-for-azure-autoscale"></a>Co jsou osvědčené postupy pro automatické škálování Azure?

Osvědčené postupy pro automatické škálování najdete v tématu [osvědčené postupy pro automatické škálování virtuálních počítačů](../azure-monitor/platform/autoscale-best-practices.md).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Kde najdu názvy metrik pro automatické škálování, které používá metriky založené na hostiteli?

Názvy metrik pro automatické škálování, které používá metriky založené na hostiteli, najdete v tématu [podporované metriky s Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Existují nějaké příklady automatického škálování na základě Azure Service Busho tématu a délky fronty?

Ano. Příklady automatického škálování na základě Azure Service Busho tématu a délky fronty najdete v tématu [Azure monitor automatické škálování běžných metrik](../azure-monitor/platform/autoscale-common-metrics.md).

Pro Service Bus frontu použijte následující kód JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Pro frontu úložiště použijte následující kód JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Nahraďte příklady hodnot pomocí identifikátorů URI (Uniform Resource Identifier) prostředků.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Je vhodné automatické škálování pomocí metrik založených na hostiteli nebo rozšíření diagnostiky?

Na virtuálním počítači můžete vytvořit nastavení automatického škálování, které bude používat metriky na úrovni hostitele nebo metriky na základě hostovaného operačního systému.

Seznam podporovaných metrik najdete v tématu [Azure monitor automatické škálování běžných metrik](../azure-monitor/platform/autoscale-common-metrics.md).

Úplnou ukázku pro Virtual Machine Scale Sets najdete v tématu [Pokročilá konfigurace automatického škálování pomocí Správce prostředků šablon pro Virtual Machine Scale Sets](../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md).

Ukázka používá metriku CPU na úrovni hostitele a metriku počtu zpráv.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Návody nastavit pravidla upozornění pro sadu škálování virtuálního počítače?

Pomocí PowerShellu nebo rozhraní příkazového řádku Azure můžete vytvářet upozornění na metriky pro služby Virtual Machine Scale Sets. Další informace najdete v tématu ukázky rychlého startu ukázek [Azure monitor PowerShellu](../azure-monitor/samples/powershell-samples.md#create-metric-alerts) a [Azure monitor pro rychlé zprovoznění CLI pro různé platformy](../azure-monitor/samples/cli-samples.md#work-with-alerts).

Parametrem targetresourceid sady škálování virtuálního počítače vypadá takto:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Můžete zvolit libovolný čítač výkonu virtuálního počítače jako metriku pro nastavení výstrahy. Další informace najdete v článku [metriky hostovaného operačního systému Správce prostředků pro virtuální počítače s Windows](../azure-monitor/platform/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) a [metriky hostovaného operačního systému pro virtuální počítače](../azure-monitor/platform/autoscale-common-metrics.md#guest-os-metrics-linux-vms) se systémem Linux v článku věnovaném [Azure monitor automatické škálování běžných metrik](../azure-monitor/platform/autoscale-common-metrics.md) .

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Návody nastavit automatické škálování pro sadu škálování virtuálního počítače pomocí PowerShellu?

Pokud chcete nastavit automatické škálování pro sadu škálování virtuálního počítače pomocí PowerShellu, přečtěte si téma [Automatické škálování sady škálování virtuálních počítačů](tutorial-autoscale-powershell.md). Automatické škálování můžete nakonfigurovat taky pomocí [Azure CLI](tutorial-autoscale-cli.md) a [šablon Azure](tutorial-autoscale-template.md) .


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Pokud jsem virtuální počítač přestal (navrácený), je tento virtuální počítač spuštěný jako součást operace automatického škálování?

Ne. Pokud pravidla automatického škálování vyžadují další instance virtuálních počítačů v rámci sady škálování, vytvoří se nová instance virtuálního počítače. Instance virtuálních počítačů, které jsou zastavené (přidělení zrušeno), se nespouští jako součást události automatického škálování. Zastavené (navrácené) virtuální počítače se ale můžou odstranit jako součást události automatického škálování, která se škáluje podle počtu instancí stejným způsobem, jakým se může libovolná instance virtuálního počítače odstranit na základě pořadí ID instance virtuálního počítače.



## <a name="certificates"></a>Certifikáty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Návody bezpečně dodávat certifikát do virtuálního počítače?

K bezpečnému odeslání certifikátu do virtuálního počítače můžete nainstalovat certifikát zákazníka přímo do úložiště certifikátů Windows z trezoru klíčů zákazníka.

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

Kód podporuje systémy Windows a Linux.

Další informace najdete v tématu [Vytvoření nebo aktualizace sady škálování virtuálních počítačů](/rest/api/compute/virtualmachinescalesets/createorupdate).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Návody používat certifikáty podepsané svým držitelem zřízené pro clustery Azure Service Fabric?
V posledním příkladu použijte následující příkaz Azure CLI v prostředí Azure Shell, přečtěte si dokumentaci k příkladu modulu CLI služby Service Fabric, která se vytiskne na stdout:

```azurecli
az sf cluster create -h
```

Certifikáty podepsané svým držitelem se nedají použít pro distribuovanou důvěryhodnost poskytovanou certifikační autoritou a neměla by se používat pro žádný Service Fabric cluster určený k hostování podnikových produkčních řešení. Další Service Fabric doprovodné materiály k zabezpečení najdete v článku [osvědčené postupy zabezpečení Azure Service Fabric](../security/fundamentals/service-fabric-best-practices.md) a [Service Fabric scénáře zabezpečení clusteru](../service-fabric/service-fabric-cluster-security.md).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Můžu zadat pár klíčů SSH, který se má použít pro ověřování SSH se sadou škálování virtuálního počítače se systémem Linux ze šablony Správce prostředků?

Ano. REST API pro **osProfile** se podobá standardnímu REST API virtuálního počítače.

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

Tento blok JSON se používá v [této šabloně Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Další informace najdete v tématu [Vytvoření nebo aktualizace sady škálování virtuálních počítačů](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Návody odebrat zastaralé certifikáty?

Pokud chcete odebrat zastaralé certifikáty, odeberte starý certifikát ze seznamu certifikáty trezoru. Ponechte všechny certifikáty, které chcete v tomto počítači v seznamu zůstat. Tím se certifikát neodebere ze všech virtuálních počítačů. Certifikát také nepřidá do nových virtuálních počítačů, které jsou vytvořeny v sadě škálování virtuálního počítače.

Pokud chcete odebrat certifikát z existujících virtuálních počítačů, pomocí rozšíření vlastních skriptů ručně odeberte certifikáty z úložiště certifikátů.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Při zřizování Návody vložit stávající veřejný klíč SSH do vrstvy protokolu SSH sady škálování virtuálního počítače?

Pokud vytváříte virtuální počítače pouze s veřejným klíčem SSH, nemusíte do Key Vault umístit veřejné klíče. Veřejné klíče nejsou tajné.

Veřejné klíče SSH můžete zadat jako prostý text při vytváření virtuálního počítače se systémem Linux:

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

název elementu linuxConfiguration | Požaduje se | Typ | Description
--- | --- | --- | ---
protokoly | No | Kolekce | Určuje konfiguraci klíče SSH pro operační systém Linux.
program | Ano | Řetězec | Určuje cestu k souboru pro Linux, kde se mají najít klíče SSH nebo certifikát.
keyData | Ano | Řetězec | Určuje veřejný klíč SSH kódovaný ve formátu base64.

Příklad najdete v tématu [Šablona pro rychlý Start pro 101-VM-sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Při spuštění `Update-AzVmss` po přidání více než jednoho certifikátu ze stejného trezoru klíčů se zobrazí následující zpráva:

>Update-AzVmss: list Secret obsahuje opakované instance/Subscriptions/ \<my-subscription-id> /resourceGroups/Internal-RG-dev/Providers/Microsoft.KeyVault/Vaults/Internal-keyvault-dev, které nejsou povoleny.

K tomu může dojít, pokud se pokusíte znovu přidat stejný trezor namísto použití nového certifikátu trezoru pro existující zdrojový trezor. `Add-AzVmssSecret`Pokud přidáváte další tajné kódy, příkaz nefunguje správně.

Chcete-li přidat další tajné kódy ze stejného trezoru klíčů, aktualizujte $vmss. Properties. osProfile. tajných kódů [0]. vaultCertificates list.

Očekávanou vstupní strukturu najdete v tématu [Vytvoření nebo aktualizace sady virtuálních počítačů](/rest/api/compute/virtualmachinescalesets/createorupdate).

Vyhledejte tajný klíč v objektu sady škálování virtuálního počítače, který je v trezoru klíčů. Pak přidejte odkaz na certifikát (adresa URL a název úložiště tajného klíče) do seznamu přidruženého k trezoru.

> [!NOTE]
> V současné době nemůžete odebrat certifikáty z virtuálních počítačů pomocí rozhraní API pro škálování sady virtuálních počítačů.
>

Nové virtuální počítače nebudou mít starý certifikát. Virtuální počítače s certifikátem, které jsou už nasazené, ale budou mít starý certifikát.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Je možné nabízet certifikáty do sady škálování virtuálních počítačů bez zadání hesla, pokud je certifikát v úložišti tajných dat?

Ve skriptech nemusíte vytvářet hesla s pevným kódem. Hesla můžete dynamicky načítat pomocí oprávnění, která používáte ke spuštění skriptu nasazení. Pokud máte skript, který přesouvá certifikát z trezoru klíčů úložiště tajného kódu, pak příkaz tajné úložiště obsahuje `get certificate` také výstup hesla souboru. pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak funguje vlastnost tajných kódů virtualMachineProfile. osProfile pro sadu škálování virtuálního počítače? Proč potřebuji hodnotu sourceVault, když je nutné zadat absolutní identifikátor URI pro certifikát pomocí vlastnosti certificateUrl?

V vlastnosti tajných kódů profilu operačního systému musí být přítomen odkaz na certifikát Vzdálená správa systému Windows (WinRM).

Účelem naznačení zdrojového trezoru je vymáhat zásady seznamu řízení přístupu (ACL), které existují v modelu Azure Cloud Service. Pokud není zadaný zdrojový trezor, uživatelé, kteří nemají oprávnění k nasazení nebo přístupu k tajným klíčům k trezoru klíčů, by mohli být prostřednictvím poskytovatele výpočetních prostředků (CRP). Seznamy ACL existují i pro prostředky, které neexistují.

Pokud zadáte nesprávné ID úložiště zdroje, ale platnou adresu URL trezoru klíčů, při dotazování na operaci se nahlásí chyba.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Pokud přidávám tajným klíčům do existující sady škálování virtuálních počítačů, jsou tajné klíče vloženy do stávajících virtuálních počítačů nebo pouze do nových?

Certifikáty se přidávají do všech virtuálních počítačů, dokonce i těch, které už existují. Pokud je vlastnost upgradePolicy sady škálování virtuálního počítače nastavená na **Ruční**, certifikát se přidá do virtuálního počítače, když na virtuálním počítači provedete ruční aktualizaci.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Kam můžu umístit certifikáty pro virtuální počítače se systémem Linux?

Informace o tom, jak nasadit certifikáty pro virtuální počítače se systémem Linux, najdete v tématu [nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Návody přidat nový certifikát trezoru do nového objektu Certificate?

Pokud chcete přidat certifikát trezoru k existujícímu tajnému kódu, přečtěte si následující příklad PowerShellu. Použijte pouze jeden tajný objekt.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co se stane s certifikáty při obnovení bitové kopie virtuálního počítače?

Pokud virtuální počítač převedete do bitové kopie, certifikáty se odstraní. Obnovování imagí odstraní celý disk s operačním systémem.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co se stane, když odstraníte certifikát z trezoru klíčů?

Pokud se tajný klíč odstraní z trezoru klíčů a potom se spustí `stop deallocate` pro všechny virtuální počítače a znovu se spustí, dojde k chybě. K selhání dojde, protože CRP potřebuje načíst tajné kódy z trezoru klíčů, ale nemůže. V tomto scénáři můžete certifikáty odstranit z modelu sady škálování virtuálních počítačů.

Komponenta CRP neuchovává tajné kódy zákazníků. Pokud spustíte `stop deallocate` u všech virtuálních počítačů v sadě škálování virtuálního počítače, mezipaměť se odstraní. V tomto scénáři se tajné klíče načítají z trezoru klíčů.

K tomuto problému se nemůžete při horizontálním navýšení kapacity dostat z důvodu nedostatku kopie tajného kódu do mezipaměti v Azure Service Fabric (v modelu tenanta s jedním Fabric).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Proč musím při použití Key Vault zadat verzi certifikátu?

Účelem Key Vault požadavků na určení verze certifikátu je, aby byl uživatel jasný, který certifikát je nasazený na svých virtuálních počítačích.

Pokud vytvoříte virtuální počítač a potom aktualizujete tajný klíč v trezoru klíčů, nový certifikát se nestáhne do vašich virtuálních počítačů. Ale vaše virtuální počítače se jeví k odkazování a nové virtuální počítače získají nový tajný klíč. Aby k tomu nedocházelo, je nutné, abyste odkazovali na tajnou verzi.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Můj tým spolupracuje s několika certifikáty, které jsou distribuovány jako veřejné klíče. cer. Jaký je doporučený postup nasazení těchto certifikátů do sady škálování virtuálního počítače?

Pokud chcete nasadit veřejné klíče. cer do sady škálování virtuálních počítačů, můžete vygenerovat soubor. pfx, který obsahuje jenom soubory. cer. K tomu použijte `X509ContentType = Pfx` . Například načtěte soubor. CER jako objekt x509Certificate2 v jazyce C# nebo PowerShell a potom zavolejte metodu.

Další informace naleznete v tématu [Metoda certifikátu x509. Export (X509ContentType, String)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?view=netcore-3.1#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Návody certifikáty předávat jako řetězce base64?

Pro emulaci předávání certifikátu jako řetězce Base64 můžete v šabloně Správce prostředků získat nejnovější verzi URL. Do šablony Správce prostředků zahrňte následující vlastnost JSON:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Musím v úložištích klíčů v objektech JSON zabalit certifikáty?

Ve virtuálních počítačích a virtuálních počítačích musí být certifikáty zabaleny v objektech JSON.

Podporujeme také typ obsahu application/x-PKCS12.

V tuto chvíli nepodporujeme soubory. cer. Chcete-li použít soubory. CER, exportujte je do kontejnerů. pfx.



## <a name="compliance-and-security"></a>Dodržování předpisů a zabezpečení

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Jsou kompatibilní se standardem PCI, Virtual Machine Scale Sets?

Škálovací sady virtuálních počítačů představují tenkou vrstvu rozhraní API nad CRP. Obě komponenty jsou součástí výpočetní platformy ve stromu služeb Azure.

Z hlediska kompatibility jsou škálovací sady virtuálních počítačů základní součástí výpočetní platformy Azure. S vlastním CRP sdílejí tým, nástroje, procesy, metodiku nasazení, kontrolních mechanismy zabezpečení, kompilaci za běhu (JIT), monitorování, výstrahy a tak dále. Škálovací sady virtuálních počítačů jsou kompatibilní se standardem PCI (Payment Card Industry), protože CRP je součástí aktuální atestace standardu DSS (Data Security Standard) pro PCI.

Další informace najdete na webu [Centrum zabezpečení Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Pracují [spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/overview.md) se službou Virtual Machine Scale Sets?

Ano. V šablonách rychlého startu Azure pro [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) a [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)se můžete podívat na příklady šablon MSI.

## <a name="deleting"></a>odstraňování

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Budou při odstraňování instancí dodrženy zámky nastavené na instancích sady škálování virtuálních počítačů?

Na webu Azure Portal máte možnost Odstranit jednotlivou instanci nebo hromadné odstranění tím, že vyberete více instancí. Pokud se pokusíte odstranit jednu instanci, která má zámek, je dodržen zámek a nebudete moci instanci odstranit. Pokud ale hromadně vyberete víc instancí a kterákoli z těchto instancí má zámek, zámky se nerespektují a všechny vybrané instance se odstraní.

V Azure CLI máte možnost jenom odstranit jednotlivou instanci. Pokud se pokusíte odstranit jednu instanci, která má zámek, bude zachován zámek a tuto instanci nebudete moci odstranit.

## <a name="extensions"></a>Rozšíření

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Návody odstranit rozšíření sady škálování virtuálního počítače?

Pokud chcete odstranit rozšíření sady škálování virtuálního počítače, použijte následující příklad PowerShellu:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Hodnotu rozšíření můžete najít v `$vmss` .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Je zde příklad šablony sady škálování virtuálních počítačů, který se integruje s protokoly Azure Monitor?

Příklad šablony pro sadu škálování virtuálního počítače, který se integruje s protokoly Azure Monitor, najdete v druhém příkladu v tématu [nasazení clusteru Azure Service Fabric a povolení monitorování pomocí protokolů Azure monitor](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Návody přidat rozšíření ke všem virtuálním počítačům v sadě škálování virtuálního počítače?

Pokud je zásada aktualizace nastavená na hodnotu **automaticky**, šablona se znovu nasadí s novými vlastnostmi rozšíření všechny virtuální počítače.

Pokud je zásada aktualizace nastavená na **Ruční**, nejdřív aktualizujte rozšíření a pak ručně aktualizujte všechny instance virtuálních počítačů.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Pokud jsou rozšíření přidružená k existující sadě škálování virtuálních počítačů aktualizovaná, jsou ovlivněné stávající virtuální počítače?

Pokud je definice rozšíření v modelu sady škálování virtuálního počítače aktualizována a vlastnost upgradePolicy je nastavená na hodnotu **automaticky**, aktualizuje se virtuální počítače. Pokud je vlastnost upgradePolicy nastavena na **Ruční**, rozšíření jsou označena jako nevyhovující modelu.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Spouštějí se rozšíření znovu, když je stávající počítač zacelený nebo přepsaný z Image?

Pokud je stávající virtuální počítač zacelený pomocí služby, zobrazí se jako restart a rozšíření se znovu nespustí. Pokud se virtuální počítač obnoví z image, proces se podobá nahrazení jednotky operačního systému zdrojovou imagí. Všechny specializace z nejnovějšího modelu, jako jsou rozšíření, se spustí znovu.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Návody se připojit k doméně služby Active Directory do sady škálování virtuálního počítače?

Pokud chcete připojit sadu škálování virtuálního počítače k doméně služby Active Directory (AD), můžete definovat rozšíření.

Pro definování rozšíření použijte vlastnost JsonADDomainExtension:

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Rozšíření Virtual Machine Scale set se snaží nainstalovat něco, co vyžaduje restart.

Pokud se rozšíření Virtual Machine Scale set pokusí nainstalovat něco, co vyžaduje restart, můžete použít rozšíření Azure Automation požadované konfigurace stavu (Automatizace DSC). Pokud je operačním systémem Windows Server 2012 R2, Azure si vyžádá v instalaci Windows Management Frameworku (WMF) 5,0, restartuje a pak pokračuje v konfiguraci.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Návody zapnout Antimalwarový program v sadě škálování virtuálního počítače?

Pokud chcete zapnout Antimalwarový program v sadě škálování virtuálních počítačů, použijte následující příklad PowerShellu:

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

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Návody spustit vlastní skript, který je hostovaný v privátním účtu úložiště?

Pokud chcete spustit vlastní skript, který je hostovaný v privátním účtu úložiště, nastavte chráněná nastavení pomocí klíče a názvu účtu úložiště. Další informace najdete v tématu [rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity).

## <a name="passwords"></a>Hesla

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Návody resetovat heslo pro virtuální počítače v rámci sady škálování virtuálních počítačů?

Existují dva hlavní způsoby, jak změnit heslo pro virtuální počítače ve službě Scale Sets.

- Změňte přímo model sady škálování virtuálního počítače. K dispozici s rozhraním API 2017-12-01 a novějším.

    Aktualizujte přihlašovací údaje správce přímo v modelu sady škálování (například pomocí Azure Resource Explorer, PowerShellu nebo rozhraní příkazového řádku). Po aktualizaci sady škálování mají všechny nové virtuální počítače nové přihlašovací údaje. Existující virtuální počítače mají pouze nové přihlašovací údaje, pokud jsou přeobrazované.

- Resetujte heslo pomocí rozšíření přístupu virtuálních počítačů. Ujistěte se, že dodržujete požadavky na heslo, jak je popsáno [zde](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

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

## <a name="networking"></a>Sítě

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Je možné přiřadit skupině škálování skupinu zabezpečení sítě (NSG), aby platila pro všechny síťové karty virtuálních počítačů v sadě?

Ano. Skupinu zabezpečení sítě můžete použít přímo na sadu škálování tak, že na ni odkazujete v části networkInterfaceConfigurations profilu sítě. Příklad:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Návody udělat v rámci stejného předplatného a stejné oblasti swap VIP pro služby Virtual Machine Scale Sets?

Pokud máte dvě sady škálování virtuálních počítačů s Azure Load Balancer front-endy a jsou ve stejném předplatném a oblasti, můžete z nich zrušit přidělení veřejných IP adres a přiřadit ji k druhému. Podívejte [se na téma prohození VIP: nasazení Blue-zelená v Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) například. To má za následek zpoždění, i když se prostředky nadělí nebo přidělí na úrovni sítě. Rychlejší je možnost použít Azure Application Gateway se dvěma back-end fondy a pravidlo směrování. Alternativně můžete hostovat aplikaci pomocí [služby Azure App Service](https://azure.microsoft.com/services/app-service/) , která poskytuje podporu pro rychlé přepínání mezi pracovními a provozními sloty.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Návody zadejte rozsah privátních IP adres, které se mají použít pro alokaci statických privátních IP adres?

IP adresy se vyberou z podsítě, kterou zadáte.

Metoda přidělování IP adres sady škálování virtuálního počítače je vždycky "dynamická", ale to neznamená, že se tyto IP adresy můžou změnit. V tomto případě "dynamický" jenom znamená, že neurčíte IP adresu v žádosti o vložení. Zadejte statickou sadu pomocí podsítě.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Návody nasadit sadu škálování virtuálního počítače do existující virtuální sítě Azure?

Pokud chcete nasadit sadu škálování virtuálního počítače do existující virtuální sítě Azure, přečtěte si téma [nasazení sady škálování virtuálních počítačů do existující virtuální sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Můžu použít sady škálování s akcelerovanými síťovými službami?

Ano. Pokud chcete používat akcelerované síťové služby, nastavte enableAcceleratedNetworking na hodnotu true v nastavení networkInterfaceConfigurations sady škálování. Například

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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak můžu nakonfigurovat servery DNS používané sadou škálování?

Pokud chcete vytvořit sadu škálování virtuálního počítače s vlastní konfigurací DNS, přidejte do oddílu networkInterfaceConfigurations sady škálování dnsSettings paketu JSON. Příklad:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak můžu nakonfigurovat sadu škálování tak, aby každému virtuálnímu počítači přiřadila veřejnou IP adresu?

Pokud chcete vytvořit sadu škálování virtuálního počítače, která každému virtuálnímu počítači přiřadí veřejnou IP adresu, ujistěte se, že verze rozhraní API prostředku Microsoft. COMPUTE/virtualMachineScaleSets je 2017-03-30, a přidejte do oddílu IPConfiguration sady škálování _publicipaddressconfiguration_ paketu JSON. Příklad:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Můžu nakonfigurovat sadu škálování tak, aby fungovala s více aplikačními bránami?

Ano. ID prostředků pro více Application Gateway fond adres back-endu můžete přidat do seznamu _applicationGatewayBackendAddressPools_ v části _IPConfiguration_ v profilu sítě sady škálování.

## <a name="scale"></a>Měřítko

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>V jakém případě je možné vytvořit sadu škálování virtuálního počítače s méně než dvěma virtuálními počítači?

Jedním z důvodů vytvoření sady škálování virtuálních počítačů s méně než dvěma virtuálními počítači by bylo použití elastických vlastností sady škálování virtuálních počítačů. Například můžete nasadit sadu škálování virtuálního počítače s nulovými virtuálními počítači k definování infrastruktury bez placení nákladů na spouštění virtuálních počítačů. Až budete připraveni nasadit virtuální počítače, Zvyšte kapacitu sady škálování virtuálního počítače na hodnotu počet instancí výroby.

Dalším důvodem, proč byste mohli vytvořit sadu škálování virtuálního počítače s méně než dvěma virtuálními počítači, je v případě, že máte menší nároky na dostupnost než v rámci používání skupiny dostupnosti se diskrétními virtuálními počítači. Virtual Machine Scale Sets vám dává možnost pracovat s nerozlišenými výpočetními jednotkami, které jsou fungible. Tato jednotnost je klíčovým rozdílem pro sady škálování virtuálních počítačů a skupiny dostupnosti. Mnoho bezstavových úloh nesleduje jednotlivé jednotky. Pokud zatížení klesne, můžete škálovat dolů na jednu výpočetní jednotku a po zvýšení zátěže škálovat až na mnoho.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Návody změnit počet virtuálních počítačů v sadě škálování virtuálního počítače?

Pokud chcete změnit počet virtuálních počítačů v sadě škálování virtuálního počítače v Azure Portal, v části Vlastnosti sady škálování virtuálního počítače klikněte na okno škálování a použijte posuvník.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Návody definovat vlastní výstrahy pro při dosažení určitých prahových hodnot?

Máte značnou flexibilitu v tom, jak zpracovávat výstrahy pro zadané prahové hodnoty. Můžete například definovat přizpůsobené Webhooky. Následující příklad Webhooku je ze šablony Správce prostředků:

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


## <a name="patching-and-operations"></a>Opravy a operace

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Můžu vytvořit sadu škálování v existující skupině prostředků?

Ano, můžete vytvořit sadu škálování v existující skupině prostředků.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Můžu přesunout sadu škálování na jinou skupinu prostředků?

Ano, prostředky sady škálování můžete přesunout do nového předplatného nebo skupiny prostředků.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak aktualizovat sadu škálování virtuálního počítače na novou image? Návody spravovat opravy?

Pokud chcete aktualizovat sadu škálování virtuálního počítače na novou image a spravovat opravy, přečtěte si téma [upgrade sady škálování virtuálních počítačů](./virtual-machine-scale-sets-upgrade-scale-set.md).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Můžu použít operaci obnovení obrazu k resetování virtuálního počítače bez změny image? (To znamená, že chci resetovat virtuální počítač na tovární nastavení, nikoli na nový obrázek.)

Ano, pomocí operace obnovení bitové kopie můžete virtuální počítač resetovat beze změny. Pokud ale vaše sada škálování virtuálního počítače odkazuje na Image platformy s `version = latest` , může se váš virtuální počítač při volání aktualizovat na novější bitovou kopii operačního systému `reimage` .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Je možné integrovat sady škálování s protokoly Azure Monitor?

Ano, můžete instalaci rozšíření Azure Monitor nainstalovat na virtuální počítače sady škálování. Tady je příklad rozhraní příkazového řádku Azure CLI:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Požadované ID pracovního prostoru a workspaceKey najdete v pracovním prostoru Log Analytics Azure Portal. Na stránce Přehled klikněte na dlaždici nastavení. Klikněte na kartu připojené zdroje v horní části.

> [!NOTE]
> Pokud je vaše sada škálování nastavená na ruční _, je nutné_ použít rozšíření na všechny virtuální počítače v sadě tak, že na ně zavoláte upgrade. V rozhraní příkazového řádku by to bylo _AZ VMSS Update-Instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Řešení potíží

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Návody zapnout diagnostiku spouštění?

Pokud chcete zapnout diagnostiku spouštění, nejdřív vytvořte účet úložiště. Pak tento blok JSON vložte do **virtualMachineProfile**sady škálování virtuálních počítačů a aktualizujte sadu škálování virtuálního počítače:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Když se vytvoří nový virtuální počítač, zobrazí vlastnost InstanceView virtuálního počítače podrobnosti pro snímek obrazovky atd. Tady je příklad:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Vlastnosti virtuálního počítače

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Návody získat informace o vlastnostech pro každý virtuální počítač bez provedení více volání? Například jak získám doménu selhání pro každý virtuální počítač 100 v rámci sady škálování virtuálních počítačů?

Chcete-li získat informace o vlastnostech pro každý virtuální počítač bez provedení více volání, můžete volat `ListVMInstanceViews` pomocí REST API `GET` v následujícím identifikátoru URI prostředku:

/Subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines? $expand = instanceView&$select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Můžu předávat různé argumenty rozšíření různým virtuálním počítačům v sadě škálování virtuálního počítače?

Ne, nemůžete předat různé argumenty rozšíření různým virtuálním počítačům v sadě škálování virtuálního počítače. Rozšíření ale můžou fungovat na základě jedinečných vlastností virtuálního počítače, na kterém běží, jako je třeba název počítače. Rozšíření také mohou dotazovat metadata instance na http://169.254.169.254 a získat tak další informace o virtuálním počítači.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Proč mezi virtuálními počítači virtuálních počítačů a identifikátory virtuálních počítačů existují mezery? Například: 0, 1, 3...

Mezi názvy počítačů virtuálních počítačů virtuálního počítače a ID virtuálních počítačů, které **jsou nastavené** na výchozí hodnotu **true**, se nevztahují žádné mezery. Pokud je převýšení nastavené na **true**, vytvoří se víc virtuálních počítačů, než kolik jich bylo požadováno. Další virtuální počítače se pak odstraní. V takovém případě získáte lepší spolehlivost nasazení, ale na úkor souvislých názvů a souvislých pravidel překladu síťových adres (NAT).

Tuto vlastnost můžete nastavit na **hodnotu NEPRAVDA**. U malých sad Virtual Machine Scale Sets to nijak neovlivní spolehlivost nasazení.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaký je rozdíl mezi odstraněním virtuálního počítače v sadě škálování virtuálního počítače a zrušením přidělení virtuálního počítače? Kdy je vhodné zvolit jednu z nich po druhé?

Hlavním rozdílem mezi odstraněním virtuálního počítače v sadě škálování virtuálního počítače a zrušením přidělení virtuálního počítače je to, že `deallocate` neodstraňují virtuální pevné disky (VHD). Existují náklady na úložiště spojené se spuštěným systémem `stop deallocate` . Můžete použít jeden z těchto důvodů:

- Chcete přestat zaplatit výpočetní náklady, ale chcete zachovat stav disku virtuálních počítačů.
- Chcete spustit sadu virtuálních počítačů rychleji, než byste mohli škálovat sadu škálování virtuálního počítače.
  - V souvislosti s tímto scénářem jste mohli vytvořit vlastní modul automatického škálování a chtít rychlejší ucelený rozsah.
- Máte sadu škálování virtuálního počítače, která je rovnoměrně distribuovaná napříč doménami selhání nebo aktualizačními doménami. Důvodem může být to, že jste selektivně odstraněné virtuální počítače nebo virtuální počítače po přezřizování odstranili. Spuštění, `stop deallocate` po kterém následuje `start` sada škálování virtuálního počítače, rovnoměrně distribuuje virtuální počítače napříč doménami selhání nebo aktualizačními doménami.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Návody pořídit snímek instance sady škálování virtuálního počítače?
Vytvoří snímek z instance sady škálování virtuálního počítače.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Vytvořte ze snímku spravovaný disk.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
