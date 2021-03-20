---
title: 'Kurz: Přidání úložiště do clusteru Azure FXT Edge souborového'
description: Jak nakonfigurovat back-end úložiště a klientské pseudonamespace pro Azure FXT Edge souborového
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 7d10c6c1ce440b2ffe964dc78379ef3ab108e78e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217520"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Kurz: přidání back-endu úložiště a konfigurace virtuálního oboru názvů

V tomto kurzu se dozvíte, jak přidat back-Edge úložiště pro mezipaměť a jak nastavit virtuální počítač s klientským přístupem.

Cluster se připojuje k back-endovém systémům úložiště pro přístup k požadavku na datové klienty a trvale ukládá změny, než je mezipaměť.

Obor názvů je systém, který je pro klientské počítače, který umožňuje odkládací úložiště back-endu bez změny pracovních postupů na straně klienta.

V tomto kurzu se dozvíte:

> [!div class="checklist"]
>
> * Postup přidání úložiště back-endu do clusteru Azure FXT Edge souborového
> * Jak definovat cestu k klientovi pro úložiště

## <a name="about-back-end-storage"></a>O back-endu úložiště

Cluster Azure FXT Edge souborového používá základní definici *souborového* k propojení záložního úložného systému s clusterem FXT.

Azure FXT Edge souborového je kompatibilní s několika oblíbenými hardwarovými systémy NAS a může používat prázdné kontejnery z Azure Blob nebo jiného cloudového úložiště.

Kontejnery cloudového úložiště musí být po přidání prázdné, aby operační systém FXT mohl úplně spravovat všechna data ve svazku cloudového úložiště. Po přidání kontejneru do clusteru jako základního souborového můžete přesunout existující data do kontejneru cloudu.

Pomocí ovládacích panelů přidejte do systému základní souborového.

> [!NOTE]
>
> Pokud chcete použít službu Amazon AWS nebo Google Cloud Storage, musíte nainstalovat licenci na funkci FlashCloud<sup>TM</sup> . Pro [Přidání nebo odebrání licencí funkcí](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)se obraťte na zástupce společnosti Microsoft a potom postupujte podle pokynů v Průvodci konfigurací starší verze.
>
> Podpora služby Azure Blob Storage je součástí licence na software Azure FXT Edge souborového.

Podrobnější informace o přidání základních filers najdete v těchto částech Průvodce konfigurací clusteru:

* Další informace o výběru a přípravě pro přidání základní souborového najdete v článku [práce s jádrem filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Podrobné požadavky a podrobné pokyny najdete v těchto článcích:

  * [Přidání nového serveru NAS Core souborového](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Přidává se nový Cloud Core souborového.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Po přidání základního souborového můžete aktualizovat jeho nastavení na stránce základní nastavení podrobností souborového.

## <a name="add-a-core-filer"></a>Přidat základní souborového

Kliknutím na tlačítko **vytvořit** na stránce **Core souborového**  >  **spravovat základní nastavení filers** definujte základní souborového.

![Kliknutím na tlačítko vytvořit nad seznamem základních filers na stránce spravovat základní filers](media/fxt-cluster-config/create-core-filer-button.png)

Průvodce **přidáním nového základního souborového** vás provede procesem vytvoření základního souborovéhou, který odkazuje na vaše back-endové úložiště. Průvodce konfigurací clusteru obsahuje podrobné popisy procesu, který je odlišný pro úložiště NFS/NAS a pro cloudové úložiště (odkazy jsou uvedené výše).

Mezi dílčí úkoly patří:

* Zadejte typ základního souborového (NAS nebo Cloud).

  ![První stránka průvodce hardwarovým serverem NAS New Core souborového Wizard. Možnost "Cloud Core souborového" je zakázána a zobrazí se chybová zpráva s informacemi o chybějící licenci.](media/fxt-cluster-config/new-nas-1.png)

* Nastavte název základního souborového. Vyberte název, který pomůže správcům clusteru pochopit, který úložný systém představuje.

* V části NAS Core filers zadejte plně kvalifikovaný název domény (FQDN) nebo IP adresu. Pro všechny základní filers se doporučuje plně kvalifikovaný název domény a vyžaduje se pro přístup přes protokol SMB.

* Výběr zásad mezipaměti – druhá stránka průvodce obsahuje seznam dostupných zásad mezipaměti pro nové jádro souborového. Podrobnosti najdete v [části zásady mezipaměti v Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html).

  ![Druhá stránka průvodce hardwarovým serverem NAS New Core souborového Wizard; rozevírací nabídka zásady mezipaměti je otevřená, zobrazuje několik zakázaných možností a tři platné možnosti zásad mezipaměti (nepoužívat, ukládání do mezipaměti a čtení a zápis do mezipaměti).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* V případě cloudového úložiště musíte zadat přihlašovací údaje cloudové služby a přístup mezi další parametry. Podrobnosti najdete v tématu [cloudová služba a protokol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) v Průvodci konfigurací clusteru.

  ![Informace o cloud Core souborového v Průvodci novým jádrem souborového](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  Pokud jste už přidali přihlašovací údaje cloudového přístupu pro tento cluster, zobrazí se v seznamu. Aktualizujte a přidejte přihlašovací údaje na  >  stránce nastavení **přihlašovacích údajů cloudového** clusteru.

Po vyplnění všech požadovaných nastavení v průvodci klikněte na tlačítko **přidat souborového** a odešlete změnu.

Po chvíli se systém úložiště zobrazí v seznamu Core filers ( **řídicí panel** ) a bude se k němu přihlédnout prostřednictvím základních stránek nastavení souborového.

![Core souborového "Flurry-NAS" na stránce Správa základních filers nastavení s rozbaleným zobrazením podrobností souborového](media/fxt-cluster-config/core-filer-in-manage-page.png)

V jádru souborového na tomto snímku obrazovky chybí VServer. Musíte propojit základní souborového s VServer a vytvořit spojení, aby klienti mohli získat přístup k úložišti. Tyto kroky jsou popsané níže v části [Konfigurace oboru názvů](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurace oboru názvů

Cluster Azure FXT Edge souborového vytvoří virtuální systém souborů pojmenovaný *obor názvů clusteru* , který zjednodušuje klientský přístup k datům uloženým v různých systémech back-end. Vzhledem k tomu, že klienti požadují soubory pomocí virtuální cesty, je možné přidat nebo nahradit systémy úložišť bez nutnosti měnit pracovní postup klienta.

Obor názvů clusteru taky umožňuje zobrazit cloudové a síťové úložné systémy v podobné struktuře souborů.

Vservers clusteru udržuje obor názvů a obsluhuje obsah pro klienty. Existují dva kroky pro vytvoření oboru názvů clusteru:

1. Vytvoření VServer
1. Nastavení spojení mezi back-endové systémy úložiště a cestami k systémovým systémům pro klientské počítače

### <a name="create-a-vserver"></a>Vytvoření VServer

VServers jsou virtuální souborové servery, které řídí způsob toku dat mezi klientem a jádrem filers clusteru:

* IP adresy pro klientské hostitele VServers
* VServers vytvořit obor názvů a definovat spojení, která mapují klientské struktury virtuálních adresářů na export v úložišti back-endu
* VServers vynutila řízení přístupu k souborům, včetně základních zásad exportu souborového a systémů ověřování uživatelů.
* VServers poskytuje infrastrukturu SMB.

Než začnete konfigurovat cluster vserver, přečtěte si odkazovanou dokumentaci a obraťte se na zástupce Microsoftu, kde najdete nápovědu k oboru názvů a vservers. Pokud používáte sítě VLAN, [vytvořte je](fxt-configure-network.md#adjust-network-settings) ještě před vytvořením VServer.

Tyto části Průvodce konfigurací clusteru vám pomůžou se seznámení s funkcemi FXT VServer a globálním oborem názvů:

* [Vytváření a práce s VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Použití globálního oboru názvů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Vytvoření VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Pro svůj cluster potřebujete aspoň jeden VServer.

K vytvoření nového VServer potřebujete následující informace:

* Název, který se má nastavit pro VServer

* Rozsah IP adres směřujících klientovi, které bude VServer zpracovávat

  Při vytváření VServer je nutné, abyste zadali jeden rozsah souvislých IP adres. Další adresy můžete přidat později pomocí stránky nastavení **sítě klientské sítě** .

* Pokud má síť sítě VLAN, kterou síť VLAN použijete pro tento VServer

  >  K vytvoření nového VServer použijte stránku VServer **Spravovat nastavení VServers** . Podrobnosti najdete v [tématu Vytvoření VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) v Průvodci konfigurací clusteru.

![automaticky otevírané okno pro vytvoření nového VServer](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Vytvoření spojení

*Spojení* mapuje cestu k úložišti back-endu na obor názvů viditelný pro klienta.

Tento systém můžete použít ke zjednodušení cesty používané v přípojných bodech klienta a k bezproblémovému škálování kapacity, protože jedna virtuální cesta může připojovat úložiště od více jader filers.

![Stránka Průvodce přidáním nového spojení s vyplněnými nastaveními](media/fxt-cluster-config/add-junction-full.png)

Kompletní informace o vytvoření spojení oboru názvů najdete v tématu [ **VServer**  >  **Namespace**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) v Průvodci konfigurací clusteru.

![Stránka nastavení oboru názvů VServer > zobrazující podrobnosti o spojení](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurace pravidel exportu

Až budete mít VServer i Core souborového, měli byste přizpůsobit pravidla exportu a exportovat zásady, které řídí, jak můžou klienti přistupovat k souborům v základních exportech souborového.

Nejprve pomocí   >  stránky **export pravidel** VServer přidejte nová pravidla, upravte výchozí zásady nebo vytvořte vlastní zásadu exportu.

Za druhé použijte stránku **VServer**  >  Export Policy (**zásady exportu** ), pomocí které můžete přizpůsobené zásady použít pro export základních souborovéhoů při použití v tomto VServer.

Podrobnosti najdete v článku Průvodce konfigurací clusteru [řízení přístupu k základním exportům souborového](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) .

## <a name="next-steps"></a>Další kroky

Po přidání úložiště a konfiguraci oboru názvů s přístupem klienta dokončete počáteční nastavení clusteru: 

> [!div class="nextstepaction"]
> [Konfigurace síťových nastavení clusteru](fxt-configure-network.md)
