---
title: Konfigurace úložiště Avere vFXT – Azure
description: Jak přidat back-end ový úložný systém do avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252607"
---
# <a name="configure-storage"></a>Konfigurace úložiště

Tento krok nastaví back-end ový úložný systém pro váš cluster vFXT.

> [!TIP]
> Pokud jste vytvořili nový kontejner objektů blob Azure spolu s clusterem Avere vFXT, tento kontejner je již nakonfigurovaný a připravený k použití.

Pokud jste s clusterem nevytvořili nový kontejner objektů Blob, postupujte podle těchto pokynů nebo pokud chcete přidat další hardwarový nebo cloudový úložný systém.

Existují dva hlavní úkoly:

1. [Vytvořte základní filer](#create-a-core-filer), který propojí váš cluster vFXT s existujícím systémem úložiště nebo kontejnerem účtu Úložiště Azure.

1. [Vytvořte křižovatku oboru názvů](#create-a-junction), která definuje cestu, kterou budou klienti připojovat.

Tyto kroky používají ovládací panel Avere. Přečtěte [si Přístup ke clusteru vFXT,](avere-vfxt-cluster-gui.md) abyste se dozvěděli, jak ho používat.

## <a name="create-a-core-filer"></a>Vytvoření základního fileru

"Core filer" je termín vFXT pro back-end úložný systém. Úložiště může být hardwarové zařízení NAS, jako je NetApp nebo Isilon, nebo může být úložiště objektů v cloudu. Další informace o základních filers naleznete v [průvodci nastavením clusteru Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Chcete-li přidat základní filer, zvolte jeden ze dvou hlavních typů základních filerů:

* [Základní filer NAS](#nas-core-filer) - popisuje, jak přidat základní filer NAS
* [Azure Storage cloud core filer](#azure-blob-storage-cloud-core-filer) – popisuje, jak přidat kontejner úložiště objektů Blob Azure jako filer jádra cloudu

### <a name="nas-core-filer"></a>Základní filer NAS

Základní filer NAS může být místní zařízení NetApp nebo Isilon nebo koncový bod NAS v cloudu. Systém úložiště musí mít spolehlivé vysokorychlostní připojení ke clusteru Avere vFXT – například připojení 1GBps ExpressRoute (nikoli VPN) – a musí poskytnout kořenovému přístupu k používaným exportům NAS.

Chcete-li přidat základní filer NAS, postupujte takto:

1. V ovládacím panelu Avere klikněte nahoře na kartu **Nastavení.**

1. Vlevo klikněte na **Základní filer** > **Spravovat základní filery.**

1. Klikněte na **Vytvořit**.

   ![Snímek obrazovky s novou základní stránkou fileru s kurzorem nad tlačítkem Vytvořit](media/avere-vfxt-add-core-filer-start.png)

1. Vyplňte požadované informace v průvodci:

   * Pojmenujte základní filer.
   * Zadejte plně kvalifikovaný název domény (FQDN), pokud je k dispozici. V opačném případě zadejte ADRESU IP nebo název hostitele, který se překládá na základní filer.
   * Ze seznamu zvolte třídu filer. Pokud si nejste jisti, zvolte **Jiné**.

     ![Snímek obrazovky stránky Přidat nové jádro fileru s názvem základního souboru a jeho plně kvalifikovaným názvem domény](media/avere-vfxt-add-core-filer.png)
  
   * Klikněte na **Další** a zvolte zásadu mezipaměti.
   * Klepněte na **tlačítko Přidat filer**.
   * Podrobnější informace najdete [v části Přidání nového fileru jádra NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) v průvodci nastavením clusteru Avere.

Dále pokračujte [k vytvoření křižovatky](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Základní filer cloudového úložiště Azure blob Storage

Chcete-li použít úložiště objektů blob Azure jako back-endové úložiště clusteru vFXT, potřebujete prázdný kontejner, který chcete přidat jako základní filer.

Přidání úložiště objektů Blob do clusteru vyžaduje tyto úkoly:

* Vytvoření účtu úložiště (krok 1, níže)
* Vytvoření prázdnékontejnerové nádoby objektu Blob (kroky 2-3)
* Přidání přístupového klíče úložiště jako cloudového pověření pro cluster vFXT (kroky 4–6)
* Přidání kontejneru objektů Blob jako základního fileru pro cluster vFXT (kroky 7–9)
* Vytvořte spojení oboru názvů, které klienti používají k připojení základního fileru ([Vytvoření křižovatky](#create-a-junction), stejné pro hardwarové i cloudové úložiště)

> [!TIP]
> Pokud vytvoříte nový kontejner objektů Blob při vytváření Avere vFXT pro cluster Azure, šablona nasazení automaticky nakonfiguruje kontejner jako základní filer. (To platí také v případě, že používáte skript pro vytváření, který je k dispozici na vyžádání.) Není nutné konfigurovat základní filer později.
>
> Nástroj pro vytváření clusteru provádí tyto konfigurační úlohy za vás:
>
> * Vytvoří nový kontejner objektů Blob v účtu poskytovaného úložiště.
> * Definuje kontejner jako základní filer.
> * Vytvoří spojení oboru názvů ke kontejneru.
> * Vytvoří koncový bod služby úložiště uvnitř virtuální sítě clusteru.

Pokud chcete po vytvoření clusteru přidat úložiště objektů blob, postupujte takto.

1. Vytvořte účet úložiště pro obecné účely V2 s těmito nastaveními:

   * **Předplatné** – stejné jako vFXT clusteru
   * **Skupina prostředků** – stejná jako skupina clusterů vFXT (volitelné)
   * **Umístění** – stejné jako vFXT clusteru
   * **Výkon** – standardní (úložiště Premium není podporováno)
   * **Typ účtu** - Pro všeobecné účely V2 (StorageV2)
   * **Replikace** – místně redundantní úložiště (LRS)
   * **Přístupová úroveň** - hot
   * **Vyžadován bezpečný přenos** - zakázat tuto možnost (nevýchozí hodnota)
   * **Virtuální sítě** - není vyžadováno

   Můžete použít portál Azure nebo klikněte na tlačítko "Nasadit do Azure" níže.

   [![tlačítko pro vytvoření účtu úložiště](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Po vytvoření účtu přejděte na stránku účtu úložiště.

   ![Nový účet úložiště na webu Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Vytvoření nového kontejneru objektů Blob: Klikněte na stránce s přehledem na **kontejnery** a potom klikněte na **+Kontejner**. Použijte libovolný název kontejneru a ujistěte se, že je přístup nastaven na **soukromé**.

   ![Stránka objektů BLOB úložiště s tlačítkem +container v kroužku a vytvořením nového kontejneru na místní stránce](media/avere-vfxt-new-blob.png)

1. Klíč účtu Azure Storage získáte kliknutím na **přístupové klíče v** části **Nastavení**. Zkopírujte jeden z uvedených klíčů.

   ![Grafické rozhraní portálu Azure pro kopírování klíče](media/avere-vfxt-copy-storage-key.png)

1. Otevřete ovládací panel Avere pro váš cluster. Klepněte na tlačítko **Nastavení**a v levém navigačním podokně otevřete**pověření cloudu** **clusteru.** >  Na stránce Přihlašovací údaje v cloudu klikněte na **Přidat pověření**.

   ![Klikněte na tlačítko Přidat přihlašovací údaje na konfigurační stránce Pověření cloudu.](media/avere-vfxt-new-credential-button.png)

1. Vyplňte následující informace a vytvořte přihlašovací údaje pro filer jádra cloudu:

   | Pole | Hodnota |
   | --- | --- |
   | Název přihlašovacího údaje | jakýkoli popisný název |
   | Typ služby | (vyberte přístupový klíč úložiště Azure) |
   | Tenant | název účtu úložiště |
   | Předplatné | subscription ID |
   | Přístupový klíč úložiště | Klíč účtu úložiště Azure (zkopírovaný v předchozím kroku) |

   Klepněte na **tlačítko Odeslat**.

   ![Vyplněný formulář pověření cloudu v ovládacím panelu Avere](media/avere-vfxt-new-credential-submit.png)

1. Dále vytvořte základní filer. Na levé straně ovládacího panelu Avere klepněte na **položku Core Filer** >  **Manage Core Filers**.

1. Klepněte na tlačítko **Vytvořit** na stránce **Spravovat základní soubory.**

1. Vyplňte průvodce:

   * Vyberte filer typu **Cloud**.
   * Pojmenujte nový základní filer a klepněte na tlačítko **Další**.
   * Přijměte výchozí zásady mezipaměti a pokračujte na třetí stránku.
   * V **typu Služby**zvolte **úložiště Azure**.
   * Zvolte pověření vytvořené dříve.
   * Nastavit **obsah bloku** na **prázdný**
   * Změnit **ověření certifikátu** na **Zakázáno**
   * Změnit **režim komprese** na **Žádný**
   * Klikněte na **Další**.
   * Na čtvrté stránce zadejte název kontejneru v **názvu bloku** jako *storage_account_name*/*container_name*.
   * Volitelně můžete nastavit **typ šifrování** na **žádný**.  Azure Storage je šifrované ve výchozím nastavení.
   * Klepněte na **tlačítko Přidat filer**.

   Podrobnější informace najdeš v [části Přidání nového fileru jádra cloudu](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) v průvodci konfigurací clusteru Avere.

Stránka se aktualizuje nebo můžete aktualizovat stránku a zobrazit nový základní filer.

Dále je třeba [vytvořit křižovatku](#create-a-junction).

## <a name="create-a-junction"></a>Vytvoření křižovatky

Křižovatka je cesta, kterou vytvoříte pro klienty. Klienti připojit cestu a dorazí na cíl, který si vyberete.

Můžete například vytvořit `/vfxt/files` mapování na export základního `/vol0/data` `/project/resources` fileru NetApp a podadresář.

Další informace o křižovatkách naleznete v části obor názvů v [průvodci konfigurací clusteru Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

V rozhraní ovládacího panelu Avere postupujte takto:

* V levém horním rohu klikněte na**Obor názvů** **serveru VServer.** > 
* Zadejte cestu oboru názvů začínající na ``/vfxt/data``/ (lomítko), jako je .
* Vyberte si základní filer.
* Zvolte základní export fileru.
* Klikněte na **Další**.

  ![Snímek obrazovky stránky "Přidat novou křižovatku" s vyplněnými poli pro křižovatku, základní filer a export](media/avere-vfxt-add-junction.png)

Křižovatka se objeví po několika sekundách. Podle potřeby vytvořte další křižovatky.

Po vytvoření spojení klienti používají cestu oboru názvů pro přístup k souborům ze systému úložiště.

## <a name="next-steps"></a>Další kroky

* [Připojení clusteru Avere vFXT](avere-vfxt-mount-clients.md)
* Naučte se efektivní způsoby [přesunutí dat do nového kontejneru objektů Blob](avere-vfxt-data-ingest.md)
