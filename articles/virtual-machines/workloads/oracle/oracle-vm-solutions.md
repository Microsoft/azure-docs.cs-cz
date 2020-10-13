---
title: Řešení Oracle na virtuálních počítačích Azure | Microsoft Docs
description: Přečtěte si o podporovaných konfiguracích a omezeních imagí virtuálních počítačů Oracle na Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 32e79e12eae9997df8163401d2abc5f06bc02fc0
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993491"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Image virtuálních počítačů Oracle a jejich nasazení v Microsoft Azure

Tento článek obsahuje informace o řešeních Oracle na základě imagí virtuálních počítačů publikovaných Oracle v Azure Marketplace. Pokud máte zájem o řešení aplikací pro více cloudů pomocí cloudové infrastruktury Oracle, přečtěte si téma [řešení pro aplikace Oracle integrace Microsoft Azure a Oracle cloudové infrastruktury](oracle-oci-overview.md).

Chcete-li získat seznam aktuálně dostupných imagí, spusťte následující příkaz:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od června 2020 jsou k dispozici následující Image:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Tyto image se považují za "Přineste si vlastní licenci", takže se vám budou účtovat jenom náklady na výpočetní prostředky, úložiště a síť vzniklé spuštěním virtuálního počítače.  Předpokládá se, že máte řádně licenci na používání softwaru Oracle a máte k dispozici aktuální smlouvu o podpoře pro Oracle. Oracle garantuje mobilitu licencí z místního prostředí do Azure. Podrobnosti o mobilitě licencí najdete v publikovaných [Oracle a](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) v poznámkách Microsoftu.

Jednotlivci si také můžou zvolit, že budou svá řešení založit na vlastní imagi, kterou vytvoří od začátku v Azure, nebo nahrajte vlastní image ze svého místního prostředí.

## <a name="oracle-database-vm-images"></a>Image virtuálních počítačů Oracle Database

Oracle podporuje na imagích virtuálních počítačů, které jsou založené na Oracle Linux, operační systémy Oracle Database 12,1 a vyšší verze Standard a Enterprise v Azure.  Pokud chcete dosáhnout nejlepšího výkonu pro produkční úlohy Oracle Database v Azure, ujistěte se, že máte správně nastavenou velikost image virtuálního počítače a používáte SSD úrovně Premium nebo SSD úrovně Ultra Managed Disks. Pokyny, jak rychle získat Oracle Database a spustit v Azure pomocí image virtuálního počítače s Oracle, najdete v [Průvodci rychlým startem Oracle Database](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Možnosti konfigurace připojeného disku

Připojené disky využívají službu Azure Blob Storage. Každý standardní disk může mít teoretickou maximální hodnotu přibližně 500 vstupních/výstupních operací za sekundu (IOPS). Naše disková nabídka Premium je preferována pro vysoce výkonné databázové úlohy a může na disku dosáhnout až 5000 vstupně-výstupních operací. Pokud to vyhovuje vašim požadavkům na výkon, můžete použít jeden disk. Můžete ale zvýšit výkon platných IOPS, pokud použijete více připojených disků, rozprostřete mezi ně databázová data a pak použijete Oracle pro správu automatického úložiště (ASM). Další informace specifické pro Oracle ASM najdete v tématu [Přehled automatického úložiště Oracle](https://www.oracle.com/technetwork/database/index-100339.html) . Příklad toho, jak nainstalovat a nakonfigurovat Oracle ASM na virtuálním počítači se systémem Linux Azure, najdete v kurzu [instalace a konfigurace Oracle Automated Storage Management](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Možnosti konfigurace sdíleného úložiště

Azure NetApp Files byla navržena tak, aby splňovala základní požadavky na spouštění vysoce výkonných úloh, jako jsou databáze v cloudu, a nabízí;

- Služba Azure Native Shared Storage pro spouštění úloh Oracle buď prostřednictvím nativního klienta systému souborů NFS, nebo Oracle dNFS
- Škálovatelné úrovně výkonu, které odráží reálný rozsah požadavků IOPS
- Nízká latence
- Vysoká dostupnost, vysoká odolnost a možnosti správy ve velkém měřítku, které obvykle vyžadují důležité podnikové úlohy (například SAP a Oracle).
- Rychlé a efektivní zálohování a obnovení, abyste dosáhli nejúčinnějších smluv SLA pro RTO a RPO

Tyto možnosti jsou možné, protože Azure NetApp Files jsou založené na NetApp® ONTAP® všechny systémy, které běží v prostředí Azure Data Center – jako nativní službu Azure. Výsledkem je ideální technologie databázového úložiště, kterou lze zřídit a spotřebovat stejně jako jiné možnosti služby Azure Storage. Další informace o nasazení a přístupu Azure NetApp Files ke svazkům systému souborů NFS najdete v [dokumentaci Azure NetApp Files](../../../azure-netapp-files/index.yml) . Osvědčené [postupy pro nasazení Oracle v Azure](https://www.netapp.com/us/media/tr-4780.pdf) najdete v tématu Příručka k osvědčeným postupům pro použití Azure NetApp Files pro doporučení pro provoz databáze Oracle v Azure NetApp Files.

## <a name="licensing-oracle-database--software-on-azure"></a>Licencování Oracle Database & software v Azure

Microsoft Azure je autorizované cloudové prostředí pro spouštění Oracle Database. Tabulka Oracle Core Factor se nedá použít při licencování databází Oracle v cloudu. Místo toho se při použití virtuálních počítačů s povolenou technologií Hyper-Threading pro databáze Enterprise Edition počítá dvě vCPU stejně jako jedna licence procesoru Oracle, pokud je povolená funkce webthreading (jak je uvedeno v dokumentu zásady). Podrobnosti o zásadách najdete [tady](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Databáze Oracle obecně vyžadují větší paměť a vstupně-výstupní operace. Z tohoto důvodu se pro tyto úlohy doporučují [paměťově optimalizované virtuální počítače](../../sizes-memory.md) . Pro lepší optimalizaci vašich úloh se doporučují [základní vcpu](../../constrained-vcpu.md) Oracle Database pro úlohy, které vyžadují vysoké množství paměti, úložiště a vstupně-výstupní propustnosti, ale ne vysoký počet jader.

Když migrujete software Oracle a úlohy z místního prostředí do Microsoft Azure, Oracle nabízí mobilitu licencí, jak je uvedeno v tématu [Oracle on Azure – Nejčastější dotazy](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) .

## <a name="oracle-real-application-cluster-oracle-rac"></a>Cluster reálné aplikace Oracle (Oracle RAC)

Cluster reálné aplikace Oracle (Oracle RAC) je navržený tak, aby zmírnil selhání jednoho uzlu v místní konfiguraci clusteru s více uzly. Spoléhá se na dvě místní technologie, které nejsou nativní pro prostředí veřejného cloudu bez technologie Hyper-v: síťové vícenásobné vysílání a sdílený disk. Pokud vaše databázové řešení vyžaduje pro Azure RAC v Azure, budete potřebovat software třetí strany, který tyto technologie povolí. Další informace o účtu Oracle RAC najdete na [stránce FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Požadavky na vysokou dostupnost a zotavení po havárii

Při používání databází Oracle v Azure zodpovídáte za implementaci řešení vysoké dostupnosti a zotavení po havárii, abyste se vyhnuli výpadkům.

Vysoká dostupnost a zotavení po havárii pro Oracle Database Enterprise Edition (bez spoléhání na Oracle RAC) se dá v Azure dosáhnout pomocí [ochrany dat, aktivní ochrany dat](https://www.oracle.com/database/technologies/high-availability/dataguard.html)nebo [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)se dvěma databázemi na dvou různých virtuálních počítačích. Oba virtuální počítače by měly být ve stejné [virtuální síti](https://azure.microsoft.com/documentation/services/virtual-network/) , aby se zajistilo, že budou mít přístup k sobě přes soukromou trvalou IP adresu.  Kromě toho doporučujeme umístit virtuální počítače do stejné skupiny dostupnosti, aby je bylo možné do Azure umístit do samostatných domén selhání a upgradovacích domén. Pokud chcete mít geografickou redundanci, nastavte dvě databáze tak, aby se replikují mezi dvěma různými oblastmi, a propojte tyto dvě instance s VPN Gateway.

Kurz [implementace Oracle data Guard v Azure](configure-oracle-dataguard.md) vás provede základními postupy nastavení v Azure.  

Pomocí Oracle data Guard je možné dosáhnout vysoké dostupnosti s primární databází na jednom virtuálním počítači, sekundární (pohotovostní) databází na jiném virtuálním počítači a nastavením jednosměrné replikace. Výsledkem je přístup pro čtení k kopii databáze. Pomocí Oracle GoldenGate můžete nakonfigurovat obousměrnou replikaci mezi oběma databázemi. Informace o tom, jak nastavit řešení vysoké dostupnosti pro vaše databáze pomocí těchto nástrojů, najdete v dokumentaci k [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) na webu Oracle. Pokud potřebujete přístup pro čtení a zápis k kopii databáze, můžete použít [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Kurz [implementace Oracle GoldenGate v Azure](configure-oracle-golden-gate.md) vás provede základními postupy nastavení v Azure.

Kromě toho, že máte v Azure navržený řešení HA a zotavení po havárii, byste měli mít k dispozici strategii zálohování pro obnovení databáze. Kurz [zálohování a obnovení Oracle Database](./oracle-overview.md) vás provede základním postupem pro vytvoření konzistentní zálohy.

## <a name="support-for-jd-edwards"></a>Podpora pro řešení JD Edwards

Podle informací o podpoře Oracle Poznámka k [dokumentu s ID 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), řešení JD Edwards EnterpriseOne verze 9,2 a vyšší jsou podporovány v **jakékoli veřejné cloudové nabídce** , která splňuje jejich konkrétní `Minimum Technical Requirements` (MTR).  Musíte vytvořit vlastní image, které splňují specifikace MTR pro kompatibilitu s operačním systémem a softwarovou aplikací.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Nabídky virtuálních počítačů Oracle WebLogic Server

Oracle a Microsoft spolupracují na převedení serveru WebLogic Server na Azure Marketplace ve formě kolekce nabídek aplikací Azure.  Tyto nabídky jsou popsané v článku [Oracle WebLogic Server Azure Applications](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Image virtuálních počítačů Oracle WebLogic Server

- **Clustering se podporuje jenom v edici Enterprise.** Máte licenci pro použití clusteringu WebLogic jenom v případě, že používáte edici Enterprise serveru Oracle WebLogic Server. Nepoužívejte clusteringu s edicí Oracle WebLogic Server Standard.
- **Vícesměrové vysílání UDP se nepodporuje.** Azure podporuje jednosměrové vysílání UDP, ale ne vícesměrové nebo všesměrové vysílání. Oracle WebLogic Server je schopný spoléhat na možnosti jednosměrového vysílání UDP Azure. Pro dosažení nejlepších výsledků, které závisí na jednosměrovém vysílání UDP, doporučujeme, aby velikost clusteru WebLogic byla udržována staticky nebo udržována bez více než 10 spravovaných serverů.
- **Oracle WebLogic server očekává, že veřejné a privátní porty budou stejné jako pro přístup T3 (například při použití Enterprise JavaBeans).** Vezměte v úvahu scénář s více vrstvami, kde je spuštěná aplikace vrstvy služby (EJB) v clusteru Oracle WebLogic Server skládající se ze dvou nebo více virtuálních počítačů ve virtuální síti s názvem *SLWLS*. Úroveň klienta je v jiné podsíti ve stejné virtuální síti, přičemž je spuštěn jednoduchý program Java, který se pokouší zavolat EJB ve vrstvě služeb. Vzhledem k tomu, že je nutné vyrovnávat zatížení vrstvy služby, je nutné pro virtuální počítače v clusteru Oracle WebLogic Server vytvořit veřejný koncový bod s vyrovnáváním zatížení. Pokud se privátní port, který zadáte, liší od veřejného portu (například 7006:7008), dojde k chybě, například následující:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Důvodem je to, že u vzdáleného přístupu T3 očekává Oracle WebLogic server port nástroje pro vyrovnávání zatížení a port spravovaného serveru WebLogic. V předchozím případě klient přistupuje k portu 7006 (port nástroje pro vyrovnávání zatížení) a spravovaný server naslouchá na 7008 (privátní port). Toto omezení se vztahuje pouze na přístup T3, nikoli na HTTP.

   Chcete-li se tomuto problému vyhnout, použijte jedno z následujících řešení:

- Pro koncové body s vyrovnáváním zatížení vyhrazené pro přístup T3 používejte stejná čísla privátních a veřejných portů.
- Při spouštění serveru Oracle WebLogic Server zahrňte následující parametr JVM:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Související informace najdete v článku znalostní báze **860340,1** na adrese <https://support.oracle.com> .

- **Omezení dynamického clusteringu a vyrovnávání zatížení.** Předpokládejme, že chcete použít dynamický cluster na serveru Oracle WebLogic Server a zveřejnit ho pomocí jediného veřejného koncového bodu s vyrovnáváním zatížení v Azure. To se dá udělat, pokud použijete pevné číslo portu pro každý ze spravovaných serverů (nedynamicky přiřadíte z rozsahu) a nespustíte více spravovaných serverů, než jsou počítače, které správce sleduje. To znamená, že pro každý virtuální počítač není k dispozici více než jeden spravovaný server. Pokud vaše konfigurace vede k více serverům Oracle WebLogic, než jsou virtuální počítače (tj. kde více instancí Oracle WebLogic Server sdílí stejný virtuální počítač), není možné, aby se více než jedna z těchto instancí serveru Oracle WebLogic mohla přivážet k danému číslu portu. Ostatní na tomto virtuálním počítači selžou.

   Pokud nakonfigurujete server pro správu tak, aby na jeho spravovaných serverech automaticky přiřadil jedinečná čísla portů, pak není vyrovnávání zatížení možné, protože Azure nepodporuje mapování z jednoho veřejného portu na několik privátních portů, které by se vyžadovaly pro tuto konfiguraci.
- **Několik instancí Oracle WebLogic serveru na virtuálním počítači.** V závislosti na požadavcích vašeho nasazení můžete zvážit spuštění více instancí Oracle WebLogic serveru na stejném virtuálním počítači, pokud je virtuální počítač dostatečně velký. Například na virtuálním počítači střední velikosti, který obsahuje dvě jádra, můžete zvolit spuštění dvou instancí serveru Oracle WebLogic Server. Přesto však doporučujeme, abyste se vyhnuli zavedení jednoduchých selhání do vaší architektury, což by znamenalo, pokud jste použili jenom jeden virtuální počítač, na kterém běží víc instancí Oracle WebLogic serveru. Používání aspoň dvou virtuálních počítačů může být lepším přístupem a každý virtuální počítač pak může spustit víc instancí Oracle WebLogic serveru. Každá instance Oracle WebLogic serveru může být i nadále součástí stejného clusteru. V současné době není možné použít Azure k vyrovnávání zatížení koncových bodů, které jsou zveřejněné v nasazeních Oracle WebLogic Server v rámci stejného virtuálního počítače, protože nástroj pro vyrovnávání zatížení Azure vyžaduje, aby byly servery s vyrovnáváním zatížení distribuované mezi jedinečnými virtuálními počítači.

## <a name="oracle-jdk-virtual-machine-images"></a>Image virtuálních počítačů Oracle JDK

- **Nejnovější aktualizace pro JDK 6 a 7** I když doporučujeme použít nejnovější veřejnou, podporovanou verzi Java (aktuálně Java 8), Azure taky zpřístupní dostupné image JDK 6 a 7. To je určené pro starší verze aplikací, které ještě nejsou připravené k upgradu na JDK 8. I když již nemusí být aktualizace na předchozí image JDK k dispozici široké veřejnosti, vzhledem k partnerství Microsoftu s Oracle, image JDK 6 a 7, které poskytuje Azure, jsou určené k zahrnutí novější neveřejné aktualizace, kterou obvykle nabízí Oracle, jenom k výběru skupiny podporovaných zákazníků Oracle. Nové verze imagí JDK budou k dispozici v průběhu času s aktualizovanými verzemi JDK 6 a 7.

   JDK dostupné v imagích JDK 6 a 7 a virtuální počítače a image, které z nich jsou odvozené, se dají používat jenom v Azure.
- **64 bitová JDK.** Image virtuálních počítačů Oracle WebLogic Server a image virtuálních počítačů Oracle JDK, které poskytuje Azure, obsahují 64 verze Windows serveru i JDK.

## <a name="next-steps"></a>Další kroky

Nyní máte přehled o aktuálních řešeních Oracle na základě imagí virtuálních počítačů v Microsoft Azure. Vaším dalším krokem je nasazení první databáze Oracle v Azure.

> [!div class="nextstepaction"]
> [Vytvoření databáze Oracle v Azure](oracle-database-quick-create.md)