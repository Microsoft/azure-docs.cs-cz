---
title: 'Rychlý Start: spuštění pracovního postupu – Microsoft Genomics'
description: Tento článek ukazuje, jak načíst vstupní data do Azure Blob Storage a jak spustit pracovní postup ve službě Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: 4beb1c31f34ec4e8d26228cfe4f30f5109a1b60c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394539"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Rychlý start: Spuštění pracovního postupu ve službě Microsoft Genomics

V tomto rychlém startu nahrajete vstupní data do účtu služby Azure Blob Storage a spustíte pracovní postup prostřednictvím služby Microsoft Genomics pomocí klienta Pythonu s využitím genomiky. Microsoft Genomics je škálovatelná a bezpečná služba pro sekundární analýzu, která dokáže rychle analyzovat genom počínaje od nezpracovaných čtení a vytváří zarovnaná čtení a volání variant. 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12 +](https://www.python.org/downloads/release/python-2714/), s `pip` nainstalovaným a `python` v systémové cestě. Klient Microsoft Genomics není kompatibilní s Pythonem 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Příprava: vytvoření účtu Microsoft Genomics na webu Azure Portal

Pokud chcete vytvořit účet Microsoft Genomics, přejděte k části [Vytvoření účtu genomiky](https://portal.azure.com/#create/Microsoft.Genomics) v Azure Portal. Pokud ještě nemáte předplatné Azure, založte si ho před vytvořením účtu Microsoft Genomics. 

![Microsoft Genomics v Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics v Azure Portal")

Nastavte v účtu Genomics následující údaje, viz předchozí obrázek. 

 |**Nastavení**          |  **Navrhovaná hodnota**  | **Popis pole** |
 |:-------------       |:-------------         |:----------            |
 |Předplatné         | Název vašeho předplatného|Toto je fakturační jednotka pro vaše služby Azure – podrobnosti o vašem předplatném najdete v části [Předplatná](https://account.azure.com/Subscriptions) |      
 |Skupina prostředků       | MyResourceGroup       |  Skupiny prostředků umožňují sdružení několika prostředků Azure (účet úložiště, účet Genomics atd.) do jedné skupiny pro zjednodušení správy. Další informace najdete v tématu [Skupiny prostředků](../azure-resource-manager/management/overview.md#resource-groups). Platné názvy skupin prostředků najdete v tématu [Pravidla pojmenování](/azure/architecture/best-practices/resource-naming). |
 |Název účtu         | MyGenomicsAccount     |Zvolte jedinečný identifikátor účtu. Informace o platných názvech najdete v části [Pravidla pojmenování](/azure/architecture/best-practices/resource-naming) |
 |Umístění                   | Západní USA 2                    |    Služba je dostupná v oblastech USA – západ 2, Západní Evropa a Jihovýchodní Asie |

V horním řádku nabídek můžete vybrat **oznámení** a monitorovat proces nasazení.

![Oznámení](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Oznámení")

Další informace o Microsoft Genomics najdete v tématu [co je Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Příprava: instalace pythonového klienta Microsoft Genomics

V místním prostředí je potřeba nainstalovat Python i Microsoft Genomics klienta Pythonu `msgen` . 

### <a name="install-python"></a>Instalace Pythonu

Klient Pythonu Microsoft Genomics je kompatibilní s Pythonem 2.7.12 nebo novější verzí 2.7. xx. 2.7.14 je navržená verze. Můžete si ji stáhnout [tady](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3. x není kompatibilní s Pythonem 2.7. xx.  `msgen` je aplikace Python 2,7. Pokud je spuštěný `msgen` , ujistěte se, že aktivní prostředí Pythonu používá verzi 2.7. xx v Pythonu. Při pokusu o použití `msgen` se 3. x verze Pythonu se můžou zobrazit chyby.

### <a name="install-the-microsoft-genomics-python-client-msgen"></a>Instalace klienta Microsoft Genomics Pythonu `msgen`

Pomocí Pythonu `pip` nainstalujte klienta Microsoft Genomics `msgen` . Následující pokyny předpokládají, že Python2. x je již ve vaší systémové cestě. Pokud máte problémy s `pip` nerozpoznaným instalačním rozhraním, musíte do systémové cesty přidat Python a složku Scripts.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Pokud nechcete instalovat `msgen` jako binární soubor v rámci systému a upravovat balíčky Pythonu v rámci systému, použijte `–-user` příznak with `pip` .
Když použijete instalaci nebo setup.py založenou na balíčku, nainstalují se všechny požadované balíčky.

### <a name="test-msgen-python-client"></a>Test `msgen` klienta v Pythonu
Pokud chcete otestovat klienta Microsoft Genomics, Stáhněte si konfigurační soubor z vašeho účtu genomiky. V Azure Portal přejděte na svůj účet genomika tak, že vyberete **všechny služby** v levém horním rohu a pak vyhledáte a vyberete účty genomiky.

![Najít Microsoft Genomics v Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Najít Microsoft Genomics v Azure Portal")

Vyberte účet genomiky, který jste právě vytvořili, přejděte na **přístupové klíče** a Stáhněte si konfigurační soubor.

![Stáhnout konfigurační soubor z Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Stáhnout konfigurační soubor z Microsoft Genomics")

Vyzkoušejte správnou funkci pythonového klienta Microsoft Genomics pomocí následujícího příkazu

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Vytvoření účtu Microsoft Azure Storage 
Služba Microsoft Genomics očekává vstupy uložené jako objekty blob bloku v účtu úložiště Azure. Také výstupní soubory zapisuje jako objekty blob bloku do uživatelem zadaného kontejneru v účtu úložiště Azure. Vstupy a výstupy můžou patřit do různých účtů úložiště.
Pokud již máte data v účtu úložiště Azure, stačí se ujistit, že je ve stejném umístění jako účet Genomics. V opačném případě se za provozu Microsoft Genomics služby účtují poplatky za odchozí data. Pokud ještě nemáte účet úložiště Azure, budete ho muset vytvořit a nahrát data. Další informace o účtech Azure Storage najdete [tady](../storage/common/storage-account-create.md), včetně toho, co je účet úložiště a jaké služby nabízí. Pokud chcete vytvořit účet úložiště Azure, přejděte k části [Vytvoření účtu úložiště](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) v Azure Portal.  

![Stránka pro vytvoření účtu úložiště](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Stránka pro vytvoření účtu úložiště")

Nakonfigurujte účet úložiště pomocí následujících informací, jak je znázorněno na předchozím obrázku. Použijte většinu standardních možností pro účet úložiště a určete jenom to, že je účet BlobStorage, ne pro obecné účely. Úložiště objektů blob nabízí 2–5× rychlejší stahování a nahrávání.  Doporučuje se výchozí model nasazení Azure Resource Manager.  

 |**Nastavení**          |  **Navrhovaná hodnota**  | **Popis pole** |
 |:-------------------------       |:-------------         |:----------            |
 |Předplatné         | Vaše předplatné Azure |Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.azure.com/Subscriptions). |      
 |Skupina prostředků       | MyResourceGroup       |  Můžete vybrat stejnou skupinu prostředků jako váš účet genomiky. Platné názvy skupin prostředků najdete v tématu [pravidla pojmenování](/azure/architecture/best-practices/resource-naming) . |
 |Název účtu úložiště         | MyStorageAccount     |Zvolte jedinečný identifikátor účtu. Platné názvy najdete v tématu [pravidla pojmenování](/azure/architecture/best-practices/resource-naming) . |
 |Umístění                  | Západní USA 2                  | Použijte stejné umístění jako umístění vašeho účtu genomika, abyste snížili náklady na výstup a snížili latenci.  | 
 |Výkon                  | Standard                   | Výchozí nastavení je Standard. Další podrobnosti o účtech úložiště úrovně Standard a Premium najdete v tématu [Úvod do služby Microsoft Azure Storage](../storage/common/storage-introduction.md) .    |
 |Druh účtu       | Blob Storage       |  Úložiště objektů blob nabízí 2–5× rychlejší stahování a nahrávání než úložiště pro obecné účely. |
 |Replikace                  | (Locally redundant storage) Místně redundantní úložiště                  | Místně redundantní úložiště replikuje data třikrát v rámci oblasti, ve které jste vytvořili účet úložiště. Další informace najdete v tématu [replikace Azure Storage](../storage/common/storage-redundancy.md) .    |
 |Úroveň přístupu                  | Horká                   | Horká úroveň přístupu znamená, že k objektům v účtu úložiště budete přistupovat častěji.    |

Pak vyberte **zkontrolovat + vytvořit** a vytvořte účet úložiště. Stejně jako při vytváření účtu genomiky můžete vybrat **oznámení** v horním řádku nabídek a monitorovat proces nasazení. 

## <a name="upload-input-data-to-your-storage-account"></a>Nahrání vstupních dat do účtu úložiště

Služba Microsoft Genomics očekává spárované čtení (soubory souborů fastq nebo BAM) jako vstupní soubory. Můžete nahrát svoje vlastní data, nebo použít veřejně dostupná ukázková data, která jsme pro vás připravili. Pokud chcete použít veřejně dostupné ukázková data, najdete je tady:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

V rámci účtu úložiště budete potřebovat jeden kontejner objektů blob pro vstupní data a druhý kontejner objektů blob pro výstupní data.  Nahrajte vstupní data do vstupního kontejneru objektů blob. K tomu můžete použít různé nástroje, včetně [Průzkumník služby Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter)nebo [AzCopy](../storage/common/storage-use-azcopy-v10.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-msgen-python-client"></a>Spuštění pracovního postupu prostřednictvím služby Microsoft Genomics pomocí `msgen` klienta Pythonu

Pokud chcete spustit pracovní postup prostřednictvím služby Microsoft Genomics, upravte soubor *config.txt* a určete vstupní a výstupní kontejner úložiště pro vaše data.
Otevřete soubor *config.txt* , který jste stáhli ze svého účtu genomiky. V sekcích, které potřebujete zadat, jsou klíč předplatného a šest položek dole, název účtu úložiště, klíč a název kontejneru pro vstup i výstup. Tyto informace můžete najít tak, že přejdete na Azure Portal a **získáte přístup k klíčům** pro svůj účet úložiště nebo přímo z Průzkumník služby Azure Storage.  

![Konfigurace genomiky](./media/quickstart-run-genomics-workflow-portal/genomics-config.PNG "Konfigurace genomiky")

Chcete-li spustit GATK4, nastavte `process_name` parametr na `gatk4` .

Ve výchozím nastavení služba genomiky produkuje soubory VCF. Pokud byste chtěli výstup gVCF místo výstupu VCF (ekvivalent `-emitRefConfidence` Genome 3. x a `emit-ref-confidence` v Genome 4. x), přidejte `emit_ref_confidence` parametr do *config.txt* a nastavte jej na `gvcf` , jak je znázorněno na předchozím obrázku.  Chcete-li změnit zpět na výstup VCF, odeberte jej z *config.txt* souboru nebo nastavte `emit_ref_confidence` parametr na `none` . 

`bgzip` je nástroj, který komprimuje soubor VCF nebo gvcf, a `tabix` vytvoří index pro komprimovaný soubor. Ve výchozím nastavení se služba genomiky spouští `bgzip` za `tabix` výstupem ". g. vcf", ale nespouští tyto nástroje ve výchozím nastavení pro výstup ". vcf". Při spuštění služba vytvoří soubory. gz (bgzip Output) a ". TBI" (tabix Output). Argument je logická hodnota, která je ve výchozím nastavení nastavena na hodnotu false pro výstup ". vcf" a na hodnotu true ve výchozím nastavení pro výstup ". g. vcf". Chcete-li použít příkaz v příkazovém řádku, zadejte `-bz` nebo `--bgzip-output` jako `true` (spusťte bgzip a tabix) nebo `false` . Chcete-li použít tento argument v souboru *config.txt* , přidejte `bgzip_output: true` nebo `bgzip_output: false` do souboru.

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-using-the-msgen-python-client"></a>Odeslání pracovního postupu službě Microsoft Genomics pomocí `msgen` klienta Python

Pomocí pythonového klienta Microsoft Genomics odešlete svůj pracovní postup pomocí následujícího příkazu:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

Stav pracovního postupu můžete zkontrolovat následujícím příkazem: 
```python
msgen list -f c:\temp\config.txt 
```

Po dokončení pracovního postupu můžete zobrazit výstupní soubory v účtu služby Azure Storage ve výstupním kontejneru, který jste nakonfigurovali. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste nahráli ukázková vstupní data do služby Azure Storage a odeslali jste pracovní postup službě Microsoft Genomics pomocí `msgen` klienta Pythonu. Další informace o dalších typech vstupních souborů, které se dají použít spolu s Microsoft Genomics službou, najdete na následujících stránkách: [spárované souborů fastq](quickstart-input-pair-FASTQ.md)  |  [BAMS](quickstart-input-BAM.md)  |  [Multiple souborů fastq nebo BAM](quickstart-input-multiple.md). Tento kurz můžete také prozkoumat pomocí našeho [Azure Notebooks příkladu](https://aka.ms/genomicsnotebook) stažením souboru "genomiky. ipynb" a pomocí čtečky poznámkového bloku, jako je [Jupyter](../notebooks/tutorial-create-run-jupyter-notebook.md) , otevřít soubor a spustit ho.