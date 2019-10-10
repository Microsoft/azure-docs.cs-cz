---
title: 'Rychlý Start: spuštění pracovního postupu'
description: V tomto rychlém startu se dozvíte, jak načíst vstupní data do Azure Blob Storage a spustit pracovní postup prostřednictvím služby Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: e7c90cc0ce85f2a90cc2ddc2cd086fd2626f4d96
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248526"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Rychlý Start: spuštění pracovního postupu prostřednictvím služby Microsoft Genomics

V tomto rychlém startu se dozvíte, jak načíst vstupní data do Azure Blob Storage a spustit pracovní postup prostřednictvím služby Microsoft Genomics. Microsoft Genomics je škálovatelná a zabezpečená služba pro sekundární analýzu, která může rychle zpracovat genom od nezpracovaných čtení a vyprodukovat zarovnané čtení a volání variant. 

Začněte v několika krocích: 
1.  Nastavení: vytvořte účet Microsoft Genomics přes Azure Portal a nainstalujte do svého místního prostředí klienta Microsoft Genomics Python. 
2.  Nahrávání vstupních dat: Vytvořte Microsoft Azure účet úložiště pomocí Azure Portal a nahrajte vstupní soubory. Vstupní soubory by měly být spárované koncové čtení (soubory souborů fastq nebo BAM).
3.  Spustit: pomocí rozhraní příkazového řádku Microsoft Genomics můžete spouštět pracovní postupy prostřednictvím služby Microsoft Genomics. 

Další informace o Microsoft Genomics najdete v tématu [co je Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Nastavení: vytvoření účtu Microsoft Genomics v Azure Portal

Účet Microsoft Genomics vytvoříte tak, že přejdete do [Azure Portal](https://portal.azure.com/#create/Microsoft.Genomics). Pokud ještě nemáte předplatné Azure, vytvořte si ho ještě před vytvořením účtu Microsoft Genomics. 

![Microsoft Genomics na Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics na Azure Portal")



Nakonfigurujte svůj účet genomiky pomocí následujících informací, jak je znázorněno na předchozím obrázku. 

 |**Nastavením**          |  **Navrhovaná hodnota**  | **Popis pole** |
 |:-------------       |:-------------         |:----------            |
 |Předplatné         | Název vašeho předplatného|Toto je fakturační jednotka pro služby Azure – Podrobnosti o předplatném najdete v tématu [předplatná](https://account.azure.com/Subscriptions) . |      
 |Skupina prostředků       | MyResourceGroup       |  Skupiny prostředků umožňují seskupit více prostředků Azure (účet úložiště, účet genomiky atd.) do jedné skupiny pro jednoduchou správu. Další informace najdete v tématu [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Platné názvy skupin prostředků najdete v tématu [pravidla pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) . |
 |Název účtu         | MyGenomicsAccount     |Vyberte jedinečný identifikátor účtu. Platné názvy najdete v tématu [pravidla pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) . |
 |Umístění                   | Západní USA 2                    |    Služba je dostupná v Západní USA 2, Západní Evropa a jihovýchodní Asie |




Kliknutím na oznámení na horním panelu nabídek můžete monitorovat proces nasazení.
Oznámení ![Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box.png "Microsoft Genomics oznámení")



## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Nastavení: instalace klienta Microsoft Genomics Python

Uživatelé musí do svého místního prostředí instalovat jak Python, tak i Microsoft Genomics klienta Pythonu. 

### <a name="install-python"></a>Instalace Pythonu

Klient Pythonu Microsoft Genomics je kompatibilní s Pythonem 2,7. 12 nebo novější 2.7. xx verze; 2.7.15 je nejnovější verze v době psaní tohoto textu; 2.7.14 je navržená verze. Soubor ke stažení najdete [tady](https://www.python.org/downloads/). 

Poznámka: Python 3. x není kompatibilní s Pythonem 2.7. xx.  MSGen je aplikace Python 2,7. Pokud používáte MSGen, ujistěte se, že aktivní prostředí Pythonu používá verzi 2.7. xx v Pythonu. Při pokusu o použití MSGen s verzí 3. x jazyka Python se může zobrazit chyba.


### <a name="install-the-microsoft-genomics-client"></a>Instalace klienta Microsoft Genomics

K instalaci klienta Microsoft Genomics `msgen` použijte Python PIP. Následující pokyny předpokládají, že Python je již ve vaší systémové cestě. Pokud máte problémy s instalací PIP neznámou, musíte do systémové cesty přidat Python a složku Scripts.


```
pip install --upgrade --no-deps msgen
pip install msgen
```


Pokud nechcete instalovat `msgen` jako binární soubor v rámci systému a upravovat balíčky Pythonu v rámci systému, použijte příznak `–-user` s `pip`.
Použijete-li instalaci nebo setup.py založenou na balíčku, jsou nainstalovány všechny nezbytné požadované balíčky. V opačném případě jsou základní požadované balíčky pro msgen 

 * [Úložiště Azure](https://pypi.python.org/pypi/azure-storage). 
 * [Žádosti](https://pypi.python.org/pypi/requests). 


Tyto balíčky můžete nainstalovat pomocí `pip`, `easy_install` nebo pomocí standardních postupů `setup.py`. 





### <a name="test-the-microsoft-genomics-client"></a>Test klienta Microsoft Genomics
Pokud chcete otestovat klienta Microsoft Genomics, Stáhněte si konfigurační soubor z vašeho účtu genomiky. Přejděte na svůj účet genomika kliknutím na **všechny služby** v levém horním rohu, filtrováním a výběrem pro účty genomiky.


![Filtr pro Microsoft Genomics v Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "filtru pro Microsoft Genomics na Azure Portal")



Vyberte účet genomiky, který jste právě vytvořili, přejděte na **přístupové klíče** a Stáhněte si konfigurační soubor.

![Stažení konfiguračního souboru z Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "stažení konfiguračního souboru z Microsoft Genomics")


Otestujte, že Microsoft Genomics klient Python pracuje s následujícím příkazem


```
msgen list -f “<full path where you saved the config file>”
```

## <a name="create-a-microsoft-azure-storage-account"></a>Vytvoření účtu Microsoft Azure Storage 
Služba Microsoft Genomics očekává, že vstupy budou uloženy jako objekty blob bloku v účtu úložiště Azure. Zapisuje také výstupní soubory jako objekty blob bloku do kontejneru zadaného uživatelem v účtu úložiště Azure. Vstupy a výstupy se můžou nacházet v různých účtech úložiště.
Pokud již máte data v účtu úložiště Azure, musíte se ujistit, že je ve stejném umístění jako váš účet genomiky. V opačném případě se při spuštění služby genomiky účtují poplatky za výstup. Pokud ještě nemáte účet Microsoft Azure Storage, budete ho muset vytvořit a nahrát data. [Tady](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)najdete další informace o Azure Storagech účtech, včetně toho, co je účet úložiště a jaké služby nabízí. Účet Microsoft Azure Storage vytvoříte tak, že přejdete do [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM ).  

![](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade.png "Okno") vytvořit úložiště okno pro vytvoření úložiště

Nakonfigurujte účet úložiště pomocí následujících informací, jak je znázorněno na předchozím obrázku. Použijte pro účet úložiště většinu standardních možností a určete jenom to, že účet je úložiště objektů blob, ne pro obecné účely. Úložiště objektů BLOB může být 2 – pětinásobné rychlejší pro soubory ke stažení a nahrávání.  Doporučuje se výchozí model nasazení Azure Resource Manager.  


 |**Nastavením**          |  **Navrhovaná hodnota**  | **Popis pole** |
 |:-------------------------       |:-------------         |:----------            |
 |Předplatné         | Vaše předplatné Azure |Podrobnosti o předplatném najdete v tématu [předplatná](https://account.azure.com/Subscriptions) . |      
 |Skupina prostředků       | MyResourceGroup       |  Můžete vybrat stejnou skupinu prostředků jako váš účet genomiky. Platné názvy skupin prostředků najdete v tématu [pravidla pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) . |
 |Název účtu úložiště         | MyStorageAccount     |Vyberte jedinečný identifikátor účtu. Platné názvy najdete v tématu [pravidla pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) . |
 |Umístění                  | Západní USA 2                  | Použijte stejné umístění jako umístění vašeho účtu genomika, abyste snížili náklady na výstup a snížili latenci.  | 
 |Výkon                  | Standardní                   | Výchozí hodnota je standard. Další podrobnosti o účtech úložiště úrovně Standard a Premium najdete v tématu [Úvod do Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Druh účtu       | Úložiště objektů BLOB       |  Úložiště objektů BLOB může být 2 – pětinásobné rychleji než obecné účely ke stažení a nahrávání. |
 |Replikace                  | Místně redundantní úložiště                  | Místně redundantní úložiště replikuje vaše data v datacentru v oblasti, ve které jste vytvořili účet úložiště. Další informace najdete v tématu [replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) .    |
 |Úroveň přístupu                  | Provozu                   | Hot Access indikuje, že se k objektům v účtu úložiště budou přistupovat častěji.    |


Potom kliknutím na `Review + create` vytvořte účet úložiště. Stejně jako při vytváření účtu genomiky můžete kliknutím na oznámení na horním panelu nabídek monitorovat proces nasazení. 


## <a name="upload-input-data-to-your-storage-account"></a>Nahrání vstupních dat do účtu úložiště

Služba Microsoft Genomics očekává, že spárované konce přečtou jako vstupní soubory. Můžete si vybrat buď nahrávání vlastních dat, nebo prozkoumat pomocí veřejně dostupných ukázkových dat, která máte k dispozici. Pokud chcete použít veřejně dostupná ukázková data, je hostována zde:


[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)


V rámci vašeho účtu úložiště musíte pro vstupní data vytvořit jeden kontejner objektů BLOB a druhý kontejner objektů BLOB pro vaše výstupní data.  Nahrajte vstupní data do vstupního kontejneru objektů BLOB. K tomu můžete použít různé nástroje, včetně [Průzkumník služby Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter)nebo [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 



## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Spuštění pracovního postupu prostřednictvím služby Microsoft Genomics pomocí klienta Pythonu 

Pokud chcete spustit pracovní postup prostřednictvím služby Microsoft Genomics, upravte soubor config. txt a určete vstupní a výstupní kontejner úložiště pro vaše data.
Otevřete soubor config. txt, který jste stáhli z vašeho účtu genomiky. V sekcích, které potřebujete zadat, jsou klíč předplatného a šest položek dole, název účtu úložiště, název klíče a kontejneru pro vstup i výstup. Tyto informace můžete najít tak, že přejdete na portál a **získáte přístup k klíčům** pro svůj účet úložiště nebo přímo z Průzkumník služby Azure Storage.  


Konfigurace genomiky ![Konfigurace](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "genomiky")


Pokud byste chtěli spustit GATK4, nastavte parametr `process_name` na `gatk4`.

Ve výchozím nastavení služba genomiky produkuje soubory VCF. Pokud byste chtěli výstup gVCF místo výstupu VCF (ekvivalent `-emitRefConfidence` v GENOME 3. x a `emit-ref-confidence` v GENOME 4. x), přidejte do `config.txt` parametr `emit_ref_confidence` a nastavte jej na `gvcf`, jak je znázorněno na obrázku výše.  Pokud chcete změnit zpátky na výstup VCF, odeberte ho ze souboru `config.txt` nebo nastavte parametr `emit_ref_confidence` na `none`. 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Odešlete pracovní postup do služby Microsoft Genomics klienta Microsoft Genomics

Použijte klienta Microsoft Genomics Python k odeslání pracovního postupu pomocí následujícího příkazu:


```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```


Stav pracovních postupů můžete zobrazit pomocí následujícího příkazu: 
```python
msgen list -f c:\temp\config.txt 
```


Po dokončení pracovního postupu můžete zobrazit výstupní soubory ve vašem účtu Azure Storage v nakonfigurovaném kontejneru výstupu. 


## <a name="next-steps"></a>Další kroky
V tomto článku jste nahráli ukázková vstupní data do Azure Storage a odeslali pracovní postup do služby Microsoft Genomics prostřednictvím klienta Pythonu `msgen`. Další informace o dalších typech vstupních souborů, které lze použít s Microsoft Genomics službou, najdete na následujících stránkách: [SPÁROVANÉ souborů fastq](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [více souborů fastq nebo BAM](quickstart-input-multiple.md). Tento kurz můžete prozkoumat také pomocí našeho [kurzu pro Azure Poznámkový blok.](https://aka.ms/genomicsnotebook)
