---
title: Co je potřeba pro službu Azure NetApp Files (Volume Hard quoty)? Microsoft Docs
description: Popisuje změnu pro použití pevného disku Volume quot, jak naplánovat změnu a jak monitorovat a spravovat kapacity.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764274"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Co je potřeba pro vaši službu Azure NetApp Files, která se mění na Volume hard disk

Od začátku služby Azure NetApp Files používal mechanizmus zřizování a Automatický růst kapacity fondu. Azure NetApp Files svazky se dynamicky zřídí v nakládání fondu kapacit s podporou pro zákazníky u vybrané úrovně a velikosti. Velikosti svazků (kvóty) se používají k zajištění výkonu a kapacity a kvóty můžete kdykoli upravit. Toto chování znamená, že v současné době je kvóta svazku pomocí páčky výkonu pro řízení šířky pásma pro svazek. V současné době se podkladové fondy kapacit při vyplňování kapacity zvětšují automaticky.   

> [!IMPORTANT] 
> Chování při zřizování svazku a fondu kapacit Azure NetApp Files se změní na *manuální a ovládaný* mechanismus.  **Od 30. dubna 2021 (aktualizované) budou velikosti svazků (kvóta) spravovat výkon šířky pásma i zřízené kapacity a základní fondy kapacity se už nebudou automaticky rozšiřovat.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Důvody pro změnu pevné kvóty multilicence

Mnoho zákazníků oznámilo tři hlavní výzvy při *počátečním* chování:
* Klienti virtuálních počítačů by viděli kapacitu dynamicky zajištěné (100 TiB) libovolného svazku při použití nástrojů pro monitorování prostoru operačního systému nebo kapacity, což zajistí, aby nepřesné viditelnost kapacity klienta nebo aplikace byly dostupné.
* Vlastníci aplikace by nemuseli mít žádnou kontrolu nad zřizovacím prostorem kapacity fondu (a přidruženými náklady) z důvodu chování automatického růstu fondu kapacit. Tato situace je nenáročná v prostředích, kde se "procesy za běhu" mohli rychle vyplnit a rozšířit zřízené kapacity a náklady.
* Zákazníci chtějí zobrazit a udržovat přímou korelaci mezi velikostí svazku (kvótu) a výkonem. Díky aktuálnímu chování (implicitnímu), které přesáhne odběr svazku (v kapacitě) a automaticky rostoucího fondu, zákazníci nemají přímou korelaci, dokud se neaktivně nenastaví nebo neobnoví kvóta svazku. 

Mnoho zákazníků požádalo o přímou kontrolu nad zřízenou kapacitou. Chtějí řídit a vyrovnávat kapacitu a využití úložiště. Také chtějí řídit náklady na straně aplikace a na straně klienta k dispozici, využité a zřízené kapacity a výkonu jejich aplikačních svazků. 

## <a name="what-is-the-volume-hard-quota-change"></a>Jaká je změna objemu pevné kvóty   

Po změně pevného kvóty se Azure NetApp Files svazky už nebudou dynamicky zřizovat na (maximum) 100 TiB. Svazky se zřídí ve skutečné nakonfigurované velikosti (kvótě). I fondy kapacit nakládání se už nebudou automaticky rozšiřovat při dosažení plné kapacity. Tato změna odráží chování jako Azure Managed disks, které se taky zřídí tak, jak jsou, bez automatické zvýšení kapacity.

Představte si například Azure NetApp Files svazek nakonfigurovaný ve TiB velikosti (kvóta) na TiB Ultra úrovně služby. Aplikace průběžně zapisuje data do svazku.

*Počáteční* chování:  
* Očekávaná šířka pásma: 128 MiB/s
* Celková použitelná kapacita (a viditelná klient): 100 TiB   
    Po této velikosti nebude možné zapisovat více dat na svazku.
* Fond kapacit: automaticky roste s 1 TiB přírůstcích, když je plný.
* Změna kvóty svazku: pouze změny výkonu (šířka pásma) svazku. Nemění viditelné nebo použitelné kapacity klienta.

*Změněné* chování:  
* Očekávaná šířka pásma: 128 MiB/s
* Celková použitelná kapacita (a viditelná klient): 1 TiB nebudete moct na svazku po této velikosti zapisovat víc dat.
* Fond kapacit: zůstává 4 TiB velikosti a automaticky se nezvětšuje. 
* Změna kvóty svazku: změní výkon (šířku pásma) a viditelné nebo použitelné kapacity svazku.

Musíte aktivně monitorovat využití Azure NetApp Filesch svazků a fondů kapacit. Pro zajištění úplného využití svazku a fondu je potřeba, abyste provedete jeho účel. Azure NetApp Files bude nadále umožňovat [průběžné operace změny velikosti fondu kapacity a objemu](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Jak zprovoznění změnu pevné kvóty

V této části najdete pokyny k tomu, jak zprovoznění změnu v případě hladkého přechodu na Volume Hard. Poskytuje také přehledy pro zpracování aktuálně zřízených svazků a fondů kapacit, monitorování a možnosti správy výstrah a kapacity.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Aktuálně zřízené svazky a fondy kapacit

Kvůli změně objemu pevné kvóty byste měli změnit svůj operační model. Zřízené svazky a fondy kapacit budou vyžadovat průběžnou správu kapacity.  Vzhledem k tomu, že změněné chování proběhne okamžitě, Azure NetApp Files tým doporučuje řadu jednorázových opravných měr pro existující, dříve zřízené svazky a fondy kapacit, jak je popsáno v této části.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Doporučení jednorázových nebo preventivních měr  

Změna objemu pevné kvóty bude mít za následek změny zřízené a dostupné kapacity pro dříve zřízené svazky a fondy. V důsledku toho může dojít k nějakým problémům s přidělením kapacity. Aby nedocházelo k krátkodobým situacím při nedostatku místa pro zákazníky, Azure NetApp Files tým doporučuje následující, jednorázové nebo preventivní opatření: 

* **Zřízené velikosti svazků**:   
    Změňte velikost každého zřízeného svazku tak, aby měl odpovídající vyrovnávací paměť na základě míry změny a výstrahy nebo změny velikosti času vyřízení (například 20% na základě typických požadavků na úlohy) s maximální 100 TiB (což je [limit velikosti svazku](azure-netapp-files-resource-limits.md#resource-limits)). Tato nová velikost svazku, včetně kapacity vyrovnávací paměti, by měla být založená na následujících faktorech:
    * **Zřízená** kapacita svazku pro případ, že použitá kapacita je menší než zajištěná kvóta svazku.
    * **Využitá** kapacita svazku pro případ, že použitá kapacita je větší než zřízená kvóta svazku.  
    Zvýšení kapacity na úrovni svazku se nezvyšuje, pokud není potřeba vypěstovat fond kapacit pro pokládání. V důsledku této změny můžete sledovat *zvýšení* limitu šířky pásma pro daný svazek (v případě, že se používá [typ fondu kapacity auto QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) ).

* **Zřízené velikosti fondů kapacity**:   
    Pokud se po úpravách velikosti svazků změní součet velikostí svazků, než je velikost fondu kapacity hostitele, bude nutné zvýšit kapacitu fondu kapacity na velikost, která je větší nebo rovna součtu svazků, maximálně 500 TiB (což je [limit velikosti fondu kapacity](azure-netapp-files-resource-limits.md#resource-limits)). Kapacita fondu další kapacity bude platit jako ACR poplatek za normální.

Pokud potřebujete pomáhat s nastavením monitorování nebo upozorňování, jak je popsáno v následujících částech, měli byste spolupracovat s Azure NetApp Files specialisty na vaše prostředí.

### <a name="ongoing-capacity-management"></a>Průběžná správa kapacity  

Po provedení jednorázových opravných měr byste měli společně s probíhajícími procesy sledovat a spravovat kapacitu. V následujících částech jsou uvedeny návrhy a alternativy k monitorování a správě kapacity.

### <a name="monitor-capacity-utilization"></a>Monitorování využití kapacity

Využití kapacity můžete monitorovat na různých úrovních. 

#### <a name="vm-level-monitoring"></a>Monitorování na úrovni virtuálních počítačů 

Nejvyšší úroveň monitorování (nejblíže k aplikaci) se nachází v rámci virtuálního počítače aplikace. V rámci vytváření sestav kapacity z klientského operačního systému virtuálního počítače se dodržujte změna chování.

V následujících dvou scénářích zvažte Azure NetApp Files svazek nakonfigurovaný ve velikosti 1-TiB (kvóta) ve fondu kapacity 4 TiB, Ultra úrovně služby. 

##### <a name="windows"></a>Windows

Klienti systému Windows mohou kontrolovat využitou a dostupnou kapacitu svazku pomocí vlastností mapované jednotky sítě. Můžete použít   ->    ->  možnost **vlastnosti** jednotky v Průzkumníkovi.  

Následující příklady znázorňují možnosti vytváření sestav kapacity svazku ve Windows *před* změněným chováním:

![Snímky obrazovky, které ukazují ukázkovou kapacitu úložiště svazku před změnou chování.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Můžete použít také `dir` příkaz na příkazovém řádku, jak je znázorněno níže:

![Snímek obrazovky, který ukazuje použití příkazu k zobrazení kapacity úložiště pro svazek před změnou chování.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Následující příklady znázorňují možnosti generování sestav kapacity svazku ve Windows *po* změně chování:

![Snímky obrazovky, které ukazují ukázkovou kapacitu úložiště svazku po změně chování.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

Následující příklad ukazuje `dir` výstup příkazu:  

![Snímek obrazovky, který ukazuje použití příkazu k zobrazení kapacity úložiště pro svazek po změně chování.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Klienti se systémem Linux mohou kontrolovat využitou a dostupnou kapacitu svazku pomocí [ `df` příkazu](https://linux.die.net/man/1/df). Tato `-h` možnost zobrazí velikost, využité místo a dostupné místo ve formátu čitelném pro čtení, přičemž používá velikosti jednotek M, G a T.

Následující příklad ukazuje vytváření sestav kapacity svazku v systému Linux *před* změněným chováním:  

![Snímek obrazovky, který ukazuje použití systému Linux k zobrazení kapacity úložiště pro svazek před změnou chování.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

Následující příklad ukazuje generování sestav kapacity svazku v systému Linux *po* změně chování:  

![Snímek obrazovky zobrazující, že se systémem Linux po změně chování zobrazuje kapacitu úložiště pro svazek.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Konfigurace výstrah pomocí ANFCapacityManager

K monitorování kapacity Azure NetApp Files a získání přizpůsobeného upozorňování můžete použít nástroj Logic Apps ANFCapacityManager podporovaný komunitou. Nástroj ANFCapacityManager je k dispozici na [stránce GitHubu ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager).

ANFCapacityManager je aplikace logiky Azure, která spravuje pravidla pro výstrahy založená na kapacitě. Automaticky zvyšuje velikosti svazků, aby nedocházelo k nedostatku Azure NetApp Files svazků. Nasazení je snadné a poskytuje následující možnosti Alert Management:

* Když se vytvoří fond kapacit Azure NetApp Files nebo svazek, ANFCapacityManager vytvoří pravidlo upozornění metriky na základě zadané procentuální prahové hodnoty.
* Když se změní velikost fondu kapacity Azure NetApp Files nebo svazku, ANFCapacityManager upraví pravidlo výstrahy metriky na základě zadané prahové hodnoty spotřebovaného procenta kapacity. Pokud pravidlo výstrahy neexistuje, vytvoří se.
* Když se odstraní fond kapacit Azure NetApp Files nebo svazek, odstraní se odpovídající pravidlo upozornění na metriky.

Můžete nakonfigurovat následující nastavení upozornění na klíč:  

* **Plný práh fondu kapacit%** – toto nastavení určuje spotřebované prahové hodnoty, které aktivuje výstrahu pro fondy kapacit. Hodnota 90 by způsobila, že se aktivuje výstraha v případě, že fond kapacit dosáhne 90% spotřebovaného.
* **Plný práh svazku%** – toto nastavení určuje spotřebované prahové hodnoty, které aktivuje výstrahu pro svazky. Hodnota 80 by způsobila, že se aktivuje výstraha, když svazek dosáhne 80% spotřebovaného.
* **Existující skupina akcí pro oznámení o kapacitě** – toto nastavení je skupina akcí, která se aktivuje pro upozorňování na základě kapacity. Toto nastavení by mělo být předem vytvořeno uživatelem. Skupina akcí může odesílat e-maily, zprávy SMS nebo jiné formáty.

Konfigurace výstrah je znázorněna na následujícím obrázku:  

![Obrázek zobrazující konfiguraci výstrah pomocí ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Po instalaci ANFCapacityManager můžete očekávat následující chování: když se Azure NetApp Files fond kapacit nebo svazek vytvoří, upraví nebo odstraní, aplikace logiky automaticky vytvoří, upraví nebo odstraní pravidlo upozornění na základě kapacity s názvem `ANF_Pool_poolname` nebo `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Správa kapacity

Kromě monitorování a upozorňování byste měli také začlenit postup správy kapacity aplikací pro správu Azure NetApp Files (zvýšené) spotřeby kapacity. Když se Azure NetApp Files svazek nebo fond kapacit vyplní, [můžete průběžně poskytovat další kapacitu bez přerušení aplikace](azure-netapp-files-resize-capacity-pools-or-volumes.md). Tato část popisuje různé ruční a automatizované způsoby, jak podle potřeby zvýšit objem a rozsah kapacity fondu kapacit.
 
#### <a name="manual"></a>Ruční 

Pomocí portálu nebo rozhraní příkazového řádku můžete ručně zvětšit svazky nebo velikosti fondů kapacity. 

##### <a name="portal"></a>Portál 

[Velikost svazku](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) můžete podle potřeby změnit. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.

1. V okně Spravovat účet NetApp klikněte na **svazky**.  
2. Klikněte pravým tlačítkem na název svazku, u kterého chcete změnit velikost, nebo klikněte na `…` ikonu na konci řádku svazku, aby se zobrazila kontextová nabídka. 
3. Pro změnu velikosti nebo odstranění svazku použijte možnosti místní nabídky.   

   ![Snímek obrazovky, který zobrazuje možnosti kontextové nabídky pro svazek.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Snímek obrazovky, který zobrazuje okno kvóta aktualizace svazku.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

V některých případech fond kapacit hostování nemá dostatečnou kapacitu pro změnu velikosti svazků. [Velikost fondu kapacity](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) ale můžete změnit v TIB přírůstcích nebo snížení. Velikost fondu kapacity nemůže být menší než 4 TiB. *Změna velikosti fondu kapacity změní zakoupenou kapacitu Azure NetApp Files.*

1. V okně Spravovat účet NetApp klikněte na fond kapacit, u kterého chcete změnit velikost.
2. Kliknutím pravým tlačítkem myši na název fondu kapacity nebo kliknutím na `…` ikonu na konci řádku fondu kapacity zobrazte kontextovou nabídku.
3. Pro změnu velikosti nebo odstranění fondu kapacity použijte možnosti místní nabídky.    

   ![Snímek obrazovky zobrazující možnosti kontextové nabídky pro fond kapacit](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Snímek obrazovky, který zobrazuje okno změnit velikost fondu.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI nebo PowerShell

Pokud chcete ručně změnit velikost svazku nebo fondu kapacity, můžete použít [Azure NetApp Files nástroje CLI](azure-netapp-files-sdk-cli.md#cli-tools), včetně Azure CLI a Azure PowerShell.  Ke správě Azure NetApp Files prostředků svazků a fondů můžete použít následující dva příkazy:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Pokud chcete spravovat Azure NetApp Files prostředky pomocí rozhraní příkazového řádku Azure, můžete otevřít Azure Portal a v horní části řádku nabídek vyberte odkaz Azure **Cloud Shell** : 

[![Snímek obrazovky, který ukazuje, jak získat přístup k Cloud Shell propojení. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Tato akce otevře Azure Cloud Shell:

[![Snímek obrazovky, který zobrazuje okno Cloud Shell. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Následující příklady používají příkazy k [zobrazení](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) a [aktualizaci](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) velikosti svazku:
 
[![Snímek obrazovky, který ukazuje použití PowerShellu k zobrazení velikosti svazku. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Snímek obrazovky, který ukazuje použití PowerShellu k aktualizaci velikosti svazku. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Následující příklady používají příkazy k [zobrazení](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) a [aktualizaci](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) velikosti fondu kapacity:

[![Snímek obrazovky, který ukazuje použití PowerShellu k zobrazení velikosti ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) fondu kapacity](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Snímek obrazovky, který ukazuje, jak aktualizovat velikost ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) fondu kapacity pomocí PowerShellu](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizovaná  

Můžete vytvořit automatizovaný proces pro správu změněného chování.

##### <a name="rest-api"></a>Rozhraní REST API   

REST API služby Azure NetApp Files definuje operace HTTP s prostředky, jako je účet NetApp, fond kapacit, svazky a snímky. Specifikace REST API pro Azure NetApp Files se zveřejňuje na [stránce Azure NetApp Files správce prostředků GitHubu](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. [Příklad kódu pro použití s rozhraními REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) najdete v GitHubu.

Další informace najdete v tématu [vývoj pro Azure NetApp Files s REST API](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>REST API s využitím PowerShellu  

REST API služby Azure NetApp Files definuje operace HTTP s prostředky, jako je účet NetApp, fond kapacit, svazky a snímky. [Specifikace REST API pro Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) je publikovaná prostřednictvím GitHubu.

Přečtěte si téma [vývoj pro Azure NetApp Files s REST API pomocí PowerShellu](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Správa kapacity pomocí ANFCapacityManager

ANFCapacityManager je aplikace logiky Azure, která spravuje pravidla pro výstrahy založená na kapacitě. Automaticky zvyšuje velikosti svazků, aby nedocházelo k nedostatku Azure NetApp Files svazků. Kromě odesílání výstrah může povolit automatické zvýšení velikosti svazků a fondů kapacit, aby nedocházelo k nedostatku Azure NetApp Files svazků. 

* Pokud je v případě, že svazek Azure NetApp Files dosáhne zadané prahové hodnoty spotřebované v procentech, bude kvóta svazku (velikost) zvýšena o procento zadané mezi 10-100.  
* Pokud zvýšení velikosti svazku překročí kapacitu nadřazeného fondu kapacity, zvýší se velikost fondu kapacity tak, aby odpovídala nové velikosti svazku.

Můžete nakonfigurovat následující nastavení správy kapacity klíče:  

* **Autogrow procento zvýšení** – procento stávající velikosti svazku pro automatické zvětšení svazku, pokud dosáhne zadané **mezní hodnoty% plné prahové hodnoty**. Hodnota 0 (nula) zakáže funkci AutoGrow. Doporučuje se hodnota mezi 10 a 100.

    ![Snímek obrazovky zobrazující okno nastavit procento automatického nárůstu hlasitosti](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Časté otázky 

Tato část obsahuje odpovědi na některé otázky týkající se změny pevné kvóty pevného disku. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Počítá se místo snímku k použitelné nebo zřízené kapacitě svazku?

Ano, spotřebované kapacity snímků se počítají do zřízeného místa ve svazku. V případě, že svazek běží plný, zvažte dvě možnosti nápravy:

* Změňte velikost svazku, jak je popsáno v tomto článku.
* Odeberte starší snímky, abyste uvolnili místo na hostujícím svazku.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Tato změna znamená, že se chování při automatickém zvětšení svazku z Azure NetApp Files ztratí?

Běžný nepojmový koncept je, že Azure NetApp Files *svazky* se při vyplňování automaticky zvětšují. Svazky se dynamicky zřídily o velikost 100 TiB, a to bez ohledu na skutečnou kvótu sady, zatímco *fond kapacit* pro pokládání se automaticky zvětšuje o 1 – TIB zvýšení. Tato změna bude adresovat (zobrazitelná a použitelná) velikost *svazku* do nastavené kvóty a *fondy kapacity* se už nebudou automaticky rozšiřovat. Tato změna vede k tomu, že se běžně požaduje přesný prostor na straně klienta a vytváření sestav kapacity. Nevylučuje spotřebu kapacity "navýšení kapacity".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Má tato změna vliv na svazky replikované pomocí replikace mezi oblastmi (Preview)? 

Kvóta pevného objemu není vynucená na cílových svazcích replikace.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Má tato změna vliv na metriky, které jsou aktuálně k dispozici v Azure Monitor?

Metrika portálu a statistika Azure Monitor budou přesně odpovídat novému modelu přidělování a využití.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Má tato změna vliv na omezení prostředků pro Azure NetApp Files?

U Azure NetApp Files mimo změny kvóty popsané v tomto článku se nezměnila žádná změna v omezeních prostředků.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Existuje ukázkový pracovní postup ANFCapacityManager?  

Ano. Podívejte se na [stránku autogrow pracovní postup Volume pro GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Je ANFCapacityManager Microsoftu podporovaný?  

[Aplikace logiky ANFCapacityManager je poskytována tak, jak je, a není podporována společností NetApp ani Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Doporučujeme upravit, aby vyhovovala vašemu konkrétnímu prostředí nebo požadavkům. Funkčnost byste měli před nasazením do libovolného podnikového nebo produkčního prostředí otestovat.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Jak můžu ohlásit chybu nebo odeslat žádost o funkci pro ANFCapacityManger?
Chyby a žádosti o funkce můžete odeslat kliknutím na **Nový problém** na [stránce GitHubu ANFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Další kroky
* [Změna velikosti fondu kapacity nebo svazku](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metriky pro Azure NetApp Files](azure-netapp-files-metrics.md)
