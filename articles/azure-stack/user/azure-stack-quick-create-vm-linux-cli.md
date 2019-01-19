---
title: Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 1/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 77194de0b175ae66c0a119a816f32b152f269e56
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413341"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Rychlý start: vytvoření serveru virtuálního počítače s Linuxem pomocí Azure CLI ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete vytvořit virtuální počítač Ubuntu Server 16.04 LTS pomocí Azure CLI. Postupujte podle kroků v tomto článku, jak vytvořit a používat virtuální počítač. Tento článek také obsahuje postup:

* Připojení k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalovat webový server NGINX a zobrazit výchozí domovskou stránku.
* Vyčištění nevyužitých prostředků.

## <a name="prerequisites"></a>Požadavky

* **Image Linuxu v Tržišti Azure Stack**

   Tržiště Azure Stack neobsahuje image Linuxu ve výchozím nastavení. Získat Azure Stack operátor poskytnout **Ubuntu Server 16.04 LTS** image chcete použít. Operátor, který můžete použít postup popsaný v [stažení položek z marketplace z Azure do služby Azure Stack](../azure-stack-download-azure-marketplace-item.md) článku.

* Azure Stack vyžaduje určitou verzi rozhraní příkazového řádku Azure můžete vytvořit a spravovat prostředky. Pokud nemáte rozhraní příkazového řádku Azure, který je nakonfigurovaný pro službu Azure Stack, přihlaste se k [sada](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo na základě Windows externí klienta máte [připojené prostřednictvím sítě VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) a postupujte podle kroků pro [ instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

* Veřejný klíč SSH s názvem id_rsa.pub uložen v adresáři .ssh uživatelského profilu Windows. Podrobné informace o vytváření klíčů SSH najdete v tématu [vytváření klíčů SSH ve Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém můžete nasadit a spravovat prostředky služby Azure Stack. Z vývojová sada nebo ve službě Azure Stack integrovaného systému, spustit [vytvořit skupiny az](/cli/azure/group#az-group-create) příkazu vytvořte skupinu prostředků.

>[!NOTE]
 Hodnoty jsou přiřazeny pro všechny proměnné v příkladech kódu. Pokud chcete, ale můžete přiřadit nové hodnoty.

Následující příklad vytvoří skupinu prostředků myResourceGroup v místním umístění.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [az vm vytvořit](/cli/azure/vm#az-vm-create) příkazu. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. Tento příklad používá Demouser pro uživatelské jméno správce a Demouser@123 jako heslo uživatele. Tyto hodnoty změňte na něco, co je pro vaše prostředí vhodný.

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

Veřejnou IP adresu se vrátí v **PublicIpAddress** parametru. Tato adresa zapište, protože mu umožní přístup k virtuálnímu počítači potřebujete.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Protože tento virtuální počítač bude spouštět na webovém serveru IIS, budete muset otevřít port 80 pro přenosy z Internetu. Požadovaný port otevřete pomocí příkazu [az vm open-port](/cli/azure/vm).

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Pomocí SSH se připojte k virtuálnímu počítači

Z klientského počítače pomocí protokolu SSH, nainstalovat připojte k virtuálnímu počítači. Pokud pracujete na klientovi Windows, použijte [Putty](http://www.putty.org/) k vytvoření připojení. Pro připojení k virtuálnímu počítači, použijte následující příkaz:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

Pokud chcete aktualizaci zdrojů balíčku a nainstalujete nejnovější balíček NGINX, spusťte následující skript:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

NGINX nainstalovaný a port 80 otevřený ve vašem virtuálním počítači můžete přístup k webovému serveru pomocí veřejné IP adresy virtuálního počítače. Otevřete webový prohlížeč a přejděte do ```http://<public IP address>```.

![Úvodní stránku serveru NGINX webového serveru](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Můžete použít [odstranění skupiny az](/cli/azure/group#az-group-delete) příkazu odeberte tyto prostředky. Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní server virtuálního počítače s Linuxem s webovým serverem. Další informace o virtuálních počítačích Azure Stack, [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
