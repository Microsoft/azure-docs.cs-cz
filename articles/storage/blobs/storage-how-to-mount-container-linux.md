---
title: Tom, jak připojit úložiště objektů Blob v Azure jako systém souborů v systému Linux | Microsoft Docs
description: Kontejner úložiště objektů Blob v Azure s POJISTKA připojení v systému Linux
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 44b0c36ad366d50480212adff9cdb8b10a17db05
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>Tom, jak připojit úložiště objektů Blob jako systém souborů s blobfuse (Preview)

## <a name="overview"></a>Přehled
[Blobfuse](https://github.com/Azure/azure-storage-fuse) je ovladač virtuálním souborovém systému pro Azure Blob Storage, který umožňuje přístup k vaší stávající data objektů blob bloku v účtu úložiště v systému Linux. Azure Blob Storage je služba objekt úložiště a proto nemá hierarchického oboru názvů. Blobfuse poskytuje tento obor názvů pomocí schéma virtuálního adresáře s použitím nástroje dopředného lomítko '/' jako oddělovač.  

Tento průvodce vám ukáže, jak používat blobfuse a připojte kontejner úložiště objektů Blob na data o přístupu a Linux. Další informace o blobfuse, přečtěte si podrobnosti v [úložiště blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nezaručí POSIX 100 % dodržování předpisů, stejně jako jednoduše převádí požadavky do [Blob REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Například jsou atomické POSIX, ale ne v blobfuse operace přejmenování.
> Úplný seznam rozdíly mezi nativní systém souborů a blobfuse, najdete v článku [úložiště zdrojového kódu blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Nainstalujte blobfuse v systému Linux
Binární soubory Blobfuse jsou k dispozici na [úložiště softwaru společnosti Microsoft pro Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Abyste mohli nainstalovat blobfuse, nakonfigurujte jednu z těchto úložiště.

### <a name="configure-the-microsoft-package-repository"></a>Konfigurace úložiště balíčků Microsoft
Konfigurace [úložiště Linux balíčků pro produkty společnosti Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Jako příklad v distribučním Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobně, změňte adresu url na `.../rhel/7/...` tak, aby odkazoval jako distribučního Enterprise Linux 7.

Dalším příkladem na Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobně, změňte adresu url na `.../ubuntu/16.04/...` tak, aby odkazoval Ubuntu 16.04 distribuční.

### <a name="install-blobfuse"></a>Nainstalujte blobfuse

V distribučním Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

V distribučním Enterprise Linux:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Příprava pro připojení
Blobfuse vyžaduje dočasná cesta v souborovém systému do vyrovnávací paměti a mezipaměti všech otevřených souborů, která pomáhá poskytuje nativní jako výkon. Tato dočasná cesta zvolte většina původce disku nebo pro nejlepší výkon použijte disku paměti RAM. 

> [!NOTE]
> Blobfuse ukládá veškerý obsah otevřít soubor v dočasné složce. Zajistěte, aby mohla pojmout všechny otevřené soubory k dispozici dostatek místa. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Volitelné) Použít disku paměti RAM pro dočasná cesta
Následující příklad vytvoří ramdisk 16 GB, jakož i vytváření adresáře pro blobfuse. Zvolte velikost na základě potřeb. Tato disku paměti RAM umožňuje blobfuse k otevření souborů až 16 GB velikost. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Použijte SSD pro dočasná cesta
V Azure můžete zajistit vyrovnávací paměti s nízkou latencí pro blobfuse používat dočasné disky (SSD Solid-State Drive) k dispozici na virtuální počítače. V Ubuntu distribuce, je tento disk pro dočasné připojena v ' / mnt, zatímco je připojena v ' / mnt/prostředků /' v distribuce RedHat a CentOS.

Zajistěte, aby byl uživatel přístup k dočasná cesta:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurovat přihlašovací údaje účtu úložiště
Blobfuse vyžaduje vaše přihlašovací údaje k uložení do textového souboru v následujícím formátu: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Po vytvoření tohoto souboru, ujistěte se, zda je omezení přístupu, aby žádný jiný uživatel ho může číst.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Vytvoření prázdného adresáře pro připojení
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>připojení

> [!NOTE]
> Úplný seznam možností připojení, zkontrolujte [úložiště blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Aby blobfuse připojení spusťte následující příkaz pomocí svého uživatele. Tento příkaz připojí zadaný v kontejner ' / path/to/fuse_connection.cfg' do umístění, nebo můj_kontejner '.

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teď byste měli mít přístup k vaší objekty BLOB bloku v pravidelných systému rozhraní API. Všimněte si, že připojený adresář můžete přistupovat pouze uživatele, připojení, který zabezpečuje přístup. Pokud chcete povolit přístup pro všechny uživatele, můžete připojit prostřednictvím možnosti ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Další postup

* [Blobfuse domovské stránky](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Sestava blobfuse problémy](https://github.com/Azure/azure-storage-fuse/issues) 

