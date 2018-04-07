---
title: Připojení RedHat Linux virtuálního počítače do Azure Active Directory DS | Microsoft Docs
description: Postup připojení k existující Red Hat Enterprise Linux 7 virtuální počítač do služby Azure Active Directory Domain Services.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 68971372a3c9d9671ccad43db8d7b543d286fc7a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Připojení RedHat Linux virtuálního počítače do domény služby Azure Active Directory

Tento článek ukazuje, jak připojit virtuální počítač Red Hat Enterprise Linux (RHEL) 7 k spravované doméně služby Azure Active Directory Domain Services (AADDS).  Požadavky:

- [Účet Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md)

- [Azure Active Directory Domain Services řadiče domény](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Rychlé příkazy

_Nahradí všechny příklady s vlastním nastavením._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Přepínač příkazového řádku azure k nasazení classic

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Hledat verze RHELU a bitové kopie

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Vytvoření Redhat Linux virtuálního počítače

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>Přístup k virtuálnímu počítači přes SSH

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Balíčky aktualizací YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Instalovat balíčky potřeby

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Teď, když požadované balíčky jsou nainstalovány na virtuální počítač Linux, dalším úkolem je připojení virtuálního počítače k spravované doméně.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Zjistit spravované doméně služby AAD Domain Services

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Inicializace protokolu kerberos

Ujistěte se, že zadáváte uživatel, který patří do skupiny "Administrators AAD řadič domény. Pouze tito uživatelé se může připojit počítače k spravované doméně.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Připojení počítače k doméně

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Ověřte, zda že je počítač připojený k doméně

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Další kroky

* [Red Hat aktualizace infrastruktury (RHUI) pro na vyžádání Red Hat Enterprise Linux virtuálních počítačů v Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Nastavení pro virtuální počítače ve službě Správce prostředků Azure Key Vault](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Nasazení a správě virtuálních počítačů pomocí šablon Azure Resource Manageru a rozhraní příkazového řádku Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
