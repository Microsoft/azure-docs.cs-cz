---
title: Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure v zásobníku Azure | Microsoft Docs
description: Vytvořte virtuální počítač s Linuxem pomocí rozhraní příkazového řádku v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 69036b522b375eced604256340b532ad14a8708e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Rozhraní příkazového řádku Azure slouží k vytváření a správě prostředků Azure zásobníku z příkazového řádku. Tento rychlý start údaje, pomocí rozhraní příkazového řádku Azure k vytvoření virtuální počítač s Linuxem v Azure zásobníku.  Po vytvoření virtuálního počítače je nainstalována webového serveru a port 80 je otevřen umožňující webový provoz.

## <a name="prerequisites"></a>Požadavky 

* Ověřte, že vaše operátor zásobník Azure má přidaná bitovou kopii "Ubuntu Server 16.04 LTS" do zásobníku Azure marketplace. 

* Azure zásobníku vyžaduje konkrétní verzi rozhraní příkazového řádku Azure k vytváření a správě prostředků. Pokud nemáte nakonfigurován pro Azure zásobníku rozhraní příkazového řádku Azure, přihlaste se k [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo externí klienta se systémem Windows Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) a postup [instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

* Veřejný klíč SSH, název id_rsa.pub by se vytvořit v adresáři .ssh profil uživatele systému Windows. Podrobné informace o vytváření klíčů SSH naleznete v tématu [vytváření SSH klíčů v systému Windows](../../virtual-machines/linux/ssh-from-windows.md). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, do které zásobník Azure jsou nasadit a spravovat prostředky. Z vaší development kit nebo zásobník Azure integrovaný systém, [vytvořit skupinu az](/cli/azure/group#az_group_create) příkazu vytvořte skupinu prostředků. Přiřadili jsme hodnoty pro všechny proměnné v tomto dokumentu, můžete použít jejich nebo přiřadit jinou hodnotu. Následující příklad vytvoří skupinu prostředků s názvem myResourceGroup do místního umístění.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. Tento příklad používá Demouser pro název správce a Demouser@123 jako heslo. Aktualizujte tyto hodnoty na nějaké vhodné pro vaše prostředí. Tyto hodnoty je třeba při připojení k virtuálnímu počítači.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Po dokončení bude výstup příkazu parametry pro virtuální počítač.  Poznamenejte si *PublicIPAddress*, protože to používáte k připojení a správě virtuálního počítače.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Ve výchozím nastavení jsou na virtuální počítače s Linuxem, které jsou nasazené v Azure, povolená pouze připojení SSH. Pokud bude tento virtuální počítač webovým serverem, budete muset otevřít port 80 z internetu. Požadovaný port otevřete pomocí příkazu [az vm open-port](/cli/azure/vm#open-port).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači

Ze systému s nainstalovaným SSH se pomocí následujícího příkazu připojte k virtuálnímu počítači. Pokud pracujete v systému Windows, můžete k vytvoření připojení použít [PuTTy](http://www.putty.org/). Nezapomeňte nahradit správné veřejnou IP adresu virtuálního počítače. V našem příkladu výše IP adresa byla 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalace serveru NGINX

Pomocí následujícího skriptu bash provedete aktualizaci zdrojů balíčku a nainstalujete nejnovější balíček NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

S nainstalovaným serverem NGINX na virtuálním počítači a nyní otevřeným portem 80 z internetu můžete použít libovolný webový prohlížeč a zobrazit výchozí úvodní stránku serveru NGINX. Nezapomeňte pro návštěvu výchozí stránky použít veřejnou IP adresu (*publicIpAddress*) popsanou výše. 

![Výchozí web NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete).

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tento rychlý start jste nasadili jednoduchý systém Linux virtuálního počítače. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).

