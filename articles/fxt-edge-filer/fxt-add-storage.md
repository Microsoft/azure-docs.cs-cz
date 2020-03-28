---
title: 'Kurz: Přidání úložiště do clusteru Azure FXT Edge Filer'
description: Konfigurace back-endového úložiště a pseudooborového prostoru pro klienta pro Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239214"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Kurz: Přidání back-endového úložiště a konfigurace virtuálního oboru názvů 

Tento kurz vysvětluje, jak přidat úložiště back-edge pro vaši mezipaměť a jak nastavit virtuální souborový systém orientovaný na klienta. 

Cluster se připojuje k back-endovým úložným systémům pro přístup k požadavku datových klientů a k trvalému ukládání změn než v mezipaměti. 

Obor názvů je pseudo souborový systém orientovaný na klienta, který umožňuje zaměnit back-endové úložiště bez změny pracovních postupů na straně klienta. 

V tomto kurzu se dozvíte: 

> [!div class="checklist"]
> * Jak přidat back-endové úložiště do clusteru Azure FXT Edge Filer 
> * Jak definovat cestu směřující ke klientovi pro úložiště

## <a name="about-back-end-storage"></a>O back-endovém úložišti

Cluster Azure FXT Edge Filer používá *základní definici fileru* k propojení back-endového úložného systému s clusterem FXT.

Azure FXT Edge Filer je kompatibilní s několika oblíbenými hardwarovými systémy NAS a může používat prázdné kontejnery z Azure Blob nebo jiného cloudového úložiště. 

Kontejnery cloudového úložiště musí být při přidání prázdné, aby operační systém FXT mohl zcela spravovat všechna data na svazku cloudového úložiště. Existující data můžete přesunout do cloudového kontejneru po přidání kontejneru do clusteru jako základní filer.

Pomocí Ovládacích panelů můžete do systému přidat základní filer.

> [!NOTE]
> 
> Pokud chcete používat úložiště Amazon AWS nebo Google Cloud, musíte nainstalovat licenci funkce<sup>FlashCloud TM.</sup> Požádejte o licenční klíč zástupce společnosti Microsoft a postupujte podle pokynů v příručce ke starší konfiguraci [pro přidání nebo odebrání licencí funkcí](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Podpora úložiště objektů blob Azure je součástí softwarové licence Azure FXT Edge Filer. 

Podrobnější informace o přidávání základních filerů načtete v těchto částech Průvodce konfigurací clusteru:

* Další informace o výběru a přípravě na přidání základního fileru načláneknete [článek Práce s jádrem Filerů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Podrobné požadavky a podrobné pokyny naleznete v těchto článcích:

  * [Přidání nového základního fileru NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Přidání nového fileru cloudového jádra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Po přidání základního fileru můžete aktualizovat jeho nastavení na stránce Nastavení podrobností základního souboru.

## <a name="add-a-core-filer"></a>Přidání základního fileru

Definujte základní filer klepnutím na tlačítko **Vytvořit** na stránce **Nastavení základního souboru správce** > **jádra fileru.**

![Klepnutí na tlačítko Vytvořit nad seznamem základních filerů na stránce Spravovat základní filery](media/fxt-cluster-config/create-core-filer-button.png)

Průvodce **Přidat nové jádro fileru** vás provede procesem vytváření základního fileru, který bude propojen s úložištěm back-end. Průvodce konfigurací clusteru obsahuje podrobný popis procesu, který se liší pro úložiště NFS/NAS a pro cloudové úložiště (odkazy jsou výše). 

Dílčí úkoly zahrnují:

* Určení typu základního fileru (NAS nebo cloud)

  ![První stránka průvodce novým základním filerem na NAS. Možnost "cloud core filer" je zakázána a zobrazuje chybovou zprávu o chybějící licenci.](media/fxt-cluster-config/new-nas-1.png)

* Nastavte název základního fileru. Zvolte název, který správcům clusteru pomůže pochopit, který systém úložiště představuje.

* Pro filery jádra NAS zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresu. FQDN se doporučuje pro všechny základní filers a vyžaduje pro přístup SMB.

* Vyberte zásadu mezipaměti – na druhé stránce průvodce jsou uvedeny dostupné zásady mezipaměti pro nový základní filer. Podrobnosti naleznete v [části zásady mezipaměti průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Druhá stránka hardwaru NAS nové jádro filer průvodce; Rozevírací nabídka Zásady mezipaměti je otevřená a zobrazuje několik zakázaných možností a tři platné možnosti zásad mezipaměti (obejít, číst ukládání do mezipaměti a ukládání do mezipaměti pro čtení a zápis).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Pro cloudové úložiště je nutné zadat cloudovou službu a přihlašovací údaje pro přístup, mimo jiné parametry. Podrobnosti [načtěte cloudovou službu a protokol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) v Průvodci konfigurací clusteru.

  ![Informace o základním souboru v průvodci New Core Filer](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Pokud jste pro tento cluster již přidali přihlašovací údaje pro přístup ke cloudu, zobrazí se v seznamu. Aktualizujte a přidejte pověření na stránce Nastavení**pověření cloudu** **clusteru.** >  

Po vyplnění všech požadovaných nastavení v průvodci klepnutím na tlačítko **Přidat soubor a** odeslat změnu.

Po několika okamžicích se úložný systém objeví v seznamu základních souborů **dashboardu** a je přístupný prostřednictvím stránek nastavení základního souboru.

![základní filer "Flurry-NAS" na stránce Spravovat základní filers nastavení, s filer podrobnosti zobrazení rozšířené](media/fxt-cluster-config/core-filer-in-manage-page.png)

Jádru filer na tomto snímku chybí vserver. Základní filer je nutné propojit s vserverem a vytvořit spojení, aby klienti měli přístup k úložišti. Tyto kroky jsou popsány níže v [části Konfigurace oboru názvů](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurace oboru názvů

Cluster Azure FXT Edge Filer vytvoří virtuální souborový systém nazývaný *obor názvů clusteru,* který zjednodušuje přístup klientů k datům uloženým v různých back-endových systémech. Vzhledem k tomu, že klienti požadují soubory pomocí virtuální cesty, lze systémy úložiště přidat nebo nahradit bez nutnosti měnit pracovní postup klienta. 

Obor názvů clusteru také umožňuje prezentovat cloudové a NAS úložné systémy v podobné struktuře souborů. 

Servery v clusteru udržují obor názvů a obsluhují obsah klientům. K vytvoření oboru názvů clusteru jsou dva kroky: 

1. Vytvoření vserveru 
1. Nastavení spojení mezi back-endovými úložnými systémy a cestami souborového systému směřujících ke klientům 

### <a name="create-a-vserver"></a>Vytvoření vserveru

VServers jsou virtuální souborové servery, které řídí toky dat mezi klientem a základními filery clusteru:

* VServers hostují ip adresy orientované na klienta
* VServers vytvořit obor názvů a definovat spojení, které mapují klient-směřující virtuální adresářové struktury exportovat na back-end úložiště
* VServers vynucují ovládací prvky přístupu k souborům, včetně základních zásad exportu souborů a systémů ověřování uživatelů
* VServers poskytují infrastrukturu SMB

Než začnete konfigurovat cluster ový server, přečtěte si propojenou dokumentaci a poraďte se se zástupcem společnosti Microsoft, který vám pomůže porozumět oboru názvů a serverům vservers. Pokud používáte názvy VLAN, [vytvořte je](fxt-configure-network.md#adjust-network-settings) před vytvořením vserveru. 

Tyto části Průvodce konfigurací clusteru vám pomohou seznámit se s funkcemi FXT vserver a globálního oboru názvů:

* [Vytváření a práce s VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Použití globálního oboru názvů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Vytvoření serveru VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Potřebujete alespoň jeden vserver pro váš cluster. 

Chcete-li vytvořit nový vserver, potřebujete následující informace:

* Název, který chcete nastavit pro vserver

* Rozsah adres IP orientovaných na klienta, které bude server zpracovávat

  Při vytváření serveru vserver je nutné zadat jeden rozsah souvislých adres IP. Další adresy můžete přidat později pomocí stránky Nastavení **sítě pro klienty.**

* Pokud má vaše síť sítě sítě VLAN, které síť VLAN použít pro tento

K vytvoření **nového** > vserveru použijte stránku Nastavení**vserver správa vserverů** vserveru. Podrobnosti [načtěte článek Vytvoření serveru VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) v Průvodci konfigurací clusteru. 

![automaticky otevírané okno pro vytvoření nového vserveru](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Vytvoření křižovatky

*Křižovatka* mapuje cestu back-end úložiště do oboru názvů viditelného klientem.

Tento systém můžete použít ke zjednodušení cesty používané v přípojcích klientských bodů a škálování kapacity bez problémů, protože jedna virtuální cesta může pojmout úložiště z více základních filerů.

![Přidat stránku průvodce novou křižovatkou s vyplněným nastavením](media/fxt-cluster-config/add-junction-full.png)

Podrobné informace o vytvoření spojení oboru názvů naleznete v oboru názvů v serveru [ **VServer** > ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) v Průvodci konfigurací clusteru.

![Stránka nastavení oboru názvů v systému VServer >, která zobrazuje podrobnosti o spojení](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurace pravidel exportu

Poté, co máte vserver a základní filer, měli byste přizpůsobit pravidla exportu a zásady exportu, které řídí, jak mohou klienti přistupovat k souborům při exportu základního fileru.

Nejprve použijte stránku**Pravidla exportu** **serveru VServer** > k přidání nových pravidel, k úpravě výchozí zásady nebo k vytvoření vlastních zásad exportu.

Za druhé, použijte stránku**Zásady exportu** **serveru VServer** > k použití přizpůsobených zásad pro exporty základního fileru při přístupu prostřednictvím tohoto serveru vserver.

Podrobnosti naleznete v článku Průvodce konfigurací clusteru [Řízení přístupu k exportům základních souborů.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html)


## <a name="next-steps"></a>Další kroky

Po přidání úložiště a konfiguraci oboru názvů směřujícího ke klientům dokončete počáteční nastavení clusteru: 

> [!div class="nextstepaction"]
> [Konfigurace síťových nastavení clusteru](fxt-configure-network.md)
