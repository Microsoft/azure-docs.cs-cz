---
title: 'Úvodní příručka: Spuštění pracovního postupu – Microsoft Genomics'
description: Tento článek ukazuje, jak načíst vstupní data do Azure Blob Storage a jak spustit pracovní postup ve službě Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76931810"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Rychlý start: Spuštění pracovního postupu ve službě Microsoft Genomics

V tomto rychlém startu nahrajete vstupní data do účtu úložiště objektů Blob Azure a spustíte pracovní postup prostřednictvím služby Microsoft Genomics pomocí klienta Python Genomics. Microsoft Genomics je škálovatelná a bezpečná služba pro sekundární analýzu, která dokáže rychle analyzovat genom počínaje od nezpracovaných čtení a vytváří zarovnaná čtení a volání variant. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12+](https://www.python.org/downloads/release/python-2714/) `pip` , s `python` nainstalovaným a v systémové cestě. Klient Microsoft Genomics není kompatibilní s Pythonem 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Příprava: vytvoření účtu Microsoft Genomics na webu Azure Portal

Pokud chcete vytvořit účet Microsoft Genomics, přejděte na [vytvoření účtu Genomics](https://portal.azure.com/#create/Microsoft.Genomics) na webu Azure Portal. Pokud ještě nemáte předplatné Azure, založte si ho před vytvořením účtu Microsoft Genomics. 

![Microsoft Genomics na portálu Azure](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics na portálu Azure")

Nastavte v účtu Genomics následující údaje, viz předchozí obrázek. 

 |**Nastavení**          |  **Navrhovaná hodnota**  | **Popis pole** |
 |:-------------       |:-------------         |:----------            |
 |Předplatné         | Název vašeho předplatného|Toto je fakturační jednotka pro vaše služby Azure – podrobnosti o vašem předplatném najdete v části [Předplatná](https://account.azure.com/Subscriptions) |      
 |Skupina prostředků       | MyResourceGroup       |  Skupiny prostředků umožňují sdružení několika prostředků Azure (účet úložiště, účet Genomics atd.) do jedné skupiny pro zjednodušení správy. Další informace najdete v tématu [Skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Platné názvy skupin prostředků najdete v tématu [Pravidla pojmenování](/azure/architecture/best-practices/resource-naming). |
 |Název účtu         | MyGenomicsAccount     |Zvolte jedinečný identifikátor účtu. Informace o platných názvech najdete v části [Pravidla pojmenování](/azure/architecture/best-practices/resource-naming) |
 |Umístění                   | USA – západ 2                    |    Služba je dostupná v oblastech USA – západ 2, Západní Evropa a Jihovýchodní Asie |

Můžete vybrat **Oznámení** v horním řádku nabídek pro sledování procesu nasazení.

![Oznámení](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Oznámení")

Další informace o microsoft genomices najdete v tématu [Co je Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Příprava: instalace pythonového klienta Microsoft Genomics

Musíte nainstalovat Python a klientmicrosoft Genomics Python ve vašem místním prostředí. 

### <a name="install-python"></a>Instalace Pythonu

Klient Microsoft Genomics Python je kompatibilní s Pythonem 2.7.12 nebo novější verzí 2.7.xx. 2.7.14 je navrhovaná verze. Můžete si ji stáhnout [tady](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x není kompatibilní s Pythonem 2.7.xx.  MSGen je aplikace Pythonu 2.7. Pokud používáte MSGen, ujistěte se, že vaše aktivní prostředí Pythonu používá Python verze 2.7.xx. Pokud se pokusíte použít MSGen s Pythonem verze 3.x, může docházet k chybám.

### <a name="install-the-microsoft-genomics-client"></a>Instalace pythonového klienta Microsoft Genomics

Pomocí `pip` Pythonu nainstalujte klienta `msgen`Microsoft Genomics . Následující pokyny předpokládají, že je Python zahrnutý v proměnné path. Pokud máte problémy s `pip` instalací nebyl rozpoznán, je třeba přidat Python a podsložky skriptů do systémové cesty.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Pokud nechcete instalovat `msgen` jako binární soubor pro celý systém a upravovat balíčky Pythonu pro celý systém, použijte `–-user` příznak s . `pip`
Pokud použijete instalaci využívající balíčky nebo skript setup.py, nainstalují se všechny potřebné balíčky. V opačném případě jsou `msgen` základní požadované balíčky 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage). 
 * [Žádosti](https://pypi.python.org/pypi/requests). 

Tyto balíčky můžete nainstalovat pomocí `pip`, `easy_install` nebo standardní procedury `setup.py`. 

### <a name="test-the-microsoft-genomics-client"></a>Test pythonového klienta Microsoft Genomics
Chcete-li otestovat klienta Microsoft Genomics, stáhněte si konfigurační soubor ze svého účtu Genomics. Na webu Azure Portal přejděte na svůj účet Genomics tak, že vyberete **Všechny služby** v levém horním rohu a pak vyhledáte a vyberete účty Genomics.

![Najít Microsoft Genomics na webu Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Najít Microsoft Genomics na webu Azure Portal")

Vyberte účet Genomics, který jste právě vytvořili, přejděte na **přístupové klávesy**a stáhněte si konfigurační soubor.

![Stažení konfiguračního souboru z microsoft genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Stažení konfiguračního souboru z microsoft genomics")

Vyzkoušejte správnou funkci pythonového klienta Microsoft Genomics pomocí následujícího příkazu

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Vytvoření účtu úložiště Microsoft Azure 
Služba Microsoft Genomics očekává vstupy uložené jako objekty blob bloku v účtu úložiště Azure. Také výstupní soubory zapisuje jako objekty blob bloku do uživatelem zadaného kontejneru v účtu úložiště Azure. Vstupy a výstupy můžou patřit do různých účtů úložiště.
Pokud již máte data v účtu úložiště Azure, stačí se ujistit, že je ve stejném umístění jako účet Genomics. V opačném případě jsou při spuštění služby Microsoft Genomics účtovány poplatky za odchozí přenos. Pokud ještě nemáte účet úložiště Azure, musíte si ho vytvořit a nahrát data. Další informace o účtech úložiště Azure najdete [tady](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), včetně toho, co je účet úložiště a jaké služby poskytuje. Pokud chcete vytvořit účet úložiště Azure, přejděte na [Vytvořit účet úložiště](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) na webu Azure Portal.  

![Stránka vytvoření účtu úložiště](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Stránka vytvoření účtu úložiště")

Nakonfigurujte účet úložiště pomocí následujících informací, jak je znázorněno na předchozím obrázku. Použijte většinu standardních možností pro účet úložiště a určete jenom, že účet je BlobStorage, ne pro obecné účely. Úložiště objektů blob nabízí 2–5× rychlejší stahování a nahrávání.  Doporučuje se výchozí model nasazení Azure Resource Manager.  

 |**Nastavení**          |  **Navrhovaná hodnota**  | **Popis pole** |
 |:-------------------------       |:-------------         |:----------            |
 |Předplatné         | Vaše předplatné Azure |Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.azure.com/Subscriptions). |      
 |Skupina prostředků       | MyResourceGroup       |  Můžete vybrat stejnou skupinu prostředků jako váš účet Genomics. Platné názvy skupin prostředků naleznete v tématu [Zásady pojmenování](/azure/architecture/best-practices/resource-naming) |
 |Název účtu úložiště         | MyStorageAccount     |Zvolte jedinečný identifikátor účtu. Platné názvy naleznete v [tématu Pojmenování pravidel](/azure/architecture/best-practices/resource-naming) |
 |Umístění                  | USA – západ 2                  | Použijte stejné umístění jako umístění vašeho účtu Genomics, abyste snížili poplatky za odchozí přenos a snížili latenci.  | 
 |Výkon                  | Standard                   | Výchozí nastavení je Standard. Další podrobnosti o standardních a prémiových účtech úložiště najdete [v tématu Úvod do úložiště Microsoft Azure.](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Account kind (Druh účtu)       | Úložiště objektů BlobStorage       |  Úložiště objektů blob nabízí 2–5× rychlejší stahování a nahrávání než úložiště pro obecné účely. |
 |Replikace                  | (Locally redundant storage) Místně redundantní úložiště                  | Místně redundantní úložiště replikuje data třikrát v rámci oblasti, ve které jste vytvořili účet úložiště. Další informace najdete v tématu [Replikace úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Access tier (Vrstva přístupu)                  | Hot                   | Horká úroveň přístupu znamená, že k objektům v účtu úložiště budete přistupovat častěji.    |

Pak vyberte **Zkontrolovat + vytvořit** a vytvořte si účet úložiště. Stejně jako při vytváření účtu Genomics můžete vybrat **oznámení** v horním řádku nabídek a sledovat proces nasazení. 

## <a name="upload-input-data-to-your-storage-account"></a>Nahrání vstupních dat do účtu úložiště

Služba Microsoft Genomics očekává jako vstupní soubory spárované koncové čtení (soubory fastq nebo bam). Můžete nahrát svoje vlastní data, nebo použít veřejně dostupná ukázková data, která jsme pro vás připravili. Pokud chcete použít veřejně dostupné ukázková data, najdete je tady:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

V rámci účtu úložiště budete potřebovat jeden kontejner objektů blob pro vstupní data a druhý kontejner objektů blob pro výstupní data.  Nahrajte vstupní data do vstupního kontejneru objektů blob. K tomu lze použít různé nástroje, včetně [Průzkumníka úložiště Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter)nebo [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Spuštění pracovního postupu ve službě Microsoft Genomics pomocí pythonového klienta 

Chcete-li spustit pracovní postup prostřednictvím služby Microsoft Genomics, upravte soubor *config.txt* a určete vstupní a výstupní kontejner úložiště pro vaše data.
Otevřete soubor *config.txt,* který jste stáhli ze svého účtu Genomics. Oddíly, které je třeba zadat, jsou klíč předplatného a šest položek v dolní části, název účtu úložiště, klíč a název kontejneru pro vstup i výstup. Tyto informace najdete na webu Azure Portal na **přístupové klíče** pro váš účet úložiště nebo přímo z Průzkumníka úložiště Azure.  

![Genomika config](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomika config")

Pokud chcete spustit GATK4, nastavte `process_name` parametr `gatk4`na .

Ve výchozím nastavení služba Genomics vypisuje soubory VCF. Pokud chcete výstup gVCF spíše než výstup VCF `-emitRefConfidence` (ekvivalentní gatk `emit-ref-confidence` 3.x a GATK `emit_ref_confidence` 4.x), přidejte parametr do `gvcf` *souboru config.txt* a nastavte jej na , jak je znázorněno na předchozím obrázku.  Chcete-li přejít zpět na výstup VCF, odeberte jej `emit_ref_confidence` ze `none`souboru *config.txt* nebo nastavte parametr na . 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Odešlete svůj pracovní postup službě Microsoft Genomics pomocí klienta Microsoft Genomics

Pomocí pythonového klienta Microsoft Genomics odešlete svůj pracovní postup pomocí následujícího příkazu:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

Stav pracovního postupu můžete zkontrolovat následujícím příkazem: 
```python
msgen list -f c:\temp\config.txt 
```

Po dokončení pracovního postupu můžete zobrazit výstupní soubory ve vašem účtu úložiště Azure ve výstupním kontejneru, který jste nakonfigurovali. 

## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli ukázková vstupní data do úložiště Azure a `msgen` odeslali pracovní postup službě Microsoft Genomics prostřednictvím klienta Pythonu. Další informace o dalších typech vstupních souborů, které lze použít se službou Microsoft Genomics, naleznete na následujících stránkách: [paired FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ nebo BAM](quickstart-input-multiple.md). Tento kurz můžete prozkoumat také prostřednictvím našeho [kurzu ke službě Azure Notebooks](https://aka.ms/genomicsnotebook).
