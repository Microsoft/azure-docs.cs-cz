---
title: Jak fungují Azure NetApp Files snímky | Microsoft Docs
description: Vysvětluje, jak Azure NetApp Files snímky fungují, včetně způsobů vytváření snímků, způsobů obnovení snímků, způsobu použití snímků v nastavení replikace mezi oblastmi.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625243"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Jak fungují snímky Azure NetApp Files

Tento článek vysvětluje, jak Azure NetApp Files snímky fungují. Technologie Azure NetApp Files snímků zajišťuje stabilitu, škálovatelnost a rychlejší obnovu bez dopadu na výkon. Technologie Azure NetApp Files snímků poskytuje základ pro řešení ochrany dat, včetně obnovení jednoho souboru, obnovení a klonování svazků a replikace mezi jednotlivými oblastmi. 

Postup, jak používat snímky svazků, najdete v tématu [Správa snímků pomocí Azure NetApp Files](azure-netapp-files-manage-snapshots.md). Informace o správě snímků při replikaci mezi jednotlivými oblastmi najdete v tématu [požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Jaké snímky svazků jsou  

Snímek Azure NetApp Files je bitová kopie systému souborů (Volume) v čase. Může sloužit jako ideální online zálohování. Snímek můžete použít k [Vytvoření nového svazku](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [obnovení souboru](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)nebo [Vrácení svazku](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). V konkrétních datech aplikace uložených na Azure NetApp Files svazcích můžou být potřeba další kroky k zajištění konzistence aplikace.  

Díky jedinečným funkcím technologie virtualizace multilicencí, která je součástí Azure NetApp Files, jsou možnosti nízké režie. Podobně jako u databáze Tato vrstva používá ukazatele na skutečné datové bloky na disku. Ale na rozdíl od databáze nepřepisuje existující bloky; zapisuje aktualizované údaje do nového bloku a změní ukazatel. Azure NetApp Files snímek jednoduše manipuluje s ukazateli na blokování a vytvoří "zmrazené", jen pro čtení svazku, který umožňuje aplikacím přístup ke starším verzím souborů a hierarchiím adresářů bez speciálního programování. Skutečné bloky dat se nekopírují. V takovém případě jsou snímky v době potřebné k jejich vytvoření efektivní. Nejedná se o okamžitou, bez ohledu na velikost svazku. Snímky jsou v prostoru úložiště také efektivní. Nespotřebovávají žádné místo a zachovají se jenom bloky rozdílů mezi snímky a aktivním svazkem. 

Následující diagramy znázorňují koncepty:  

![Diagramy, které znázorňují klíčové koncepty snímků](../media/azure-netapp-files/snapshot-concepts.png)

V diagramech se snímek bere na obrázku 1a. Na obrázku 1b se změněná data zapisují do *nového bloku* a ukazatel se aktualizuje. Ale ukazatel snímku stále ukazuje na *dříve zapsaný blok*, který vám poskytne živý a historický pohled na data. Další snímek je povedený na obrázku 1C. Nyní máte přístup k třem generacím dat (živá data, snímek 2 a snímek 1 v řádu stáří), aniž byste museli zabírat místo na svazku, které by vyžadovalo tři úplné kopie. 

Snímek má pouze kopii metadat svazků (*tabulka inode*). Vytvoření může trvat jen několik sekund, bez ohledu na velikost svazku, využitou kapacitu nebo úroveň aktivity na svazku. Takže pořizování snímku svazku 100 TiB má stejný čas (vedle nuly) jako pořízení snímku svazku 100-GiB. Po vytvoření snímku se změny datových souborů projeví v aktivní verzi souborů jako normální.

V obou případech jsou bloky dat, na které se odkazuje ze snímku, pořád stabilní a neměnné. Kvůli "přesměrování při zápisu" z Azure NetApp Filesch svazků nevzniká žádné nároky na výkon a sama o sobě nespotřebovává žádné místo. V průběhu času můžete ukládat až 255 snímků na celý svazek, které jsou přístupné jako verze dat jen pro čtení a online, a to s využitím minimální kapacity jako počtu změněných bloků mezi jednotlivými snímky. Změněné bloky jsou uloženy v aktivním svazku. Bloky, na které se odkazuje v snímcích, se uchovávají (jako jen pro čtení) ve svazku pro bezpečné použití, pokud se vymažou všechny ukazatele (v aktivních svazcích a snímcích). Z toho vyplývá, že využití svazku se v průběhu času zvětšuje buď novými datovými bloky, nebo (upravenými) bloky dat uloženými na snímcích.

 Následující diagram znázorňuje snímky svazku a využité místo v čase: 

![Diagram znázorňující snímky svazku a využité místo v čase](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Vzhledem k tomu, že snímek svazku zaznamenává pouze změny bloku od posledního snímku, poskytuje následující klíčové výhody:

* Snímky jsou ***efektivní pro úložiště***.   
    Snímky spotřebovávají minimální prostor úložiště, protože nekopírují bloky dat celého svazku. Dva snímky provedené v sekvenci se liší pouze bloky přidanými nebo změněnými v časovém intervalu mezi oběma. Tento blok – přírůstkové chování omezuje využití přiřazené kapacity úložiště. Mnoho alternativních implementací snímků spotřebovává úložné svazky, které se rovnají aktivnímu systému souborů, a vyvolává požadavky na kapacitu úložiště. V závislosti na denních sazbách pro změny na *úrovni* aplikace budou Azure NetApp Files snímky spotřebovávat více nebo méně kapacity, ale jenom u změněných dat. Průměrný denní objem spotřeby snímků vychází jenom z 1-5% využité kapacity svazku pro mnoho svazků aplikace nebo až 20-30% pro svazky, jako jsou SAP HANA databázové svazky. Nezapomeňte [monitorovat využití svazku a snímků](azure-netapp-files-metrics.md#volumes) pro spotřebu kapacity snímku vzhledem k počtu vytvořených a udržovaných snímků.   

* Snímky jsou ***rychlé vytváření, replikace, obnovení nebo klonování***.   
    Vytvoření, replikaci, obnovení nebo klonování snímku trvá během několika sekund, a to bez ohledu na velikost svazku a úroveň aktivit. Snímek svazku můžete vytvořit [na vyžádání](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). Pomocí [zásad snímku](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) můžete také určit, kdy má Azure NetApp Files automaticky vytvořit snímek a kolik snímků má být pro svazek zachováno.  Konzistenci aplikací lze dosáhnout orchestrací snímků pomocí aplikační vrstvy, například pomocí [nástroje AzAcSnap](azacsnap-introduction.md) pro SAP HANA.

* Snímky nemají žádný vliv na ***výkon*** svazku.   
    Z důvodu "přesměrování na zápis" na technologii pro nakládání, ukládání nebo uchovávání Azure NetApp Files snímků nemá žádný vliv na výkon, ani u těžké aktivity dat. Odstranění snímku má ve většině případů také malý vliv na výkon. 

* Snímky poskytují ***škálovatelnost*** , protože je možné je vytvářet často a mnoho je možné zachovat.   
    Azure NetApp Files svazky podporují až 255 snímků. Schopnost ukládat velký počet nepříznivých, často vytvořených snímků zvyšuje pravděpodobnost, že se požadovaná verze dat může úspěšně obnovit.

* Snímky poskytují ***viditelnost uživatelů** _ a _ *_obnovování souborů_* *.   
Vysoce výkonná, škálovatelnost a stabilita technologie Azure NetApp Filesho snímku znamená, že poskytuje ideální online zálohování pro uživatele řízené obnovením. Pro účely obnovení souborů, adresářů a svazků je možné snímky zpřístupnit uživateli. Další řešení vám umožní zkopírovat zálohy do offline úložiště nebo [replikovat různé oblasti](cross-region-replication-introduction.md) pro účely uchovávání informací nebo zotavení po havárii.

## <a name="ways-to-create-snapshots"></a>Způsoby vytváření snímků   

K vytváření a údržbě snímků můžete použít několik metod:

* Ručně (na vyžádání) pomocí:   
    * Nástroje [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot)nebo [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Skripty (viz [Příklady](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatizovaná pomocí:
    * Zásady snímků prostřednictvím nástrojů [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy)nebo [PowerShellu](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)
    * Nástroje pro snímky konzistentní vzhledem k aplikacím, jako je [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Jak jsou svazky a snímky replikovány mezi oblastmi pro zotavení po havárii  

Azure NetApp Files podporuje [replikaci mezi oblastmi](cross-region-replication-introduction.md) pro účely zotavení po havárii (Dr). Replikace mezi oblastmi Azure NetApp Files používá technologii SnapMirror. V komprimovaném, efektivním formátu se přes síť odesílají jenom změněné bloky. Po zahájení replikace mezi různými oblastmi mezi svazky se celý obsah svazku (tj. vlastní bloky uložených dat) přenese jenom jednou. Tato operace se nazývá *přenos podle směrného plánu*. Po počátečním přenosu se přenesou jenom změněné bloky (jako zachycené ve snímcích). Výsledkem je asynchronní 1:1 replika zdrojového svazku, včetně všech snímků. Toto chování se řídí úplným a přírůstkovým replikačním mechanismem. Tato technologie minimalizuje množství dat potřebných pro replikaci v různých oblastech, takže šetří náklady na přenos dat. Také zkrátí dobu replikace. Můžete dosáhnout menšího cíle bodu obnovení (RPO), protože je možné vytvořit více snímků a přenášet je častěji pomocí omezených přenosů dat. Dále si vyloučí potřebu hostitelských mechanismů replikace, aby se předešlo nákladům na virtuální počítače a licence softwaru.

Následující diagram znázorňuje provoz snímků ve scénářích replikace mezi oblastmi: 

![Diagram, který zobrazuje provoz snímků ve scénářích replikace mezi oblastmi](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Způsoby obnovení dat ze snímků  

Technologie Azure NetApp Files snímků významně vylepšuje četnost a spolehlivost záloh. Dojde k minimální režii výkonu a je možné ji bezpečně vytvořit na aktivním svazku. Azure NetApp Files snímky umožňují téměř okamžité, zabezpečené a volitelné obnovení spravované uživateli. Tato část popisuje různé způsoby, kterými je možné k datům přistupovat nebo obnovit z Azure NetApp Files snímků.

### <a name="restoring-files-or-directories-from-snapshots"></a>Obnovování souborů nebo adresářů ze snímků 

Pokud není [viditelnost cesty snímků](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) nastavená na `hidden` , můžou uživatelé získat přímý přístup k snímkům při obnovení z náhodného odstranění, poškození nebo změny jejich dat. Zabezpečení souborů a adresářů se uchovává ve snímku a snímky jsou jen pro čtení. V takovém případě je obnovení zabezpečené a jednoduché. 

Následující diagram zobrazuje soubor nebo adresář s přístupem ke snímku: 

![Diagram, který zobrazuje přístup k souboru nebo adresáři ke snímku](../media/azure-netapp-files/snapshot-file-directory-access.png)

Snímek 1 v diagramu používá pouze bloky rozdílů mezi aktivním svazkem a okamžikem vytvoření snímku. Když ale přistupujete ke snímku přes cestu ke snímku svazku, data se *zobrazí* , jako by to byla kapacita celého svazku v době vytváření snímku. Přístupem ke složkám snímků mohou uživatelé automaticky obnovit data kopírováním souborů a adresářů ze snímku výběru.

Podobně jsou snímky v cílových svazcích replikace mezi oblastmi dostupné jen pro čtení a obnovení dat v oblasti zotavení po havárii.  

Následující diagram znázorňuje přístup k snímku ve scénářích replikace mezi oblastmi: 

![Diagram, který zobrazuje přístup k snímku v případě replikace mezi oblastmi](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Viz [obnovení souboru ze snímku pomocí klienta](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) o obnovení jednotlivých souborů nebo adresářů ze snímků.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Obnovení (klonování) snímku na nový svazek

Snímky Azure NetApp Files můžete obnovit do samostatného nezávislého svazku. Tato operace je skoro okamžitá, bez ohledu na velikost svazku a spotřebovaný objem. Nově vytvořený svazek je skoro hned k dispozici pro přístup, zatímco se kopírují skutečné bloky dat svazku a snímku. V závislosti na velikosti svazku a kapacitě může tento proces trvat značnou dobu, než se nadřazený svazek a snímek nedá odstranit. Po počátečním vytvoření však může být svazek již k dispozici, zatímco proces kopírování probíhá na pozadí. Tato schopnost umožňuje rychlé vytváření svazků pro obnovení dat nebo klonování svazků pro testování a vývoj. Podle povahy procesu kopírování dat se spotřeba fondu kapacity úložiště po dokončení obnovení zdvojnásobí a na novém svazku se zobrazí plná aktivní kapacita původního snímku. Po dokončení tohoto procesu se svazek nezávisle a zruší jeho přidružení k původnímu svazku a zdrojové svazky a snímky můžete spravovat nebo odebírat nezávisle na novém svazku.

Následující diagram znázorňuje nový svazek vytvořený obnovením (klonování) snímku:   

![Diagram, který zobrazuje nový svazek vytvořený obnovením snímku](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Stejné operace se dají provádět u replikovaných snímků na svazek zotavení po havárii (DR). Libovolný snímek se dá obnovit na nový svazek, i když replikace mezi oblastmi zůstane aktivní nebo probíhající. Tato možnost umožňuje Nerušit vytváření testovacích a vývojových prostředí v oblasti zotavení po havárii, takže se data použijí, zatímco replikované svazky by se jinak používaly jenom pro účely zotavení po havárii. Tento případ použití umožňuje, aby byl test a vývoj izolovaný od výroby, což eliminuje potenciální dopad na produkční prostředí.  

Následující diagram znázorňuje obnovení svazku (klonování) pomocí snímku cílového svazku DR během replikace mezi oblastmi:  

![Diagram znázorňující obnovení svazku pomocí snímku cílového svazku DR](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Viz část [obnovení snímku na nový svazek](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) o operacích obnovení svazku.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Obnovení (vrácení) snímku na místě

V některých případech, protože nový svazek bude využívat kapacitu úložiště, nemusí být vytvoření nového svazku ze snímku nutné nebo vhodné. Aby bylo možné rychle obnovit z důvodu poškození dat (například poškození databáze nebo útoky ransomwarem), může být vhodnější obnovit snímek v rámci samotného svazku. Tuto operaci lze provést pomocí funkce pro obnovení snímku Azure NetApp Files. Tato funkce umožňuje rychle vrátit svazek do stavu, ve kterém byl proveden konkrétní snímek. Ve většině případů je vrácení svazku mnohem rychlejší než obnovení jednotlivých souborů ze snímku do aktivního systému souborů, zejména ve velkých TiB svazcích. 

Vrácení snímku svazku je blízko sebe a dokončení trvá jenom několik sekund, a to i pro největší svazky. Metadata aktivního svazku (*tabulka inode*) se nahradí metadaty snímku z doby vytváření snímku, takže se svazek do daného konkrétního bodu v čase vrátí. Aby se změny projevily, není nutné kopírovat žádné datové bloky. V takovém případě je více místa efektivní než obnovení snímku na nový svazek. 

Následující diagram znázorňuje svazek, který se vrací na předchozí snímek:  

![Diagram, který ukazuje svazek, který se vrací na předchozí snímek](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Data aktivního systému souborů, která byla zapsána a snímky, které byly pořízeny po pořízení vybraného snímku, budou ztraceny. Operace vrátit se změnami snímku nahradí všechna data v cílovém svazku daty ve vybraném snímku. Při výběru snímku byste měli věnovat pozornost obsahu snímku a datu vytvoření. Operaci vrácení snímku nelze vrátit zpět.  

Informace o tom, jak používat tuto funkci, najdete v tématu věnovaném [Vrácení svazku zpět pomocí snímku](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) .

## <a name="how-snapshots-are-deleted"></a>Jak se odstraňují snímky 

Snímky využívají kapacitu úložiště. V takovém případě nejsou obvykle uchovávány po neomezenou dobu. V případě ochrany dat, uchovávání a obnovení je řada snímků (vytvořených v různých časových okamžicích) obvykle udržována online po určitou dobu v závislosti na požadavcích smlouvy SLA pro RPO, RTO a uchování. Starší snímky se ale často nemusí uchovávat ve službě úložiště a možná je budete muset odstranit, abyste uvolnili místo. Libovolný snímek se dá kdykoli odstranit (ne nutně v pořadí vytváření) správcem. 

> [!IMPORTANT]
> Operaci odstranění snímku nelze vrátit zpět. Měli byste uchovávat offline kopie svazku pro účely ochrany a uchovávání dat. 

Když se odstraní snímek, odeberou se všechny ukazatele z tohoto snímku na stávající datové bloky. Pokud blok dat nemá žádné další ukazatele ukazující na něj (aktivní svazek nebo jiné snímky ve svazku), vrátí se datový blok na svazek volného místa pro budoucí použití. Proto odebrání snímků obvykle uvolňuje větší kapacitu ve svazku než odstranění dat z aktivního svazku, protože bloky dat jsou často zachyceny v dříve vytvořených snímcích. 

Následující diagram znázorňuje účinek odstranění snímku 3 ze svazku na využití úložiště:  

![Diagram, který zobrazuje efekt spotřeby úložiště při odstraňování snímku](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Nezapomeňte [monitorovat spotřebu svazků a snímků](azure-netapp-files-metrics.md#volumes) a porozumět tomu, jak aplikace, aktivní svazek a spotřeba snímků spolupracují. 

Informace o tom, jak spravovat odstranění snímků, najdete v tématu [odstranění snímků](azure-netapp-files-manage-snapshots.md#delete-snapshots) . Postup automatizace tohoto procesu najdete v tématu [Správa zásad snímků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) .

## <a name="next-steps"></a>Další kroky

* [Správa snímků s využitím služby Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Monitorovat metriky svazků a snímků](azure-netapp-files-metrics.md#volumes)
* [Řešení potíží se zásadami snímků](troubleshoot-snapshot-policies.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video o Azure NetApp Files snímků 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Přehled Azure NetApp Files snímku](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



