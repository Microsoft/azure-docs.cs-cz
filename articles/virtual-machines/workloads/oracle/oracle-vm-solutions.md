---
title: Řešení Oracle na virtuálních počítačích Azure | Microsoft Docs
description: Přečtěte si o podporovaných konfiguracích a omezeních imagí virtuálních počítačů Oracle na Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 44fb955c4539a3330386c38a7798c9f3c3fed2d9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846056"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Image virtuálních počítačů Oracle a jejich nasazení v Microsoft Azure

Tento článek obsahuje informace o řešeních Oracle na základě imagí virtuálních počítačů publikovaných Oracle v Azure Marketplace. Pokud máte zájem o řešení aplikací pro více cloudů pomocí cloudové infrastruktury Oracle, přečtěte si téma [řešení pro aplikace Oracle integrace Microsoft Azure a Oracle cloudové infrastruktury](oracle-oci-overview.md).

Chcete-li získat seznam aktuálně dostupných imagí, spusťte následující příkaz:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od května 2019 jsou k dispozici následující Image:

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

Tyto image se považují za "Přineste si vlastní licenci", takže se vám budou účtovat jenom náklady na výpočetní prostředky, úložiště a síť vzniklé spuštěním virtuálního počítače.  Předpokládá se, že máte řádně licenci na používání softwaru Oracle a máte k dispozici aktuální smlouvu o podpoře pro Oracle. Oracle garantuje mobilitu licencí z místního prostředí do Azure. Podrobnosti o mobilitě licencí najdete v publikovaných [Oracle a](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) v poznámkách Microsoftu. 

Jednotlivci si také můžou zvolit, že budou svá řešení založit na vlastní imagi, kterou vytvoří od začátku v Azure, nebo nahrajte vlastní image ze svého místního prostředí.

## <a name="support-for-jd-edwards"></a>Podpora pro řešení JD Edwards
Podle informací o podpoře Oracle Poznámka k [dokumentu s ID 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), řešení JD Edwards EnterpriseOne verze 9,2 a vyšší jsou podporovány v **jakékoli veřejné cloudové nabídce** , která splňuje jejich konkrétní `Minimum Technical Requirements` (MTR).  Musíte vytvořit vlastní image, které splňují specifikace MTR pro kompatibilitu s operačním systémem a softwarovou aplikací. 

## <a name="oracle-database-vm-images"></a>Image virtuálních počítačů Oracle Database
Oracle podporuje v Azure image virtuálních počítačů s operačním systémem Oracle DB 12,1 Standard a Enterprise a na základě Oracle Linux.  Pokud chcete dosáhnout nejlepšího výkonu pro produkční úlohy Oracle DB v Azure, ujistěte se, že máte správně nastavenou velikost image virtuálního počítače a používáte Managed Disks, které se zálohují Premium Storage. Pokyny, jak rychle získat Oracle DB a spustit v Azure pomocí image virtuálního počítače s Oracle, najdete v [Průvodci rychlým startem Oracle DB](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Možnosti konfigurace připojeného disku

Připojené disky využívají službu Azure Blob Storage. Každý standardní disk může mít teoretickou maximální hodnotu přibližně 500 vstupních/výstupních operací za sekundu (IOPS). Naše disková nabídka Premium je preferována pro vysoce výkonné databázové úlohy a může na disku dosáhnout až 5000 vstupně-výstupních operací. Pokud to vyhovuje vašim požadavkům na výkon, můžete použít jeden disk. Můžete ale zvýšit výkon platných IOPS, pokud použijete více připojených disků, rozprostřete mezi ně databázová data a pak použijete Oracle pro správu automatického úložiště (ASM). Další informace specifické pro Oracle ASM najdete v tématu [Přehled automatického úložiště Oracle](https://www.oracle.com/technetwork/database/index-100339.html) . Příklad toho, jak nainstalovat a nakonfigurovat Oracle ASM na virtuálním počítači se systémem Linux Azure, najdete v kurzu [instalace a konfigurace Oracle Automated Storage Management](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Možnosti konfigurace sdíleného úložiště

Azure NetApp Files byla navržena tak, aby splňovala základní požadavky na spouštění vysoce výkonných úloh, jako jsou databáze v cloudu, a nabízí;
- Služba Azure Native Shared Storage pro spouštění úloh Oracle buď prostřednictvím nativního klienta systému souborů NFS, nebo Oracle dNFS
- Škálovatelné úrovně výkonu, které odráží reálný rozsah požadavků IOPS
- Nízká latence
- Vysoká dostupnost, vysoká odolnost a možnosti správy ve velkém měřítku, které obvykle vyžadují důležité podnikové úlohy (například SAP a Oracle).
- Rychlé a efektivní zálohování a obnovení, abyste dosáhli nejúčinnějších smluv SLA pro RTO a RPO

Tyto možnosti jsou možné, protože Azure NetApp Files jsou založené na NetApp® ONTAP® všechny systémy, které běží v prostředí Azure Data Center – jako nativní službu Azure. Výsledkem je ideální technologie databázového úložiště, kterou lze zřídit a spotřebovat stejně jako jiné možnosti služby Azure Storage. Další informace o nasazení a přístupu Azure NetApp Files ke svazkům systému souborů NFS najdete v [dokumentaci Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) . Osvědčené [postupy pro nasazení Oracle v Azure](https://www.netapp.com/us/media/tr-4780.pdf) najdete v tématu Příručka k osvědčeným postupům pro použití Azure NetApp Files pro doporučení pro provoz databáze Oracle v Azure NetApp Files.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Cluster reálné aplikace Oracle (Oracle RAC)
Oracle RAC je navržený tak, aby zmírnil selhání jednoho uzlu v místní konfiguraci clusteru s více uzly. Spoléhá se na dvě místní technologie, které nejsou nativní pro prostředí veřejného cloudu bez technologie Hyper-v: síťové vícenásobné vysílání a sdílený disk. Pokud vaše databázové řešení vyžaduje pro Azure RAC v Azure, budete potřebovat software třetích stran, který tyto technologie povoluje. Další informace o účtu Oracle RAC najdete na [stránce FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Požadavky na vysokou dostupnost a zotavení po havárii
Při používání databází Oracle v Azure zodpovídáte za implementaci řešení vysoké dostupnosti a zotavení po havárii, abyste se vyhnuli výpadkům. 

Vysoká dostupnost a zotavení po havárii pro Oracle Database Enterprise Edition (bez spoléhání na Oracle RAC) se dá v Azure dosáhnout pomocí [ochrany dat, aktivní ochrany dat](https://www.oracle.com/database/technologies/high-availability/dataguard.html)nebo [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)se dvěma databázemi na dvou oddělených virtuálních počítačích. virtuální. Oba virtuální počítače by měly být ve stejné [virtuální síti](https://azure.microsoft.com/documentation/services/virtual-network/) , aby se zajistilo, že budou mít přístup k sobě přes soukromou trvalou IP adresu.  Kromě toho doporučujeme umístit virtuální počítače do stejné skupiny dostupnosti, aby je bylo možné do Azure umístit do samostatných domén selhání a upgradovacích domén. Pokud chcete mít geografickou redundanci, nastavte dvě databáze tak, aby se replikují mezi dvěma různými oblastmi, a propojte tyto dvě instance s VPN Gateway.

Kurz [implementace Oracle data Guard v Azure](configure-oracle-dataguard.md) vás provede základními postupy nastavení v Azure.  

Pomocí Oracle data Guard je možné dosáhnout vysoké dostupnosti s primární databází na jednom virtuálním počítači, sekundární (pohotovostní) databází na jiném virtuálním počítači a nastavením jednosměrné replikace. Výsledkem je přístup pro čtení k kopii databáze. Pomocí Oracle GoldenGate můžete nakonfigurovat obousměrnou replikaci mezi oběma databázemi. Informace o tom, jak nastavit řešení vysoké dostupnosti pro vaše databáze pomocí těchto nástrojů, najdete v dokumentaci k [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) na webu Oracle. Pokud potřebujete přístup pro čtení a zápis k kopii databáze, můžete použít [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Kurz [implementace Oracle GoldenGate v Azure](configure-oracle-golden-gate.md) vás provede základními postupy nastavení v Azure.

Kromě toho, že máte v Azure navržený řešení HA a zotavení po havárii, byste měli mít k dispozici strategii zálohování pro obnovení databáze. Kurz [zálohování a obnovení Oracle Database](oracle-backup-recovery.md) vás provede základním postupem pro vytvoření konzistentní zálohy.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Image virtuálních počítačů Oracle WebLogic Server

* **Clustering se podporuje jenom v edici Enterprise.** Máte licenci pro použití clusteringu WebLogic jenom v případě, že používáte edici Enterprise serveru WebLogic Server. Nepoužívejte clusteringu se serverem WebLogic Server Standard Edition.
* **Vícesměrové vysílání UDP se nepodporuje.** Azure podporuje jednosměrové vysílání UDP, ale ne vícesměrové nebo všesměrové vysílání. WebLogic Server se může spoléhat na možnosti jednosměrového vysílání UDP Azure. Pro dosažení nejlepších výsledků, které závisí na jednosměrovém vysílání UDP, doporučujeme, aby velikost clusteru WebLogic byla udržována staticky nebo udržována bez více než 10 spravovaných serverů.
* **WebLogic server očekává, že veřejné a privátní porty budou pro přístup T3 (například při použití podnikového JavaBeans) stejné.** Vezměte v úvahu scénář s více vrstvami, kde je spuštěná aplikace vrstvy služby (EJB) v clusteru serveru WebLogic, který se skládá ze dvou nebo více virtuálních počítačů ve virtuální síti s názvem *SLWLS*. Úroveň klienta je v jiné podsíti ve stejné virtuální síti, přičemž je spuštěn jednoduchý program Java, který se pokouší zavolat EJB ve vrstvě služeb. Vzhledem k tomu, že je nutné vyrovnávat zatížení vrstvy služby, je nutné pro virtuální počítače v clusteru serveru WebLogic vytvořit veřejný koncový bod s vyrovnáváním zatížení. Pokud se privátní port, který zadáte, liší od veřejného portu (například 7006:7008), dojde k chybě, například následující:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Důvodem je to, že u vzdáleného přístupu T3 Server WebLogic očekává, že port nástroje pro vyrovnávání zatížení a port spravovaného serveru WebLogic je stejný. V předchozím případě klient přistupuje k portu 7006 (port nástroje pro vyrovnávání zatížení) a spravovaný server naslouchá na 7008 (privátní port). Toto omezení se vztahuje pouze na přístup T3, nikoli na HTTP.

   Chcete-li se tomuto problému vyhnout, použijte jedno z následujících řešení:

  * Pro koncové body s vyrovnáváním zatížení vyhrazené pro přístup T3 používejte stejná čísla privátních a veřejných portů.
  * Při spouštění serveru WebLogic Server zahrňte následující parametr JVM:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Související informace najdete v článku znalostní báze **860340,1** na <https://support.oracle.com>adrese.

* **Omezení dynamického clusteringu a vyrovnávání zatížení.** Předpokládejme, že chcete použít dynamický cluster na serveru WebLogic a zveřejnit ho pomocí jediného veřejného koncového bodu s vyrovnáváním zatížení v Azure. To se dá udělat, pokud použijete pevné číslo portu pro každý ze spravovaných serverů (nedynamicky přiřadíte z rozsahu) a nespustíte více spravovaných serverů, než jsou počítače, které správce sleduje. To znamená, že pro každý virtuální počítač není k dispozici více než jeden spravovaný server. Pokud vaše konfigurace vede k většímu počtu spuštěných serverů WebLogic, než je virtuálních počítačů (tj. Pokud více instancí serveru WebLogic Server sdílí stejný virtuální počítač), není možné použít více než jednu z těchto instancí serverů WebLogic. pro vytvoření vazby na dané číslo portu. Ostatní na tomto virtuálním počítači selžou.

   Pokud nakonfigurujete server pro správu tak, aby na jeho spravovaných serverech automaticky přiřadil jedinečná čísla portů, pak není vyrovnávání zatížení možné, protože Azure nepodporuje mapování na jeden veřejný port na více privátních portů, jak by to bylo nutné. rozšířeného.
* **Několik instancí WebLogic serveru na virtuálním počítači** V závislosti na požadavcích vašeho nasazení můžete zvážit spuštění více instancí WebLogic serveru na stejném virtuálním počítači, pokud je virtuální počítač dostatečně velký. Například na virtuálním počítači střední velikosti, který obsahuje dvě jádra, můžete zvolit spuštění dvou instancí serveru WebLogic. Přesto však doporučujeme, abyste se vyhnuli zavedení jednoduchých selhání do vaší architektury, což by znamenalo, pokud jste použili jenom jeden virtuální počítač, na kterém běží víc instancí WebLogic serveru. Používání aspoň dvou virtuálních počítačů může být lepším přístupem a každý virtuální počítač pak může spustit víc instancí WebLogic serveru. Každá instance serveru WebLogic by mohla být i nadále součástí stejného clusteru. V současné době ale není možné použít Azure k vyrovnávání zatížení koncových bodů, které jsou zpřístupněny v nasazeních serveru WebLogic Server v rámci stejného virtuálního počítače, protože nástroj pro vyrovnávání zatížení Azure vyžaduje, aby byly servery s vyrovnáváním zatížení distribuované mezi jedinečnými. virtuální počítače.

## <a name="oracle-jdk-virtual-machine-images"></a>Image virtuálních počítačů Oracle JDK
* **Nejnovější aktualizace pro JDK 6 a 7** I když doporučujeme použít nejnovější veřejnou, podporovanou verzi Java (aktuálně Java 8), Azure taky zpřístupní dostupné image JDK 6 a 7. To je určené pro starší verze aplikací, které ještě nejsou připravené k upgradu na JDK 8. I když již nemusí být aktualizace na předchozí image JDK k dispozici široké veřejnosti, vzhledem k partnerství Microsoftu s Oracle, image JDK 6 a 7, které poskytuje Azure, mají k dispozici novější neveřejnou aktualizaci, kterou obvykle nabízí Oracle pro pouze výběr skupiny podporovaných zákazníků Oracle. Nové verze imagí JDK budou k dispozici v průběhu času s aktualizovanými verzemi JDK 6 a 7.

   JDK dostupné v imagích JDK 6 a 7 a virtuální počítače a image, které z nich jsou odvozené, se dají používat jenom v Azure.
* **64 bitová JDK.** Image virtuálních počítačů Oracle WebLogic Server a image virtuálních počítačů Oracle JDK, které poskytuje Azure, obsahují 64 verze Windows serveru i JDK.

## <a name="next-steps"></a>Další postup
Nyní máte přehled o aktuálních řešeních Oracle na základě imagí virtuálních počítačů v Microsoft Azure. Vaším dalším krokem je nasazení první databáze Oracle v Azure.

> [!div class="nextstepaction"]
> [Vytvoření databáze Oracle v Azure](oracle-database-quick-create.md)
