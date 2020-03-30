---
title: Jak připojit azure blob úložiště jako souborový systém na Linux | Dokumenty společnosti Microsoft
description: Připojení kontejneru úložiště objektů Blob Azure s FUSE na Linuxu
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: a0a03df59bc6ecffcb4f0a701616297f2da78fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061431"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Jak připojit úložiště objektů Blob jako souborový systém s blobfuse

## <a name="overview"></a>Přehled
[Blobfuse](https://github.com/Azure/azure-storage-fuse) je ovladač virtuálního systému souborů pro úložiště objektů Blob Azure. Blobfuse umožňuje přístup k existujícím datům blob bloku ve vašem účtu úložiště prostřednictvím souborového systému Linux. Blobfuse používá schéma virtuálního adresáře s lomítkem pro lomítko '/' jako oddělovač.  

Tato příručka ukazuje, jak používat blobfuse a připojit kontejner úložiště objektů Blob na Linuxu a přístup k datům. Chcete-li se dozvědět více o blobfuse, přečtěte si podrobnosti v [úložišti blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nezaručuje 100% posix dodržování předpisů, protože jednoduše překládá požadavky do [objektů API BLOB REST](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Operace přejmenování jsou například atomické v POSIX, ale ne v blobfuse.
> Úplný seznam rozdílů mezi nativním souborovým systémem a blobfuse naleznete [v úložišti zdrojového kódu blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalace blobfuse na Linuxu
Binární soubory blobfuse jsou k dispozici [na softwarových repozitářích společnosti Microsoft pro linuxové](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) distribuce a distribuce RHEL. Chcete-li nainstalovat blobfuse na tyto distribuce, nakonfigurujte jeden z úložišť ze seznamu. Binární soubory můžete také vytvořit ze zdrojového kódu podle [kroků instalace úložiště Azure,](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) pokud pro vaši distribuci nejsou k dispozici žádné binární soubory.

Blobfuse podporuje instalaci na Ubuntu 14.04, 16.04 a 18.04. Spusťte tento příkaz a ujistěte se, že máte jednu z těchto verzí nasazenou:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Konfigurace úložiště balíčků Microsoftu
Konfigurace [úložiště balíčků Linuxu pro produkty společnosti Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Jako příklad, na enterprise linuxové distribuce 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobně změňte adresu `.../rhel/7/...` URL tak, aby přectola na distribuci Enterprise Linux 7.

Dalším příkladem na distribuci Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobně změňte adresu `.../ubuntu/16.04/...` URL `.../ubuntu/18.04/...` na jinou verzi Ubuntu nebo na ni.

### <a name="install-blobfuse"></a>Instalace blůzy

Na distribuci Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

Na distribuci Enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Připravte se na montáž
Blobfuse poskytuje nativní výkon tím, že vyžaduje dočasnou cestu v systému souborů do vyrovnávací paměti a ukládání do mezipaměti všech otevřených souborů. Pro tuto dočasnou cestu zvolte nejvýkonnější disk nebo použijte ramdisk pro dosažení nejlepšího výkonu. 

> [!NOTE]
> Blobfuse ukládá veškerý otevřený obsah souboru v dočasné cestě. Ujistěte se, že máte dostatek místa pro všechny otevřené soubory. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Nepovinné) Použití ramdisku pro dočasnou cestu
Následující příklad vytvoří ramdisk 16 GB a adresář pro blobfuse. Vyberte velikost podle svých potřeb. Tento ramdisk umožňuje blobfuse otevřít soubory až do velikosti 16 GB. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Použití ssd diody jako dočasné cesty
V Azure můžete použít dočasné disky (SSD) dostupné na virtuálních počítačích k poskytnutí vyrovnávací paměti s nízkou latencí pro blobfuse. V distribucích Ubuntu je tento dočasný disk namontován na '/mnt'. V distribucích Red Hat a CentOS je disk připojen na '/mnt/resource/'.

Ujistěte se, že uživatel má přístup k dočasné cestě:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurace přihlašovacích údajů účtu úložiště
Blobfuse vyžaduje, aby vaše přihlašovací údaje byly uloženy v textovém souboru v následujícím formátu: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Jedná `accountName` se o předponu pro váš účet úložiště – nikoli úplnou adresu URL.

Vytvořte tento soubor pomocí:

```
touch ~/fuse_connection.cfg
```

Po vytvoření a úpravě tohoto souboru nezapomeňte omezit přístup, aby ho nemohli přečíst žádní jiní uživatelé.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Pokud jste vytvořili konfigurační soubor `dos2unix` v systému Windows, ujistěte se, že spustit dezinfikovat a převést soubor do formátu Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Vytvoření prázdného adresáře pro montáž
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Připojení

> [!NOTE]
> Úplný seznam možností připojení naleznete [v úložišti blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Chcete-li připojit blobfuse, spusťte následující příkaz s uživatelem. Tento příkaz připojí kontejner zadaný v '/path/to/fuse_connection.cfg' na umístění '/mycontainer'.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Nyní byste měli mít přístup k objektům BLOB bloku prostřednictvím běžných souborů API systému souborů. Uživatel, který připojí adresář, je ve výchozím nastavení jedinou osobou, která k němu má přístup, což zabezpečuje přístup. Chcete-li povolit přístup všem uživatelům, ```-o allow_other```můžete připojit pomocí možnosti . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Další kroky

* [Domovská stránka blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Nahlásit problémy s blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

