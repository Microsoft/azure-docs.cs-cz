---
title: Spouštění vlastních skriptů na virtuálních počítačích SIO v Azure
description: Automatizace úloh konfigurace virtuálních počítačů s Linuxem pomocí rozšíření vlastní skript v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: b75b232c048a1ea49256b12ce1b65c4bd87a1cf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535438"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Použití rozšíření vlastních skriptů Azure verze 2 s linuxovými virtuálními počítači
Rozšíření vlastního skriptu verze 2 stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakoukoli jinou úlohu konfigurace/správy. Skripty si můžete stáhnout z Azure Storage nebo z jiného dostupného internetového umístění, nebo je můžete poskytnout do runtime rozšíření. 

Rozšíření vlastní skript integruje se šablonami Azure Resource Manager. Můžete ji spustit také pomocí azure cli, PowerShell nebo rozhraní API Azure virtual machines REST.

Tento článek podrobně popisuje, jak používat rozšíření vlastního skriptu z Azure CLI a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také obsahuje kroky řešení potíží pro systémy Linux.


Existují dvě rozšíření vlastního skriptu pro Linux:
* Verze 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Verze 2 - Microsoft.Azure.Extensions.CustomScript

Chcete-li místo toho použít novou verzi 2, přepněte nové a stávající nasazení. Nová verze představuje funkční náhradu starší verze. Proto při migraci není potřeba měnit konfiguraci rozšíření, ale stačí změnit jeho název a verzi.


### <a name="operating-system"></a>Operační systém

Vlastní skript rozšíření pro Linux poběží na rozšíření podporované rozšíření operačních systémů, další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Umístění skriptu

Toto rozšíření můžete použít k použití přihlašovacích údajů úložiště objektů Blob Azure pro přístup k úložišti objektů Blob Azure. Případně umístění skriptu může být libovolné místo, tak dlouho, dokud virtuální ho virtuálního serveru můžete směrovat do tohoto koncového bodu, jako je například GitHub, interní souborový server atd.

### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud potřebujete stáhnout skript externě, jako je GitHub nebo Azure Storage, pak je třeba otevřít další porty firewallu/skupiny zabezpečení sítě. Pokud je například váš skript umístěný ve službě Azure Storage, můžete povolit přístup pomocí značek služby Azure NSG pro [úložiště](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Pokud je skript na místním serveru, může být stále nutné otevřít další porty brány firewall/skupiny zabezpečení sítě.

### <a name="tips-and-tricks"></a>Tipy a triky
* Selhání tohoto rozšíření nejčastěji způsobují chyby syntaxe ve skriptu. Otestujte, že se skript spustí bez chyb, a implementujte do skriptu dodatečné protokolování, které vám pomůže snadněji zjistit, kde došlo k selhání.
* Pište skripty, které jsou idempotentní, takže když se omylem spustí více než jednou, nezpůsobí to změny systému.
* Ujistěte se, že skripty nevyžadují vstup uživatele při jejich spuštění.
* Je povoleno 90 minut pro spuštění skriptu, cokoli v delším případě bude mít za následek neúspěšné poskytnutí rozšíření.
* Nevkládáme restarty uvnitř skriptu, to způsobí problémy s jinými rozšířeními, které jsou instalovány, a po restartu, rozšíření nebude pokračovat po restartu. 
* Pokud máte skript, který způsobí restart, pak nainstalovat aplikace a spouštět skripty atd. Restart byste měli naplánovat pomocí úlohy Cron nebo pomocí nástrojů, jako je DSC nebo Chef, Puppet extensions.
* Rozšíření spustí skript pouze jednou, pokud chcete spustit skript při každém spuštění, pak můžete použít [image cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) a použít modul [Skripty na startu.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Případně můžete použít skript k vytvoření servisní jednotky SystemD.
* Pokud chcete naplánovat, kdy bude skript spuštěn, měli byste použít rozšíření k vytvoření úlohy Cron. 
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. Pokud chcete častější aktualizace stavu spuštěného skriptu, budete muset vytvořit vlastní řešení.
* Vlastní skript rozšíření nepodporuje nativně proxy servery, ale můžete použít nástroj pro přenos souborů, který podporuje proxy servery v rámci skriptu, jako je *například Curl*. 
* Uvědomte si, že umístění adresáře, na která se mohou spolehnout skripty nebo příkazy, mají logiku, která to zvládnou.
*  Při nasazování vlastního skriptu do produkčních instancí VMSS se doporučuje nasadit pomocí šablony json a uložit účet úložiště skriptu, kde máte kontrolu nad tokenem SAS. 


## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastního skriptu určuje věci, jako je umístění skriptu a příkaz, který má být spuštěn. Tuto konfiguraci můžete uložit do konfiguračních souborů, zadat ji na příkazovém řádku nebo ji zadat v šabloně Správce prostředků Azure. 

Citlivá data můžete ukládat v chráněné konfiguraci, která je šifrovaná a dešifrovaná pouze uvnitř virtuálního počítače. Chráněná konfigurace je užitečná, pokud příkaz spuštění obsahuje tajné klíče, například heslo.

Tyto položky by měly být považovány za citlivá data a zadali v konfiguraci nastavení chráněné rozšíření. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrovaná jenom na cílovém virtuálním počítači.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> vlastnost managedIdentity **nesmí** být používána ve spojení s vlastnostmi storageAccountName nebo storageAccountKey

### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / Příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| vydavatel | Microsoft.Compute.Extensions | řetězec |
| type | Vlastní script | řetězec |
| typeHandlerVersion | 2.1 | int |
| fileUris (např. | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (např. | python \<MyPythonScript.py my-param1> | řetězec |
| . | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQqqQgdXBnCmFkZSAteQo= | řetězec |
| skipDos2Unix (např. | false (nepravda) | Boolean |
| časové razítko (např. | 123456789 | 32bitové celé číslo |
| storageAccountName (např. | examplestorageacct | řetězec |
| storageAccountKey (např. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |
| managedIdentity (např. | { } nebo { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } nebo { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json objekt |

### <a name="property-value-details"></a>Podrobnosti o hodnotě nemovitosti
* `apiVersion`: Nejaktuálnější apiVersion lze nalézt pomocí [Průzkumníka prostředků](https://resources.azure.com/) nebo z Azure CLI pomocí následujícího příkazu`az provider list -o json`
* `skipDos2Unix`: (volitelné, logické) přeskočit dos2unix převod skript-založené souborurl nebo skript.
* `timestamp`(volitelné, 32bitové celé číslo) použijte toto pole pouze k aktivaci opětovného spuštění skriptu změnou hodnoty tohoto pole.  Jakákoli celá hodnota je přijatelná; musí se lišit pouze od předchozí hodnoty.
* `commandToExecute`: (**vyžadováno,** pokud skript není nastaven, řetězec) skript vstupního bodu ke spuštění. Toto pole použijte místo toho, pokud příkaz obsahuje tajné kódy, například hesla.
* `script`: (**požadováno,** pokud příkazToExecute není nastaven, řetězec)a base64 kódovaný (a volitelně gzip'ed) skript spuštěný /bin/sh.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory, které mají být staženy.
* `storageAccountName`: (nepovinné, řetězec) název účtu úložiště. Pokud zadáte přihlašovací údaje `fileUris` úložiště, všechny musí být adresy URL pro objekty Blob Azure.
* `storageAccountKey`: (nepovinné, řetězec) přístupový klíč účtu úložiště
* `managedIdentity`: (volitelně, json objekt) [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro stahování souborů
  * `clientId`: (nepovinné, řetězec) ID klienta spravované identity
  * `objectId`: (volitelné, řetězec) ID objektu spravované identity


Následující hodnoty lze nastavit ve veřejném nebo chráněném nastavení, rozšíření odmítne jakoukoli konfiguraci, kde jsou níže uvedené hodnoty nastaveny ve veřejném i chráněném nastavení.
* `commandToExecute`
* `script`
* `fileUris`

Použití veřejné nastavení může být užitečné pro ladění, ale důrazně doporučujeme použít chráněné nastavení.

Veřejná nastavení se odesílají ve prostém textu do virtuálního počítače, kde bude skript spuštěn.  Chráněná nastavení se šifrují pomocí klíče známého jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače při jejich odeslání, tj. Certifikát použitý k dešifrování šifrovaných hodnot je uložen na virtuálním počítači a používá se k dešifrování nastavení (v případě potřeby) za běhu.

#### <a name="property-skipdos2unix"></a>Vlastnost: skipDos2Unix

Výchozí hodnota je false, což znamená, že **je** proveden převod dos2unix.

Předchozí verze Jazyka CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, by automaticky převést DOS `\r\n` soubory `\n`do souborů UNIX překladem do . Tento překlad stále existuje a je ve výchozím nastavení zapnutý. Tento převod se použije na všechny soubory stažené z fileUris nebo nastavení skriptu na základě některého z následujících kritérií.

* Pokud je rozšíření `.sh`jedním `.txt` `.py`z `.pl` , , nebo bude převedeno. Nastavení skriptu bude vždy odpovídat tomuto kritériu, protože se předpokládá, že je skript spuštěný s /bin/sh a je uložen jako script.sh na virtuálním počítači.
* Pokud soubor začíná `#!`na .

Dos2unix převod lze přeskočit nastavením skipDos2Unix na true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Vlastnost: skript

CustomScript podporuje spuštění uživatelem definovaného skriptu. Nastavení skriptu pro sloučení příkazuToExecute a fileUris do jednoho nastavení. Místo nutnosti nastavení souboru ke stažení z úložiště Azure nebo githubu můžete skript jednoduše zakódovat jako nastavení. Skript lze nahradit příkazToExecute a fileUris.

Skript **musí** být zakódován base64.  Skript může být **volitelně** gzip'ed. Nastavení skriptu lze použít ve veřejném nebo chráněném nastavení. Maximální velikost dat parametru skriptu je 256 kB. Pokud skript tuto velikost překročí, nebude spuštěn.

Například vzhledem k následujícímu skriptu uloženému do souboru /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Správné nastavení skriptu CustomScript by bylo sestaveno tak, že by se navýstupu následujícího příkazu.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skript může být volitelně gzip'ed dále snížit velikost (ve většině případů). (CustomScript automaticky detekuje použití komprese gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript používá následující algoritmus ke spuštění skriptu.

 1. uplatní délku hodnoty skriptu nepřesahuje 256 KB.
 1. base64 dekóduje hodnotu skriptu
 1. _pokus o_ gunzip hodnota dekódované hodnoty base64
 1. zapsat dekódovanou (a volitelně dekomprimovo) hodnotu na disk (/var/lib/waagent/custom-script/#/script.sh)
 1. spusťte skript pomocí _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Vlastnost: managedIdentity

CustomScript (verze 2.1 a dále) podporuje [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro stahování souborů z adres URL uvedených v nastavení "fileUris". Umožňuje CustomScript přístup k privátním objektům BLOB nebo kontejnerům azure storage, aniž by uživatel musel předávat tajné kódy, jako jsou tokeny SAS nebo klíče účtů úložiště.

Chcete-li použít tuto funkci, uživatel musí přidat [systémem přiřazenou](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) nebo [uživatelem přiřazenou](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identitu do virtuálního počítače nebo v oblasti VMSS, kde se očekává spuštění CustomScriptu, a [udělit přístup ke spravované identitě kontejneru nebo objektu blob služby Azure Storage](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Chcete-li použít systémem přiřazenou identitu na cílovém virtuálním počítači/vmss, nastavte pole "managedidentity" na prázdný objekt json. 

> Příklad:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Chcete-li použít identitu přiřazenou uživateli na cílovém virtuálním počítači/virtuálním počítači, nakonfigurujte pole "managedidentity" s ID klienta nebo ID objektu spravované identity.

> Příklady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> vlastnost managedIdentity **nesmí** být používána ve spojení s vlastnostmi storageAccountName nebo storageAccountKey

## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Správce prostředků Azure ke spuštění rozšíření vlastního skriptu během nasazení šablony Azure Resource Manager. Ukázková šablona, která obsahuje rozšíření vlastní skript, najdete zde [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Tyto názvy vlastností rozlišují malá a velká písmena. Chcete-li se vyhnout problémům s nasazením, použijte názvy, jak je znázorněno zde.

## <a name="azure-cli"></a>Azure CLI
Když používáte Azure CLI ke spuštění rozšíření vlastního skriptu, vytvořte konfigurační soubor nebo soubory. Minimálně musíte mít 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Volitelně můžete zadat nastavení v příkazu jako řetězec ve formátu JSON. To umožňuje konfiguraci zadat během provádění a bez samostatného konfiguračního souboru.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Příklady Azure CLI

#### <a name="public-configuration-with-script-file"></a>Veřejná konfigurace se souborem skriptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Příkaz Příkaz příkazu KONS Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Veřejná konfigurace bez souboru skriptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Příkaz Příkaz příkazu KONS Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Veřejné a chráněné konfigurační soubory

K určení identifikátoru URI souboru skriptu se používá veřejný konfigurační soubor. K určení příkazu, který má být spuštěn, se používá chráněný konfigurační soubor.

Veřejný konfigurační soubor:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chráněný konfigurační soubor:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Příkaz Příkaz příkazu KONS Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Řešení potíží
Při spuštění rozšíření vlastní skript je skript vytvořen nebo stažen do adresáře, který je podobný následujícímu příkladu. Výstup příkazu je také uložen `stdout` `stderr` do tohoto adresáře a souborů.

```bash
/var/lib/waagent/custom-script/download/0/
```

Chcete-li vyřešit potíže, nejprve zkontrolujte protokol agenta Linuxu, zkontrolujte, zda je rozšíření spuštěno, zkontrolujte:

```bash
/var/log/waagent.log 
```

Měli byste se podívat na spuštění rozšíření, bude to vypadat jako:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Některé body na vědomí:
1. Povolit je, když se příkaz spustí.
2. Stahování se týká stahování balíčku rozšíření CustomScript z Azure, nikoli souborů skriptů zadaných v fileUris.


Rozšíření Azure Script extension vytvoří protokol, který najdete tady:

```bash
/var/log/azure/custom-script/handler.log
```

Měli byste se podívat na individuální provedení, bude to vypadat jako:

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

Zde si můžete prohlédnout:
* Spuštění příkazu Povolit je tento protokol
* Nastavení předaná rozšíření
* Rozšíření stahování souboru a výsledek, který.
* Příkaz je spuštěn a výsledek.

Můžete také načíst stav spuštění rozšíření vlastního skriptu pomocí Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Výstup vypadá takto:

```output
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Další kroky
Chcete-li zobrazit kód, aktuální problémy a verze, viz [vlastní skript-rozšíření-linux repo](https://github.com/Azure/custom-script-extension-linux).

