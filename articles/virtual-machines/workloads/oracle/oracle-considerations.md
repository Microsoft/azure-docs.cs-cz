---
title: Oracle řešení v Microsoft Azure | Microsoft Docs
description: Další informace o podporovaných konfiguracích a omezeních Oracle řešení v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
manager: jeconnoc
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: daed709b4b4be87ba75f5539bd31c666b3a37414
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656342"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Řešení Oracle a jejich nasazení v Microsoft Azure
Tento článek se zabývá požadované informace o úspěšně nasadit různá řešení Oracle na Microsoft Azure. Tato řešení jsou založené na bitové kopie virtuálních počítačů, které zveřejnil Oracle v Azure Marketplace. Chcete-li získat seznam aktuálně dostupných imagí, spusťte následující příkaz:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
Od verze 6-16-2017 seznamu obrázků jsou následující:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Tyto Image jsou považovány za "Přineste vlastní licence" a jako takový vám bude jenom účtována pro výpočty, úložiště a náklady na sítě způsobené s virtuálním počítačem.  Předpokládá se, zda že jsou správně licenci k používání softwaru Oracle a zda máte aktuální smlouvu o podpoře na místě s Oracle. Oracle má zaručit mobilita licencí z místního do Azure. V tématu publikovaná [Oracle a Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Poznámka podrobnosti o mobilita licencí. 

Jednotlivce můžete taky rozhodnout základní svá řešení na vlastní image se vytvořit nový v Azure nebo nahrát vlastní image z jejich v místním prostředí.

## <a name="support-for-jd-edwards"></a>Podpora pro JD Edwards
Podle Oracle podporu Poznámka [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , JD Edwards EnterpriseOne verze 9.2 a vyšší jsou podporovány v **všechny veřejné Cloudová nabídka** , která splňuje jejich konkrétní `Minimum Technical Requirements` (MTR).  Budete muset vytvořit vlastní Image, které splňují jejich MTR specifikace pro kompatibilitu aplikací operačního systému a softwaru. 

## <a name="oracle-database-virtual-machine-images"></a>Bitové kopie virtuálních počítačů pro databázi Oracle
Oracle podporuje spuštěné verze Oracle DB 12.1 Standard a Enterprise v Azure na Image virtuálního počítače založené na Oracle Linux.  Nejlepšího výkonu dosáhnete pro produkční zatížení databáze Oracle na platformě Azure nezapomeňte správně velikost bitové kopie virtuálních počítačů a používat spravované disky, které jsou zajišťované Storage úrovně Premium. Pokyny o tom, jak rychle zprovoznění Oracle DB v Azure pomocí Oracle publikovaného image virtuálního počítače, [zkuste návod rychlý start databáze Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Možnosti konfigurace připojený disk

Připojené disky spoléhají na službu úložiště objektů Blob v Azure. Každý – standardní disk je schopen teoretické maximálně přibližně 500 vstupně výstupních operací za sekundu (IOPS). Naše nabídka disku premium upřednostňuje databáze vysoce výkonné úlohy a můžete dosáhnout až 5000 IOps na disku. Když jediný disk můžete použít, pokud, která vyhovuje vašim požadavkům na výkon – můžete zvýšit efektivní výkon IOPS, pokud používáte více připojených disků, data databáze rozloženy je a pak pomocí Oracle automatické úložiště Management (ASM). V tématu [Oracle automatické úložiště – přehled](http://www.oracle.com/technetwork/database/index-100339.html) Oracle ASM konkrétní informace. Příklad toho, jak nainstalovat a nakonfigurovat Oracle ASM na virtuálním počítači Azure Linux – můžete zkusit [instalace a konfigurace Oracle automatizované úložiště správy](configure-oracle-asm.md) kurzu.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle reálné aplikaci clusteru (Oracle RAC)
Oracle RAC slouží ke zmírnění selhání jednoho uzlu v místní konfiguraci clusteru s několika uzly. Přitom spoléhá na dva místní technologie, které nejsou součástí velkého rozsahu veřejných cloudových prostředích: vícesměrovým vysíláním sítě a sdíleného disku. Pokud vaše řešení databáze vyžaduje Oracle RAC v Azure, je třeba 3. softwarem a umožňuje tyto technologie.  A **Microsoft Azure certifikované** nabídky názvem [FlashGrid uzel pro Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) je k dispozici v Azure Marketplace, které zveřejnil FlashGrid Inc. Další informace o tomto řešení a jak to funguje v Azure, najdete v tématu [FlashGrid řešení stránky](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Aspekty vysoké dostupnosti a po havárii obnovení
Při použití Oracle – databáze v Azure, jste zodpovědní za implementaci vysoké dostupnosti a po havárii řešení obnovení žádné výpadky. 

Jde dosáhnout vysoké dostupnosti a zotavení po havárii pro Oracle Database Enterprise Edition (bez spoléhání na Oracle RAC) na Azure pomocí [Data Guard, aktivní Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), nebo [Oracle Golden brány](http://www.oracle.com/technetwork/middleware/goldengate), s dvě databáze na dva samostatné virtuální počítače. Virtuální počítače by měla být ve stejné [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) zajistit získají přístup k sobě navzájem přes trvalé privátní IP adresu.  Kromě toho doporučujeme umístit virtuální počítače ve stejné dostupnosti nastavit, aby umožňovala Azure a umístěte je do domén samostatné selhání a upgradu domény.  Budete chtít mít geografická redundance – může mít tyto dvě databáze replikují mezi dvěma různých oblastech a připojení dvě instance s bránou sítě VPN.

Máme kurzu "[DataGuard Oracle implementace v Azure](configure-oracle-dataguard.md)", který vás provede postup základní nastavení pro zkušební verzi to v Azure.  

S Oracle Data Guard jde dosáhnout vysoké dostupnosti s primární databází v jeden virtuální počítač, sekundární databáze (pohotovostní) v jiném virtuálním počítači a nastavení mezi nimi jednosměrný replikace. Výsledkem je přístup pro čtení k kopii databáze. S GoldenGate Oracle můžete nakonfigurovat obousměrnou replikaci mezi těmito dvěma databázemi. Zjistěte, jak nastavit řešení vysoké dostupnosti pro své databáze pomocí těchto nástrojů, najdete v tématu [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) a [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentaci na webu Oracle. Pokud budete potřebovat pro čtení a zápis přístup na kopii databáze, můžete použít [Oracle aktivní Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Máme kurzu "[GoldenGate Oracle implementace v Azure](configure-oracle-golden-gate.md)", který vás provede postup základní nastavení pro zkušební verzi to v Azure.

Navzdory s řešení s vysokou DOSTUPNOSTÍ a zotavení po Havárii navržen v Azure, budete chtít Ujistěte se, že máte zavedenou k obnovení databáze strategie zálohování.  Máme kurz [zálohování a obnovení databáze Oracle](oracle-backup-recovery.md) který vás provede procesem základní postup pro vytvoření konzistentního zálohování.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Bitové kopie virtuálního počítače Oracle WebLogic Server
* **Clustering je podporována v edici Enterprise jenom.** Máte licenci na použití WebLogic clustering jenom při použití Enterprise Edition WebLogic Server. Nepoužívejte clustering s WebLogic Server Standard Edition.
* **Vícesměrové vysílání UDP není podporována.** Azure podporuje jednosměrové vysílání UDP, ale není vícesměrové nebo všesměrové vysílání. WebLogic Server je moct spoléhají na Funkce jednosměrového vysílání Azure UDP. Pro nejlepší výsledky spoléhat na jednosměrového vysílání UDP, doporučujeme, aby velikost clusteru WebLogic zachovány statické, nebo zachovány s více než 10 spravovaných serverů, které jsou součástí clusteru.
* **WebLogic Server očekává veřejné a privátní porty být stejné pro přístup k T3 (například při použití podnikové JavaBeans).** Zvažte vícevrstvé scénář, kde aplikace služby vrstvy (EJB) běží na WebLogic Server cluster obsahuje dvě nebo více virtuálních počítačů, ve virtuální síti s názvem **SLWLS**. Úroveň klienta je v jiné podsíti, ve stejné virtuální síti, systémem jednoduchý program Java pokusu o volání EJB ve vrstvě služby. Protože je nutné k vrstvě služby Vyrovnávání zatížení, musí být vytvořen pro virtuální počítače v clusteru serveru WebLogic veřejný koncový bod Vyrovnávání zatížení sítě. Pokud privátní port, který zadáte, se liší od veřejný port (například 7006:7008), dojde k chybě například následující:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   To je proto pro všechny vzdálený přístup T3 WebLogic Server očekává portu služby Vyrovnávání zatížení a port serveru WebLogic spravované být stejné. V předchozím případě klient přistupuje k portu 7006 (port služby Vyrovnávání zatížení) a spravovaný server naslouchá na 7008 (privátní port). Toto omezení se vztahuje pouze pro přístup k T3, ne protokolu HTTP.

   K tomuto problému vyhnout, použijte jednu z následujících náhradních postupů:

  * Používejte stejná čísla portů privátní a veřejné pro koncové body skupinu s vyrovnáváním zatížení vyhrazený pro T3 přístup.
  * Při spouštění WebLogic Server, zahrnují následující parametr JVM:

         -Dweblogic.rjvm.enableprotocolswitch=true

Související informace najdete v článku **860340.1** v <http://support.oracle.com>.

* **Dynamické clustering a vyrovnávání zatížení omezení.** Předpokládejme, že chcete používat cluster s podporou dynamického v WebLogic Server a zpřístupnit ji prostřednictvím jednoho, veřejné Vyrovnávání zatížení sítě koncový bod v Azure. To lze provést, dokud, použijte pevný port číslo pro každou ze spravovaných serverů (přiřazen není dynamicky z rozsahu) a nespouštět více spravovaných serverů, než je počet počítačů ke sledování správce (to znamená, více než jeden spravovaný server na virtuální m achine). Pokud vaše konfigurace výsledkem další servery WebLogic spuštění než virtuální počítače (to znamená, kde více instancí serveru WebLogic sdílet stejný virtuální počítač), pak není možné pro více než jeden z těchto instancí serverů WebLogic Chcete-li vytvořit vazbu k dané číslo portu – jiné na tomto virtuálním počítači nezdaří.

   Při konfiguraci serveru pro správu automaticky přiřadit jedinečná čísla portů na jeho spravované servery, pak Vyrovnávání zatížení není možné protože Azure nepodporuje mapování z jedné veřejný port na více privátní porty, jako by byla zapotřebí pro to konfigurace.
* **Více instancí systému Weblogic Server na virtuálním počítači.** V závislosti na požadavcích vaší nasazení můžete zvážit možnost spuštěním několika instancí WebLogic Server na stejném virtuálním počítači, pokud je dostatečně velký virtuální počítač. Na virtuálním počítači střední velikosti, která obsahuje dvě jádra, například může zvolit spustit dvě instance WebLogic serveru. Doporučujeme ale stále vyhnout Úvod do vaší architektury, který by byl tento případ, pokud se používá jenom jeden virtuální počítač, který je spuštěno více instancí serveru WebLogic jediný bod selhání systému. Pomocí aspoň dva virtuální počítače může být lepším řešením a každý z těchto virtuálních počítačů pak může spustit víc instancí WebLogic Server. Každý z těchto instancí WebLogic Server stále může být součástí stejného clusteru. Poznámka: je však aktuálně není možné použít Azure k vyrovnávání zatížení koncových bodů, které jsou vystavené takovýchto nasazeních WebLogic Server v rámci stejného virtuálního počítače, protože pro vyrovnávání zatížení Azure vyžaduje serverů Vyrovnávání zatížení sítě se musí distribuovat mezi jedinečný virtuální počítače.

## <a name="oracle-jdk-virtual-machine-images"></a>Bitové kopie virtuálních počítačů JDK Oracle
* **JDK 6 a 7 nejnovější aktualizace.** Když vám doporučujeme používat nejnovější veřejné, podporovanou verzi jazyka Java (aktuálně Java 8), Azure také umožní JDK 6 a 7 bitové kopie k dispozici. To je určený pro starší aplikace, které ještě nejsou připravené k upgradu na JDK 8. Během aktualizace předchozí JDK Image může nadále již nebudou dostupné široké veřejnosti, danou Microsoft partnerství s Oracle, jsou určené JDK 6 a 7 Image poskytovaný Azure tak, aby obsahovala novější neveřejný aktualizace, které obvykle nabízí Oracle pro Vyberte skupiny Oracle je podporována zákazníků. Nové verze bitových kopií JDK bude k dispozici v čase s aktualizované verze JDK 6 a 7.

   K dispozici v této JDK 6 a 7 Image a virtuální počítače a bitové kopie z nich, odvozené JDK dá použít jenom v rámci Azure.
* **64bitová verze JDK.** Oracle WebLogic Server Image virtuálních počítačů a bitové kopie virtuálních počítačů Oracle JDK poskytovaný platformou Azure obsahují 64bitové verze systému Windows Server a sadu JDK.

## <a name="next-steps"></a>Další postup
Teď máte přehled o aktuální řešení Oracle na Microsoft Azure. Dalším krokem je nasazení první databáze Oracle na platformě Azure.
- Zkuste [vytvořit databázi Oracle na platformě Azure](oracle-database-quick-create.md) kurz a začněte.