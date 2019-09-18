---
title: Přidání úložiště do mezipaměti prostředí Azure HPC
description: Jak definovat cíle úložiště, aby mezipaměť prostředí Azure HPC mohla používat místní systém souborů NFS nebo kontejnery objektů blob Azure pro dlouhodobé ukládání souborů
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: ca8e13e322c3e192b697248f1252b65f6cbeda7f
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037220"
---
# <a name="add-storage"></a>Přidání úložiště

*Cíle úložiště* jsou back-endové úložiště pro soubory, které jsou dostupné prostřednictvím instance mezipaměti HPC Azure. Můžete přidat úložiště systému souborů NFS, jako je místní hardwarový systém, nebo uložit data do objektu blob Azure.

Pro jednu mezipaměť můžete definovat až deset různých cílů úložiště. Mezipaměť prezentuje všechny cíle úložiště v jednom agregovaném oboru názvů.

Mějte na paměti, že exporty úložiště musí být dostupné z virtuální sítě vaší mezipaměti. U místních hardwarových úložišť možná budete muset nastavit server DNS, který dokáže přeložit názvy hostitelů pro přístup k úložišti NFS. Další informace najdete v tématu věnovaném [přístupu DNS](hpc-cache-prereqs.md#dns-access).

Můžete přidat cíle úložiště při vytváření mezipaměti HPC Azure nebo později. Postup se mírně liší v závislosti na tom, zda přidáváte službu Azure Blob Storage nebo export systému souborů NFS. Podrobnosti jsou uvedené níže.

## <a name="add-storage-targets-while-creating-the-cache"></a>Přidání cílů úložiště při vytváření mezipaměti

Pomocí karty **cíle úložiště** v Průvodci vytvořením mezipaměti definujte úložiště ve stejnou dobu, kdy vytvoříte instanci mezipaměti.

![snímek obrazovky se stránkou cílů úložiště](media/hpc-cache-storage-targets-pop.png)

Kliknutím na odkaz **Přidat cíl úložiště** přidejte úložiště.

## <a name="add-storage-targets-from-the-cache"></a>Přidání cílů úložiště z mezipaměti

Z Azure Portal otevřete instanci mezipaměti a na levém bočním panelu klikněte na možnost **cíle úložiště** . Stránka cíl úložiště obsahuje seznam všech stávajících cílů a obsahuje odkaz na přidání nového.

![snímek obrazovky s odkazem na cíle úložiště na bočním panelu pod nadpisem konfigurovat, což je mezi záhlavími a nastaveními a monitorováním kategorie](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Přidat nový cíl úložiště objektů BLOB v Azure

Nový cíl úložiště objektů BLOB potřebuje prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu cloudového systému souborů Azure HPC cache. Přečtěte si další informace o předběžném načítání kontejneru objektů BLOB v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

Pro definování kontejneru objektů BLOB v Azure zadejte tyto informace.

![snímek obrazovky s cílovou stránkou pro přidání cílení úložiště s informacemi o novém cíli úložiště Azure Blob Storage](media/hpc-cache-add-blob.png)

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.
* **Cílový typ** – vyberte **objekt BLOB**.
* **Účet úložiště** – vyberte účet s kontejnerem, na který se má odkazovat.

  Pro přístup k účtu úložiště bude nutné autorizovat instanci mezipaměti, jak je popsáno v tématu [Přidání rolí přístupu](#add-the-access-control-roles-to-your-account).
* **Kontejner úložiště** – vyberte kontejner objektů BLOB pro tento cíl.

* **Cesta virtuálního oboru názvů** – nastavte cestu pro klientský přístup pro tento cíl úložiště. Další informace o funkci virtuálního oboru názvů najdete v tématu [Konfigurace agregovaného oboru názvů](hpc-cache-namespace.md) .

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

### <a name="add-the-access-control-roles-to-your-account"></a>Přidání rolí řízení přístupu ke svému účtu

Mezipaměť prostředí Azure HPC používá [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) k autorizaci aplikace mezipaměti pro přístup k vašemu účtu úložiště pro cíle služby Azure Blob Storage.

Vlastník účtu úložiště musí explicitně přidat role přispěvatele [účtu úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) a přispěvatel [dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) pro uživatele "StorageCache Resource Provider".

Můžete to provést předem nebo kliknutím na odkaz na stránce, kam přidáte cíl úložiště objektů BLOB.

Postup přidání rolí RBAC:

1. Otevřete stránku **řízení přístupu (IAM)** pro účet úložiště. (Odkaz na stránce **Přidat cíl úložiště** automaticky otevře tuto stránku pro vybraný účet.)

1. Klikněte na v horní části stránky a vyberte **Přidat přiřazení role.** **+**

1. V seznamu vyberte roli Přispěvatel účtu úložiště.

1. V poli **přiřadit přístup k** ponechte vybranou výchozí hodnotu ("uživatel služby Azure AD, skupina nebo instanční objekt").  

1. V poli **Vybrat** vyhledejte "storagecache".  Tento řetězec by měl odpovídat jednomu objektu zabezpečení s názvem "poskytovatel prostředků mezipaměti HPC". Kliknutím vyberte objekt zabezpečení.

1. Kliknutím na tlačítko **Uložit** přidejte přiřazení role do účtu úložiště.

1. Tento postup opakujte, pokud chcete přiřadit roli "Přispěvatel dat objektů BLOB úložiště".  

![snímek obrazovky s grafickým uživatelským rozhraním pro přiřazení role](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Přidání nového cíle úložiště systému souborů NFS

Cíl úložiště NFS obsahuje některá další pole, která určují, jak se dostat k exportu úložiště a jak efektivně ukládat data do mezipaměti. Můžete také vytvořit více cest oboru názvů z jednoho hostitele systému souborů NFS, pokud má k dispozici více než jeden export.

![Snímek obrazovky s definovaným cílovou stránkou pro přidání úložiště s cílovým systémem souborů NFS](media/hpc-cache-add-nfs-target.png)

Zadejte tyto informace pro cíl úložiště zálohovaného systémem souborů NFS:

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.

* **Cílový typ** – vyberte **systém souborů NFS**.

* **Název hostitele** – zadejte IP adresu nebo plně kvalifikovaný název domény pro systém úložiště NFS. (Použijte název domény jenom v případě, že vaše mezipaměť má přístup k serveru DNS, který dokáže tento název přeložit.)

* **Model využití** – vyberte jeden z profilů ukládání dat do mezipaměti na základě pracovního postupu, který je popsaný v [části Výběr modelu použití níže](#choose-a-usage-model).

Můžete vytvořit několik cest oboru názvů, které reprezentují různé exporty ve stejném systému úložiště NFS, ale musíte je vytvořit všechny z jednoho cíle úložiště.

Pro každý export vyplňte tyto hodnoty:

* **Cesta virtuálního oboru názvů** – nastavte cestu pro klientský přístup pro tento cíl úložiště. Další informace o funkci virtuálního oboru názvů najdete v tématu [Konfigurace agregovaného oboru názvů](hpc-cache-namespace.md) .

<!--  The virtual path should start with a slash ``/``. -->

* **Cesta exportu NFS** – zadejte cestu k exportu NFS.

* **Cesta k podadresáři** : Pokud chcete připojit určitý podadresář exportu, zadejte ho sem. Pokud ne, ponechte toto pole prázdné. 

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

### <a name="choose-a-usage-model"></a>Výběr modelu použití 
<!-- link in GUI to this heading -->

Při vytváření cíle úložiště, který odkazuje na systém úložiště NFS, je nutné zvolit *model využití* pro tento cíl. Tento model určuje, jak jsou data ukládána do mezipaměti.

* Číst silná – Pokud většinou používáte mezipaměť ke zrychlení přístupu pro čtení dat, vyberte tuto možnost. 

* Čtení a zápis – Pokud klienti používají mezipaměť ke čtení a zápisu, vyberte tuto možnost.

* Klienti obcházejí mezipaměť – tuto možnost vyberte, pokud budou vaši klienti zapisovat data přímo do systému úložiště bez předchozího zápisu do mezipaměti.

## <a name="next-steps"></a>Další postup

Po vytvoření cílů úložiště Vezměte v úvahu jednu z těchto úloh:

* [Připojení mezipaměti HPC Azure](hpc-cache-mount.md)
* [Přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md)