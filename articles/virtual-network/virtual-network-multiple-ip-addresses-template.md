---
title: "Více IP adres pro virtuální počítače Azure - šablony | Microsoft Docs"
description: "Zjistěte, jak přiřadit více IP adres pro virtuální počítač pomocí šablony Azure Resource Manager."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Přiřadit více IP adres pro virtuální počítače pomocí šablony Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) pomocí modelu nasazení Azure Resource Manager pomocí šablony Resource Manageru. Několik veřejných a privátních IP adres nelze přiřadit stejný síťový adaptér při nasazování virtuálního počítače pomocí modelu nasazení classic. Další informace o modelech nasazení Azure, najdete [pochopit modely nasazení](../resource-manager-deployment-model.md) článku.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Popis šablony

Nasazení šablony můžete rychle a konzistentně vytváření prostředků Azure s hodnotami jinou konfiguraci. Pro čtení [názorný Průvodce šablonou Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článek, pokud si nejste obeznámeni s šablon Azure Resource Manageru. [Nasazení virtuálního počítače s více IP adres](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) šablony je použity v tomto článku.

<a name="resources"></a>Nasazení šablony vytvoří v následujících zdrojích informací:

|Prostředek|Name (Název)|Popis|
|---|---|---|
|Síťové rozhraní|*myNic1*|Tři konfigurace protokolu IP, které jsou popsané v části scénář v tomto článku jsou vytvořeny a přiřazeny tento síťový adaptér.|
|Prostředek veřejné IP adresy|jsou vytvořené 2: *myPublicIP* a *myPublicIP2*|Tyto prostředky jsou přiřazené statické veřejné IP adresy a jsou přiřazeny *IPConfig-1* a *IPConfig 2* konfigurace protokolu IP, které jsou popsané v tomto scénáři.|
|Virtuální počítač|*myVM1*|Standardní DS3 virtuálních počítačů.|
|Virtuální síť|*myVNet1*|Virtuální síť s jednou podsítí s názvem *mySubnet*.|
|Účet úložiště|Jedinečné pro nasazení|Účet úložiště.|

<a name="parameters"></a>Při nasazení šablony, je nutné zadat hodnoty následujících parametrů:

|Name (Název)|Popis|
|---|---|
|adminUsername|Uživatelské jméno správce. Uživatelské jméno musí být v souladu s [požadavky na Azure uživatelské jméno](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Heslo správce heslo musí být v souladu s [požadavky na heslo pro platformu Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|Název DNS pro PublicIPAddressName1. Název DNS se přeložit na jednu z veřejné IP adresy přiřazené k virtuálnímu počítači. Název musí být jedinečný v rámci Azure vytvořit virtuální počítač v oblasti (umístění).|
|dnsLabelPrefix1|Název DNS pro PublicIPAddressName2. Název DNS se přeložit na jednu z veřejné IP adresy přiřazené k virtuálnímu počítači. Název musí být jedinečný v rámci Azure vytvořit virtuální počítač v oblasti (umístění).|
|OSVersion|Verze Windows nebo Linuxem pro virtuální počítač. Operační systém je plně opravou bitovou kopii daného vybrané verze Windows nebo Linuxem.|
|imagePublisher|Vydavatel image Windows nebo Linuxem pro vybraný virtuální počítač.|
|imageOffer|Obrázek Windows nebo Linuxem pro vybraný virtuální počítač.|

Všechny prostředky nasazené šablonou nakonfigurované s několika výchozí nastavení. Můžete zobrazit tato nastavení libovolného z následujících metod:

- **Zobrazit šablonu na Githubu:** Pokud jste obeznámeni s šablony, můžete zobrazit nastavení v rámci [šablony](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Zobrazit nastavení po nasazení:** Pokud nejste obeznámeni s šablony, můžete šablonu nasadit pomocí kroků v jednom z těchto částí a zobrazte si nastavení po nasazení.

Pokud chcete nasadit šablonu můžete použít portál Azure, PowerShell nebo rozhraní příkazového řádku Azure (CLI). Všechny metody vytvořila stejný výsledek. Pokud chcete nasadit šablonu, proveďte kroky v jednom z následujících částí:

## <a name="deploy-using-the-azure-portal"></a>Nasazení pomocí portálu Azure

Pokud chcete nasadit šablonu pomocí portálu Azure, proveďte následující kroky:

1. Upravte šablonu, v případě potřeby. Šablona nasadí prostředky a nastavení uvedené v [prostředky](#resources) tohoto článku. Další informace o šablonách a postupu pro jejich vytváření, čtení [šablon pro tvorbu Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)článku.
2. Nasazení šablony s jedním z následujících metod:
    - **Vyberte šablonu v portálu:** kroky [nasadit prostředky z vlastní šablony](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) článku. Vyberte existující šablonu s názvem *101-vm několika ipconfig*.
    - **Přímo:** kliknutím na následující tlačítko Otevřít šablonu přímo na portálu:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Bez ohledu na metodu si zvolíte, bude nutné zadat hodnoty pro [parametry](#parameters) uvedené dříve v tomto článku. Po nasazení virtuálního počítače připojit k virtuálnímu počítači a přidejte privátních IP adres do operačního systému, který jste nasadili pomocí kroků v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku. Nepřidávejte veřejné IP adresy v operačním systému.

## <a name="deploy-using-powershell"></a>Nasazení pomocí prostředí PowerShell

Pokud chcete nasadit šablonu pomocí prostředí PowerShell, proveďte následující kroky:

1. Nasazení šablony pomocí kroků v [nasazení šablony v prostředí PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md) článku. Článek popisuje několik možností nasazení šablony. Pokud se rozhodnete nasadit pomocí `-TemplateUri parameter`, je identifikátor URI pro tuto šablonu *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Pokud zvolíte možnost nasadit pomocí `-TemplateFile` parametr, zkopírujte obsah [soubor šablony](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) z Githubu do nového souboru na vašem počítači. Upravte obsah šablony, v případě potřeby. Šablona nasadí prostředky a nastavení uvedené v [prostředky](#resources) tohoto článku. Další informace o šablonách a postupu pro jejich vytváření, čtení [šablon pro tvorbu Azure Resource Manageru ](../azure-resource-manager/resource-group-authoring-templates.md)článku.

    Bez ohledu na to možnost zvolit pro nasazení šablony s, je nutné zadat hodnoty pro parametr hodnoty uvedené v [parametry](#parameters) tohoto článku. Pokud zvolíte možnost zadat parametry s využitím soubor parametrů, zkopírujte obsah [soubor parametrů](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) z Githubu do nového souboru ve vašem počítači. Upravte hodnoty v souboru. Použít soubor, který jste vytvořili jako hodnota `-TemplateParameterFile` parametr.

    Chcete-li zjistit platné hodnoty pro OSVersion, ImagePublisher a imageOffer parametry, proveďte kroky v [vyhledání a vyberte článek bitové kopie virtuálního počítače s Windows](../virtual-machines/windows/cli-ps-findimage.md) článku.

    >[!TIP]
    >Pokud si nejste jistí, jestli je k dispozici dnslabelprefix, zadejte `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` příkaz chcete zjistit. Pokud je k dispozici, příkaz vrátí `True`.

2. Po nasazení virtuálního počítače připojit k virtuálnímu počítači a přidejte privátních IP adres do operačního systému, který jste nasadili pomocí kroků v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku. Nepřidávejte veřejné IP adresy v operačním systému.

## <a name="deploy-using-the-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

Pokud chcete šablonu nasadit pomocí Azure CLI 1.0, proveďte následující kroky:

1. Nasazení šablony pomocí kroků v [nasazení šablony pomocí Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) článku. Článek popisuje několik možností nasazení šablony. Pokud se rozhodnete nasadit pomocí `--template-uri` (-f), identifikátor URI pro tuto šablonu je *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Pokud zvolíte možnost nasadit pomocí `--template-file` (-f) parametr, zkopírujte obsah [soubor šablony](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) z Githubu do nového souboru na vašem počítači. Upravte obsah šablony, v případě potřeby. Šablona nasadí prostředky a nastavení uvedené v [prostředky](#resources) tohoto článku. Další informace o šablonách a postupu pro jejich vytváření, čtení [šablon pro tvorbu Azure Resource Manageru ](../azure-resource-manager/resource-group-authoring-templates.md)článku.

    Bez ohledu na to možnost zvolit pro nasazení šablony s, je nutné zadat hodnoty pro parametr hodnoty uvedené v [parametry](#parameters) tohoto článku. Pokud zvolíte možnost zadat parametry s využitím soubor parametrů, zkopírujte obsah [soubor parametrů](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) z Githubu do nového souboru ve vašem počítači. Upravte hodnoty v souboru. Použít soubor, který jste vytvořili jako hodnota `--parameters-file` (-e) parametr.

    Chcete-li zjistit platné hodnoty pro OSVersion, ImagePublisher a imageOffer parametry, proveďte kroky v [vyhledání a vyberte článek bitové kopie virtuálního počítače s Windows](../virtual-machines/windows/cli-ps-findimage.md) článku.

2. Po nasazení virtuálního počítače připojit k virtuálnímu počítači a přidejte privátních IP adres do operačního systému, který jste nasadili pomocí kroků v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku. Nepřidávejte veřejné IP adresy v operačním systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
