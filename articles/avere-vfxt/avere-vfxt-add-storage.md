---
title: Konfigurace úložiště vFXT Avere – Azure
description: Postup přidání systém back endové úložiště do vašeho Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: cd868996066110c8d0457b177e60523886912dd8
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163167"
---
# <a name="configure-storage"></a>Konfigurace úložiště

Tento krok vytvoří back-end systém úložiště pro váš cluster vFXT.

> [!TIP]
> Pokud jste použili `create-cloudbacked-cluster` prototypu skript k vytvoření nového kontejneru objektů Blob společně s clusterem vFXT Avere, že kontejner již nastaven pro použití a není nutné přidávat úložiště.

Postupujte podle těchto pokynů, pokud jste použili `create-minimal-cluster` skript prototypu pro váš cluster, nebo pokud chcete přidat další hardware nebo systém úložiště založené na cloudu.

Existují dvě hlavní úlohy:

1. [Vytvoření základní filer](#create-a-core-filer), propojuje vFXT cluster existující úložný systém nebo účet úložiště Azure.

1. [Vytvoření oboru názvů spojení](#create-a-junction), která definuje cestu, která se připojení klientů.

Tyto kroky používají Avere ovládacích panelech. Čtení [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) se naučíte používat.

## <a name="create-a-core-filer"></a>Vytvořit filtr core

"Základní filtr" je termín označující vFXT úložný systém back-endu. Úložiště může jednat o hardwaru zařízení NAS jako je NetApp nebo Isilon nebo může být objekt cloudového úložiště. Další informace o filtrech core najdete [v Avere clusteru nastavení průvodce](http://library.averesystems.com/ops_guide/4_7/settings_overview.html#managing-core-filers).

Přidat filtr core, vyberte jednu z dva hlavní typy křížového core:

  * [NAS základní filer](#nas-core-filer) – popisuje, jak přidat filtr core NAS 
  * [Azure Storage účet cloudu core filer](#azure-storage-account-cloud-core-filer) – popisuje, jak přidat účet služby Azure Storage jako filer core cloudu

### <a name="nas-core-filer"></a>Základní filtr NAS

Vyfiltrovat core NAS může být NetApp místní nebo Isilon nebo koncový bod serveru NAS v cloudu.  
Systém úložiště musí mít spolehlivé vysokorychlostní připojení ke clusteru vFXT Avere – například 1 GB/s připojení ExpressRoute (ne VPN) – a je nutné udělit přístup kořenového clusteru k exporty NAS se používají.

Takto přidejte filtr core NAS:

1. V Ovládacích panelech Avere, klikněte **nastavení** kartě v horní části.

1. Klikněte na tlačítko **Core Filer** > **spravovat křížového Core** na levé straně.

1. Klikněte na možnost **Vytvořit**.

   ![Přidat novou základní vyfiltrovat stránku se ukazatel myši na tlačítko vytvořit snímek](media/avere-vfxt-add-core-filer-start.png)

1. Vyplňte požadované informace v průvodci: 

   * Zadejte název vaší základní filtr.
   * Pokud je k dispozici, zadejte plně kvalifikovaný název domény (FQDN). V opačném případě zadejte IP adresu nebo název hostitele, který se přeloží na vaší základní filtr.
   * Zvolte třídu filer ze seznamu. Pokud nejste jisti, zvolte **jiných**.

     ![Snímek obrazovky, přidat nové základní filtr stránky s názvem filer core a jeho plně kvalifikovaný název domény](media/avere-vfxt-add-core-filer.png)
  
   * Klikněte na tlačítko **Další** a vyberte zásady mezipaměti. 
   * Klikněte na tlačítko **přidat filtr**.
   * Podrobnější informace o [přidání nového serveru NAS základní filer](http://library.averesystems.com/ops_guide/4_7/new_core_filer_nas.html) v Avere clusteru nastavení průvodce.

Potom pokračujte [vytvořit spojení](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Azure filer základní cloudové úložiště

Používání úložiště objektů Blob v Azure jako úložiště clusteru vFXT back-endu, potřebujete prázdného zásobníku přidáte jako filtr core.

> [!TIP] 
> ``create-cloudbacked-cluster`` Ukázkový skript vytvoří kontejner úložiště, ji definuje jako základní vyfiltrovat a vytvoří obor názvů spojení jako součást vytváření vFXT clusteru. ``create-minimal-cluster`` Vzorový skript není vytvoření kontejneru služby Azure storage. Chcete-li vyhnout se tak nutnosti vytvořit a nakonfigurovat filer základní Azure Storage po vytvoření clusteru, použijte ``create-cloudbacked-cluster`` skriptu pro nasazení clusteru vFXT.

Přidání úložiště objektů Blob ke svému clusteru vyžaduje tyto úlohy:

* Vytvoření účtu úložiště (krok 1 níže)
* Vytvoření prázdného kontejneru objektů Blob (kroků 2 až 3)
* Přidat přístupový klíč úložiště jako přihlašovacích údajů cloudu pro cluster vFXT (kroky 4 až 6)
* Přidat kontejner objektů Blob jako základní filtr pro cluster vFXT (kroky 7. až 9)
* Vytvořit spojení obor názvů, který klienti používají k připojení core filer ([vytvořit spojení](#create-a-junction), stejný jako hardware a cloudového úložiště)

Chcete-li přidat úložiště objektů Blob po vytvoření clusteru, postupujte takto. 

1. Vytvořte účet úložiště pro obecné účely verze 2 s těmito nastaveními:

   * **Předplatné** – stejný jako vFXT cluster
   * **Skupina prostředků** – stejná jako skupina clusteru vFXT (volitelné)
   * **Umístění** – stejný jako vFXT cluster
   * **Výkon** – standardní (Premium storage se nepodporuje)
   * **Druh účtu** – obecné účely V2 (StorageV2)
   * **Replikace** – místně redundantní úložiště (LRS)
   * **Úroveň přístupu** – aktivní
   * **Vyžádání bezpečného přenosu** -vypnout tuto volbu (jiné než výchozí hodnota)
   * **Virtuální sítě** – nepožaduje se

   Můžete použít na webu Azure portal nebo klikněte na tlačítko "Nasazení do Azure" níže.

   [![pro vytvoření účtu úložiště](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Po vytvoření účtu, přejděte na stránku účtu úložiště.

   ![Nový účet úložiště na webu Azure portal](media/avere-vfxt-new-storage-acct.png)

1. Vytvořte kontejner objektů blob kliknutím **objekty BLOB** na stránce Přehled a pak klikněte na tlačítko **+ kontejner**. Použít libovolný název kontejneru a ujistěte se, že přístup je nastavena na **privátní**.

   ![Úložiště objektů BLOB stránky se žádná existující kontejnery](media/avere-vfxt-blob-no-container.png)

1. Získání klíče účtu úložiště Azure kliknutím **přístupové klíče** pod **nastavení**:

   ![Portál Azure portal grafického uživatelského rozhraní pro kopírování klíč](media/avere-vfxt-copy-storage-key.png) 

1. Otevřete ovládací Panel Avere pro váš cluster. Klikněte na tlačítko **nastavení**a potom otevřete **clusteru** > **přihlašovací údaje ke cloudu** na levém navigačním podokně. Na stránce přihlašovací údaje do cloudu, klikněte na tlačítko **přidat přihlašovací údaje**.

   ![Klikněte na tlačítko Přidat přihlašovací údaje na stránce konfigurace přihlašovací údaje do cloudu](media/avere-vfxt-new-credential-button.png)

1. Zadejte následující informace o vytvoření přihlašovacích údajů pro základní filer cloudu: 

   | Pole | Hodnota |
   | --- | --- |
   | Název přihlašovacího údaje | jakýkoli popisný název |
   | Typ služby | (vyberte přístupový klíč k úložišti Azure) |
   | Tenant | Název účtu úložiště |
   | Předplatné | ID předplatného |
   | Přístupový klíč k úložišti | Klíč účtu úložiště Azure (zkopírovali v předchozím kroku) | 

   Klikněte na tlačítko **odeslat**.

   ![Vyplnění formuláře pro přihlašovacích údajů cloudu v Ovládacích panelech Avere](media/avere-vfxt-new-credential-submit.png)

1. Potom vytvořte filtr core. V levé části Avere ovládacích panelech klikněte na tlačítko **Core Filer** >  **spravovat křížového Core**. 

1. Klikněte na tlačítko **vytvořit** tlačítko **spravovat křížového Core** stránku nastavení.

1. Vyplňte průvodce:

   * Vyberte typ filer **cloudu**. 
   * Pojmenujte nový filtr jádra a klikněte na tlačítko **Další**.
   * Přijměte výchozí zásady mezipaměti a pokračujte na třetí stránce.
   * V **typ služby**, zvolte **služby Azure storage**. 
   * Zvolte přihlašovací údaje, které vytvořili dříve.
   * Nastavte **kontejneru obsah** k **prázdný**
   * Změna **certifikátu ověření** k **zakázáno**
   * Změna **režim komprese** k **None**  
   * Klikněte na **Další**.
   * Na čtvrté stránce zadejte název kontejneru v **název sektoru** jako *název_účtu_úložiště*/*container_name*.
   * Volitelně můžete nastavit **typ šifrování** k **žádný**.  Azure Storage se šifrují ve výchozím nastavení.
   * Klikněte na tlačítko **přidat filtr**.

  Podrobnější informace, přečtěte si [přidává nový filtr základní cloudové](<http://library.averesystems.com/ops_guide/4_7/new_core_filer_cloud.html>) v Průvodci konfigurací Avere clusteru. 

Na stránce se aktualizuje nebo můžete aktualizovat na stránce zobrazit vaše nové základní filtr.

V dalším kroku je potřeba [vytvořit spojení](#create-a-junction).

## <a name="create-a-junction"></a>Vytvořit spojení

Spojení je cesta, kterou vytvoříte pro klienty. Klienti připojit cestu a přejít na zvoleném cíli.

Můžete například vytvořit `/avere/files` mapovat na vaší základní filer NetApp `/vol0/data` exportovat a `/project/resources` podadresáře.

Další informace o spojovacích bodech najdete v [oboru názvů část Průvodce konfigurací clusteru Avere](http://library.averesystems.com/ops_guide/4_7/gui_namespace.html).

Postupujte podle kroků v nastavení rozhraní Avere ovládací panely:

* Klikněte na tlačítko **VServer** > **Namespace** v levém horním rohu.
* Zadejte obor názvů cesta začínající s / (lomítko), třeba ``/avere/data``.
* Zvolte váš filtr core.
* Zvolte možnost exportu filer core.
* Klikněte na **Další**.

  ![Snímek obrazovky stránky "Přidání nové spojení" s poli pro spojení, core vyfiltrovat a export](media/avere-vfxt-add-junction.png)

Spojení se zobrazí po pár sekundách. Podle potřeby vytvořte další spojení.

Po vytvoření spojení, klienti se mohou [připojení clusteru vFXT Avere](avere-vfxt-mount-clients.md) pro přístup k systému souborů.