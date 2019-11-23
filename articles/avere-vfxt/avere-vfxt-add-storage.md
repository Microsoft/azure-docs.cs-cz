---
title: Konfigurace úložiště avere vFXT – Azure
description: Postup přidání back-endového úložného systému do služby avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: 86b63e6d9799387347093e469015fbd3019069d1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255040"
---
# <a name="configure-storage"></a>Konfigurace úložiště

Tento krok nastaví systém back-endu úložiště pro cluster vFXT.

> [!TIP]
> Pokud jste vytvořili nový kontejner Azure Blob společně s clusterem avere vFXT, tento kontejner už je nastavený na použití a nemusíte přidávat úložiště.

Pokud jste nevytvořili nový kontejner objektů BLOB s clusterem nebo pokud chcete přidat další hardware nebo cloudový systém úložiště, postupujte podle těchto pokynů.

Existují dva hlavní úlohy:

1. [Vytvořte základní souborového](#create-a-core-filer), který propojí cluster vFXT s existujícím systémem úložiště nebo účtem Azure Storage.

1. [Vytvořte spojení oboru názvů](#create-a-junction), které definuje cestu, kterou budou klienti připojovat.

Tyto kroky používají ovládací panel avere. Pokud se chcete dozvědět, jak ho používat, získáte [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) .

## <a name="create-a-core-filer"></a>Vytvoření základního souborového

"Core souborového" je vFXT termín pro back-end systém úložiště. Úložiště může být hardwarové zařízení NAS, jako je NetApp nebo Isilon, nebo se může jednat o cloudové úložiště objektů. Další informace o Core filers najdete [v Průvodci nastavením clusteru avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Pokud chcete přidat základní souborového, vyberte jeden ze dvou hlavních typů základních filers:

  * [Server Core souborového](#nas-core-filer) – popisuje, jak přidat Server Core souborového 
  * [Azure Storage Cloud Core souborového](#azure-storage-cloud-core-filer) – popisuje, jak přidat účet Azure Storage jako Cloud Core souborového.

### <a name="nas-core-filer"></a>Jádro NAS souborového

Jádro NAS souborového může být místní NetApp nebo Isilon nebo koncový bod NAS v cloudu. Systém úložiště musí mít spolehlivé vysokorychlostní připojení ke clusteru avere vFXT, například připojení 1Gb/s ExpressRoute (ne síť VPN), a musí mu poskytnout kořenový přístup clusteru k používaným exportům serveru NAS.

V následujících krocích se přidá server NAS Core souborového:

1. V ovládacím panelu avere klikněte na kartu **Nastavení** v horní části.

1. Klikněte na **Core souborového** > na levé straně **spravovat základní filers** .

1. Klikněte na možnost **Vytvořit**.

   ![Snímek obrazovky se stránkou přidat novou základní souborového s ukazatelem na tlačítku pro vytvoření](media/avere-vfxt-add-core-filer-start.png)

1. Vyplňte požadované informace v průvodci: 

   * Pojmenujte základní souborového.
   * Zadejte plně kvalifikovaný název domény (FQDN), pokud je k dispozici. V opačném případě zadejte IP adresu nebo název hostitele, který se přeloží na váš základní souborového.
   * Ze seznamu vyberte třídu souborového. Pokud si nejste jistí, vyberte **jiný**.

     ![Snímek obrazovky se stránkou přidat novou základní souborového se základním názvem souborového a jeho plně kvalifikovaným názvem domény](media/avere-vfxt-add-core-filer.png)
  
   * Klikněte na **Další** a vyberte zásadu mezipaměti. 
   * Klikněte na **přidat souborového**.
   * Podrobnější informace najdete v tématu [Přidání nového serveru NAS Core souborového](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) v Průvodci nastavením clusteru avere.

Potom přejděte k [Vytvoření spojení](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Azure Storage Cloud Core souborového

Pokud chcete jako úložiště back-endu clusteru vFXT použít úložiště objektů BLOB v Azure, budete potřebovat prázdný kontejner, který se přidá jako základní souborového.

> [!TIP] 
> Pokud se rozhodnete vytvořit kontejner objektů BLOB současně s vytvořením clusteru avere vFXT, šablona nebo skript nasazení vytvoří kontejner úložiště, definuje ho jako základní souborového a vytvoří spojení oboru názvů jako součást vytváření clusteru vFXT. Šablona také vytvoří koncový bod služby úložiště uvnitř virtuální sítě clusteru. 

Přidání úložiště objektů blob do clusteru vyžaduje tyto úlohy:

* Vytvoření účtu úložiště (krok 1, níže)
* Vytvoření prázdného kontejneru objektů BLOB (kroky 2-3)
* Přidejte přístupový klíč úložiště jako přihlašovací údaje cloudu pro cluster vFXT (kroky 4-6).
* Přidání kontejneru objektů BLOB jako základního souborového pro cluster vFXT (kroky 7-9)
* Vytvoření spojení oboru názvů, které klienti používají k připojení základního souborového ([Vytvoření spojení](#create-a-junction)stejné pro hardwarové i cloudové úložiště)

Pokud chcete přidat úložiště objektů BLOB po vytvoření clusteru, postupujte podle těchto kroků. 

1. Vytvořte účet úložiště pro obecné účely v2 s těmito nastaveními:

   * **Předplatné** – stejné jako cluster vFXT
   * **Skupina prostředků** – totéž jako skupina clusteru vFXT (volitelné)
   * Stejné **umístění** jako cluster vFXT
   * **Performance** Standard (Premium Storage není podporován)
   * **Druh účtu** – pro obecné účely v2 (StorageV2)
   * **Replikace** – místně redundantní úložiště (LRS)
   * **Úroveň přístupu** – horká
   * **Vyžadován zabezpečený přenos** – zakázat tuto možnost (jiná než výchozí hodnota)
   * **Virtuální sítě** – nevyžadují se

   Můžete použít Azure Portal nebo kliknout na tlačítko nasadit do Azure níže.

   [![tlačítko pro vytvoření účtu úložiště](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Po vytvoření účtu přejděte na stránku účtu úložiště.

   ![Nový účet úložiště v Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Vytvořte kontejner objektů BLOB kliknutím na **objekty blob** na stránce Přehled a pak klikněte na **+ kontejner**. Použijte libovolný název kontejneru a ujistěte se, že je přístup nastavený na **Private**.

   ![Stránka objektů BLOB úložiště bez existujících kontejnerů](media/avere-vfxt-blob-no-container.png)

1. Klíč účtu Azure Storage získáte tak, že kliknete na **přístupové klíče** v části **Nastavení**:

   ![Azure Portal grafické uživatelské rozhraní pro kopírování klíče](media/avere-vfxt-copy-storage-key.png) 

1. Otevřete ovládací panel avere pro váš cluster. Klikněte na **Nastavení**a pak otevřete **cluster** > **cloudové přihlašovací údaje** v levém navigačním podokně. Na stránce přihlašovací údaje cloudu klikněte na **Přidat přihlašovací údaje**.

   ![Klikněte na tlačítko Přidat pověření na stránce konfigurace přihlašovacích údajů cloudu.](media/avere-vfxt-new-credential-button.png)

1. Pro vytvoření přihlašovacích údajů pro Cloud Core souborového zadejte následující informace: 

   | Pole | Hodnota |
   | --- | --- |
   | Název přihlašovacího údaje | libovolný popisný název |
   | Typ služby | (vyberte Azure Storage přístupová klávesa) |
   | Tenant | název účtu úložiště |
   | Předplatné | subscription ID |
   | Přístupový klíč k úložišti | Klíč účtu úložiště Azure (zkopírovaný v předchozím kroku) | 

   Klikněte na **Submit** (Odeslat).

   ![Dokončený formulář přihlašovacích údajů cloudu v Ovládacích panelech avere](media/avere-vfxt-new-credential-submit.png)

1. Dále vytvořte základní souborového. Na levé straně ovládacího panelu avere klikněte na **Core souborového** >  **spravovat základní filers**. 

1. Klikněte na tlačítko **vytvořit** na stránce **spravovat základní filers** nastavení.

1. Vyplňte Průvodce:

   * Vyberte souborového typ **Cloud**. 
   * Pojmenujte nový Core souborového a klikněte na **Další**.
   * Přijměte výchozí zásadu mezipaměti a pokračujte na třetí stránku.
   * V možnosti **typ služby**vyberte **Azure Storage**. 
   * Vyberte dříve vytvořené přihlašovací údaje.
   * Nastavit **obsah intervalu** jako **prázdný**
   * Změnit **ověření certifikátu** na **zakázáno**
   * Změnit **režim komprese** na **žádný**  
   * Klikněte na **Další**.
   * Na čtvrté stránce zadejte název kontejneru do pole **název** sady jako *storage_account_name*/*container_name*.
   * Volitelně můžete nastavit **typ šifrování** na **žádný**.  Azure Storage je ve výchozím nastavení šifrovaný.
   * Klikněte na **přidat souborového**.

   Podrobnější informace najdete v článku [Přidání nového Cloud Core souborového](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) v Průvodci konfigurací clusteru avere. 

Stránka se aktualizuje nebo můžete stránku aktualizovat, aby se zobrazila nová základní souborového.

Dále musíte [vytvořit spojení](#create-a-junction).

## <a name="create-a-junction"></a>Vytvoření spojení

Spojení je cesta, kterou vytvoříte pro klienty. Klienti připojí cestu a dorazí na cíl, který zvolíte.

Můžete například vytvořit `/avere/files` k mapování `/vol0/data` exportu NetApp Core souborového a `/project/resources` podadresáři.

Další informace o spojeních najdete v [části obor názvů Průvodce konfigurací clusteru avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Postupujte podle těchto kroků v rozhraní nastavení ovládacího panelu avere:

* V levém horním rohu klikněte na **VServer** > **obor názvů** .
* Zadejte cestu k oboru názvů začínající znakem/(lomítko), například ``/avere/data``.
* Vyberte si základní souborového.
* Vyberte základní export souborového.
* Klikněte na **Další**.

  ![Snímek obrazovky se stránkou přidat nový odkaz s poli dokončenými pro spojení, Core souborového a export](media/avere-vfxt-add-junction.png)

Spojení se zobrazí po několika sekundách. Podle potřeby vytvořte další spojení.

Po vytvoření spojení můžou klienti [připojit cluster avere vFXT](avere-vfxt-mount-clients.md) pro přístup k systému souborů.
