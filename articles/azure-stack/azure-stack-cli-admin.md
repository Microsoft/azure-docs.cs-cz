---
title: Povolení rozhraní příkazového řádku Azure pro uživatele Azure stacku | Dokumentace Microsoftu
description: Další informace o použití multiplatformního rozhraní příkazového řádku (CLI) ke správě a nasazování prostředků ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: mabrigg
ms.openlocfilehash: 09c551ea7196ae20a60a5dd34c1cda889ff5df46
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643273"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Povolení rozhraní příkazového řádku Azure pro uživatele Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Certifikát kořenové certifikační Autority můžete poskytnout uživatelům Azure stacku, tak, aby se používat rozhraní příkazového řádku Azure na svých počítačích vývojářů. Uživatelé budou potřebovat certifikát ke správě prostředků prostřednictvím rozhraní příkazového řádku.

* **Kořenový certifikát certifikační Autority Azure stacku** je vyžadována, pokud uživatelé používají rozhraní příkazového řádku z pracovní stanice mimo Azure Stack Development Kit.  

* **Koncový bod virtuálního počítače aliasy** poskytuje alias, jako je "UbuntuLTS" nebo "Win2012Datacenter,", který odkazuje vydavatel image, nabídky, SKU a verze jako jediný parametr při nasazování virtuálních počítačů.  

Následující části popisují, jak získat tyto hodnoty.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportujte certifikát kořenové certifikační Autority Azure stacku

Kořenový certifikát certifikační Autority Azure Stack najdete na vývojové sadě a virtuálnímu počítači tenanta, na kterém běží ve vývojovém prostředí sady. Export kořenového certifikátu služby Azure Stack ve formátu PEM, přihlaste se k development kit nebo tomuto virtuálnímu počítači a spusťte následující skript:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Nastavení koncového bodu virtuálního počítače aliasy

Operátoři Azure stacku byste nastavit veřejně přístupném koncovém bodu, který je hostitelem soubor alias virtuálního počítače. Soubor alias virtuálního počítače je soubor JSON, který poskytuje běžný název pro image. Tento název je následně zadat při nasazení virtuálního počítače jako parametr příkazového řádku Azure.  

Předtím, než přidáte položku do souboru alias, ujistěte se, že jste [stažení Image z Azure Marketplace](azure-stack-download-azure-marketplace-item.md), nebo mít [publikovat vlastní image](azure-stack-add-vm-image.md). Pokud publikujete vlastní image, poznamenejte si informace vydavatele, nabídky, SKU a verze, které jste zadali během publikování. Pokud se jedná image z marketplace, můžete zobrazit informace s použitím ```Get-AzureVMImage``` rutiny.  

A [ukázkový soubor alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) běžné imagi aliasů je k dispozici. Který můžete použít jako výchozí bod. Hostování tento soubor do místa, kde vaši klienti rozhraní příkazového řádku k němu přistoupit. Jedním ze způsobů je hostitelem souboru v účtu blob storage a sdílejte její adresu URL s uživateli:

1. Stáhněte si [ukázkový soubor](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z Githubu.
2. Vytvořte nový účet úložiště ve službě Azure Stack. Po dokončení, který vytvoříte nový kontejner objektů blob. Nastavit zásady přístupu k "public".  
3. Nahrajte soubor JSON do nového kontejneru. Po dokončení, který se zobrazí adresa URL objektu blob výběrem názvu objektu blob a pak vyberete adresu URL z vlastností objektu blob.

## <a name="next-steps"></a>Další postup

- [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)

- [Připojení přes PowerShell](azure-stack-connect-powershell.md)

- [Správa uživatelských oprávnění](azure-stack-manage-permissions.md)
