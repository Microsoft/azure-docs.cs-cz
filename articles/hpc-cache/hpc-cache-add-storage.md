---
title: Přidání úložiště do mezipaměti prostředí Azure HPC
description: Jak definovat cíle úložiště, aby mezipaměť prostředí Azure HPC mohla používat místní systém souborů NFS nebo kontejnery objektů blob Azure pro dlouhodobé ukládání souborů
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: bba6745a4cc0be30648e23501f9a9e2f0cc6c8db
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563232"
---
# <a name="add-storage-targets"></a>Přidání cílů úložiště

*Cíle úložiště* jsou back-endové úložiště pro soubory, které jsou dostupné prostřednictvím mezipaměti HPC Azure. Můžete přidat úložiště NFS (jako je místní hardwarový systém) nebo ukládat data do objektu blob Azure.

Pro jednu mezipaměť můžete definovat až 20 různých cílů úložiště. Mezipaměť prezentuje všechny cíle úložiště v jednom agregovaném oboru názvů.

Cesty oboru názvů se konfigurují samostatně po přidání cílů úložiště. Obecně platí, že cíl úložiště systému souborů NFS může mít až deset cest oboru názvů nebo více pro některé velké konfigurace. Podrobnosti najdete v [názvech oborů názvů systému souborů NFS](add-namespace-paths.md#nfs-namespace-paths) .

Mějte na paměti, že exporty úložiště musí být dostupné z virtuální sítě vaší mezipaměti. U místních hardwarových úložišť možná budete muset nastavit server DNS, který dokáže přeložit názvy hostitelů pro přístup k úložišti NFS. Další informace najdete v tématu věnovaném [přístupu DNS](hpc-cache-prerequisites.md#dns-access).

Přidejte cíle úložiště po vytvoření mezipaměti. Použijte tento postup:

1. [Vytvoření mezipaměti](hpc-cache-create.md)
1. Definování cíle úložiště (informace v tomto článku)
1. [Vytvoření cest směřujících klientovi](add-namespace-paths.md) (pro [agregovaný obor názvů](hpc-cache-namespace.md))

Postup přidání cíle úložiště se mírně liší v závislosti na typu úložiště, které používá. Podrobnosti jsou uvedené níže.

Kliknutím na obrázek níže si můžete přehrát [ukázku](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) pro vytvoření mezipaměti a Přidání cíle úložiště z Azure Portal.

[![Miniatura videa: mezipaměť prostředí Azure HPC: Instalační program (kliknutím můžete navštívit stránku video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Přidat nový cíl úložiště objektů BLOB v Azure

Nový cíl úložiště objektů BLOB potřebuje prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu cloudového systému souborů mezipaměti HPC Azure HPC. Přečtěte si další informace o předběžném načítání kontejneru objektů BLOB v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

Stránka Azure Portal **Přidat cíl úložiště** obsahuje možnost vytvořit nový kontejner objektů BLOB těsně před tím, než ho přidáte.

> [!NOTE]
> Pro úložiště objektů BLOB připojené k systému souborů NFS použijte typ [cíle úložiště adls-NFS](#) .

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal otevřete instanci mezipaměti a na levém bočním panelu klikněte na možnost **cíle úložiště** .

![snímek obrazovky s nastavením > cílovou stránkou úložiště se dvěma existujícími cíli úložiště v tabulce a zvýrazněním tlačítka + Přidat cíl úložiště nad tabulkou](media/add-storage-target-button.png)

Stránka **cíle úložiště** obsahuje seznam všech stávajících cílů a obsahuje odkaz na přidání nového.

Klikněte na tlačítko **Přidat cíl úložiště** .

![snímek obrazovky s cílovou stránkou pro přidání cílení úložiště s informacemi o novém cíli úložiště Azure Blob Storage](media/hpc-cache-add-blob.png)

Pro definování kontejneru objektů BLOB v Azure zadejte tyto informace.

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.
* **Cílový typ** – vyberte **objekt BLOB**.
* **Účet úložiště** – vyberte účet, který chcete použít.

  Pro přístup k účtu úložiště bude nutné autorizovat instanci mezipaměti, jak je popsáno v tématu [Přidání rolí přístupu](#add-the-access-control-roles-to-your-account).

  Informace o druhu účtu úložiště, který můžete použít, najdete v článku [požadavky na úložiště objektů BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Kontejner úložiště** – vyberte kontejner objektů BLOB pro tento cíl nebo klikněte na **vytvořit nový**.

  ![snímek obrazovky dialogového okna pro zadání názvu a úrovně přístupu (privátní) pro nový kontejner](media/add-blob-new-container.png)

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

> [!NOTE]
> Pokud je brána firewall účtu úložiště nastavená tak, aby omezila přístup jenom na vybrané sítě, použijte dočasné řešení popsané v tématu [práce s nastavením brány firewall účtu úložiště BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Přidání rolí řízení přístupu ke svému účtu

Azure HPC Cache používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/index.yml) k autorizaci služby cache pro přístup k vašemu účtu úložiště pro cíle Azure Blob Storage.

Vlastník účtu úložiště musí explicitně přidat role přispěvatele [účtu úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor) a přispěvatel [dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) pro uživatele "poskytovatel prostředků mezipaměti HPC".

Můžete to provést předem nebo kliknutím na odkaz na stránce, kam přidáte cíl úložiště objektů BLOB. Mějte na paměti, že může trvat až pět minut, než se nastavení role rozšíří přes prostředí Azure, takže byste před vytvořením cíle úložiště měli počkat několik minut od přidání rolí.

Postup přidání rolí Azure:

1. Otevřete stránku **řízení přístupu (IAM)** pro účet úložiště. (Odkaz na stránce **Přidat cíl úložiště** automaticky otevře tuto stránku pro vybraný účet.)

1. Klikněte na v **+** horní části stránky a vyberte **Přidat přiřazení role**.

1. V seznamu vyberte roli Přispěvatel účtu úložiště.

1. V poli **přiřadit přístup k** ponechte vybranou výchozí hodnotu ("uživatel služby Azure AD, skupina nebo instanční objekt").  

1. V poli **Vybrat** vyhledejte "HPC".  Tento řetězec by měl odpovídat jednomu instančnímu objektu s názvem "poskytovatel prostředků mezipaměti HPC". Kliknutím vyberte objekt zabezpečení.

   > [!NOTE]
   > Pokud hledání "HPC" nefunguje, zkuste místo toho použít řetězec "storagecache". Uživatelé, kteří se účastnili verzí Preview (před GA), můžou potřebovat použít starší název instančního objektu.

1. V dolní části klikněte na tlačítko **Uložit** .

1. Tento postup opakujte, pokud chcete přiřadit roli "Přispěvatel dat objektů BLOB úložiště".  

![snímek obrazovky s grafickým uživatelským rozhraním pro přiřazení role](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Předpoklad: přístup k účtu úložiště

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Před přidáním cíle služby Blob Storage ověřte, zda má mezipaměť správné role pro přístup k účtu úložiště, a zda nastavení brány firewall umožní vytvoření cíle úložiště.

Azure HPC Cache používá [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/index.yml) k autorizaci služby cache pro přístup k vašemu účtu úložiště pro cíle Azure Blob Storage.

Vlastník účtu úložiště musí explicitně přidat role přispěvatele [účtu úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor) a přispěvatel [dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) pro uživatele "poskytovatel prostředků mezipaměti HPC".

Vytvoření cíle úložiště se nezdaří, pokud mezipaměť tyto role nemá.

Může trvat až pět minut, než se nastavení role rozšíří přes prostředí Azure, takže byste před vytvořením cíle úložiště měli počkat několik minut od přidání rolí.

Podrobné pokyny najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md) .

Taky ověřte nastavení brány firewall účtu úložiště. Pokud je brána firewall nastavená tak, aby omezila přístup jenom na vybrané sítě, může vytvoření cíle úložiště selhat. Použijte alternativní řešení popsané v části [práce s nastavením brány firewall účtu úložiště BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Přidání cíle úložiště objektů BLOB pomocí Azure CLI

K definování cíle Azure Blob Storage použijte rozhraní [AZ HPC-cache BLOB-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) .

> [!NOTE]
> Příkazy rozhraní příkazového řádku Azure v současné době vyžadují, abyste při přidávání cíle úložiště vytvořili cestu k oboru názvů. To se liší od procesu používaného s rozhraním Azure Portal.

Kromě standardních parametrů skupiny prostředků a názvu mezipaměti musíte poskytnout tyto možnosti pro cíl úložiště:

* ``--name`` -Nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.

* ``--storage-account``– Identifikátor účtu, v tomto formátu:/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/Providers/Microsoft.Storage/storageAccounts/*<account_name*>

  Informace o druhu účtu úložiště, který můžete použít, najdete v článku [požadavky na úložiště objektů BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` -Zadejte název kontejneru, který chcete použít pro tento cíl úložiště.

* ``--virtual-namespace-path`` – Nastavte cestu k souboru pro klienta pro tento cíl úložiště. Uzavřete cesty do uvozovek. Další informace o funkci virtuálního oboru názvů najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .

Příklad příkazu:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Přidání nového cíle úložiště systému souborů NFS

Cíl úložiště NFS má různá nastavení z cíle úložiště objektů BLOB. Nastavení modelu využití pomáhá mezipaměti efektivně ukládat data z tohoto systému úložiště.

![Snímek obrazovky s definovaným cílovou stránkou pro přidání úložiště s cílovým systémem souborů NFS](media/add-nfs-target.png)

> [!NOTE]
> Před vytvořením cíle úložiště NFS se ujistěte, že je váš systém úložiště přístupný z mezipaměti HPC Azure a splňuje požadavky na oprávnění. Vytvoření cíle úložiště se nezdaří, pokud mezipaměť nemá přístup k systému úložiště. Podrobnosti najdete v tématu [požadavky na úložiště NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) a [řešení potíží s cíli konfigurace serveru NAS a úložiště NFS](troubleshoot-nas.md) .

### <a name="choose-a-usage-model"></a>Výběr modelu použití
<!-- referenced from GUI - update aka.ms link to point at new article when published -->

Při vytváření cíle úložiště, který používá systém souborů NFS k dosažení systému úložiště, je nutné zvolit model využití pro tento cíl. Tento model určuje, jak jsou data ukládána do mezipaměti.

Další podrobnosti o všech těchto nastaveních najdete v tématu [pochopení modelů používání](cache-usage-models.md) .

Integrované modely použití vám umožňují zvolit způsob vyrovnávání rychlé odezvy s rizikem při získávání zastaralých dat. Chcete-li optimalizovat rychlost čtení souborů, nesmíte se starat o to, zda jsou soubory v mezipaměti zkontrolovány proti back-endové soubory. Na druhé straně, pokud chcete mít jistotu, že jsou soubory vždycky aktuální se vzdáleným úložištěm, vyberte model, který se často kontroluje.

Tyto tři možnosti se týkají většiny situací:

* **Čtení těžkých, málo častých zápisů** – zrychluje přístup pro čtení souborů, které jsou statické nebo zřídka změněné.

  Tato možnost ukládá soubory z klientského čtení do mezipaměti, ale dokončí zápisy klientů do úložiště back-endu okamžitě. Soubory uložené v mezipaměti nejsou automaticky porovnány se soubory na svazku úložiště NFS.

  Tuto možnost nepoužívejte, pokud existuje riziko, že soubor může být upraven přímo v systému úložiště, aniž byste ho nejdřív napsali do mezipaměti. Pokud k tomu dojde, verze souboru v mezipaměti nebude synchronizována s back-end souborem.

* Více **než 15% zápisů** – Tato možnost zrychluje výkon čtení i zápisu.

  Čtení klienta a zápisy klientů jsou ukládány do mezipaměti. Předpokládá se, že soubory v mezipaměti budou novější než soubory v back-endovém systému úložiště. Soubory v mezipaměti jsou automaticky kontrolovány proti souborům v úložišti back-endu každých 8 hodin. Změněné soubory v mezipaměti se zapisují do back-endového systému úložiště po dobu 20 minut v mezipaměti bez dalších změn.

  Tuto možnost nepoužívejte, pokud některý z klientů přímo připojí svazek úložiště back-endu, protože existuje riziko, že budou mít zastaralé soubory.

* **Klienti zapisují do cíle NFS, vynechá mezipaměť** – tuto možnost vyberte, pokud klienti v pracovním postupu zapisují data přímo do systému úložiště, aniž by museli nejdřív zapisovat do mezipaměti, nebo pokud chcete optimalizovat konzistenci dat.

  Soubory, které klienti požadují, jsou ukládány do mezipaměti, ale všechny změny těchto souborů z klienta jsou okamžitě předány do back-endového systému úložiště. Soubory v mezipaměti jsou často kontrolovány proti verzím back-endu pro aktualizace. Toto ověření udržuje konzistenci dat při změně souborů přímo v systému úložiště namísto mezipaměti.

Podrobnosti o dalších možnostech najdete v článku [porozumění modelům používání](cache-usage-models.md).

Tato tabulka shrnuje rozdíly mezi všemi modely využití:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

<!-- | Usage model | Caching mode | Back-end verification | Maximum write-back delay |
|--|--|--|--|
| Read heavy, infrequent writes | Read | Never | None |
| Greater than 15% writes | Read/write | 8 hours | 20 minutes |
| Clients bypass the cache | Read | 30 seconds | None |
| Greater than 15% writes, frequent back-end checking (30 seconds) | Read/write | 30 seconds | 20 minutes |
| Greater than 15% writes, frequent back-end checking (60 seconds) | Read/write | 60 seconds | 20 minutes |
| Greater than 15% writes, frequent write-back | Read/write | 30 seconds | 30 seconds |
| Read heavy, checking the backing server every 3 hours | Read | 3 hours | None | -->

> [!NOTE]
> Hodnota **ověření back-endu** ukazuje, kdy mezipaměť automaticky porovnává své soubory se zdrojovými soubory ve vzdáleném úložišti. Porovnání můžete ale aktivovat odesláním žádosti klienta, která zahrnuje operaci READDIRPLUS v systému back-end úložiště. READDIRPLUS je standardní rozhraní API pro systém souborů NFS (označované také jako rozšířené čtení), které vrací metadata adresáře, což způsobí, že mezipaměť porovná a aktualizuje soubory.

### <a name="create-an-nfs-storage-target"></a>Vytvoření cíle úložiště NFS

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal otevřete instanci mezipaměti a na levém bočním panelu klikněte na možnost **cíle úložiště** .

![snímek obrazovky s nastavením > cílovou stránkou úložiště se dvěma existujícími cíli úložiště v tabulce a zvýrazněním tlačítka + Přidat cíl úložiště nad tabulkou](media/add-storage-target-button.png)

Stránka **cíle úložiště** obsahuje seznam všech stávajících cílů a obsahuje odkaz na přidání nového.

Klikněte na tlačítko **Přidat cíl úložiště** .

![Snímek obrazovky s definovaným cílovou stránkou pro přidání úložiště s cílovým systémem souborů NFS](media/add-nfs-target.png)

Zadejte tyto informace pro cíl úložiště zálohovaného systémem souborů NFS:

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.

* **Cílový typ** – vyberte **systém souborů NFS**.

* **Název hostitele** – zadejte IP adresu nebo plně kvalifikovaný název domény pro systém úložiště NFS. (Použijte název domény jenom v případě, že vaše mezipaměť má přístup k serveru DNS, který dokáže tento název přeložit.)

* **Model využití** – vyberte jeden z profilů ukládání dat do mezipaměti na základě pracovního postupu, který je popsaný v části [Výběr modelu použití](#choose-a-usage-model) výše.

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Použijte příkaz Azure CLI [AZ HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) k vytvoření cíle úložiště.

> [!NOTE]
> Příkazy rozhraní příkazového řádku Azure v současné době vyžadují, abyste při přidávání cíle úložiště vytvořili cestu k oboru názvů. To se liší od procesu používaného s rozhraním Azure Portal.

Zadejte kromě názvu mezipaměti a skupiny prostředků mezipaměti tyto hodnoty:

* ``--name`` -Nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.
* ``--nfs3-target`` – IP adresa systému úložiště NFS. (Plně kvalifikovaný název domény můžete použít tady, pokud má vaše mezipaměť přístup k serveru DNS, který dokáže tento název přeložit.)
* ``--nfs3-usage-model`` – Jeden z profilů ukládání dat do mezipaměti popsaný v části [Výběr modelu použití](#choose-a-usage-model)výše.

  Ověřte názvy modelů použití pomocí příkazu [AZ HPC-cache Usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction`` – Parametr spojení propojuje cestu k virtuálnímu souboru pro klienta s cestou exportu v systému úložiště.

  Cíl úložiště NFS může mít několik virtuálních cest, pokud každá cesta představuje jiný export nebo podadresář ve stejném systému úložiště. Vytvořte všechny cesty pro jeden systém úložiště na jednom cíli úložiště.

  [Cesty oboru názvů můžete kdykoli přidat a upravit](add-namespace-paths.md) v cíli úložiště.

  ``--junction``Parametr používá tyto hodnoty:

  * ``namespace-path`` – Cesta k virtuálnímu souboru s přístupem klienta
  * ``nfs-export`` – Export systému úložiště, který se má přidružit k cestě s přístupem klienta
  * ``target-path`` (volitelné) – podadresář exportu (v případě potřeby)

  Příklad: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Další informace o funkci virtuálního oboru názvů najdete v tématu [Konfigurace agregovaného oboru názvů](hpc-cache-namespace.md) .

Příklad příkazu:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Výstup:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Přidání nového cíle úložiště ADLS-NFS (PREVIEW)

Cíle úložiště ADLS-NFS používají kontejnery objektů BLOB v Azure, které podporují protokol NFS (Network File System) 3,0.

> [!NOTE]
> Podpora protokolu NFS 3,0 pro Azure Blob Storage je ve verzi Public Preview. Dostupnost je omezená a funkce se můžou v současnosti měnit a když je funkce všeobecně dostupná. V produkčních systémech Nepoužívejte technologii verze Preview.
>
> Nejnovější informace najdete v článku [Podpora protokolu NFS 3,0](../storage/blobs/network-file-system-protocol-support.md) .

Cíle úložiště ADLS-NFS mají určité podobnosti s cíli úložiště objektů BLOB a s cíli úložiště NFS. Například:

* Podobně jako u služby Blob Storage je potřeba poskytnout oprávnění mezipaměti HPC Azure pro [přístup k vašemu účtu úložiště](#add-the-access-control-roles-to-your-account).
* Podobně jako cíl úložiště NFS musíte nastavit [model využití](#choose-a-usage-model)mezipaměti.
* Vzhledem k tomu, že kontejnery objektů BLOB s povoleným systémem souborů NFS mají hierarchickou strukturu kompatibilní se systémem souborů NFS, nemusíte tuto mezipaměť používat k ingestování dat a kontejnery jsou čitelné jinými systémy souborů NFS. Data můžete předem načíst do kontejneru ADLS-NFS a pak je přidat do mezipaměti HPC jako cíl úložiště a pak získat přístup k datům později z vnějšku mezipaměti HPC. Když použijete standardní kontejner objektů BLOB jako cíl úložiště mezipaměti HPC, data se napíší ve speciálním formátu a dají se k nim dostat jenom z jiných produktů kompatibilních s Azure HPC cache.

Než budete moct vytvořit cíl úložiště ADLS-NFS, musíte vytvořit účet úložiště s povoleným systémem souborů NFS. Postupujte podle tipů v tématu [požadavky pro mezipaměť Azure HPC](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) a pokyny v části [připojení úložiště objektů BLOB pomocí systému souborů NFS](../storage/blobs/network-file-system-protocol-support-how-to.md). Po nastavení účtu úložiště můžete při vytváření cíle úložiště vytvořit nový kontejner.

Pokud chcete vytvořit cíl úložiště ADLS-NFS, otevřete stránku **Přidat cíl úložiště** v Azure Portal. (Další metody jsou ve vývoji.)

![Snímek obrazovky se stránkou cíle pro přidání úložiště s definovaným cílem ADLS-NFS](media/add-adls-target.png)

Zadejte tyto informace.

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.
* **Cílový typ** – vyberte **adls-NFS**.
* **Účet úložiště** – vyberte účet, který chcete použít. Pokud se v seznamu nezobrazí účet úložiště s povoleným systémem souborů NFS, ověřte, zda vyhovuje požadavkům a zda k němu má mezipaměť přístup.

  Pro přístup k účtu úložiště bude nutné autorizovat instanci mezipaměti, jak je popsáno v tématu [Přidání rolí přístupu](#add-the-access-control-roles-to-your-account).

* **Kontejner úložiště** – vyberte kontejner objektů BLOB s povoleným systémem souborů NFS pro tento cíl nebo klikněte na **vytvořit novou**.

* **Model využití** – vyberte jeden z profilů ukládání dat do mezipaměti na základě pracovního postupu, který je popsaný v části [Výběr modelu použití](#choose-a-usage-model) výše.

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

<!-- **** -->

## <a name="view-storage-targets"></a>Zobrazit cíle úložiště

Pomocí Azure Portal nebo rozhraní příkazového řádku Azure můžete zobrazit cíle úložiště, které jsou už definované pro vaši mezipaměť.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal otevřete instanci mezipaměti a klikněte na možnost **cíle úložiště**, která je pod záhlavím nastavení na levém bočním panelu. Stránka cíle úložiště obsahuje seznam všech stávajících cílů a ovládacích prvků pro jejich přidání nebo odstranění.

Kliknutím na název cíle úložiště otevřete jeho stránku s podrobnostmi.

Další informace najdete v tématu [Úprava cílů úložiště](hpc-cache-edit-storage.md) .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Nastavte Azure CLI pro mezipaměť HPC Azure](./az-cli-prerequisites.md).

Pro zobrazení stávajících cílů úložiště pro mezipaměť použijte možnost [AZ HPC-cache Storage-Target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) . Zadejte název mezipaměti a skupinu prostředků (pokud ji nenastavíte globálně).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Pokud chcete zobrazit podrobnosti o konkrétním cíli úložiště, použijte [příkaz AZ HPC-cache Storage-Target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) . (Zadejte cíl úložiště podle názvu.)

Příklad:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Další kroky

Po vytvoření cílů úložiště pokračujte v těchto úlohách, abyste mohli vaši mezipaměť připravit na použití:

* [Nastavení agregovaného oboru názvů](add-namespace-paths.md)
* [Připojení služby Azure HPC Cache](hpc-cache-mount.md)
* [Přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md)

Pokud potřebujete aktualizovat nastavení, můžete [cíl úložiště upravit](hpc-cache-edit-storage.md).