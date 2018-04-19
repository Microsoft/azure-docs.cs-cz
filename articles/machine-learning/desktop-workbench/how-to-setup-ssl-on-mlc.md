---
title: "Povolit protokol SSL v clusteru Azure Machine Learning výpočetní (MLC) | Microsoft Docs"
description: "Získat pokyny k nastavení SSL pro vyhodnocování volání v clusteru Azure Machine Learning výpočetní (MLC)"
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 444803eeb77e79a8bfe3271ddf27bd428042c875
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Povolit protokol SSL v clusteru Azure Machine Learning výpočetní (MLC) 

Tyto pokyny umožňují nastavení protokolu SSL pro vyhodnocování volání na Machine Learning výpočetní (MLC) clusteru. 

## <a name="prerequisites"></a>Požadavky 

1. Použít při volání v reálném čase vyhodnocování rozhodnete na záznam CNAME (název DNS).

2. Vytvoření *bez hesla* certifikát s této CNAME.

3. Pokud certifikát ještě není ve formátu PEM, převeďte ho na PEM pomocí nástrojů, jako *openssl*.

Po dokončení požadavky, bude mít dva soubory:

* Soubor certifikátu, například `cert.pem`. Ujistěte se, že soubor má řetěz certifikátů úplné.
* Soubor klíče, například `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Nastavení certifikátu protokolu SSL na novém clusteru ACS

Pomocí Azure Machine Learning CLI, spusťte následující příkaz se `-c` přepínače k vytvoření clusteru ACS certifikát protokolu SSL, který je připojen:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Nastavit certifikát SSL u stávajícího clusteru ACS

Pokud cílíte na clusteru, který byl vytvořen bez protokolu SSL, můžete přidat certifikát pomocí rutin prostředí Azure PowerShell: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mapování CNAME a IP adresu

Vytvoření mapování mezi CNAME, který jste vybrali v požadavky a IP adresu reálném čase front-endu (FE). Pokud chcete zjistit, IP adresa FE, spusťte následující příkaz. Výstup zobrazuje pole s názvem "publicIpAddress", který obsahuje IP adresu front-endu v reálném čase clusteru. Postupujte podle pokynů svého poskytovatele DNS nastavit záznam z plně kvalifikovaný název domény, používá v CNAME veřejné IP adresy.



## <a name="auto-detection-of-a-certificate"></a>Automatické zjišťování certifikátu 

Při vytváření webu v reálném čase služby pomocí "`az ml service create realtime`" příkaz Azure Machine Learning automaticky zjistí SSL nastavení v clusteru a automaticky nastaví vyhodnocování adresy URL určené certifikátem. 

Pokud chcete zobrazit stav certifikátu, spusťte následující příkaz. Všimněte si předponu "https" vyhodnocování URI a CNAME v názvu hostitele. 

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
