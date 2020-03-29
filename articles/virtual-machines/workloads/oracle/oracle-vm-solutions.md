---
title: Řešení Oracle na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o podporovaných konfiguracích a omezeních ibi obrazů virtuálních počítačů Oracle v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 3abc09f8c82442e3b24a9edf6ef4fb42f19dfde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806945"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Image virtuálních počítačových služeb Oracle a jejich nasazení v Microsoft Azure

Tento článek popisuje informace o řešeních Oracle založených na ibi virtuálních počítačích publikovaných společností Oracle na Azure Marketplace. Pokud máte zájem o řešení aplikací napříč cloudy s infrastrukturou Oracle Cloud Infrastructure, podívejte se na [řešení aplikací Oracle integrující Microsoft Azure a Oracle Cloud Infrastructure](oracle-oci-overview.md).

Chcete-li získat seznam aktuálně dostupných obrázků, spusťte následující příkaz:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od května 2019 jsou k dispozici následující obrázky:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Tyto image se považují za "Přineste si vlastní licenci" a jako takový vám budou účtovány jenom náklady na výpočetní prostředky, úložiště a sítě, které vám vzniknou spuštěním virtuálního počítače.  Předpokládá se, že máte řádnou licenci k používání softwaru Oracle a že máte aktuální smlouvu o podpoře se společností Oracle. Společnost Oracle má zaručenou mobilitu licencí z místního do Azure. Podrobnosti o mobilitě licencí najdete v publikované poznámce [oracle a microsoft.](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 

Jednotlivci se taky můžou rozhodnout založit svá řešení na vlastní image, kterou vytvoří od začátku v Azure, nebo nahrát vlastní image z místního prostředí.

## <a name="oracle-database-vm-images"></a>Image virtuálních aplikací databáze Oracle
Společnost Oracle podporuje spuštění edic Oracle Database 12.1 a vyšších edic Standard a Enterprise v Azure na ibi virtuálních strojů založených na Oracle Linuxu.  Chcete-li dosáhnout nejlepšího výkonu pro produkční úlohy databáze Oracle database v Azure, nezapomeňte správně zvětšit image virtuálního počítače a použít spravované disky Premium SSD nebo Ultra SSD. Pokyny, jak rychle zprovoznit databázi Oracle v Azure pomocí bitové kopie virtuálního počítače publikované společností Oracle, [získáte v návodu k řešení Oracle Database Quickstart](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Možnosti konfigurace připojeného disku

Připojené disky spoléhají na službu úložiště objektů blob Azure. Každý standardní disk je schopen teoretického maxima přibližně 500 vstupních a výstupních operací za sekundu (IOPS). Naše nabídka prémiových disků je upřednostňována pro vysoce výkonné databázové úlohy a může dosáhnout až 5000 vs. Můžete použít jeden disk, pokud to odpovídá vašim potřebám výkonu. Efektivní výkon viOPV však můžete zlepšit, pokud používáte více připojených disků, rozložíte na ně data databáze a pak použijete oracle automaticstorage management (ASM). Další informace o řešení Oracle ASM najdete v [přehledu automatického úložiště](https://www.oracle.com/technetwork/database/index-100339.html) Oracle. Příklad instalace a konfigurace řešení Oracle ASM na virtuálním počítači Azure v Linuxu najdete v kurzu [Instalace a konfigurace oracle automated storage management.](configure-oracle-asm.md)

### <a name="shared-storage-configuration-options"></a>Možnosti konfigurace sdíleného úložiště

Soubory Azure NetApp byly navrženy tak, aby splňovaly základní požadavky na spouštění vysoce výkonných úloh, jako jsou databáze v cloudu, a poskytuje;
- Nativní služba sdíleného úložiště systému Zabezpečení pro systém ovou systém Azure pro spouštění úloh Oracle prostřednictvím nativního klienta nfs virtuálního počítače nebo oracle dNFS
- Škálovatelné úrovně výkonu, které odrážejí skutečný rozsah požadavků vstupně-upů
- Nízká latence
- Vysoká dostupnost, vysoká odolnost a možnosti správy ve velkém měřítku, obvykle vyžadované kritickými podnikovými úlohami (jako jsou SAP a Oracle)
- Rychlé a efektivní zálohování a obnovení, k dosažení nejagresivnější RTO a RPO SLA

Tyto možnosti jsou možné, protože soubory Azure NetApp je založen na NetApp® ONTAP® all-flash systémy spuštěné v prostředí datového centra Azure – jako služba Azure Native. Výsledkem je ideální technologie úložiště databáze, kterou lze zřídit a spotřebovat stejně jako ostatní možnosti úložiště Azure. Další informace o tom, jak nasadit a získat přístup ke svazkům systému souborů Azure NetApp NFS, najdete v [dokumentaci](https://docs.microsoft.com/azure/azure-netapp-files/) k souborům Azure NetApp. V [průvodci osvědčenými postupy nasazení Oracle v Azure pomocí souborů Azure NetApp](https://www.netapp.com/us/media/tr-4780.pdf) najdete doporučení pro osvědčené postupy pro provoz databáze Oracle v souborech Azure NetApp.


## <a name="licensing-oracle-database--software-on-azure"></a>Licencování softwaru Oracle Database & v Azure
Microsoft Azure je autorizované cloudové prostředí pro provoz databáze Oracle Database. Tabulka Oracle Core Factor není použitelná při licencování databází Oracle v cloudu. Místo toho při použití virtuálních měn s technologií Hyper-Threading povoleno u databází Enterprise Edition, počítat dva virtuální procesory jako ekvivalentní k jedné licenci oracle procesoru, pokud je povoleno hyperthreading (jak je uvedeno v dokumentu zásad). Podrobnosti o zásadách naleznete [zde](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Databáze Oracle obecně vyžadují vyšší paměť a vi. Z tohoto důvodu jsou pro tyto [úlohy doporučeny virtuální chody optimalizované pro paměť.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) Chcete-li dále optimalizovat úlohy, jsou pro úlohy databáze Oracle Database, které vyžadují vysokou paměť, úložiště a šířku pásma vstupně-v., ale nikoli vysoký počet jádra, doporučují omezené [jádrové virtuální procesory.](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu)

Při migraci softwaru a úloh Oracle z místního do Microsoft Azure poskytuje společnost Oracle mobilitu licencí, jak je uvedeno v [nejčastějších dotazech na Oracle on Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real Application Cluster (Oracle RAC) je navržen tak, aby zmírnil selhání jednoho uzlu v místní konfiguraci clusteru s více uzly. Spoléhá se na dvě místní technologie, které nejsou nativní pro hyper-škálovat veřejné cloudové prostředí: síť vícesměrového vysílání a sdílený disk. Pokud vaše databázové řešení vyžaduje oracle rac v Azure, budete potřebovat software third=party k povolení těchto technologií. Další informace o řešení Oracle RAC naleznete na [stránce FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Vysoká dostupnost a aspekty zotavení po havárii
Při používání databází Oracle v Azure jste zodpovědní za implementaci řešení s vysokou dostupností a zotavení po havárii, abyste se vyhnuli prostojům. 

Vysoké dostupnosti a zotavení po havárii pro Oracle Database Enterprise Edition (bez spoléhání se na oracle RAC) lze dosáhnout v Azure pomocí [funkce Ochrana dat, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)nebo Oracle [GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)se dvěma databázemi na dvou samostatných virtuálních počítačích. Oba virtuální počítače by měly být ve stejné [virtuální síti,](https://azure.microsoft.com/documentation/services/virtual-network/) aby se zajistilo, že k sobě budou mít přístup přes privátní trvalou IP adresu.  Kromě toho doporučujeme umístit virtuální počítače ve stejné sadě dostupnosti, aby je Azure mohl umístit do samostatných domén selhání a upgradovat domény. Pokud chcete mít geografickou redundanci, nastavte dvě databáze pro replikaci mezi dvěma různými oblastmi a propojte dvě instance s bránou VPN.

Kurz [Implementace služby Oracle Data Guard v Azure](configure-oracle-dataguard.md) vás provede základním postupem nastavení v Azure.  

Díky službě Oracle Data Guard lze dosáhnout vysoké dostupnosti pomocí primární databáze v jednom virtuálním počítači, sekundární (pohotovostní) databáze v jiném virtuálním počítači a jednosměrné replikace mezi nimi nastavené. Výsledkem je přístup pro čtení ke kopii databáze. Pomocí technologie Oracle GoldenGate můžete konfigurovat obousměrnou replikaci mezi těmito dvěma databázemi. Informace o tom, jak pomocí těchto nástrojů nastavit řešení s vysokou dostupností pro databáze, naleznete v dokumentaci [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) na webu společnosti Oracle. Pokud potřebujete přístup pro čtení a zápis ke kopii databáze, můžete použít [službu Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Kurz [Implementace oracle goldengate v Azure](configure-oracle-golden-gate.md) vás provede základním postupem nastavení v Azure.

Kromě řešení HA a ZOTAVENÍ, které je navrženo v Azure, byste měli mít zavedenou strategii zálohování k obnovení databáze. Kurz [Zálohování a obnovení databáze Oracle vás](oracle-backup-recovery.md) provede základním postupem pro vytvoření konzistentní zálohy.


## <a name="support-for-jd-edwards"></a>Podpora pro JD Edwards
Podle poznámky podpory oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)jsou verze JD Edwards EnterpriseOne 9.2 a `Minimum Technical Requirements` vyšší podporovány v jakékoli **nabídce veřejného cloudu,** která splňuje jejich specifické (MTR).  Musíte vytvořit vlastní image, které splňují jejich specifikace MTR pro kompatibilitu operačních aplikací a softwarových aplikací. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Bitové kopie virtuálních strojů oracle WebLogic Server

* **Clustering je podporován pouze v edici Enterprise Edition.** K používání clusteringu WebLogic máte licenci pouze při použití enterprise edition serveru Oracle WebLogic Server. Nepoužívejte clustering s aplikací Oracle WebLogic Server Standard Edition.
* **Vícesměrové vysílání UDP není podporováno.** Azure podporuje jednosměrové vysílání UDP, ale ne vícesměrové nebo vysílání. Oracle WebLogic Server se může spolehnout na možnosti jednosměrového vysílání Azure UDP. Nejlepších výsledků, které lze dosáhnout jednosměrového vysílání UDP, doporučujeme, aby velikost clusteru WebLogic zůstala statická nebo aby neskládala více než 10 spravovaných serverů.
* **Oracle WebLogic Server očekává, že veřejné a soukromé porty budou stejné pro přístup T3 (například při použití enterprise javabeans).** Zvažte vícevrstvý scénář, kdy je aplikace vrstvy služeb (EJB) spuštěna v clusteru serveru Oracle WebLogic Server, který se skládá ze dvou nebo více virtuálních počítačů ve virtuální síti s názvem *SLWLS*. Klientská vrstva je v jiné podsíti ve stejné virtuální síti a používá jednoduchý program Java, který se pokouší volat EJB ve vrstvě služby. Vzhledem k tomu, že je nutné vyvážit zatížení vrstvy služby, je třeba vytvořit veřejný koncový bod s vyrovnáváním zatížení pro virtuální počítače v clusteru Serveru Oracle WebLogic Server. Pokud se zadaný soukromý port liší od veřejného portu (například 7006:7008), dojde k chybě, například k následující:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Je to proto, že pro jakýkoli vzdálený přístup T3 očekává řešení Oracle WebLogic Server stejný port nástroj pro vyrovnávání zatížení a port spravovaného serveru WebLogic. V předchozím případě klient přistupuje k portu 7006 (portu vyrovnávání zatížení) a spravovaný server naslouchá na 7008 (privátní port). Toto omezení platí pouze pro přístup T3, nikoli http.

   Chcete-li se tomuto problému vyhnout, použijte jedno z následujících řešení:

  * Pro koncové body s vyrovnáváním zatížení vyhrazené pro přístup t3 použijte stejná čísla soukromých a veřejných portů.
  * Při spuštění serveru Oracle WebLogic Server uveďte následující parametr JVM:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Související informace naleznete v článku **860340.1** kb. <https://support.oracle.com>

* **Omezení dynamického clusteringu a vyrovnávání zatížení.** Předpokládejme, že chcete použít dynamický cluster v serveru Oracle WebLogic Server a vystavit jej prostřednictvím jediného koncového bodu s vyrovnáváním zatížení v Azure. To lze provést tak dlouho, dokud použijete číslo pevného portu pro každý spravovaný server (není dynamicky přiřazen z rozsahu) a nespustíte více spravovaných serverů, než jsou počítače, které správce sleduje. To znamená, že neexistuje více než jeden spravovaný server na virtuální počítač). Pokud má vaše konfigurace za následek spuštění více serverů Oracle WebLogic než virtuálnípočítače (to znamená, kde více instancí serveru Oracle WebLogic Server sdílí stejný virtuální počítač), není možné pro více než jednu z těchto instancí Servery Oracle WebLogic, které se mají vázat na dané číslo portu. Ostatní na tomto virtuálním počítači se nezdaří.

   Pokud nakonfigurujete server pro správu tak, aby automaticky přiřazoval spravovaná čísla portů, není vyrovnávání zatížení možné, protože Azure nepodporuje mapování z jednoho veřejného portu na více privátníporty, jak by to bylo nutné. Konfigurace.
* **Více instancí serveru Oracle WebLogic Server ve virtuálním počítači.** V závislosti na požadavcích nasazení můžete zvážit spuštění více instancí serveru Oracle WebLogic Server na stejném virtuálním počítači, pokud je virtuální počítač dostatečně velký. Například na virtuálním počítači střední velikosti, který obsahuje dvě jádra, můžete spustit dvě instance serveru Oracle WebLogic Server. Přesto však doporučujeme, abyste se vyvarovali zavádění jednotlivých bodů selhání do vaší architektury, což by bylo v případě, že jste použili pouze jeden virtuální počítač, který používá více instancí serveru Oracle WebLogic Server. Použití alespoň dvou virtuálních počítačů může být lepší přístup a každý virtuální počítač by pak spustit více instancí Oracle WebLogic Server. Každá instance serveru Oracle WebLogic Server může být stále součástí stejného clusteru. V současné době však není možné použít Azure k vyrovnávání zatížení koncových bodů, které jsou vystaveny takové nasazení serveru Oracle WebLogic Server v rámci stejného virtuálního počítače, protože nástroj pro vyrovnávání zatížení Azure vyžaduje, aby servery s vyrovnáváním zatížení distribuovány mezi jedinečné virtuálních počítačů.

## <a name="oracle-jdk-virtual-machine-images"></a>Bitové kopie virtuálních strojů Oracle JDK
* **JDK 6 a 7 nejnovější aktualizace.** Zatímco doporučujeme používat nejnovější veřejnou, podporovanou verzi Javy (v současné době Java 8), Azure také zpřístupňuje image JDK 6 a 7. To je určeno pro starší aplikace, které ještě nejsou připraveny k upgradu na JDK 8. Zatímco aktualizace předchozích bitových kopií JDK již nemusí být dostupné široké veřejnosti, vzhledem k partnerství společnosti Microsoft se společností Oracle jsou bitové kopie JDK 6 a 7 poskytované službou Azure určeny k novější neveřejné aktualizaci, kterou společnost Oracle běžně nabízí pouze vybraná skupina podporovaných zákazníků společnosti Oracle. Nové verze obrázků JDK budou k dispozici v průběhu času s aktualizovanými verzemi JDK 6 a 7.

   JDK k dispozici v ibi JDK 6 a 7 a virtuální počítače a image odvozené z nich, lze použít pouze v rámci Azure.
* **64bitový JDK.** Bitové kopie virtuálních strojů Oracle WebLogic Server a bitové kopie virtuálních strojů Oracle JDK poskytované službou Azure obsahují 64bitové verze systému Windows Server i JDK.

## <a name="next-steps"></a>Další kroky
Nyní máte přehled o aktuálních řešeních Oracle založených na ibi virtuálních strojů v Microsoft Azure. Dalším krokem je nasazení první databáze Oracle v Azure.

> [!div class="nextstepaction"]
> [Vytvoření databáze Oracle v Azure](oracle-database-quick-create.md)
