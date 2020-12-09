---
title: Postup připojení úložiště objektů BLOB v Azure jako systému souborů na platformě Linux | Microsoft Docs
description: Naučte se připojit kontejner úložiště objektů BLOB v Azure pomocí blobfuse, ovladače virtuálního systému souborů na platformě Linux.
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: 8de395e34b43a4edad2affa591adb8ab34ff9e66
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921695"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Postup připojení úložiště objektů BLOB jako systému souborů pomocí blobfuse

## <a name="overview"></a>Přehled
[Blobfuse](https://github.com/Azure/azure-storage-fuse) je ovladač virtuálního systému souborů pro úložiště objektů BLOB v Azure. Blobfuse umožňuje přístup k existujícím datům objektů blob bloku v účtu úložiště prostřednictvím systému souborů Linux. Blobfuse používá schéma virtuálního adresáře s lomítkem (/) jako oddělovačem.  

V této příručce se dozvíte, jak používat blobfuse a jak připojit kontejner úložiště objektů blob na Linux a přistupovat k datům. Pokud chcete získat další informace o blobfuse, přečtěte si podrobnosti v [úložišti blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nezaručuje dodržování předpisů POSIX 100%, protože jednoduše překládá požadavky do [objektů BLOB REST API](/rest/api/storageservices/blob-service-rest-api). Například operace přejmenování jsou atomické v POSIX, ale ne v blobfuse.
> Úplný seznam rozdílů mezi nativním systémem souborů a blobfuse najdete [v úložišti zdrojového kódu blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalace blobfuse v systému Linux
Blobfuse binární soubory jsou k dispozici v [úložištích softwaru společnosti Microsoft pro Linux](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) pro distribuce Ubuntu a RHEL. Pokud chcete na tyto distribuce nainstalovat blobfuse, nakonfigurujte v seznamu jedno z úložišť. Binární soubory ze zdrojového kódu můžete sestavit i v následujících [krocích Azure Storage instalace](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) , pokud pro vaši distribuci nejsou k dispozici žádné binární soubory.

Blobfuse podporuje instalaci na Ubuntu 14,04, 16,04, 18,04 a 20,04. Spusťte tento příkaz, abyste se ujistili, že máte nasazenou jednu z těchto verzí:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Konfigurace úložiště balíčků Microsoftu
Nakonfigurujte [úložiště balíčků pro Linux pro produkty Microsoftu](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Například na distribuci Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobně změňte adresu URL tak, aby `.../rhel/7/...` odkazovala na distribuci Enterprise Linux 7.

Další příklad pro distribuci Ubuntu 14,04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobně změňte adresu URL na `.../ubuntu/16.04/...` nebo `.../ubuntu/18.04/...` na odkaz na jinou verzi Ubuntu.

### <a name="install-blobfuse"></a>Nainstalovat blobfuse

Při distribuci Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Na distribuci Enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Příprava na připojení
Blobfuse poskytuje nativní výkon tím, že v systému souborů vyžaduje dočasnou cestu pro ukládání do vyrovnávací paměti a ukládání všech otevřených souborů do mezipaměti. Pro tuto dočasnou cestu vyberte nejvíce výkonného disku, nebo použijte disk RAM k dosažení nejlepšího výkonu. 

> [!NOTE]
> Blobfuse ukládá všechny otevřené obsahy souborů v dočasné cestě. Ujistěte se, že máte dostatek místa pro všechny otevřené soubory. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Volitelné Pro dočasnou cestu použít disk RAM
Následující příklad vytvoří disk RAM o velikosti 16 GB a adresář pro blobfuse. Vyberte velikost podle svých potřeb. Tento disk RAM umožňuje blobfuse otevírání souborů o velikosti až 16 GB. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Použití SSD jako dočasné cesty
V Azure můžete použít dočasné disky (SSD), které jsou k dispozici na vašich virtuálních počítačích, a poskytnout tak pro blobfuse vyrovnávací paměť s nízkou latencí. V Ubuntu distribucích je tento dočasný disk připojen k "/mnt". V distribucích Red Hat a CentOS je disk připojený k/mnt/Resource/.

Ujistěte se, že uživatel má přístup k dočasné cestě:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurace přihlašovacích údajů účtu úložiště
Blobfuse vyžaduje, aby vaše přihlašovací údaje byly uložené v textovém souboru v následujícím formátu: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName`Je předponou pro váš účet úložiště – ne úplnou adresu URL.

Vytvořit tento soubor pomocí:

```
touch ~/fuse_connection.cfg
```

Po vytvoření a úpravě tohoto souboru nezapomeňte omezit přístup, aby ho žádní jiní uživatelé nemohli číst.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Pokud jste v systému Windows vytvořili konfigurační soubor, nezapomeňte spustit program, který upraví `dos2unix` a převede soubor na formát systému UNIX. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Vytvořit prázdný adresář pro připojení
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Připojení

> [!NOTE]
> Úplný seznam možností připojení najdete [v úložišti blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Pro připojení blobfuse spusťte následující příkaz s vaším uživatelem. Tento příkaz připojí kontejner určený v souboru '/path/to/fuse_connection. cfg ' do umístění '/myContainer '.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Nyní byste měli mít přístup k objektům blob bloku prostřednictvím běžných rozhraní API systému souborů. Uživatel, který je připojuje k adresáři, je jediná osoba, která k němu má přístup, který ve výchozím nastavení přistupuje k zabezpečení přístupu. Pokud chcete přístup všem uživatelům dovolit, můžete se připojit prostřednictvím možnosti ```-o allow_other``` . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Další kroky

* [Domovská stránka Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Nahlásit problémy blobfuse](https://github.com/Azure/azure-storage-fuse/issues)
