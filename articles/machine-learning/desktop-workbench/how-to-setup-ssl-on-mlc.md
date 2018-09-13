---
title: Povolení protokolu SSL v clusteru služby Azure Machine Learning Compute (MLC) | Dokumentace Microsoftu
description: Získejte pokyny pro nastavení SSL pro vyhodnocení volání v clusteru Azure Machine Learning Compute (MLC)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 982a6807ccaf393c3aea42f39f7e60bb7e0d3ac3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643330"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Povolení protokolu SSL v clusteru služby Azure Machine Learning Compute (MLC) 

Tyto pokyny vám umožňují nastavení protokolu SSL pro vyhodnocení volání na Machine Learning Compute (MLC) clusteru. 

## <a name="prerequisites"></a>Požadavky 

1. Při volání v reálném čase bodování rozhodnete o záznam CNAME (název DNS).

2. Vytvoření *bez reklam* certifikát se tento záznam CNAME.

3. Pokud certifikát ještě není ve formátu PEM, převeďte jej na PEM s využitím nástrojů, jako *openssl*.

Po dokončení požadavků bude mít dva soubory:

* Soubor certifikátu, například `cert.pem`. Zkontrolujte, zda že soubor obsahuje úplný certifikát řetězu.
* Soubor klíče, například `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Nastavit certifikát SSL na novém clusteru ACS

Pomocí příkazového řádku Azure Machine Learning, spusťte následující příkaz se `-c` přepínače k vytvoření clusteru ACS s certifikátem SSL, který je připojen:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Nastavení certifikátu SSL v existujícím clusteru ACS

Pokud cílíte na cluster, který byl vytvořen bez SSL, můžete přidat certifikát pomocí rutin prostředí Azure PowerShell.

Je třeba zadat klíč a certifikát v nezpracovaném formátu PEM. Dají se číst do proměnné prostředí PowerShell:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Přidání certifikátu do clusteru: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mapování záznamu CNAME a IP adresu

Vytvořte mapování mezi záznam CNAME, který jste vybrali v rámci požadavků a IP adresa v reálném čase front-endu (FE). Když Pokud chcete zjistit IP adresu FE, spusťte následující příkaz. Ve výstupu nezobrazí pole s názvem "publicIpAddress", který obsahuje IP adresu front-endu v reálném čase clusteru. Použijte pokyny svého poskytovatele DNS nastavit záznam ze plně kvalifikovaný název domény používané CNAME veřejné IP adresy.



## <a name="auto-detection-of-a-certificate"></a>Automatické zjišťování certifikátu 

Při vytváření služby webu v reálném čase pomocí "`az ml service create realtime`" příkazu, Azure Machine Learning SSL na clusteru automaticky zjistí a automaticky nastaví hodnoticí adresu URL určenou certifikátem. 

Pokud chcete zobrazit stav certifikátu, spusťte následující příkaz. Všimněte si, že předpona "https" hodnocení identifikátor URI a záznam CNAME v názvu hostitele. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
