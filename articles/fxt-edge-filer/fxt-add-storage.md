---
title: Přidání back endové úložné do Microsoft Azure FXT hrany Filer clusteru
description: Konfigurace back endové úložné a pseudonamespace klienta pro Azure FXT hrany vyfiltrovat
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0a16e654ff92c450438ac91c590b42d22201d015
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450454"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Kurz: Přidat back endové úložné a nakonfigurovat virtuální obor názvů 

Tento kurz vysvětluje, jak přidat úložiště okraje zpět ke svojí mezipaměti a jak nastavit virtuální systém souborů klienta. 

Cluster se připojí k back endové úložné systémy pro přístup k žádosti klientů data a ukládat změny trvale než v mezipaměti. 

Obor názvů je pseudo klienta systému souborů, umožňující vyměnit back endové úložné bez změny pracovních postupů na straně klienta. 

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Postup přidání back endové úložiště do clusteru Azure FXT hrany vyfiltrovat 
> * Definování cestu klienta pro úložiště

## <a name="about-back-end-storage"></a>O službě storage back-end

Cluster Azure FXT hrany Filer používá *základní filer* definice propojení systému back endové úložné FXT clusteru.

Azure FXT hrany Filer je kompatibilní s několika oblíbenými NAS hardwaru pro systémy a pomocí prázdné kontejnerů objektů Blob v Azure nebo jiné cloudové úložiště. 

Kontejnery úložiště cloudu musí být prázdný, když se přidá tak, aby všechna data na svazek úložiště cloudu můžete spravovat zcela FXT operačního systému. Vaše existující data můžete přesunout do cloudového kontejneru po přidání do clusteru jako základní filtr kontejneru.

Použijte ovládací Panel Přidat core souborového systému.

> [!NOTE]
> 
> Pokud chcete použít Amazon AWS a Google Cloud storage, je nutné nainstalovat FlashCloud<sup>TM</sup> funkce licence. Kontaktujte zástupce Microsoftu pro licenčního kódu a pak postupujte podle pokynů v Průvodci starší verzi konfigurace [přidáním nebo odebráním licencí funkce](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Podpora pro Azure Blob storage je zahrnuté v licencích softwaru Azure FXT hrany vyfiltrovat. 

Podrobnější informace o přidávání křížového core, přečtěte si tyto kapitoly Průvodce konfigurací clusteru:

* Další informace o výběr a příprava na přidání core filer, najdete v článku [práce s Core křížového](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Podrobné požadavky a pokyny přečtěte si tyto články:

  * [Přidání nové Filer Core NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Přidání nové Filer Core cloudu](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Po přidání core filer můžete aktualizovat svoje nastavení na stránce s podrobnostmi Filer základní nastavení.

## <a name="add-a-core-filer"></a>Přidat filtr core

Definovat core filer kliknutím **vytvořit** tlačítko **Core Filer** > **spravovat křížového Core** stránku nastavení.

![Kliknutím na tlačítko Vytvořit nad seznam filtrech jádra na stránce Správa křížového Core](media/fxt-cluster-config/create-core-filer-button.png)

**Přidat nový filtr Core** Průvodce vás provede procesem vytvoření základní filtr, který odkazuje na back-endového úložiště. Průvodce konfigurací clusteru obsahuje podrobný popis procesu, který se liší pro systém souborů NFS nebo NAS úložiště a cloudového úložiště (odkazy jsou výše). 

Dílčí úkoly patří:

* Zadejte typ filer core (NAS nebo v cloudu)

  ![První stránka průvodce hardwaru NAS nové základní filtr Možnost "cloud core filer" je zakázané a zobrazí se chybová zpráva o chybí licence.](media/fxt-cluster-config/new-nas-1.png)

* Nastavte název základní filtr. Zvolte název, který pomáhá pochopit, jaký systém úložiště představuje správce clusteru.

* Křížového core NAS zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresu. Plně kvalifikovaný název domény se doporučuje pro všechna jádra filtrech a vyžadované pro přístup k protokolu SMB.

* Výběr zásad mezipaměti – na druhé stránce průvodce seznam zásad mezipaměti k dispozici pro nové základní filtr. Podrobnosti najdete v článku [mezipaměti zásad část Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Druhá stránka hardwaru NAS core filer Průvodce; Rozevírací nabídka zásad mezipaměti je otevřená, zobrazuje několik zakázána možnosti a tři možnosti zásad platné mezipaměti (obejít, přečtěte si ukládání do mezipaměti a ukládání do mezipaměti pro čtení a zápis).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Pro cloudové úložiště je nutné zadat cloudové služby a přístup k přihlašovací údaje, mezi další parametry. Podrobnosti najdete v článku [cloudové služby a protokol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) v Průvodci konfigurace clusteru.

  ![Informace o filer core cloudu v nástroji Průvodce novou základní filtr](media/fxt-cluster-config/new-core-filer-cloud3.png) <!-- xxx get an Azure version of this screenshot xxx -->
  
  Pokud jste už přidali přihlašovacích údajů pro přístup k cloudu pro tento cluster, jsou uvedeny v seznamu. Aktualizovat a přidat přihlašovací údaje **clusteru** > **přihlašovací údaje ke cloudu** stránku nastavení. 

Po vyplnění všechna požadovaná nastavení v průvodci, klikněte na tlačítko **přidat filtr** tlačítko k odeslání změn.

Po chvíli se systém úložiště se zobrazí na **řídicí panel** základní seznam filtrech a je přístupný prostřednictvím stránky nastavení filer core.

![Základní filtr "Flurry NAS" na stránce nastavení spravovat křížového Core pomocí zobrazení podrobností o filer rozšířit](media/fxt-cluster-config/core-filer-in-manage-page.png)

Základní filtr na tomto snímku obrazovky chybí vserver. Musíte propojit vserver filer jádra a vytvořit spojení tak, aby klienti mají přístup k úložišti. Tyto kroky jsou popsány níže v [konfigurace oboru názvů](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurace oboru názvů

Cluster Azure FXT hrany Filer vytvoří virtuálního systému souborů, volá se *clusteru obor názvů* , která zjednodušuje klientský přístup k datům uloženým na různé back endových systémů. Protože klienti požadují souborů pomocí virtuální cesty, úložných systémů lze přidat nebo nahradit bez nutnosti změny pracovního postupu klienta. 

Obor názvů clusteru také umožňuje cloudu a systémů úložišť NAS v podobné struktury. 

Clusteru vservers udržovat obor názvů a poskytování obsahu pro klienty. Existují dva kroky k vytvoření oboru názvů clusteru: 

1. Vytvoření vserver 
1. Nastavení spojení mezi back endové úložné systémy a cesty klienta systému souborů 

### <a name="create-a-vserver"></a>Vytvoření vserver

VServers jsou virtuální souborové servery, které řídí tok dat mezi klientem a křížového core clusteru:

* VServers hostitele IP adresy klienta
* VServers vytvořit obor názvů a definovat spojovacích bodech, které se mapují klienta virtuální adresářovou strukturu, exporty u back-endového úložiště
* VServers vynutit řízení přístupu k souboru, včetně základní filer export zásad a systémy ověřování uživatele
* VServers poskytují infrastrukturu protokolu SMB

Před zahájením konfigurace vserver clusteru, přečtěte si dokumentaci propojené a prostudovat si zástupce společnosti Microsoft pro obor názvů nápovědy principy a vservers. Pokud používáte sítě VLAN, [je vytvořit](fxt-configure-network.md#adjust-network-settings) před vytvořením vserver. 

Tyto části Průvodce konfigurací clusteru vám pomůže seznámit se s FXT vserver a globální obor názvů funkce:

* [Vytváření a práci s VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Pomocí globálního Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Vytváření VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Budete potřebovat alespoň jeden vserver pro váš cluster. 

Pokud chcete vytvořit nový vserver, budete potřebovat následující informace:

* Název má být nastavena pro vserver

* Rozsah adres IP klienta vserver bude zpracovávat.

  Při vytváření vserver, je třeba zadat jeden rozsah adres IP souvislé. Další adresy můžete přidat později pomocí **síť směřující klienta** stránku nastavení.

* Pokud síť obsahuje virtuální místní sítě, které sítě VLAN pro účely tohoto vserver

Použití **VServer** > **spravovat VServers** nastavení stránky vytvořte novou vserver. Přečtěte si podrobnosti [vytváření VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) v Průvodci konfigurace clusteru. 

![automaticky otevírané okno pro vytvoření nového vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Vytvořit spojení

A *spojení* back-endu úložiště cesta se mapuje na klienta viditelným oboru názvů.

Tento systém můžete použít ke zjednodušení cesty použité v přípojné body klienta a k jednoduchému škálování kapacity, protože jedna virtuální cesta zvládne úložiště z více jader filtrech.

![Přidat stránku průvodce novou spojení s nastavením vyplněna](media/fxt-cluster-config/add-junction-full.png)

Odkazovat na [ **VServer** > **Namespace** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) v Průvodci konfigurace clusteru pro kompletní informace o vytváření spojení oboru názvů.

![VServer > Namespace nastavení stránka zobrazující podrobnosti pro spojení](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurovat pravidla pro export

Až budete mít, vserver a vyfiltrovat core, by měl přizpůsobení pravidel exportu a exportovat zásady, které řídí, jak mohou klienti získat přístup souborů na exporty filer core.

Nejprve **VServer** > **exportovat pravidla** stránky přidávat nová pravidla, chcete-li změnit výchozí zásady nebo vytvořte vlastní zásadu vlastní export.

Za druhé, použijte **VServer** > **exportovat zásady** stránce vlastní zásadu chcete uplatnit na vaší základní filer exporty při přístupu prostřednictvím tohoto vserver.

Přečtěte si článek Průvodce konfigurací clusteru [řízení přístupu ke Core Filer exporty](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) podrobnosti.


## <a name="next-steps"></a>Další postup

Po přidání úložiště a konfiguraci oboru názvů klienta, dokončete počáteční nastavení vašeho clusteru: 

> [!div class="nextstepaction"]
> [Konfigurace nastavení sítě clusteru](fxt-configure-network.md)
