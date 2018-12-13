---
title: Jak připojit úložiště objektů Blob v Azure jako systém souborů v Linuxu | Dokumentace Microsoftu
description: Připojení kontejneru Azure Blob storage s FUSE v systému Linux
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: seguler
ms.openlocfilehash: 2374875512bba55409ef43906acb20238c77158f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268457"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Postup připojení služby Blob storage jako systém souborů s blobfuse

## <a name="overview"></a>Přehled
[Blobfuse](https://github.com/Azure/azure-storage-fuse) je virtuálním souborovém systému ovladače pro úložiště objektů Blob v Azure. Blobfuse umožňuje přístup k existující data objektů blob bloku v účtu úložiště v systému Linux. Azure Blob storage je služba pro úložiště objektů a nemá hierarchického oboru názvů. Blobfuse poskytuje tento obor názvů pomocí schématu virtuální adresář s dopředného lomítka "/" jako oddělovač.  

Tato příručka ukazuje, jak používat blobfuse a připojení kontejneru úložiště objektů Blob v Linuxu a používat data. Další informace o blobfuse, přečtěte si podrobnosti v [úložiště blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse nezaručuje POSIX 100 % dodržování předpisů, jako je jednoduše překládá požadavky do [rozhraní REST API služby Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Například jsou atomické v rámci specifikace POSIX, ale ne v blobfuse operace přejmenování.
> Úplný seznam rozdílů mezi nativní systém souborů a blobfuse, navštivte web [úložiště zdrojového kódu blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Instalace blobfuse v Linuxu
Blobfuse binární soubory jsou k dispozici na [Microsoft úložiště softwaru Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) pro distribuce operačních systémů se systémem Ubuntu a RHEL. K instalaci blobfuse na těchto distribucí, nakonfigurujte jednu z úložiště ze seznamu. Můžete také sestavit binární soubory z následujících zdrojového kódu [kroky instalace služby Azure Storage](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) Pokud nejsou k dispozici pro vaši distribuci žádné binární soubory.

Blobfuse podporuje instalaci na Ubuntu 14.04 a 16.04. Spusťte tento příkaz, abyste měli jistotu, že máte jeden z těchto verzí nasazení:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Nakonfigurujte úložiště balíčků tak Microsoft
Konfigurace [úložiště balíčků Linux pro produkty společnosti Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Jako příklad, distribucí Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Podobně, změňte adresu URL na `.../rhel/7/...` přejděte distribuci Enterprise Linux 7.

Další příklad Ubuntu 14.04 distribucí:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Podobně, změňte adresu URL na `.../ubuntu/16.04/...` tak, aby odkazoval Ubuntu 16.04 distribuce.

### <a name="install-blobfuse"></a>Nainstalujte blobfuse

Na Ubuntu nebo Debian distribuce:
```bash
sudo apt-get install blobfuse
```

Na Enterprise Linuxovou distribuci:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Příprava pro připojení
Blobfuse poskytuje jako nativní výkon tím, že vyžaduje dočasné cesty v systému souborů do vyrovnávací paměti a mezipaměti všechny otevřené soubory. Pro toto dočasné cesty zvolte většina výkonné disku nebo použít disku paměti RAM pro zajištění nejlepšího výkonu. 

> [!NOTE]
> Blobfuse ukládá veškerý obsah otevřít soubor v dočasné cesty. Ujistěte se, že máte dostatek místa pro toto všechny otevřené soubory. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Volitelné) Použít disku paměti RAM pro dočasné cesty
Následující příklad vytvoří disk RAM o velikosti 16 GB a adresář pro blobfuse. Zvolte velikost na základě vašich potřeb. Tato ramdisk umožňuje blobfuse na otevřené soubory ve velikosti až 16 GB. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Použít SSD jako dočasné cesty
V Azure můžete použít dočasné disky (SSD) k dispozici na virtuálních počítačích stanovit blobfuse vyrovnávací paměti s nízkou latencí. V Ubuntu distribucí, se připojí tento dočasný disk na "/ mnt". V distribuce operačních systémů Red Hat a CentOS, se připojí disk na "/ mnt/zdroje /".

Ujistěte se, že váš uživatel má přístup k dočasné cesty:
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Nakonfigurujte přihlašovací údaje účtu úložiště
Blobfuse vyžaduje vaše přihlašovací údaje k uložení do textového souboru v následujícím formátu: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```

Po vytvoření tohoto souboru, ujistěte se, že chcete omezit přístup, aby žádný jiný uživatel ho může číst.
```bash
chmod 700 fuse_connection.cfg
```

> [!NOTE]
> Pokud vytvoříte konfigurační soubor na Windows, nezapomeňte spustit `dos2unix` opravuje a soubor převést na formát Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Vytvořte prázdný adresář pro připojení
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Připojení

> [!NOTE]
> Úplný seznam možností připojení, zkontrolujte [úložiště blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

K připojení blobfuse, spusťte následující příkaz s uživateli. Tento příkaz připojí kontejner určený v "/ path/to/fuse_connection.cfg" do umístění "/ mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Teď byste měli mít přístup k objektům BLOB bloku v regulární systému rozhraní API. Uživatel, který připojí adresář je jediná osoba, která k němu přístup, ve výchozím nastavení, která chrání přístup. Chcete-li povolit přístup ke všem uživatelům, můžete připojit přes možnost ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Další postup

* [Blobfuse domovské stránky](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Sestava blobfuse problémy](https://github.com/Azure/azure-storage-fuse/issues) 

