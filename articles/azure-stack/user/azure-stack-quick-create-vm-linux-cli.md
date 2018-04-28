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
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Rychlý úvod: Vytvořte virtuální počítač s Linuxem serveru pomocí rozhraní příkazového řádku Azure v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Virtuální počítače s Ubuntu Server 16.04 LTS můžete vytvořit pomocí rozhraní příkazového řádku Azure. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače. Tento článek také poskytuje postup:

* Připojte k virtuálnímu počítači pomocí vzdáleného klienta.
* Instalace webového serveru se NGINX a zobrazit výchozí domovskou stránku.
* Vyčistěte nepoužité zdroje.

## <a name="prerequisites"></a>Požadavky

* **Bitovou kopii systému Linux v zásobníku Azure marketplace**

   Zásobník Azure marketplace neobsahuje bitovou kopii systému Linux ve výchozím nastavení. Získat operátor zásobník Azure zajistit **Ubuntu Server 16.04 LTS** bitové kopie je nutné. Operátor můžete použít postup popsaný v tématu [stáhnout položky marketplace z Azure do Azure zásobníku](../azure-stack-download-azure-marketplace-item.md) článku.

* Azure zásobníku vyžaduje konkrétní verzi rozhraní příkazového řádku Azure k vytváření a správě prostředků. Pokud nemáte nakonfigurován pro Azure zásobníku rozhraní příkazového řádku Azure, přihlaste se k [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo externí klienta se systémem Windows Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) a postup [ instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

* Veřejný klíč SSH s id_rsa.pub název uložené v adresáři .ssh profil uživatele systému Windows. Podrobné informace o vytváření klíčů SSH naleznete v tématu [vytváření SSH klíčů v systému Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, kde můžete nasadit a spravovat prostředky Azure zásobníku. Z vaší development kit nebo zásobník Azure integrovaný systém, [vytvořit skupinu az](/cli/azure/group#az_group_create) příkazu vytvořte skupinu prostředků.

>[!NOTE]
 Hodnoty se přiřazují pro všechny proměnné v příklady kódu. Pokud chcete, můžete však přiřadit nové hodnoty.

Následující příklad vytvoří skupinu prostředků s názvem myResourceGroup do místního umístění.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. Tento příklad používá Demouser pro název správce a Demouser@123 jako heslo uživatele. Tyto hodnoty změňte na něco, co je vhodné pro vaše prostředí.

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

Veřejná IP adresa je vrácený v **PublicIpAddress** parametr. Poznamenejte si tuto adresu, protože je nutné ho pro přístup k virtuálnímu počítači.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Protože tento virtuální počítač bude běžet webovém serveru IIS, budete muset otevřít port 80 pro přenosy z Internetu. Požadovaný port otevřete pomocí příkazu [az vm open-port](/cli/azure/vm#open-port).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Použití SSH se připojit k virtuálnímu počítači

Z klientského počítače pomocí protokolu SSH nainstalovaná připojte k virtuálnímu počítači. Pokud pracujete v klientovi Windows, použijte [Putty](http://www.putty.org/) k vytvoření připojení. Pro připojení k virtuálnímu počítači, použijte následující příkaz:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru se NGINX

Pokud chcete aktualizovat balíček prostředků a nainstalovat nejnovější balíček NGINX, spusťte následující skript:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

NGINX nainstalován, a port 80 otevřete na virtuálním počítači můžete přístup k webovému serveru, pomocí veřejnou IP adresu virtuálního počítače. Otevřete webový prohlížeč a přejděte do ```http://<public IP address>```.

![Úvodní stránka NGINX webového serveru](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Můžete použít [odstranění skupiny az](/cli/azure/group#az_group_delete) příkaz k odebrání těchto prostředků. Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tento rychlý start jste nasadili základní server virtuální počítač s Linuxem pomocí webového serveru. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
