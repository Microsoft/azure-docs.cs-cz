---
title: Oracle řešení na Azure virtual machines | Dokumentace Microsoftu
description: Další informace o podporovaných konfiguracích a omezeních imagí virtuálních počítačů Oracle v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
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
ms.openlocfilehash: b62b35320ba1f4473e9b3a039d181d6a2fb58257
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743617"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Image virtuálních počítačů Oracle a jejich nasazení v Microsoft Azure

Tento článek obsahuje informace o řešeních Oracle založených na imagích virtuálních počítačů publikované společností Oracle v Azure Marketplace. Pokud vás zajímají řešení pro různé cloudové aplikace s Cloudovou infrastrukturou Oracle, přečtěte si téma [Oracle aplikace řešení integrace Microsoft Azure a Oracle cloudové infrastruktury](oracle-oci-overview.md).

Pokud chcete získat seznam aktuálně dostupných imagí, spusťte následující příkaz:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od května 2019 jsou k dispozici na následujících obrázcích:

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

Tyto Image jsou považovány za "Používání vlastní licence" a v důsledku vám naúčtujeme vám jenom výpočetní prostředky, úložiště a sítě náklady na provoz virtuálního počítače.  Předpokládá se, že byly řádně licencované k používání softwaru Oracle a mají aktuální smlouvu o podpoře na místě se společností Oracle. Oracle je zaručeno, že mobility licencí v rámci místních do Azure. Zobrazit publikovanému [Oracle a Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Poznámka podrobnosti o mobilitě licencí používat. 

Na základní svá řešení na vlastní imagi vytvořit od nuly v Azure nebo nahrát vlastní image z jejich v místním prostředí můžete také zvolit jednotlivce.

## <a name="support-for-jd-edwards"></a>Podpora řešení JD Edwards
Podle podporu pro Oracle Poznámka [2178595.1 ID dokumentu](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), jsou řešení JD Edwards EnterpriseOne verze 9.2 a výše jsou podporovány v **všechny veřejné cloudové nabídky** , který vyhovuje jejich konkrétní `Minimum Technical Requirements` (MTR).  Je potřeba vytvořit vlastní Image, které vyhovují jejich MTR specifikace pro zajištění kompatibility aplikací operačního systému a softwaru. 

## <a name="oracle-database-vm-images"></a>Image virtuálních počítačů Oracle database
Oracle podporuje imagemi virtuálního počítače založeného na Oracle Linuxu spuštěné Oracle DB 12.1 edice Standard a Enterprise v Azure.  Pro zajištění nejlepšího výkonu pro produkční úlohy Oracle DB v Azure je potřeba správně velikost image virtuálního počítače a používat spravované disky, které se zálohují na Premium Storage. Návod, jak rychle zprovoznění Oracle DB v Azure s využitím Oracle publikované image virtuálního počítače, [zkuste návod Oracle DB Quickstart](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Možnosti konfigurace připojený disk

Spolehněte se na službu Azure Blob storage připojených disků. Každý standardní disk je schopen teoretické maximum přibližně 500 vstupně výstupní operace za sekundu (IOPS). Naše nabídky disk premium je upřednostňována pro vysoce výkonné databázové úlohy a může dosáhnout až 5000 vstupně-výstupních operací na disk. Můžete jeden disk, který vyhovuje vašim požadavkům na výkon. Můžete však zvýšit efektivní výkon vstupně-výstupních operací, pokud používáte více připojených disků, rozložit dat z databáze mezi nimi a pak pomocí automatického úložiště Oracle Management (ASM). Zobrazit [Oracle automatického úložiště – přehled](https://www.oracle.com/technetwork/database/index-100339.html) Oracle ASM konkrétní informace. Příklad toho, jak nainstalovat a nakonfigurovat Oracle ASM na Linuxovém virtuálním počítači Azure, najdete v článku [instalace a konfigurace Oracle automatizované Storage Management](configure-oracle-asm.md) kurzu.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Clusterová aplikace skutečný Oracle (Oracle RAC)
Oracle RAC slouží ke zmírnění selhání jednoho uzlu v konfiguraci v místním clusteru s několika uzly. Spoléhá na dva místními technologiemi, které nejsou nativní pro vysoce škálovatelné veřejných cloudových prostředích: všesměrového vysílání v síti a sdíleného disku. Pokud vaše databáze řešení nevyžaduje RAC Oracle v Azure, musíte jiného software jiného výrobce umožňují tyto technologie. Další informace o Oracle RAC, najdete v článku [FlashGrid SkyCluster stránky](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Aspekty vysoké dostupnosti a po havárii pro obnovení
Při používání databází Oracle v Azure, zodpovídáte za implementaci vysokou dostupnost a obnovení řešení žádné výpadky. 

Jde dosáhnout vysoké dostupnosti a zotavení po havárii pro Oracle Database Enterprise Edition (bez nutnosti spoléhat se na Oracle RAC) na Azure s využitím [Data Guard, aktivní Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), nebo [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), s dvěma databázemi na dva samostatné virtuální počítače. Oba virtuální počítače by měl být ve stejném [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) zajistit přístupem k sobě navzájem prostřednictvím trvalého privátní IP adresu.  Kromě toho doporučujeme umístění virtuálních počítačů ve stejné skupině dostupnosti nastavit tak, aby Azure a umístí je do samostatných doménách selhání a upgradovacími doménami. By měl, budete chtít mít geografickou redundanci, nastavte dvě databáze replikovat mezi dvěma různými oblastmi a připojení brány VPN Gateway dvě instance.

Tento kurz [implementovat Oracle Data Guard na Azure](configure-oracle-dataguard.md) vás provede postupem základní nastavení v Azure.  

S Oracle Data Guard, jde dosáhnout vysoké dostupnosti s primární databází v jednom z virtuálních počítačů, sekundární (pohotovostní) databáze v jiné virtuální počítače a nastavení mezi nimi jednosměrná replikace. Výsledkem je oprávnění ke čtení pro kopii databáze. S Oracle GoldenGate můžete nakonfigurovat obousměrnou replikaci mezi dvěma databázemi. Zjistěte, jak nastavit řešení vysoké dostupnosti databází pomocí těchto nástrojů, najdete v článku [aktivní Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentaci na webu Oracle. Pokud budete potřebovat pro čtení i zápis přístup na kopii databáze, můžete použít [Oracle aktivní Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Tento kurz [Oracle GoldenGate implementovat v Azure](configure-oracle-golden-gate.md) vás provede postupem základní nastavení v Azure.

Kromě řešení vysokou dostupnost a zotavení po Havárii navržený v Azure byste měli mít strategii zálohování v umístění pro obnovení databáze. Tento kurz [zálohování a obnovení databáze Oracle](oracle-backup-recovery.md) vás provede základní postup pro vytvoření zálohy konzistentní vzhledem k aplikacím.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Image virtuálních počítačů Oracle WebLogic serveru

* **Clustering je podporována v edici Enterprise pouze.** Mají licenci k používání, WebLogic clustering, pouze pokud používáte edici Enterprise systému WebLogic Server. Nepoužívají vytváření clusterů se WebLogic Server Standard Edition.
* **Vícesměrové vysílání UDP se nepodporuje.** Azure podporuje použito jednosměrové vysílání UDP, ale ne vícesměrové nebo všesměrové vysílání. WebLogic Server se využívají možnosti jednosměrového vysílání Azure UDP. Nejlepších výsledků dosáhnete spoléhat na jednosměrového vysílání UDP doporučujeme vám, že velikost clusteru WebLogic zůstane statické nebo uchované s více než 10 spravovaných serverů.
* **WebLogic Server očekává, že veřejné a soukromé porty stejný přístup T3 (například při použití Enterprise JavaBeans).** Představte si třeba situaci vícevrstvé aplikace služby vrstvy (EJB) se spuštěným systémem na WebLogic Server clusteru, který se skládá ze dvou nebo více virtuálních počítačů ve virtuální síti s názvem *SLWLS*. Klientská vrstva je v jiné podsíti ve stejné virtuální síti s jednoduchý program v jazyce Java pokusu o volání EJB ve vrstvě služeb. Protože je nutné k vrstvě služby Vyrovnávání zatížení, veřejný koncový bod s vyrovnáváním zatížení je potřeba vytvořit pro virtuální počítače v clusteru WebLogic Server. Pokud se privátní port, který zadáte, se liší od veřejný port (například 7006:7008), dojde k chybě například následující:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Je to proto, že pro vzdálený přístup T3, WebLogic Server očekává, že port nástroje pro vyrovnávání zatížení a port serveru WebLogic, managed být stejné. V předchozím případě klient přistupuje k portu 7006 (port nástroje pro vyrovnávání zatížení) a spravovaný server naslouchá na 7008 (privátní port). Toto omezení platí pouze pro přístup k T3, ne HTTP.

   K tomuto problému vyhnout, použijte jednu z následujících náhradních postupů:

  * Používejte stejná čísla portů privátních a veřejných koncových bodů s vyrovnáváním zatížení vyhrazený pro T3 přístup.
  * Při spouštění WebLogic serveru, přidejte následující parametr JVM:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Související informace naleznete v článku KB **860340.1** na <https://support.oracle.com>.

* **Dynamický clustering a omezení pro vyrovnávání zatížení.** Předpokládejme, že budete chtít použít dynamické clusteru v WebLogic Server a zpřístupnit ji prostřednictvím jediné, veřejné s vyrovnáváním zatížení koncového bodu v Azure. To můžete udělat za předpokladu, použijte pevný port číslo pro všechny spravované servery (přiřazení není dynamicky z rozsahu) a nelze spustit více spravovaných serverů, než jsou počítače, který sleduje správce. To znamená, že není více než jeden spravovaný server na virtuální počítač). Pokud je výsledkem vaší konfigurace více serverů WebLogic se spouští, než jsou virtuální počítače (to znamená, pokud více instancí WebLogic Server sdílet stejný virtuální počítač), pak není možné pro více než jeden z těchto instancí serverů WebLogic Chcete-li vytvořit vazbu na dané číslo portu. Ostatní na tomto virtuálním počítači nezdaří.

   Při konfiguraci serveru pro správu automaticky přiřadit jedinečná čísla portů na spravované servery, pak Vyrovnávání zatížení není možné protože Azure nepodporuje mapování z jednoho veřejný port na několik privátních portů, jako by byla zapotřebí pro to konfigurace.
* **Více instancí WebLogic Server na virtuálním počítači.** V závislosti na požadavcích na vaše nasazení můžete zvážit spouštění více instancí WebLogic Server na stejném virtuálním počítači, pokud virtuální počítač je příliš velká. Na virtuálním počítači střední velikosti, která obsahuje dvě jádra, například může zvolit na dvě instance WebLogic Server. Přesto doporučujeme však, že se vyhnete zavedením jediné body selhání do architektury, které by případě, pokud jste použili pouze jeden virtuální počítač, na kterém běží více instancí WebLogic Server. Pomocí alespoň dva virtuální počítače může být lepším řešením a každý virtuální počítač pak může spustit víc instancí WebLogic Server. Každý výskyt WebLogic Server může být stále součástí stejného clusteru. Je však aktuálně není možné použít Azure ke koncovým bodům – nástroj pro vyrovnávání zatížení, které jsou vystavené takovýchto nasazeních WebLogic Server v rámci stejného virtuálního počítače, protože nástroj pro vyrovnávání zatížení Azure vyžaduje serverů s vyrovnáváním zatížení, které chcete distribuovat mezi jedinečný virtuální počítače.

## <a name="oracle-jdk-virtual-machine-images"></a>Image virtuálních počítačů Oracle JDK
* **JDK 6 a 7 nejnovějších aktualizací.** Přestože doporučujeme používat nejnovější veřejné, podporovanou verzi Javy (aktuálně Java 8), Azure také umožní JDK 6 a 7 obrázky k dispozici. To je určený pro starší aplikace, které ještě nejsou připravené na upgrade na sadu JDK 8. Během aktualizace předchozí sadu JDK Image může nadále již nebudou dostupné široké veřejnosti, daný partnerství s Microsoftem se společností Oracle, tak, aby obsahovala novější neveřejné aktualizace, které běžně nabízí Oracle pro jsou určené JDK 6 a 7 Image poskytované Azure pouze vybrané skupině Oracle je podporována zákazníků. Nové verze sady JDK obrázků bude k dispozici v čase s aktualizovanou verzemi sady JDK 6 a 7.

   K dispozici v JDK 6 a 7 imagí virtuálních počítačů a obrázky z nich, odvozené JDK jde použít jenom v rámci Azure.
* **64bitové sady JDK.** Image virtuálních počítačů Oracle WebLogic Server a imagí virtuálních počítačů Oracle JDK poskytovaný platformou Azure obsahují 64bitové verze systému Windows Server a sadu JDK.

## <a name="next-steps"></a>Další postup
Nyní máte přehled o aktuálním Oracle řešení založených na imagích virtuálních počítačů v Microsoft Azure. Dalším krokem je nasazení vaší první databáze Oracle v Azure.

> [!div class="nextstepaction"]
> [Vytvoření databáze Oracle v Azure](oracle-database-quick-create.md)
